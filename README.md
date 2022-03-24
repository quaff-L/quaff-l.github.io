# The Hacker theme

*Hacker is a Jekyll theme for GitHub Pages. You can [preview the theme to see what it looks like](http://pages-themes.github.io/hacker).*

## Jekyll 项目结构

一个较简单的 Jekyll 项目的文件结构如下：

```text
.
├── _config.yml  配置文件
├── _posts
│   └── 2020-03-14-jekyll-behind-github-pages.md  格式为/_posts/YYYY-mm-dd-title.md
├── about.md
├── readme.md
├── _layouts  /_layouts/ 里是 layout 模板。markdown 文件的内容会嵌入到以下 layout中作为最终的网页：
│   ├── default.html
│   ├── post.html
│   └── page.html
└── _includes   layout 中抽取出的公共部分，在 layout 中通过如 {%include footer.html %} 引入
    ├── footer.html
    └── header.html
```



> 索引页：任一目录中都会依次查找使用 `index.md` 或 `readme.md` 用以生成 `index.html`。比如 `/foo/readme.md` 对应网页的路径为 `/foo/index.html`，甚至访问 `/foo` 也可以
>
> 其它页：如 `about.md`。Github Pages 会将所有非下划线开头的路径中的 markdown 文件转为对应名字的网页。
>
> 网页模板：使用 [Liquid](https://link.zhihu.com/?target=https%3A//shopify.github.io/liquid/) 编写的模板文件（就是带 {`% if true %`} {`{ var | filter }`} {`% endif %`} 一类标记的 html 或 markdown）

## Usage

To use the Hacker theme:

1. Add the following to your site's `_config.yml`:

    ```yml
    remote_theme: pages-themes/hacker@v0.2.0
    plugins:
    - jekyll-remote-theme # add this line to the plugins list if you already have one
    ```

2. Optionally, if you'd like to preview your site on your computer, add the following to your site's `Gemfile`:

    ```ruby
    gem "github-pages", group: :jekyll_plugins
    ```

## Customizing

### Configuration variables

Hacker will respect the following variables, if set in your site's `_config.yml`:

```yml
title: [The title of your site]
description: [A short description of your site's purpose]
```

Additionally, you may choose to set the following optional variables:

```yml
show_downloads: ["true" or "false" (unquoted) to indicate whether to provide a download URL]
google_analytics: [Your Google Analytics tracking ID]
```

### Stylesheet

If you'd like to add your own custom styles:

1. Create a file called `/assets/css/style.scss` in your site
2. Add the following content to the top of the file, exactly as shown:
    ```scss
    ---
    ---
    
    @import "{{ site.theme }}";
    ```
3. Add any custom CSS (or Sass, including imports) you'd like immediately after the `@import` line

*Note: If you'd like to change the theme's Sass variables, you must set new values before the `@import` line in your stylesheet.*

### Layouts

If you'd like to change the theme's HTML layout:

1. For some changes such as a custom `favicon`, you can add custom files in your local `_includes` folder. The files [provided with the theme](https://github.com/pages-themes/hacker/tree/master/_includes) provide a starting point and are included by the [original layout template](https://github.com/pages-themes/hacker/blob/master/_layouts/default.html).
2. For more extensive changes, [copy the original template](https://github.com/pages-themes/hacker/blob/master/_layouts/default.html) from the theme's repository<br />(*Pro-tip: click "raw" to make copying easier*)
3. Create a file called `/_layouts/default.html` in your site
4. Paste the default layout content copied in the first step
5. Customize the layout as you'd like

### Customizing Google Analytics code

Google has released several iterations to their Google Analytics code over the years since this theme was first created. If you would like to take advantage of the latest code, paste it into `_includes/head-custom-google-analytics.html` in your Jekyll site.
