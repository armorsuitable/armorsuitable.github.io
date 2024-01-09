# Apache HttpClient 5 请求响应编码问题

## 请求体编码

```java
/**
 * Creates a StringEntity with the specified content and charset. The MIME type defaults
 * to "text/plain".
 *
 * @param string content to be used. Not {@code null}.
 * @param charset character set to be used. May be {@code null}, in which case the default
 *   is {@link StandardCharsets#ISO_8859_1} is assumed
 *
 * @since 4.2
 */
public StringEntity(final String string, final Charset charset) {
    this(string, ContentType.TEXT_PLAIN.withCharset(charset));
}
```

默认使用的是 ISO-8859-1编码的方式

```java

public static final ContentType TEXT_PLAIN = create(
        "text/plain", StandardCharsets.ISO_8859_1);

public static final ContentType TEXT_XML = create(
        "text/xml", StandardCharsets.UTF_8);
```

需要正确使用 UTF8 编码
```java
var httpPost = new HttpPost(url)
httpPost.setEntity(new StringEntity(requestJsonPkg, StandardCharsets.UTF_8))
```

## 响应编码

```java
String responseContent = client.execute(httpPost, new BasicHttpClientResponseHandler())
```

其中 BasicHttpClientResponseHandler 实现如下：
```java
/**
 * A {@link org.apache.hc.core5.http.io.HttpClientResponseHandler} that returns
 * the response body as a String for successful (2xx) responses. If the response
 * code was &gt;= 300, the response body is consumed
 * and an {@link org.apache.hc.client5.http.HttpResponseException} is thrown.
 * <p>
 * If this is used with
 * {@link org.apache.hc.client5.http.classic.HttpClient#execute(
 *  org.apache.hc.core5.http.ClassicHttpRequest,
 *  org.apache.hc.core5.http.io.HttpClientResponseHandler)},
 * HttpClient may handle redirects (3xx responses) internally.
 * </p>
 *
 * @since 4.0
 */
@Contract(threading = ThreadingBehavior.STATELESS)
public class BasicHttpClientResponseHandler extends AbstractHttpClientResponseHandler<String> {

    /**
     * Returns the entity as a body as a String.
     */
    @Override
    public String handleEntity(final HttpEntity entity) throws IOException {
        try {
            return EntityUtils.toString(entity);
        } catch (final ParseException ex) {
            throw new ClientProtocolException(ex);
        }
    }

    @Override
    public String handleResponse(final ClassicHttpResponse response) throws IOException {
        return super.handleResponse(response);
    }
}
```
对影响体编码使用 **EntityUtils.toString**
```java
/**
 * Reads the contents of an entity and return it as a String.
 * The content is converted using the character set from the entity (if any),
 * failing that, "ISO-8859-1" is used.
 *
 * @param entity the entity to convert to a string; must not be null
 * @return String containing the content.
 * @throws ParseException if header elements cannot be parsed
 * @throws IllegalArgumentException if entity is null or if content length &gt; Integer.MAX_VALUE
 * @throws IOException if an error occurs reading the input stream
 * @throws java.nio.charset.UnsupportedCharsetException Thrown when the named charset is not available in
 * this instance of the Java virtual machine
 */
public static String toString(final HttpEntity entity) throws IOException, ParseException {
    return toString(entity, DEFAULT_ENTITY_RETURN_MAX_LENGTH);
}

/**
 * Reads the contents of an entity and return it as a String.
 * The content is converted using the character set from the entity (if any),
 * failing that, "ISO-8859-1" is used.
 *
 * @param entity the entity to convert to a string; must not be null
 * @param maxResultLength
 *            The maximum size of the String to return; use it to guard against unreasonable or malicious processing.
 * @return String containing the content.
 * @throws ParseException if header elements cannot be parsed
 * @throws IllegalArgumentException if entity is null or if content length &gt; Integer.MAX_VALUE
 * @throws IOException if an error occurs reading the input stream
 * @throws java.nio.charset.UnsupportedCharsetException Thrown when the named charset is not available in
 * this instance of the Java virtual machine
 */
public static String toString(final HttpEntity entity, final int maxResultLength) throws IOException, ParseException {
    Args.notNull(entity, "HttpEntity");
    return toString(entity, ContentType.parse(entity.getContentType()), maxResultLength);
}
```

可以看到默认获取的是 Entity 中的编码，默认Entity 是没有指定编码的， 需要默认实现 **AbstractHttpClientResponseHandler<T>**, 并调用**`EntityUtil.toString(entity, charset)`** 来指定编码格式
```java
/**
 * Gets the entity content as a String, using the provided default character set
 * if none is found in the entity.
 * If defaultCharset is null, the default "ISO-8859-1" is used.
 *
 * @param entity must not be null
 * @param defaultCharset character set to be applied if none found in the entity,
 * or if the entity provided charset is invalid or not available.
 * @return the entity content as a String. May be null if
 *   {@link HttpEntity#getContent()} is null.
 * @throws ParseException if header elements cannot be parsed
 * @throws IllegalArgumentException if entity is null or if content length &gt; Integer.MAX_VALUE
 * @throws IOException if an error occurs reading the input stream
 * @throws java.nio.charset.UnsupportedCharsetException Thrown when the named entity's charset is not available in
 * this instance of the Java virtual machine and no defaultCharset is provided.
 */
public static String toString(
        final HttpEntity entity, final Charset defaultCharset) throws IOException, ParseException {
    return toString(entity, defaultCharset, DEFAULT_ENTITY_RETURN_MAX_LENGTH);
}
```
