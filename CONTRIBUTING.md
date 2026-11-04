# Contributing Articles

This repository accepts practical, original, and well-sourced content across technology, programming, education, productivity, and other useful subjects.

## Before writing

- Choose a topic that solves a real reader question.
- Search the repository for an existing article on the same subject.
- Prefer official documentation, standards, and primary sources for technical claims.
- Use your own explanation and examples. Do not copy entire articles from other websites.

## Create an article

1. Copy `templates/ARTICLE_TEMPLATE.md`.
2. Choose a topic folder under `articles/`. Create a new folder when a topic does not fit an existing category.
3. Name the file using lowercase words separated by hyphens.
4. Set the frontmatter `slug` to exactly match the filename, without `.md`.
5. Add a concise description, publication date, category, and relevant tags.
6. Include practical examples and explain important limitations.
7. Add links to official references at the end.

## Linking tools and other articles

For articles intended for a website with registered tools, use the exact local tool route in `relatedTools`. For example:

```yaml
relatedTools:
  - title: "JSON Formatter"
    url: "/json-formatter"
```

Only use tool routes that actually exist. Use article slugs, not guessed URLs, in `relatedArticles`.

## Images

Images are optional. Do not add a cover image path unless the corresponding image file is available. When preparing an article for the website, store its image files under `public/articles/<slug>/` in the website project and provide meaningful alternative text.

## Preparing an article for website publication

The repository is a content library; committing an article here does not automatically publish it to a website.

For the current Markdown-based website workflow:

1. Review the article and set `draft: false` only when it is ready.
2. Copy the article file to `src/content/articles/<slug>.md` in the website project. The website loader expects the filename to match the frontmatter slug.
3. Copy any referenced images to the matching `public/articles/<slug>/` directory.
4. Verify related tool routes and article slugs against the website source.
5. Run the website build and check the generated article page before deployment.
