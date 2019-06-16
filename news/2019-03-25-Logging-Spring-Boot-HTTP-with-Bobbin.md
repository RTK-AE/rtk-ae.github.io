# Logging Spring Boot HTTP with Bobbin

**2019-03-25**

Today we will demonstrate an amazing example of how [Bobbin](/Bobbin) Slf4j Logger can help in a very common use case - 
logging Spring Boot HTTP requests and responses.

Furthermore - we will log the HTTP messages into a separate log files!

This example is applicable both for `Java` and `Groovy`.

To enable logging of HTTP exchange in Spring Boot simply add the below line into `application.properties`:

```
logging.level.org.springframework.web=TRACE
```

> ❇ Above configuration will not log `HTTP Request body`.

To enable logging of `HTTP Request body`, drop-in the below configuration class:

```groovy
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.web.filter.CommonsRequestLoggingFilter
import org.springframework.web.servlet.config.annotation.ContentNegotiationConfigurer
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer
@Configuration
class WebMvcConfiguration implements WebMvcConfigurer {
    @Bean
    CommonsRequestLoggingFilter logFilter() {
        CommonsRequestLoggingFilter filter = new CommonsRequestLoggingFilter()
        filter.setIncludeQueryString(true)
        filter.setIncludePayload(true)
        filter.setMaxPayloadLength(100000)
        filter.setIncludeHeaders(false)
        filter.setAfterMessagePrefix("REQUEST DATA : ")
        return filter
    }
}
```

Now let's configure `Bobbin`.

Add dependency to your `build.gradle`:
```
compile "io.infinite:bobbin:2.0.4"
```

Create `Bobbin.json` in your application resource directory or in the working directory:

```json
{
  "levels": "['debug', 'info', 'warn', 'error'].contains(level)",
  "destinations": [
    {
      "name": "io.infinite.bobbin.destinations.FileDestination",
      "properties": {
        "fileName": "\"./LOGS/THREADS/${threadGroupName}/${threadName}/${level}/${threadName}_${level}_${date}.log\""
      },
      "classes": "className.contains('io.infinite.')"
    },
    {
      "name": "io.infinite.bobbin.destinations.FileDestination",
      "properties": {
        "fileName": "\"./LOGS/ALL/WARNINGS_AND_ERRORS_${date}.log\""
      },
      "levels": "['warn', 'error'].contains(level)"
    },
    {
      "name": "io.infinite.bobbin.destinations.ConsoleDestination",
      "levels": "['warn', 'error'].contains(level)"
    }
  ]
}
```

One of the main features of `Bobbin` is that it easily separates the logging output into dedicated log files
based on Level, Thread name, Class or Package names or other conditions.

So why would we have to bother looking for our HTTP logs in the files with other data?

Let's separate Spring Boot HTTP logs into a dedicated file for our ease!

Add a new `destination` into `Bobbin.json`:

```json
    {
      "name": "io.infinite.bobbin.destinations.FileDestination",
      "properties": {
        "fileName": "\"./LOGS/SPRING_WEB/${threadGroupName}/${threadName}/${level}/${threadName}_${level}_${date}.log\""
      },
      "classes": "className.contains('org.springframework.web')"
    }
``` 

As you can see we are writing logs from classes with Package Name having `'org.springframework.web'` into `SPRING_WEB` 
directory!

Simple, isn't it?

Here is how the complete `Bobbin.json` config looks like:

```json
{
  "levels": "['debug', 'info', 'warn', 'error'].contains(level)",
  "destinations": [
    {
      "name": "io.infinite.bobbin.destinations.FileDestination",
      "properties": {
        "fileName": "\"./LOGS/THREADS/${threadGroupName}/${threadName}/${level}/${threadName}_${level}_${date}.log\""
      },
      "classes": "className.contains('io.infinite.')"
    },
    {
      "name": "io.infinite.bobbin.destinations.FileDestination",
      "properties": {
        "fileName": "\"./LOGS/ALL/WARNINGS_AND_ERRORS_${date}.log\""
      },
      "levels": "['warn', 'error'].contains(level)"
    },
    {
      "name": "io.infinite.bobbin.destinations.FileDestination",
      "properties": {
        "fileName": "\"./LOGS/SPRING_WEB/${threadGroupName}/${threadName}/${level}/${threadName}_${level}_${date}.log\""
      },
      "classes": "className.contains('org.springframework.web')"
    },
    {
      "name": "io.infinite.bobbin.destinations.ConsoleDestination",
      "levels": "['warn', 'error'].contains(level)"
    }
  ]
}
```

And here is the `.\LOGS\SPRING_WEB\main\http-nio-8089-exec-1\debug\http-nio-8089-exec-1_debug_2019-03-22.log` file contents:

```
2019-03-22 15:16:03:404|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Detected StandardServletMultipartResolver|null;null
2019-03-22 15:16:03:441|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|enableLoggingRequestDetails='false': request parameters and headers will be masked to prevent unsafe logging of potentially sensitive data|null;null
2019-03-22 15:16:03:467|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|Before request [uri=/pigeon/plugins/input/rest/SelfTest?format=yaml]|null;null
2019-03-22 15:16:03:473|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|GET "/pigeon/plugins/input/rest/SelfTest?format=yaml", parameters={masked}|null;null
2019-03-22 15:16:03:486|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping|Mapped to public org.springframework.http.ResponseEntity<io.infinite.pigeon.springdatarest.controllers.CustomResponse> io.infinite.pigeon.springdatarest.controllers.PluginRestController.get(javax.servlet.http.HttpServletRequest)|null;null
2019-03-22 15:16:04:293|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Using 'text/yaml', given [text/yaml] and supported [application/json, application/*+json, application/json, application/*+json, application/cbor, text/yaml]|null;null
2019-03-22 15:16:04:294|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Writing [io.infinite.pigeon.springdatarest.controllers.CustomResponse(response:[outputMessages:http://localhost:8089/pigeon/outputMessages/search/searchByInputExternalIdAndSourceName?sourceName=SELF_TEST_PLUGIN&externalId=1553253364128, httpLogs:http://localhost:8089/pigeon/readableHttpLogs?format=yaml&sourceName=SELF_TEST_PLUGIN&externalId=1553253364128])]|null;null
2019-03-22 15:16:04:340|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Completed 200 OK|null;null
2019-03-22 15:16:04:341|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|REQUEST DATA : uri=/pigeon/plugins/input/rest/SelfTest?format=yaml]|null;null
2019-03-22 15:17:35:142|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|Before request [uri=/pigeon/plugins/input/rest/EchoTest]|null;null
2019-03-22 15:17:35:143|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|POST "/pigeon/plugins/input/rest/EchoTest", parameters={masked}|null;null
2019-03-22 15:17:35:146|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping|Mapped to public org.springframework.http.ResponseEntity<io.infinite.pigeon.springdatarest.controllers.CustomResponse> io.infinite.pigeon.springdatarest.controllers.PluginRestController.post(javax.servlet.http.HttpServletRequest,java.lang.String)|null;null
2019-03-22 15:17:35:148|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.RequestResponseBodyMethodProcessor|Read "application/x-www-form-urlencoded;charset=UTF-8" to ["%7B%0A%22message%22%3A+%22Test+2019-03-22+15-17-34-463%22%0A%7D="]|null;null
2019-03-22 15:17:35:271|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Using 'application/json;q=.2', given [text/html, image/gif, image/jpeg, */*;q=.2, */*;q=.2] and supported [application/json, application/*+json, application/json, application/*+json, application/cbor, text/yaml]|null;null
2019-03-22 15:17:35:272|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Writing [io.infinite.pigeon.springdatarest.controllers.CustomResponse(response:%7B%0A%22message%22%3A+%22Test+2019-03-22+15-17-34-463%22%0A%7D=)]|null;null
2019-03-22 15:17:35:273|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Completed 200 OK|null;null
2019-03-22 15:17:35:273|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|REQUEST DATA : uri=/pigeon/plugins/input/rest/EchoTest;payload=%7B%0A%22message%22%3A+%22Test+2019-03-22+15-17-34-463%22%0A%7D=]|null;null
2019-03-22 15:22:50:350|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Detected StandardServletMultipartResolver|null;null
2019-03-22 15:22:50:378|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|enableLoggingRequestDetails='false': request parameters and headers will be masked to prevent unsafe logging of potentially sensitive data|null;null
2019-03-22 15:22:50:400|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|Before request [uri=/pigeon/plugins/input/rest/SelfTest?format=yaml]|null;null
2019-03-22 15:22:50:404|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|GET "/pigeon/plugins/input/rest/SelfTest?format=yaml", parameters={masked}|null;null
2019-03-22 15:22:50:417|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping|Mapped to public org.springframework.http.ResponseEntity<io.infinite.pigeon.springdatarest.controllers.CustomResponse> io.infinite.pigeon.springdatarest.controllers.PluginRestController.get(javax.servlet.http.HttpServletRequest)|null;null
2019-03-22 15:22:51:138|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Using 'text/yaml', given [text/yaml] and supported [application/json, application/*+json, application/json, application/*+json, application/cbor, text/yaml]|null;null
2019-03-22 15:22:51:139|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Writing [io.infinite.pigeon.springdatarest.controllers.CustomResponse(response:[outputMessages:http://localhost:8089/pigeon/outputMessages/search/searchByInputExternalIdAndSourceName?sourceName=SELF_TEST_PLUGIN&externalId=1553253770986, httpLogs:http://localhost:8089/pigeon/readableHttpLogs?format=yaml&sourceName=SELF_TEST_PLUGIN&externalId=1553253770986])]|null;null
2019-03-22 15:22:51:179|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Completed 200 OK|null;null
2019-03-22 15:22:51:180|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|REQUEST DATA : uri=/pigeon/plugins/input/rest/SelfTest?format=yaml]|null;null
2019-03-22 15:24:12:749|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Detected StandardServletMultipartResolver|null;null
2019-03-22 15:24:12:783|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|enableLoggingRequestDetails='false': request parameters and headers will be masked to prevent unsafe logging of potentially sensitive data|null;null
2019-03-22 15:24:12:809|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|Before request [uri=/pigeon/plugins/input/rest/SelfTest?format=yaml]|null;null
2019-03-22 15:24:12:813|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|GET "/pigeon/plugins/input/rest/SelfTest?format=yaml", parameters={masked}|null;null
2019-03-22 15:24:12:828|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping|Mapped to public org.springframework.http.ResponseEntity<io.infinite.pigeon.springdatarest.controllers.CustomResponse> io.infinite.pigeon.springdatarest.controllers.PluginRestController.get(javax.servlet.http.HttpServletRequest)|null;null
2019-03-22 15:24:13:584|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Using 'text/yaml', given [text/yaml] and supported [application/json, application/*+json, application/json, application/*+json, application/cbor, text/yaml]|null;null
2019-03-22 15:24:13:586|debug|http-nio-8089-exec-1|org.springframework.web.servlet.mvc.method.annotation.HttpEntityMethodProcessor|Writing [io.infinite.pigeon.springdatarest.controllers.CustomResponse(response:[outputMessages:http://localhost:8089/pigeon/outputMessages/search/searchByInputExternalIdAndSourceName?sourceName=SELF_TEST_PLUGIN&externalId=1553253853415, httpLogs:http://localhost:8089/pigeon/readableHttpLogs?format=yaml&sourceName=SELF_TEST_PLUGIN&externalId=1553253853415])]|null;null
2019-03-22 15:24:13:640|debug|http-nio-8089-exec-1|org.springframework.web.servlet.DispatcherServlet|Completed 200 OK|null;null
2019-03-22 15:24:13:640|debug|http-nio-8089-exec-1|org.springframework.web.filter.CommonsRequestLoggingFilter|REQUEST DATA : uri=/pigeon/plugins/input/rest/SelfTest?format=yaml]|null;null
```

Never the logging configuration has been so easy.

Go-on and try [Bobbin](/Bobbin) today! It works perfectly with both `Java` and `Groovy`.

---

<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://i-t.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
                            