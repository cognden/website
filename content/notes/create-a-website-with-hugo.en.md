---
title: Create a Website With Hugo
date: 2025-06-22T10:04:21+08:00
tags:
  - hugo
showToc: true
TocOpen: true
draft: false
description: This article will introduce how to create a static website using Hugo and configure the PaperMod theme.
summary: This article will introduce how to create a static website using Hugo and configure the PaperMod theme.
---

## Install Hugo and Create a Website
### Install Hugo

First, ensure that Hugo is installed on your computer. You can go to the [Hugo official website](https://gohugo.io/) to download the version suitable for your operating system. After installation, enter the following command in the terminal to verify if the installation was successful:

```shell
hugo version
```

### Create a New Project

First, we need to create a new Hugo website project. Please open the terminal (or command-line tool) and enter the following commands in sequence:

```shell
hugo new site quickstart
cd quickstart
git init
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

- `hugo new site quickstart`: Creates a new Hugo project named `quickstart`.
- `cd quickstart`: Enters the project directory.
- `git init`: Initializes the Git version control system to facilitate subsequent code management.
- `git submodule add ...`: Adds the PaperMod theme as a submodule to the `themes/` directory.

> **Note**: If the network is unstable, the download may fail. Please ensure a smooth network connection or try changing the mirror source.

### Specify the Theme

Next, we tell Hugo which theme to use to render the website. Edit the `hugo.toml` file in the project root directory and add the following content:

```toml
theme = "PaperMod"
```

### Preview the Website

After completing the above steps, run the following command to start the local development server:

```shell
hugo server
```

At this time, you can visit `http://localhost:1313/` in the browser to preview the website. Press `Ctrl + C` to stop the service.

## Configure the Hugo Website
### Basic Configuration

Hugo's core configuration file is `hugo.toml`, which determines the basic behavior of the website. We can modify it as needed.

{{% details title="Here is the complete configuration" closed="true" %}}

```toml
# Basic global configuration of the website
baseURL                = "https://example.org/" # Sets the root URL of the website for generating absolute links; should be changed to the actual domain name when deploying
languageCode           = "en"                   # Specifies the language code of the website content, used for the HTML lang attribute and language-related settings
title                  = "ExampleSite"          # The main title of the website, usually displayed in the browser tab and on the homepage
theme                  = "PaperMod"             # The name of the theme to use, determining the website's appearance style

# Resource configuration for website icons, used to define website icons of different sizes and purposes
[assets]
  # Icons are placed in the static/ directory
  favicon          = "/favicon.ico"          # The main favicon icon, usually displayed on the browser tab
  favicon16x16     = "/favicon-16x16.png"    # 16x16 pixel-sized favicon, suitable for low-resolution devices
  favicon32x32     = "/favicon-32x32.png"    # 32x32 pixel-sized favicon, providing a higher-definition display
  apple_touch_icon = "/apple-touch-icon.png" # The icon used when adding to the home screen on Apple devices

# Theme parameter configuration, affecting the website's appearance and functional behavior
[params]
  # SEO-related settings, helpful for search engine optimization
  title       = "ExampleSite"             # Sets the website title, displayed in browser tabs and search engine results
  description = "ExampleSite description" # A brief description of the website, used for search engine summary display
  keywords    = ["Blog", "Portfolio"]     # Defines a list of keywords related to the website to enhance search relevance

  # Basic website behavior configuration
  DateFormat   = "2006-01-02" # Sets the date formatting style, used for displaying time fields such as article publication times
  defaultTheme = "auto"       # Sets the default theme mode: "auto" means following the system's light/dark setting, or it can be set to "light" or "dark"

  # Home page welcome information
  [params.homeInfoParams]
    Title   = "Hi there 👋"
    Content = "Welcome to my blog"

# Global navigation menu configuration, applicable to main menu items in all language environments
[menu]
  [[menu.main]] # Defines a main menu item
    identifier = "posts"   # Menu item identifier
    name       = "Posts"      # Menu item name
    url        = "/posts/" # Menu item link
    weight     = 1         # Menu item weight, used for sorting
  [[menu.main]]
    identifier = "tags"
    name       = "Tags"
    url        = "/tags/"
    weight     = 2
  [[menu.main]]
    identifier = "search"
    name       = "Search"
    url        = "/search/"
    weight     = 3

# Output format settings
[outputs]
  # Provides support for the search function
  home = ["HTML", "RSS", "JSON"]
```

{{% /details %}}

#### Website Basic Information Settings

```toml
baseURL                = "https://example.org/"
languageCode           = "en"
title                  = "ExampleSite"
theme                  = "PaperMod"
```

These configuration items define the basic information of the website, such as the title, language, and theme. Among them:

- `baseURL` is the domain name used when deploying.
- `languageCode` control the language environment of the website.
- `title` is displayed on the browser tab.
- `theme` specifies the theme to use.

#### Icon Settings

To make the website more personalized, we can set icons for it. Add the following content to `hugo.toml`:

```toml
[assets]
  favicon          = "/favicon.ico"
  favicon16x16     = "/favicon-16x16.png"
  favicon32x32     = "/favicon-32x32.png"
  apple_touch_icon = "/apple-touch-icon.png"
```

These icons should be placed in the `static/` directory for display on different devices and browsers.

#### SEO Settings

Search engine optimization (SEO) is crucial for increasing website visibility. We can add the following configuration to the website:

```toml
[params]
  title       = "ExampleSite"
  description = "ExampleSite description"
  keywords    = ["Blog", "Portfolio"]
```

- `title`: The website title, displayed in search engine results.
- `description`: A brief description, used for summary display.
- `keywords`: A list of keywords, helping to improve search rankings.

### Navigation Menu Configuration

The navigation bar is an important entry for users to browse the website. We can define menu items in the following way:

```toml
[menu]
  [[menu.main]]
    identifier = "posts"
    name       = "Posts"
    url        = "/posts/"
    weight     = 1
  [[menu.main]]
    identifier = "tags"
    name       = "Tags"
    url        = "/tags/"
    weight     = 2
  [[menu.main]]
    identifier = "search"
    name       = "Search"
    url        = "/search/"
    weight     = 3
```

The above configuration will generate three menu items: Posts, Tags, and Search, sorted by weight.

- `identifier`: Gives the menu item a unique name (ID) for easy program recognition and use.
- `name`: This is the name displayed on the web page, and users will see the words " Posts ".
- `url`: The URL to jump to when clicking the menu item, here it is the "article list page" within the website.
- `weight`: Set the sorting order. The smaller the number, the higher the priority.

### Additional Modifications

Now the Posts page are still in a 404 state because PaperMod only has the tag page built-in and does not require manual creation.

To avoid this situation, we need to create `search.md` files, in the `content/` directory and add the following configuration:

- `search.md`

```yaml
---
title: "Search"
layout: "search"
summary: "Search page"
placeholder: "You can type here..."
---
```

- `title`: Menetapkan judul halaman.
- `layout`: Menentukan template layout yang digunakan untuk halaman.
- `summary`: Memberikan deskripsi singkat tentang halaman.
- `placeholder`: Mengatur teks placeholder pada kotak pencarian di halaman.

### Pengaturan Format Output

Untuk mendukung fungsi pencarian, kita perlu menentukan format output tambahan untuk halaman utama:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

In this way, the home page will not only generate an HTML page but also provide RSS subscription and JSON data interfaces.

---

So far, the website configuration is complete and can be uploaded to platforms such as GitHub Pages, Cloudflare Pages, or Netlify for deployment. For detailed procedures, please refer to [Host and deploy | HUGO](https://gohugo.io/hosting-and-deployment/).

## Optional: Configure Multi-Language Support
### Enable Multi-Language Support

To enable multi-language functionality, first, we need to modify Hugo's global configuration file `hugo.toml` and replace the original basic menu and language settings with the following structured multi-language configuration:

```toml
[languages]
  [languages.en] # English language configuration
    languageCode = "en"
    languageName = "English"
    weight       = 1

    [languages.en.menu]
      [[languages.en.menu.main]]
        identifier = "posts"
        name       = "Posts"
        url        = "/posts/"
        weight     = 1
      [[languages.en.menu.main]]
        identifier = "tags"
        name       = "Tags"
        url        = "/tags/"
        weight     = 2
      [[languages.en.menu.main]]
        identifier = "search"
        name       = "Search"
        url        = "/search/"
        weight     = 3

  [languages.zh] # Chinese language configuration
    languageCode = "zh"
    languageName = "中文"
    weight       = 2

    [languages.zh.menu]
      [[languages.zh.menu.main]]
        identifier = "posts"
        name       = "文章"
        url        = "/posts/"
        weight     = 1
      [[languages.zh.menu.main]]
        identifier = "tags"
        name       = "标签"
        url        = "/tags/"
        weight     = 2
      [[languages.zh.menu.main]]
        identifier = "search"
        name       = "搜索"
        url        = "/search/"
        weight     = 3
```

- `[languages]` is the main configuration block for Hugo's multi-language support.
- Each sub-item (such as `languages.en` and `languages.zh`) represents a language.
- `languageCode` is used to identify the language type, usually using ISO standard codes (e.g., `en` for English, `zh` for Chinese).
- `languageName` is the language name displayed on the web page.
- `weight` controls the language switching order; the smaller the value, the higher the priority.

> **Tip**: If you need more languages (such as French, German, etc.), you can simply continue to add them according to the above format.

### Create Multi-Language Page Files

To support multiple languages, we need to create corresponding page files for each language separately.

According to the description in the [Multilingual mode | HUGO](https://gohugo.io/content-management/multilingual/) official documentation, there are two methods to create and manage future articles:

1. Distinguish by File Name
	- `/content/search.en.md`
	- `/content/search.zh.md`
2. Distinguish by Directory
	- `/content/en/search.md`
	- `/content/zh/search.md`

The way to create depends on personal preference. Here we choose the "directory" form, while using file names requires no configuration—simply add the corresponding language code to the file name of the corresponding language. Therefore, we will focus on explaining the configuration of the directory form.

Enter the `content/` directory and organize the structure as follows:

```txt
content/
├── en/
│   └── search.md    # English version search page
└── zh/
    └── search.md    # Chinese version search page
```

Edit the corresponding language page files respectively. For example:

- `en/search.md`：

```yaml
---
title: "Search"
layout: "search"
summary: "Search page"
placeholder: "You can type here..."
---
```

- `zh/search.md`：

```yaml
---
title: "搜索"
layout: "search"
summary: "搜索页面"
placeholder: "这里可以输入..."
---
```

Then make the final modification to `hugo.toml` to set the default display language and specify the directory for each language.

```toml
baseURL                = "https://example.org/"
languageCode           = "en"
defaultContentLanguage = "en" # <<--- Newly added item
title                  = "ExampleSite"
theme                  = "PaperMod"

...

[languages]
  [languages.en]
    languageCode = "en"
    languageName = "English"
    contentDir = "content/en" # <<--- Newly added item
    weight       = 1

    [languages.en.menu]
      ...
  
  [languages.zh]
    languageCode = "zh"
    languageName = "中文"
    contentDir = "content/zh" # <<--- Newly added item
    weight       = 2

    [languages.zh.menu]
      ...

```

> **Tip**: After the `defaultContentLanguage` attribute, you can continue to add `defaultContentLanguageInSubdir = true` to enable generating the default language content in a subdirectory.

## References
- [Quick start | HUGO](https://gohugo.io/getting-started/quick-start/)
- [Configure menus | HUGO](https://gohugo.io/content-management/menus/)
- [Multilingual mode | HUGO](https://gohugo.io/content-management/multilingual/)
- [Installation | hugo-PaperMod Wiki](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)