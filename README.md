# netlify-cms-oauth-firebase

This is a [Firebase Cloud Function](https://firebase.google.com/docs/functions/) that allows [Netlify CMS](https://www.netlifycms.org/) to authenticate with GitHub or GitLab via OAuth2.

## Setup
### 0) Prerequisites
These instructions require that you have created a [Firebase](https://firebase.google.com/) project and have installed and configured the [Firebase CLI Tools](https://github.com/firebase/firebase-tools). See the [Firebase CLI Reference](https://firebase.google.com/docs/cli/) for more details.

### 1) Get the code
Clone the repository and install dependencies:
```
git clone https://github.com/Herohtar/netlify-cms-oauth-firebase
cd netlify-cms-oauth-firebase/functions
npm i
```

### 2) Create an OAuth app
You will need an OAuth app to authenticate with. For GitHub, the instructions can be found in the [GitHub Developer Documentation](https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/).

For now, the values that you provide for the fields do not matter. The **authorization callback URL** will need to be configured once you have the Firebase Function URL in order for the service to work.

### 3) Configure the Firebase environment
Tell Firebase which project to use:
```
firebase use your-project-id
```

Set the `oauth.client_id` and `oauth.client_secret` Firebase environment variables using the values from the GitHub OAuth app:
```
firebase functions:config:set oauth.client_id=yourclientid oauth.client_secret=yourclientsecret
```

For GitHub Enterprise and GitLab you will need to set the `oauth.git_hostname` environment variable.

For GitLab you will also need to set the following additional environment variables as specified:
```
oauth.provider=gitlab
oauth.scopes=api
oauth.authorize_path=/oauth/authorize
oauth.token_path=/oauth/token
```

### 4) Deploy the function
Deploy the function to Firebase:
```
firebase deploy --only functions
```

At this point you should update the **authorization callback URL** in your GitHub OAuth app's settings to point to the URL of your Firebase function, which should be of the form: `https://us-central1-your-project-id.cloudfunctions.net/oauth/callback`

### 5) Configure Netlify CMS
Finally, update your Netlify CMS `config.yml` to point to the function:
```yaml
backend:
  name: github # Or gitlab
  repo: username/repo # Your username and repository
  branch: master # Branch to use
  base_url: https://us-central1-your-project-id.cloudfunctions.net # The base URL for your Firebase Function
  auth_endpoint: /oauth/auth # The path to the OAuth endpoint of the function
```

## Notes
- The OAuth2 API makes external network requests which requires a Firebase project that has billing enabled. (**Spark** or **Blaze** plan)
- Firebase does not handle the function root correctly, so calls to `https://us-central1-your-project-id.cloudfunctions.net/oauth` (no trailing slash) will fail with the error `"Cannot GET null"`. If for some reason you are trying to call the root function, you must use a trailing slash (`https://us-central1-your-project-id.cloudfunctions.net/oauth/`)


# Gatsby + Netlify CMS Starter

**Note:** This starter uses the [Gatsby v2 Beta](https://www.gatsbyjs.org/blog/2018-06-16-announcing-gatsby-v2-beta-launch/).

This repo contains an example business website that is built with [Gatsby](https://www.gatsbyjs.org/), and [Netlify CMS](https://www.netlifycms.org): **[Demo Link](https://gatsby-netlify-cms.netlify.com/)**.

It follows the [JAMstack architecture](https://jamstack.org) by using Git as a single source of truth, and [Netlify](https://www.netlify.com) for continuous deployment, and CDN distribution.

## Prerequisites

- Node (I recommend using v8.2.0 or higher)
- [Gatsby CLI](https://www.gatsbyjs.org/docs/)

## Getting Started (Recommended)

Netlify CMS can run in any frontend web environment, but the quickest way to try it out is by running it on a pre-configured starter site with Netlify. The example here is the Kaldi coffee company template (adapted from [One Click Hugo CMS](https://github.com/netlify-templates/one-click-hugo-cms)). Use the button below to build and deploy your own copy of the repository:

<a href="https://app.netlify.com/start/deploy?repository=https://github.com/AustinGreen/gatsby-starter-netlify-cms&amp;stack=cms"><img src="https://www.netlify.com/img/deploy/button.svg" alt="Deploy to Netlify"></a>

After clicking that button, you’ll authenticate with GitHub and choose a repository name. Netlify will then automatically create a repository in your GitHub account with a copy of the files from the template. Next, it will build and deploy the new site on Netlify, bringing you to the site dashboard when the build is complete. Next, you’ll need to set up Netlify’s Identity service to authorize users to log in to the CMS.

### Access Locally
```
$ git clone https://github.com/[GITHUB_USERNAME]/[REPO_NAME].git
$ cd [REPO_NAME]
$ yarn
$ npm run develop
```
To test the CMS locally, you'll need run a production build of the site:
```
$ npm run build
$ npm run serve
```

## Getting Started (Without Netlify)
```
$ gatsby new [SITE_DIRECTORY_NAME] https://github.com/AustinGreen/gatsby-starter-netlify-cms/
$ cd [SITE_DIRECTORY_NAME]
$ npm run build
$ npm run serve
```

### Setting up the CMS
Follow the [Netlify CMS Quick Start Guide](https://www.netlifycms.org/docs/quick-start/#authentication) to set up authentication, and hosting.

## Debugging
Windows users might encounter ```node-gyp``` errors when trying to npm install.
To resolve, make sure that you have both Python 2.7 and the Visual C++ build environment installed.
```
npm config set python python2.7
npm install --global --production windows-build-tools
```

[Full details here](https://www.npmjs.com/package/node-gyp 'NPM node-gyp page')

