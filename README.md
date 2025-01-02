<h1 align="center">Payload CMS</h1>

<h3 align="center">
 Reconciliation Road Challenge
</h3>

<br/>

<p align="center">
  <a href="#Requirements"><strong>Requirements</strong></a> ·
  <a href="#Quick-Start"><strong>Quick Start</strong></a> 
  <a href="#How-it-works"><strong>How it works</strong></a> 
</p>
<br/>

# Project Overview
The CMS Payload is where we store content to serve the LMS (Learning Management Service). This CMS acts as a database that provides us with custom structures for the LMS such as topic unit and topic content. 

# Requirements
- Node.js >= 18.17.0
- Docker

# Quick Start

To spin up this example locally, follow these steps:

1. Clone

   If you have not done so already, you need to have a standalone copy of this repo on your machine. If you've already cloned this repo, skip to [Development](#development).
   #### Method 1 (recommended)
   Go to Payload Cloud and [clone this template](https://payloadcms.com/new/clone/website). This will create a new repository on your GitHub account with this template's code which you can then clone to your own machine.

   #### Method 2
   Use the `create-payload-app` CLI to clone this template directly to your machine:

    npx create-payload-app@latest my-project -t websit

   #### Method 3
   Use the `git` CLI to clone this template directly to your machine:

   git clone -n --depth=1 --filter=tree:0 https://github.com/payloadcms/payload my-project && cd my-project && git sparse-checkout set --no-cone templates/website && git checkout && rm -rf .git && git init && git add . && git mv -f templates/website/{.,}* . && git add . && git commit -m "Initial commit"

2. `cp .env.example .env` to copy the example environment variables
3. `yarn` to install dependencies
4. `docker-compose up` to fetch containers and run them (one is a MongoDB container, and one is a container created from the source code of this project)
5. Seed the CMS:

   To seed the database with a few pages, posts, and projects you can run `yarn seed`. This template also comes with a `GET /api/seed` endpoint you can use to seed the database from the admin panel.
   The seed script will also create two users for demonstration purposes only:
   1. Demo Author
      - Email: `demo-author@payloadcms.com`
      - Password: `password`
      - Role: `admin`
   2. Demo User
      - Email: `demo-user@payloadcms.com`
      - Password: `password`
      - Role: `user`

> NOTICE: seeding the database is destructive because it drops your current database to populate a fresh one from the seed template. Only run this command if you are starting a new project or can afford to lose your current data.

# Production

To run Payload in production, you need to build and serve the Admin panel. To do so, follow these steps:

1. Invoke the `payload build` script by running `yarn build` or `npm run build` in your project root. This creates a `./build` directory with a production-ready admin bundle.
1. Finally run `yarn serve` or `npm run serve` to run Node in production and serve Payload from the `./build` directory.
1. When you're ready to go live, see [Deployment](#deployment) for more details.

# How it works

### We only use these collections to develop the CBH LMS app
- #### Topics
  This is where the CMS stores Topics. A Topic can be created here and we can assign Content Items to the Topic we want to. We can understand a Topic as a course unit that has many course contents (Content Items) and we can choose which content we want to be in a created unit.
- #### Content Items
  This is where the CMS stores all of its course content. We can create a new Content Item here using the schema provided by the CMS.

### The below Collections and Globals are just for show and we may remove them in the future

- #### Users (Authentication)

  Users are auth-enabled and encompass both admins and regular users based on the value of their `roles` field. Only `admin` users can access your admin panel to manage your website whereas `user` can authenticate on your front-end to leave [comments](#comments) and read [premium content](#premium-content) but have limited access to the platform. See [Access Control](#access-control) for more details.

  For additional help, see the official [Auth Example](https://github.com/payloadcms/payload/tree/main/examples/auth) or the [Authentication](https://payloadcms.com/docs/authentication/overview#authentication-overview) docs.

- #### Posts

  Posts are used to generated blog posts, news articles, or any other type of content that is published over time. All posts are layout builder enabled so you can generate unique layouts for each post using layout-building blocks, see [Layout Builder](#layout-builder) for more details. Posts are also draft-enabled so you can preview them before publishing them to your website, see [Draft Preview](#draft-preview) for more details.

  Users can also leave comments on posts if they are logged in. Then, editors can log in to review and approve comments before they are published. See [Comments](#comments) for more details.

  Posts can also restrict access to content or digital assets behind authentication, see [Premium Content](#premium-content) for more details.

- ### Comments (Collection)

  Comments are used to allow logged-in users to leave comments on posts. Comments are draft-enabled so admins can review and approve them before they are published to your website, see [Comments](#comments) for more details.

- #### Projects

  Projects are used to showcase your work. All projects are layout builder enabled so you can generate unique layouts for each project using layout-building blocks, see [Layout Builder](#layout-builder) for more details. Projects are also draft-enabled so you can preview them before publishing them to your website, see [Draft Preview](#draft-preview) for more details.

- #### Pages

  All pages are layout builder enabled so you can generate unique layouts for each page using layout-building blocks, see [Layout Builder](#layout-builder) for more details. Pages are also draft-enabled so you can preview them before publishing them to your website, see [Draft Preview](#draft-preview) for more details.

- #### Media

  This is the uploads enabled collection used by pages, posts, and projects to contain media like images, videos, downloads, and other assets.

- #### Categories

  A taxonomy used to group posts or projects together. Categories can be nested inside of one another, for example "News > Technology". See the official [Payload Nested Docs Plugin](https://github.com/payloadcms/plugin-nested-docs) for more details.

### Globals

See the [Globals](https://payloadcms.com/docs/configuration/globals) docs for details on how to extend this functionality.

- `Header`

  The data required by the header on your front-end like nav links.

- `Footer`

  Same as above but for the footer of your site.

## Access control

Basic role-based access control is setup to determine what users can and cannot do based on their roles, which are:

- `admin`: They can access the Payload admin panel to manage your site. They can see all data and make all operations.
- `user`: They cannot access the Payload admin panel and can perform limited operations based on their user (see below).

This applies to each collection in the following ways:

- `users`: Only admins and the user themselves can access their profile. Anyone can create a user but only admins can delete users.
- `posts`: Everyone can access published posts, but only admins can create, update, or delete them. Some posts may also have content that is only accessible to users who are logged in. See [Premium Content](#premium-content) for more details.
- `projects`: Everyone can access published projects, but only admins can create, update, or delete them.
- `pages`: Everyone can access published pages, but only admins can create, update, or delete them.
- `comments`: Everyone can access published comments, but only admins can access draft comments. Users can create new comments but they will be saved as drafts until an admin approves them.

For more details on how to extend this functionality, see the [Payload Access Control](https://payloadcms.com/docs/access-control/overview#access-control) docs.


### Deployment

Before deploying your app, you need to:

1. Ensure your app builds and serves in production. See [Production](#production) for more details.

The easiest way to deploy your project is to use [Payload Cloud](https://payloadcms.com/new/import), a one-click hosting solution to deploy production-ready instances of your Payload apps directly from your GitHub repo. You can also deploy your app manually, check out the [deployment documentation](https://payloadcms.com/docs/production/deployment) for full details.

## Questions

If you have any issues or questions, reach out to us on [Discord](https://discord.com/invite/payload) or start a [GitHub discussion](https://github.com/payloadcms/payload/discussions).
