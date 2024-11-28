---
title: 为大请求开启gzip
# 禁用目录
toc: true
comments: true
weight: 1
---
# Maven引入包
```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
<dependency>
     <groupId>org.aspectj</groupId>
     <artifactId>aspectjrt</artifactId>
     <version>1.9.9</version>
</dependency>
<dependency>
     <groupId>org.aspectj</groupId>
     <artifactId>aspectjweaver</artifactId>
     <version>1.9.9</version>
</dependency>
```
# 建立注解
> 利用注解实现自定义接口压缩
```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface Compress {
}
```

# 建立切面
```java

@Aspect
@Component
public class CompressionAspect {
    @Around("@annotation(com.your.program.package.Compress) || @within(com.注解位置.Compress)")
    public Object compressResponse(ProceedingJoinPoint joinPoint) throws Throwable {
        RequestAttributes requestAttributes = RequestContextHolder.getRequestAttributes();
        if (requestAttributes instanceof ServletRequestAttributes) {
            ServletRequestAttributes servletRequestAttributes = (ServletRequestAttributes) requestAttributes;
            HttpServletResponse response = servletRequestAttributes.getResponse();
            HttpServletRequest request = servletRequestAttributes.getRequest();
            if (response != null) {
                String encoding = request.getHeader("Accept-Encoding");
                if (encoding != null && encoding.contains("gzip")) {
                    GZIPResponseWrapper gzipResponseWrapper = new GZIPResponseWrapper(response);
                    response.setHeader("Content-Encoding", "gzip");
                    response.setContentType("application/json");
                    // Write the result to the GZIP response
                    Object result = joinPoint.proceed();
                    try (PrintWriter writer = gzipResponseWrapper.getWriter()) {
                        writer.write(JSONUtils.changeEntity2Json(result));
                        writer.flush();
                    }
                    gzipResponseWrapper.finishResponse();
                    return null;
                }
            }
        }

        return joinPoint.proceed();
    }
}
```

# GZIP包装
> 摘自网络
```java
public class GZIPResponseWrapper extends HttpServletResponseWrapper {
    private final ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
    private final GZIPOutputStream gzipOutputStream;
    private ServletOutputStream servletOutputStream;
    private PrintWriter printWriter;

    public GZIPResponseWrapper(HttpServletResponse response) throws IOException {
        super(response);
        gzipOutputStream = new GZIPOutputStream(byteArrayOutputStream);
    }

    @Override
    public ServletOutputStream getOutputStream() throws IOException {
        if (printWriter != null) {
            throw new IllegalStateException("getWriter() has already been called on this response.");
        }
        if (servletOutputStream == null) {
            servletOutputStream = new GZIPServletOutputStream(gzipOutputStream);
        }
        return servletOutputStream;
    }

    @Override
    public PrintWriter getWriter() throws IOException {
        if (servletOutputStream != null) {
            throw new IllegalStateException("getOutputStream() has already been called on this response.");
        }
        if (printWriter == null) {
            printWriter = new PrintWriter(new OutputStreamWriter(gzipOutputStream, getCharacterEncoding()));
        }
        return printWriter;
    }

    @Override
    public void flushBuffer() throws IOException {
        if (printWriter != null) {
            printWriter.flush();
        } else {
            servletOutputStream.flush();
        }
    }

    @Override
    public void setContentLength(int len) {
    }

    @Override
    public void setContentLengthLong(long len) {
    }

    @Override
    public void setBufferSize(int size) {
    }

    public void finishResponse() throws IOException {
        if (printWriter != null) {
            printWriter.close();
        } else if (servletOutputStream != null) {
            servletOutputStream.close();
        }
        gzipOutputStream.finish();
        byte[] compressedData = byteArrayOutputStream.toByteArray();
        ServletOutputStream responseOutputStream = getResponse().getOutputStream();
        responseOutputStream.write(compressedData);
        responseOutputStream.flush();
        responseOutputStream.close();
    }


    public class GZIPServletOutputStream extends ServletOutputStream {
        private OutputStream outputStream;

        public GZIPServletOutputStream(OutputStream outputStream) {
            this.outputStream = outputStream;
        }

        @Override
        public boolean isReady() {
            return true;
        }

        @Override
        public void setWriteListener(WriteListener writeListener) {
        }

        @Override
        public void write(int b) throws IOException {
            outputStream.write(b);
        }

        @Override
        public void write(byte[] b) throws IOException {
            outputStream.write(b);
        }

        @Override
        public void write(byte[] b, int off, int len) throws IOException {
            outputStream.write(b, off, len);
        }

        @Override
        public void flush() throws IOException {
            outputStream.flush();
        }

        @Override
        public void close() throws IOException {
            outputStream.close();
        }
    }
}

```
# Controller中加注解
```java
@Compress
public Object test(@PathVariable String key) {
    return "OK";
}
```

# 注意
如果是用的RestTemplate，springboot自带的SimpleClientHttpRequestFactory，是不支持gzip客户端自动识别的。请换为apache提供的HttpComponentsClientHttpRequestFactory。
```java
@Bean
public RestTemplate restTemplate() {
    // 请用apache的
    SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
    requestFactory.setConnectTimeout(3000);
    requestFactory.setReadTimeout(30_000);
    return new RestTemplate(requestFactory);
}

@Bean
public RestTemplate restTemplate() {
    // 采用apache的
    HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory(HttpClients.createDefault());
    requestFactory.setConnectTimeout(3000);
    requestFactory.setReadTimeout(30_000);
    return new RestTemplate(requestFactory);
}
```

