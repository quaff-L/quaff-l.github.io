# 目录结构

| 文件/目录|描述 |
| :------:|:------:|
| `_config.yml` | 保存[配置](http://jekyllcn.com/docs/configuration/)数据。很多配置选项都可以直接在命令行中进行设置，<br />但是如果你把那些配置写在这儿，你就不用非要去记住那些命令了。 |
| `_drafts` | drafts（草稿）是未发布的文章。这些文件<br />的格式中都没有 `title.MARKUP` 数据。 |
| `_includes` | 你可以加载这些包含部分到你的布局或者文章中以方便重用。可以用这个<br />标签 `{% include file.ext %}` 来把文件 `_includes/file.ext` 包含进来。 |
| `_layouts` | layouts（布局）是包裹在文章外部的模板。布局可以在 [YAML 头信息](http://jekyllcn.com/docs/frontmatter/)中根据不同文章进行选<br />择。 这将在下一个部分进行介绍。标签 `{{ content }}` 可以将content插入页面中。 |
| `_posts` | 这里放的就是你的文章了。文件格式很重要，必须要符合: `YEAR-MONTH-DAY-title.MARKUP`。<br /> [永久链接](http://jekyllcn.com/docs/permalinks/) 可以在文章中自己定制，但是数据和标记语言都是根据文件名来确定的。 |
| `_data` | 格式化好的网站数据应放在这里。jekyll 的引擎会自动加载在该目录下所有的 yaml 文件（后缀是 `.yml`, `.yaml`, `.json` 或者 `.csv` ）。这些文件可以经由 ｀site.data｀ 访问。如果有一个 `members.yml` 文件在该目录下，你就可以通过 `site.data.members` 获取该文件的内容。 |
| `_site` | 一旦 Jekyll 完成转换，就会将生成的页面放在这里（默认）。最好将<br />这个目录放进你的 `.gitignore` 文件中。 |

# `_config.yml`

### 自定义 WEBRick 标题

```yaml
# 文件: _config.yml
webrick:
  headers:
    My-Header: My-Value
    My-Other-Header: My-Other-Value
```

### 头信息默认值

通过使用 YAML 头信息可以指定站点的页面和文章的配置。设置一些东西例如布局或者自定义标题，亦或是给文章指定一个更精确的日期/时间，这都可以往页面或文章的头信息添加数据来实现。

很多时候，你会发现你在重复填写很多配置项。在每个文件里设置相同的布局，对每篇文章添加相同的分类，等等。你甚至可能添加自定义变量，如作者名，这可能对你博客上大部分的文章来说是相同的。

Jekyll 提供了一个方法在站点配置中设置这些默认值，而不是在每次创建一个新的文章或页面重复此配置。要做到这一点，你可以在项目根目录下的 `_config.yml` 文件里设置 `defaults` 的值指定全站范围的默认值。

`defaults` 保存一个范围/值的对的数组，这定义了哪些默认值要设置到一个特定的文件路径下的文件，或者可选的，在该路径下指定 的文件类型的文件。

```yaml
defaults:
  -
    scope:
      path: "" # 一个空的字符串代表项目中所有的文件
    values:
      layout: "default"
```

在这里，我们把 `values` 应用给 scope 路径里的所有文件。因为路径被设为空字符串，它将会应用到你项目里的**全部文件**。你可能不想给项目在的每个文件都设置一个布局，例如 css 文件，所以你可以在 `scope` 下指定 `type` 的值。

```yaml
defaults:
  -
    scope:
      path: "" # 一个空的字符串代表项目中所有的文件
      type: "posts" # 以前的 `post`， 在 Jekyll 2.2 里。
    values:
      layout: "default"
```

现在，这只会给类型是 `posts` 的文件设置默认布局。你可以使用的不同的类型分别是 `pages` ， `posts` ， `drafts` 或者其他你站点中的集合。当创建一个范围/值的对，如果选择了 `type`，你必须指定一个值给 `path` 。

正如前面所提到的，您可以给 `defaults` 设置多个范围/值的对。

```yaml
defaults:
  -
    scope:
      path: ""
      type: "posts"
    values:
      layout: "my-site"
  -
    scope:
      path: "projects"
      type: "pages" # 以前的 `page`， 在 Jekyll 2.2 里。
    values:
      layout: "project" # 覆盖之前的默认布局
      author: "Mr. Hyde"
```

有了这些默认值，所有的文章都会使用 `my-site` 布局。任何在 `projects/` 文件夹下的 html 文件会使用 `project` 布局。这些文件也会拥有值为 `Mr. Hyde` 的 `page.author` 这一 [liquid 变量](http://jekyllcn.com/docs/variables/)，同时这些页面的类别被设为 `project` 。

```yaml
collections:
  - my_collection:
    output: true

defaults:
  -
    scope:
      path: ""
      type: "my_collection" # 你的站点里的一个集合，复数形式
    values:
      layout: "default"
```

> ### 优先权
>
> Jekyll 会应用你在 `_config.yml` 文件里 `defaults` 部分的所有配置设定。然而，你可以选择 覆盖这些设定，通过在范围/值的对里指定一个更具体的路径。
>
> 你可以观察上一个例子。一开始我们设置了 `my-site` 这一默认的布局。然后，使用一个更具体的路径， 我们把在 `projects/` 路径下的文件的默认布局设置为 `project` 。这可以使用你想在页面或者文章的头信息里设定的任意值来达成。
>
> 最后，如果你在 `_config.yml` 的 `defaults` 部分设置了站点的默认值，你可以在页面或文章的文件里覆盖这些设定。你需要做的是在在页面或文章的头信息里指定要覆盖的设定。例如：
>
> ```yaml
> # In _config.yml
> ...
> defaults:
>   -
>     scope:
>       path: "projects"
>       type: "pages"
>     values:
>       layout: "project"
>       author: "Mr. Hyde"
>       category: "project"
> ...
> ```
>
> ```yaml
> # In projects/foo_project.md
> ---
> author: "John Smith"
> layout: "foobar"
> ---
> The post text goes here...
> ```
>
> 在站点建立时 `projects/foo_project.md` 的布局会是 `foobar`而不是`project` ， `author` 是 `John Smith`而不是 `Mr. Hyde` 。

### 默认配置

```yaml
# 目录结构
source:      .
destination: ./_site
plugins:     ./_plugins
layouts:     ./_layouts
data_source: ./_data
collections: null

# 阅读处理
safe:         false
include:      [".htaccess"]
exclude:      []
keep_files:   [".git", ".svn"]
encoding:     "utf-8"
markdown_ext: "markdown,mkdown,mkdn,mkd,md"

# 内容过滤
show_drafts: null
limit_posts: 0
future:      true
unpublished: false

# 插件
whitelist: []
gems:      []

# 转换
markdown:    kramdown
highlighter: rouge
lsi:         false
excerpt_separator: "\n\n"
incremental: false

# 服务器选项
detach:  false
port:    4000
host:    127.0.0.1
baseurl: "" # does not include hostname

# 输出
permalink:     date
paginate_path: /page:num
timezone:      null

quiet:    false
defaults: []

# Markdown 处理器
rdiscount:
  extensions: []

redcarpet:
  extensions: []

kramdown:
  auto_ids:       true
  footnote_nr:    1
  entity_output:  as_char
  toc_levels:     1..6
  smart_quotes:   lsquo,rsquo,ldquo,rdquo
  enable_coderay: false

  coderay:
    coderay_wrap:              div
    coderay_line_numbers:      inline
    coderay_line_number_start: 1
    coderay_tab_width:         4
    coderay_bold_every:        10
    coderay_css:               style
```

### Markdown 选项

Jekyll 支持的 Markdown 渲染器中有的有额外的选项。

### Redcarpet[Permalink](http://jekyllcn.com/docs/configuration/#redcarpet)

Redcarpet支持设置 `extensions` ，值为一个字符串数组，每个字符串都是 `Redcarpet::Markdown` 类的扩展，相应的扩展就会设置为 `true` 。

Jekyll 处理两个特别的 Redcarpet 扩展：

- `no_fenced_code_blocks` — 默认的， Jekyll 设置扩展 `fenced_code_blocks` (用三个波浪线或重音线标记代码区间) 为 `true` ，这或许是跟 GitHub 积极的采用有关。当使用Jekyll的时候， Redcarpet 的扩展 `fenced_code_blocks` 无效，作为替代方案，你可以使用这个语义相反的扩展来禁用 fenced code 。

注意你还可以这样来配置语言以支持语法高亮：

~~~yaml
    ```ruby
    # ...ruby code
    ```
~~~

有了 fenced code blocks 和 highlighter enabled，就会静态地高亮代码了；如果没有任何高亮语法 syntax hightlighter，将增加一个 `class="LANGUAGE"` 属性到 `<code>` 元素，用于给不同的 JavaScript 代码高亮库做为提示。

- `smart` — 这个伪扩展pseudo-extension会打开 SmartyPants ，将引号转为 " 、连字符转为 em (`---`) 和 en (`--`) 破折号。

Redcarpet 所有其他扩展保持他们本来的名字，并且在 Jekyll 中不能给 `smart` 加渲染选项。 [Redcarpet 的 README 中有可用扩展的列表。](https://github.com/vmg/redcarpet/blob/v3.2.2/README.markdown#and-its-like-really-simple-to-use) 确保你看的 README 是正确的版本：Jekyll 当前用的是 v3.2.x。最常用的扩展是如下：

- `tables`
- `no_intra_emphasis`
- `autolink`

### Kramdown[Permalink](http://jekyllcn.com/docs/configuration/#kramdown)

除了上面提到的默认值，你可以打开 Github Flavored Markdown 的识别，通过输入一个 值为 “GFM” 的 `input` 选项。

例如，在你的 `_config.yml` 中：

```
kramdown:
  input: GFM
```

### 自定义 Markdown 处理器[Permalink](http://jekyllcn.com/docs/configuration/#自定义-markdown-处理器)

如果你对创建一个自定义 Markdown 处理器感兴趣，你真的很幸运！在 `Jekyll::Converters::Markdown` 的命名空间下新建一个类：

```
class Jekyll::Converters::Markdown::MyCustomProcessor
  def initialize(config)
    require 'funky_markdown'
    @config = config
  rescue LoadError
    STDERR.puts 'You are missing a library required for Markdown. Please run:'
    STDERR.puts '  $ [sudo] gem install funky_markdown'
    raise FatalException.new("Missing dependency: funky_markdown")
  end

  def convert(content)
    ::FunkyMarkdown.new(content).convert
  end
end
```

一旦你建立了你的类，并且它作为一个插件被正确安装到 `_plugins` 文件夹 或者 作为 gem 被正确安装，你要在你的 `_config.yml` 里指定它：

```
markdown: MyCustomProcessor
```

# 

