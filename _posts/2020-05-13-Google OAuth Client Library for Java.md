---
title: "Google OAuth Client Library for Java"
categories:
  - Tec
tags:
  - OAuth
  - OAuth2.0
  - Google OAuth Client
toc: true
---
记录一下Google OAuth Java Client的学习过程，这套Lib可以很方便的集成第三方OAuth接口，如果对方没有提供SDK，这将是一个非常不错的选择。不止针对Google，任何第OAuth API都可以使用。该Lib包括了OAuth授权的流程，与Token的过期自动刷新功能。


# 安装


## 添加依赖管理器

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.google.cloud</groupId>
            <artifactId>libraries-bom</artifactId>
            <version>2.2.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```


## 添加依赖，这里使用了最新的Apache HTTP Client，否则无法支持PATCH METHOD

```xml
<dependency>
    <groupId>com.google.oauth-client</groupId>
    <artifactId>google-oauth-client</artifactId>
    <version>1.30.4</version>
</dependency>
<dependency>
    <groupId>com.google.api-client</groupId>
    <artifactId>google-api-client-jackson2</artifactId>
    <version>1.30.4</version>
</dependency>
<dependency>
    <groupId>com.google.http-client</groupId>
    <artifactId>google-http-client-apache-v2</artifactId>
    <version>1.35.0</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.12</version>
</dependency>
```


# 初始化OAuthFlow


## 初始化所需参数

```kotlin
val httpTransport by lazy { ApacheHttpTransport() } //此处使用ApacheHttpTransport替代了默认的实现
val jacksonFactory: JacksonFactory = JacksonFactory.getDefaultInstance()
val clientId: String = "Client Id"
val clientSecret: String = "Client Secret"
val tokenServerUrl: String = "Token Server URL"
val authServerUrl: String = "Auth Server URL"
val callbackUrl: String = "Call Back URL"
```


## 初始化OAuthFlow

```kotlin
AuthorizationCodeFlow.Builder(
            BearerToken.authorizationHeaderAccessMethod(),
            httpTransport, jacksonFactory, GenericUrl(tokenServerUrl),
            BasicAuthentication(clientId, clientSecret), 
            clientId, authServerUrl)
            .setCredentialDataStore(Global.getBean(CredentialDataStore::class.java))  //此处从SpringContent中获取了持久化令牌的实现，稍后会展示其代码。 
            .build()
```


# 授权流程


## 授权流程第一步，获取授权跳转连接

```kotlin
val authUrl = oauthFlow.newAuthorizationUrl()
authUrl.state = UUID.randomUUID().toString()  //此处state用于跟踪授权请求，视情况添加，一般会存储state与userId的映射，并为第二步准备。
authUrl.redirectUri = callbackUrl   //指定回调地址
return authUrl
```


## 授权流程第二步，使用回调返回的state和code获取AccessToken与RefreshToken

```kotlin
val authReq = oauthFlow.newTokenRequest(authorizationCode)  // 此处参数为回调返回的code
authReq.redirectUri = callbackUrl
val tokenResponse = authReq.execute()
return oauthFlow.createAndStoreCredential(tokenResponse, userId)  //此处的userId可以通过第一步存储的state与userId映射获取，这一步会调用DataStore中的方法存储令牌信息。
```


# 准备请求工厂与持久化组件

定义令牌初始化类

```kotlin
class CredentialInitializer(private val credential: Credential) : HttpRequestInitializer {
    override fun initialize(request: HttpRequest) {
        credential.initialize(request)
        request.parser = JsonObjectParser(jacksonFactory)
    }
}
```

通过userId获取请求工厂

```kotlin
/*
 * 从DataStore中加载指定userId的令牌
 */
fun loadCredential(userId: String): Credential? {
    return oauthFlow.loadCredential(userId)
}

fun getRequestFactory(userId: String): HttpRequestFactory? {
    val credential = loadCredential(userId)?: return null
    return httpTransport.createRequestFactory(CredentialInitializer(credential))
}
```

定义DataStoreFactory，此处没有写实现，因为此处DataStoreFactory只用于OAuth

```kotlin
class CredentialDataStoreFactory : AbstractDataStoreFactory() {
    override fun <V : Serializable?> createDataStore(id: String?): DataStore<V> = throw UnsupportedOperationException()
}
```

定义DataStore实现，Google提供了一些默认的实现，只用于存储在内存或者文件中，视情况使用

```kotlin
import com.google.api.client.auth.oauth2.StoredCredential
import com.google.api.client.util.store.AbstractDataStore
import com.google.api.client.util.store.DataStore

@Component
class CredentialDataStore : AbstractDataStore<StoredCredential>(CredentialDataStoreFactory(),
        StoredCredential.DEFAULT_DATA_STORE_ID) {

    override fun get(key: String): StoredCredential? {
        // 此处可以自己选择使用什么方式持久化令牌。
    }

    override fun set(key: String, value: StoredCredential): DataStore<StoredCredential> {
        // 此处可以自己选择使用什么方式持久化令牌。
    }

    override fun delete(key: String): DataStore<StoredCredential> {
        // 此处可以自己选择使用什么方式持久化令牌。
    }

    override fun clear(): DataStore<StoredCredential> = throw UnsupportedOperationException()

    override fun values(): MutableCollection<StoredCredential> = throw UnsupportedOperationException()

    override fun keySet(): MutableSet<String> = throw UnsupportedOperationException()
}
```


# 使用

定义URL, `GenericUrl`类可自动根据子类添加了`@Key`注解的属性生成带有`queryParam`的URL，您可根据具体的第三方API定义子类，此处`PageableUrl`模拟了添加特定的分页`queryParam`

```kotlin

abstract class PageableUrl  (
        url: String,
        @Key("page_size") val pageSize: Int = 300,
        @Key("page_number") var pageNumber: Int = 1
) : GenericUrl(url)

class UserUrl(@Key val status: String) : PageableUrl("https://api.test.com/v2/users")

```

例如：`UserUrl("active")`生成的URL为：

```
https://api.test.com/v2/users?page_number=1&page_size=300&status=active
```

实体定义, 所有需要进行序列化操作的字段都要添加`@Key`注解

```kotlin
data class User (
        @Key var id: String? = null,
        @Key("first_name") var firstName: String? = null,
        @Key("last_name") var lastName: String? = null
)
```

生成GET请求

```kotlin
val getUserRequest = requestFactory.buildGetRequest(userUrl)
```

分享一个自动获取所有分页数据的代码

```kotlin
fun <D: Any, T: PageResp<D>> getAllData(req: HttpRequest, kClass: KClass<T>, filter: ((D) -> Boolean)? = null): List<D> {
    val genericUrl = req.url
    val mutableList = mutableListOf<D>()
    return if(genericUrl is PageableUrl){
        var pageNum = 0
        var pageCount: Int
        do {
            pageNum ++
            genericUrl.pageNumber = pageNum
            val resp = req.execute().parseAs(kClass.java)  //此处会自动将返回的JSON反序列化
            mutableList.addAll(resp.getData().run {
                filter?.let { this.filter { filter(it) } }?:this
            })
            pageCount = resp.pageCount
        } while(pageCount < pageNum)
         mutableList.toList()
    } else throw IllegalArgumentException("Url Not Pageable Url")
}
```

与自动获取所有分页数据代码配套的类

```kotlin
abstract class PageResp<T> (
        @Key("page_count") var pageCount: Int = 1
) : DataResp<T>

interface DataResp<T> {
    fun getData(): List<T>
}
```

GitHub 地址：

[https://github.com/googleapis/google-oauth-java-client](https://github.com/googleapis/google-oauth-java-client){:target="_blank"}

Google帮助文档（GitHub Wiki中也有帮助文档）：

[https://developers.google.cn/api-client-library/java/google-api-java-client/oauth2?hl=zh-cn](https://developers.google.cn/api-client-library/java/google-api-java-client/oauth2?hl=zh-cn){:target="_blank"}

