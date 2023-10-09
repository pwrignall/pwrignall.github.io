# chronicles.campachoochoo.net

[![Build and Publish Github Pages](https://github.com/pwrignall/pwrignall.github.io/actions/workflows/staticsite.yml/badge.svg?branch=master)](https://github.com/pwrignall/pwrignall.github.io/actions/workflows/staticsite.yml)

An [Eleventy](https://11ty.dev) blog making use of the [Eleventy Satisfactory](https://github.com/mendhak/eleventy-satisfactory) theme.

Keeping some of the notes from the theme's README here for quick reference.

## How to run the blog locally

**Running it with Node**

Requires Node 18. First get all the dependencies.

```
npm install
```

To serve the site, and watch for changes:

```
npm run start
```

Then browse to http://localhost:8080/

To just build the site once (normally used for Github Actions):

```
npm install
npm run build
```

### Configuration

- [Set your values in the metadata.json file](https://code.mendhak.com/eleventy-satisfactory/edit-the-metadata/)
- [Set the footer links and images](https://code.mendhak.com/eleventy-satisfactory/set-footer-links/)

### Write a post

The posts go in the `posts` folder. Drafts can go in the `drafts` folder and will only appear when running locally (`npm run start` or `docker-compose up`) but not when building (`npm run build`).

- [Set the date of a post](https://code.mendhak.com/eleventy-satisfactory/set-date-of-post/)
- [Add an image](https://code.mendhak.com/eleventy-satisfactory/post-with-an-image/)
- [Add an image gallery](https://code.mendhak.com/eleventy-satisfactory/post-with-a-gallery/)
- [Link to another post or URL](https://code.mendhak.com/eleventy-satisfactory/posting-links/)
- [Add a notice panel with info, warning, success, danger](https://code.mendhak.com/eleventy-satisfactory/post-notice/)
- [Add a code block with syntax highlighting](https://code.mendhak.com/eleventy-satisfactory/post-with-code/)
- [Add a Github Gist](https://code.mendhak.com/eleventy-satisfactory/post-with-github-gists/)
- [Add a Github Repo Card](https://code.mendhak.com/eleventy-satisfactory/github-repo-card/)
- [Add videos and audio](https://code.mendhak.com/eleventy-satisfactory/post-with-iframes-videos-third-party/)
- [Controlling extra-wide images, videos, code blocks](https://code.mendhak.com/eleventy-satisfactory/extra-wide-full-width-images-videos/)
- [Set Opengraph preview image and metadata](https://code.mendhak.com/eleventy-satisfactory/opengraph-preview-data/)
