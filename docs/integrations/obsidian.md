---
title: Sync all your reading to Obsidian
editLink: true
---

# {{ $frontmatter.title }}

Omninove integrates with Obsidian via the [obsidian-omnivore](https://github.com/omnivore-app/obsidian-omnivore) plugin. The plugin allows for customizing the data synced from Omnivore to Obsidian and controlling the layout of the data.

[[toc]]

## Features

* Import your highlights and saved article
* Create graphs based on Omnivore data
* Filter imported data using Omnivores [advanced search syntax](https://omnivore.app/help/search)
* Custom templates for imported data

## Installation

1. Install and enable the plugin from the Community Plugins section of Obsidian or [build it from source](https://github.com/omnivore-app/obsidian-omnivore) and load the unpacked plugin.
2. Sign up for an [Omnivore account](https://omnivore.app).
3. Go to [Omnivore](https://omnivore.app/settings/api) and create an API key.
4. Open the Omnivore plugin settings in Obsidian and add your API key.
5. Run Omnivore sync by opening the command palette (Ctrl + P or CMD + P). Type Omnivore and select the option **Omnivore: Sync**.

## Customizing which data is synced from Omnivore to Obsidian

[Obsidian Sync Filter](./images/obsidian-sync-filter.png)

The Omnivore Obsidian plugin offers three options for which data is imported:

* Import all my articles: This will import every article you save to Omnivore and the labels (tags), highlights, and notes you have added to the article.
* Import just highlights: This will import every article you have saved that has highlights
* Advanced: This allows you to define a search filter to import. For example, import just Newsletters by using `label:Newsletter`.

## Controlling the layout of the data imported to Obsidian

The Omnivore Obsidian plugin uses the [mustache](https://mustache.github.io/) template language to define how imported data is saved to Obsidian. The plugin settings allow you to define the template for a article layout. The default templates will be used if you do not define your own.

### Mustache template language

Mustache is a simple and logic-less templating language that allows you to create templates that are easy to read and maintain. Mustache templates consist of tags, which are placeholders that are replaced with actual values when the template is rendered.

There are several types of Mustache tags, including:

::: v-pre

* `{{ variable }}`: Renders the value of a variable.
* `{{# section }}` ... `{{/ section }}`: Renders a section of the template if the value of the section is true, such as an array or an object.
* `{{^ inverted section }}` ... `{{/ inverted section }}`: Renders a section of the template if the value of the section is false.
* `{{! comment }}`: Adds a comment to the template, which is ignored when the template is rendered.

In addition to the basic Mustache tags, there are also several built-in Mustache functions that you can use in your templates, such as:

* `{{# list }}` ... `{{/ list }}`: Renders a section of the template for each item in a list.
* `{{^ emptyList }}` ... `{{/ emptyList }}`: Renders a section of the template if a list is not empty.
* `{{& variable }}` or `{{{ variable }}}`: Renders the value of a variable without escaping HTML characters.
* `{{> partial }}`: Renders a partial template, which is a reusable template that can be included in other templates.
:::

### Variables available in the template

* id: The id of the article. This will be used as the id of the file in frontmatter
* title: The title of the article
* omnivoreUrl: The URL of the article in Omnivore
* siteName: The name of the site the article was saved from
* originalUrl: The URL of the original article
* author: The author of the article
* dateSaved: The date the article was saved to Omnivore in your perferrred date format
* description: The description of the article
* content: The content of the article in Markdown format
* labels: A list of labels attached to the page
  * name: The name of the label, for example `Newsletter`
* note: The note attached to the article
* type: The type of page, for example `ARTICLE` or `FILE`
* datePublished: The date the article was published in your perferrred date format
* dateRead: The date the article was read in your perferrred date format
* dateArchived: The date the article was archived in your perferrred date format
* fileAttachment: The file attachment attached to the article
* state: The state of the article, for example `READING` or `COMPLETED`
* wordsCount: The number of words in the article
* readLength: The length of time it took to read the article in minutes
* highlights: A list of highlights attached to the article
  * text: The text of the highlight
  * highlightUrl: The URL of the highlight in Omnivore
  * labels: A list of labels attached to the highlight
    * name: The name of the label, for example `Newsletter`
  * note: The note attached to the highlight
  * dateHighlighted: The date the highlight was created in your perferrred date format

Default template:

```
---
id: {{{id}}}
title: {{{title}}}
{{#author}}
author: {{{author}}}
{{/author}}
{{#labels.length}}
tags:
{{#labels}} - {{{name}}}
{{/labels}}
{{/labels.length}}
date_saved: {{{dateSaved}}}
{{#datePublished}}
date_published: {{{datePublished}}}
{{/datePublished}}
---

# {{{title}}}
#Omnivore

[Read on Omnivore]({{{omnivoreUrl}}})
[Read Original]({{{originalUrl}}})

{{#highlights.length}}
## Highlights

{{#highlights}}
> {{{text}}} [⤴️]({{{highlightUrl}}}) {{#labels}} #{{name}} {{/labels}}
{{#note}}

{{{note}}}
{{/note}}

{{/highlights}}
{{/highlights.length}}
```

### Importing the Full Article Content

::: warning Full content import does not work for PDFs
:::

The content of articles is converted to Markdown before import, this means we need them to avoid HTML
escaping when being inserted into Obsidian. To do this, use the triple Mustache syntax.
Any highlights that you have created in Omnivore will be rendered as Markdown highlights.
So to import the full article content:

```
{{{ content }}}
```

For example:

```
---
id: {{{id}}}
title: >
  {{{title}}}
{{#author}}
author: >
  {{{author}}}
{{/author}}
{{#labels.length}}
tags:
{{#labels}} - {{{name}}}
{{/labels}}
{{/labels.length}}
date_saved: {{{dateSaved}}}
{{#datePublished}}
date_published: {{{datePublished}}}
{{/datePublished}}
---

# {{{title}}}
#Omnivore

[Read on Omnivore]({{{omnivoreUrl}}})
[Read Original]({{{originalUrl}}})

{{{ content }}}

```

### Function map in the template

* lowerCase: Converts a string to lowercase
* upperCase: Converts a string to uppercase
* upperCaseFirst: Converts the first character of a string to uppercase

Example of using a function in a template to convert the state of an article to lowerCase:

```
state:: [[{{#lowerCase}}{{state}}{{/lowerCase}}]]
```

## Sync all the articles into a single note

By default, Omnivore will sync articles into separate pages in Obsidian. If you would like to sync all your articles into a single note, you can turn on the option **Is Single File** and use a fixed **Filename** in the plugin settings.

## Scheduled Sync

By default, Omnivore does not automatically sync your data. You can enable scheduled sync by entering a number of minutes in the **Frequency** setting. For example, if you enter `60`, Omnivore will sync your data every hour and you can always turn it off by entering `0`.
