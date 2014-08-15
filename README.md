metalsmith-prismic
==================

A Metalsmith.io plugin to pull in content from [Prismic.io]


## Installation

    $ npm install --save metalsmith-prismic

## Configuration

### CLI Usage

  Install the node modules and add `metalsmith-prismic` to your list of plugins in `metalsmith.json`. Include the 
- `url` (eg. https://lesbonneschoses.prismic.io/api) of your Prismic.io repository. 
- `accessToken` is optional, depending if your repository needs it or not. 
- `release` with the name of the content release you want to generate; if none specified then master release will be generated
- `linkResolver` an optional function to generate links; if none specified then a default format of "/<document.type>/<document.id>/<document.slug>" will be used


```json
{
  "plugins": {
    "metalsmith-prismic": {
      "url": "<your repository's API url>",
      "accessToken": "<optional accessToken>",
      "release": "<optional release name>",
    }
  }
}
```

### Javascript Usage

  Instead of using the CLI, you can configure your Metalsmith.io project to use the metalsmith-prismic plugin via Javascript. 

```js
var prismic = require('metalsmith-prismic');

// pull in content from Prismic
.use(prismic({
    "url": "<your repository's API url>",
    "accessToken": "<optional access token>",
    "release": "<optional release name>",
    "linkResolver": <optional linkResolver function>
}))
```

## Usage

Pulling in content from the site's repository in [Prismic.io] for display is a two step process. 

##### Query (and Order) the Content
In your file's metadata add the Prismic queries and orderings
```yaml
---
template: index_en.hbt
prismic:
  page-header-footer:
    query: '[[:d = at(document.type, "page-header-footer")]]'
  hero-slide:
    query: '[[:d = at(document.type, "hero-slide")]]'
    orderings: '[my.hero-slide.seqNum]'
---
```
By default the query runs against the _everything_ Prismic form. To run against a different form (eg. a collection), provide the form name (eg. collection name)
```yaml
---
template: index_en.hbt
prismic:
  page-header-footer:
    query: '[[:d = at(document.type, "page-header-footer")]]'
  hero-slide:
    query: '[[:d = at(document.type, "hero-slide")]]'
    orderings: '[my.hero-slide.seqNum]'
  blog:
    query: '[[:d = at(document.type, "blog")]]'
    formName: 'tech-related'
---
```
This pulls the Prismic response into the file's metadata.

```yaml
---
  template: "index_en.hbt"
  prismic: 
    page-header-footer: 
      query: "[[:d = at(document.type, \"page-header-footer\")]]"
      results: 
        - 
          id: <id>
          type: "page-header-footer"
          href: <url>
          tags: []
          slug: "home"
          slugs: 
            - "home"
          linkedDocuments: []
          data: 
            homeLabel_en: 
              json: 
                value: "Home"
              html: "<span>Home</span>"
            videoLabel_en: 
              json: 
                value: "Video"
              html: "<span>Video</span>"
            prizesLabel_en: 
              json: 
                value: "Prizes"
              html: "<span>Prizes</span>"
            newsLabel_en: 
              json: 
                value: "News"
              html: "<span>News</span>"
            qcLabel_en: 
              json: 
                value: "Questions/Comments"
              html: "<span>Questions/Comments</span>"
    hero-slide: 
      query: "[[:d = at(document.type, \"hero-slide\")]]"
      orderings: "[my.hero-slide.seqNum]"
      results: 
        - 
          id: <id>
          type: "hero-slide"
          href: <url>
          tags: []
          slug: "welcome-to-our-website"
          slugs: 
            - "welcome"
            - "welcome-to-our-website"
          linkedDocuments: []
          data: 
            title_en: 
              json: 
                blocks: 
                  - 
                    type: "heading1"
                    text: "Introducing our new site!"
                    spans: []
              html: "<h1>Introducing our new site!</h1>"
            introduction_en: 
              json: 
                blocks: 
                  - 
                    type: "paragraph"
                    text: "Welcome to our new site, generated by Metalsmith.io with content from Prismic!"
                    spans: []
                  - 
                    type: "paragraph"
                    text: "Why? Because the two combined is a sweet combo"
                    spans: []
              html: "<p>Welcome to our new site, generated by Metalsmith.io with content from Prismic!</p><p>Why? Because the two combined is a sweet combo</p>"
  contents: []
  mode: "0644"

---
```

##### Displaying Content
Now that this content from Prismic is available in the file's metadata, you can display it by using the [metalsmith-templates] plugin. For example, here is how to do it with the plugin's [Handlebars] engine.

```html
<div class="collapse navbar-collapse" id="navbar-collapse">
    <ul class="nav navbar-nav navbar-right">
        <li class="active"><a href="#intro">{{{ prismic.page-header-footer.results.[0].data.homeLabel_en.html }}}</a></li>
        <li><a href="#video">{{{ prismic.page-header-footer.results.[0].data.videoLabel_en.html }}}</a></li>
        <li><a href="#prizes">{{{ prismic.page-header-footer.results.[0].data.prizesLabel_en.html }}}</a></li>
        <li><a href="#news">{{{ prismic.page-header-footer.results.[0].data.newsLabel_en.html }}}</a></li>
        <li><a href="#comments">{{{ prismic.page-header-footer.results.[0].data.qcLabel_en.html }}}</a></li>
    </ul>
</div>
```

## To Do
- This plugin is still early in development and has only been tested with a limited set of Prismic queries and predicates. If anything isn't working please let me know!
- Unit tests!!

## License

  MIT

[Prismic.io]:https://prismic.io/
[metalsmith-templates]:https://github.com/segmentio/metalsmith-templates
[Handlebars]:http://handlebarsjs.com/
