# 测试页面2


&lt;!--more--&gt;

# 进阶篇


探索 Hugo - **FixIt** 主题的的进阶使用。

&amp;lt;!--more--&amp;gt;

## 自定义样式

{{&amp;lt; version 0.2.8 changed &amp;gt;}}

{{&amp;lt; admonition &amp;gt;}}
Hugo **extended** 版本对于自定义样式是必需的。
{{&amp;lt; /admonition &amp;gt;}}

通过定义自定义 `.scss` 样式文件，**FixIt** 主题支持可配置的样式。

包含自定义 `.scss` 样式文件的目录相对于 **你的项目根目录** 的路径为 `assets/css`。

在 `assets/css/_override.scss` 中，你可以覆盖 `themes/FixIt/assets/css/_variables.scss` 中的变量以自定义样式。

这是一个例子：

```scss
@import url(&amp;#39;https://fonts.googleapis.com/css?family=Fira&amp;#43;Mono:400,700&amp;amp;display=swap&amp;amp;subset=latin-ext&amp;#39;);
$code-font-family: Fira Mono, Source Code Pro, Menlo, Consolas, Monaco, monospace;
```

在 `assets/css/_custom.scss` 中，你可以添加一些 CSS 样式代码以自定义样式。

### 页面宽度 {#page-style}

{{&amp;lt; version 0.2.13 &amp;gt;}}

FixIt 主题提供了页面宽度配置选项 `pageStyle` 并提供三种选项。

- **narrow** 保留 `&amp;lt;v0.2.13` 页面目录宽度比
- **normal** 新的默认页面目录宽度比
- **wide** 较大的页面目录宽度比

另外，你还可以在 `assets/css/_custom.scss` 中，自定义 `pageStyle` 值

例如： `pageStyle=&amp;#34;custom&amp;#34;`

```scss
[data-page-style=&amp;#39;custom&amp;#39;] {
  @media only screen and (min-width: 1441px) {
    %page-style {
      width: 70%;
    }
  }

  @media only screen and (min-width: 1440px) {
    %page-style {
      width: 60%;
    }
  }

  @media only screen and (min-width: 1200px) {
    %page-style {
      width: 56%;
    }
  }
}
```

### 打印样式 {#print-style}

{{&amp;lt; version 0.2.13 &amp;gt;}}

在 FixIt 主题中，提供有三个打印视图相关的 CSS 类

- `page-break-before` 在元素之前插入分页符
- `page-break-after` 在元素之后插入分页符
- `print-d-none` 在打印视图中隐藏元素

下面是一个简单的例子：

```html
&amp;lt;div class=&amp;#34;page-break-before&amp;#34;&amp;gt;&amp;lt;/div&amp;gt;
&amp;lt;div class=&amp;#34;page-break-after&amp;#34;&amp;gt;&amp;lt;/div&amp;gt;
&amp;lt;div class=&amp;#34;print-d-none&amp;#34;&amp;gt;
  你希望在打印视图中隐藏的某些内容写在此处。
&amp;lt;/div&amp;gt;
```

如果设置 `goldmark.parser.attribute.block` 为 `true`，你也可以这样用：

```markdown
{.page-break-before}
{.page-break-after}

你希望在打印视图中隐藏的某些内容写在此处。
{.print-d-none}
```

## 自定义脚本

{{&amp;lt; version 0.2.16 &amp;gt;}}

包含自定义脚本文件 `custom.js` 的目录相对于 **你的项目根目录** 的路径为 `assets/js`。

如果脚本文件 `assets/js/custom.js` 存在，它将在每篇文章和页面的末尾执行。

## 自定义模板

Hugo 允许你通过覆盖主题模板来改造主题，例如：你可以创建 `layouts/404.html` 文件以覆盖 `themes/FixIt/layouts/404.html`，从而实现自定义 FixIt 主题 404 页面模板的需求。

但是，对于主题的大多数模板，FixIt 通常不建议你这样做，因为这样可能会导致未来升级主题困难。

为了避免升级冲突问题，基于这一特性，FixIt 主题在常用的位置开放了几个空的模板以供用户客制化，详见 [主题配置][theme-config] 中的 `params.customFilePath` 参数。

例如，FixIt 主题文档站点通过创建 `layouts/partials/custom/profile.html` 自定义了首页的 profile 部分。

首先，启用自定义文件路径参数：

```toml
[params.customFilePath]
  profile = &amp;#34;custom/profile.html&amp;#34;
```

然后，自定义模板：

```go-html-template
&amp;lt;div class=&amp;#34;profile-custom&amp;#34;&amp;gt;
  {{- if .Site.BuildDrafts -}}
    &amp;lt;a href=&amp;#34;https://fixit.lruihao.cn&amp;#34; target=&amp;#34;_blank&amp;#34; rel=&amp;#34;external&amp;#34; title=&amp;#34;Go to Production Environment&amp;#34;&amp;gt;
      &amp;lt;img src=&amp;#34;https://img.shields.io/github/deployments/hugo-fixit/docs/Production?style=flat&amp;amp;label=Production&amp;amp;logo=vercel&amp;#34; alt=&amp;#34;Production environment&amp;#34;&amp;gt;
    &amp;lt;/a&amp;gt;
  {{- else -}}
    &amp;lt;a href=&amp;#34;https://pre.fixit.lruihao.cn&amp;#34; target=&amp;#34;_blank&amp;#34; rel=&amp;#34;external&amp;#34; title=&amp;#34;Go to Preview Environment&amp;#34;&amp;gt;
      &amp;lt;img src=&amp;#34;https://img.shields.io/github/deployments/hugo-fixit/docs/Preview?style=flat&amp;amp;label=Preview&amp;amp;logo=vercel&amp;#34; alt=&amp;#34;Preview environment&amp;#34;&amp;gt;
    &amp;lt;/a&amp;gt;
  {{- end -}}
  &amp;lt;a href=&amp;#34;https://demo.fixit.lruihao.cn&amp;#34; target=&amp;#34;_blank&amp;#34; rel=&amp;#34;external&amp;#34; title=&amp;#34;FixIt Demo&amp;#34;&amp;gt;
    &amp;lt;img src=&amp;#34;https://img.shields.io/badge/Demo-orange&amp;#34; alt=&amp;#34;FixIt Demo&amp;#34;&amp;gt;
  &amp;lt;/a&amp;gt;
&amp;lt;/div&amp;gt;
```

## PWA 支持

这部分内容在 [PWA 支持页面][pwa-support] 中介绍。

&amp;lt;!-- link reference definition --&amp;gt;
[theme-config]: {{&amp;lt; relref path=&amp;#34;/documentation/basics#theme-configuration&amp;#34; &amp;gt;}}
[pwa-support]: {{&amp;lt; relref path=&amp;#34;/guides/pwa-support&amp;#34; &amp;gt;}}


---

&gt; 作者: [Lruihao](https://lruihao.cn)  
&gt; URL: https://fixit.lruihao.cn/zh-cn/documentation/advanced/  


---

> 作者:   
> URL: https://blog.zy9826.site/posts/6ecca83/  

