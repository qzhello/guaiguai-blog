# Elasticsearch 源码构建

```java{lineos=table, linenostart=42}
void handleRequest(RestRequest request, RestChannel channel, NodeClient client) throws Exception;
```

```{linenos=table,hl_lines=[42,43],linenostart=42,filename="RestHandle.java"}
void handleRequest(RestRequest request, RestChannel channel, NodeClient client) throws Exception;
```

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

{{< callout emoji="🌐" >}}
Hugo 可用于创建各种网站，包括博客、作品集、文档网站等
{{< /callout >}}

{{< callout type="info" >}}
请访问 GitHub 查看最新版本
{{< /callout >}}