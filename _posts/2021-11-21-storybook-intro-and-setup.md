---
layout: post
title: "Storybook: a tool for UI development"
tags: [UI development, web development, reactjs, vuejs, angularjs, storybookjs, componentdriven]
date: 2021-11-21 00:29:48 +0530
created_at: 2021-11-21 00:29:48 +0530
updated_at: 2021-11-21 01:55:04 +0530
categories: til
---

Storybook
===
> Storybook is a development environment for UI components. It allows you to browse a component library, view the different states of each component, and interactively develop and test components.
> <https://github.com/storybookjs/storybook/>

<br />
> Storybook is a tool for UI development. It makes development faster and easier by isolating components. This allows you to work on one component at a time. You can develop entire UIs without needing to start up a complex dev stack, force certain data into your database, or navigate around your application.
>
> <https://storybook.js.org/docs/react/get-started/introduction>


<br/>
Lets get started with our dev setup...
* Create our learning folder

```bash
$ mkdir learn_react_storybook
$ cd learn_react_storybook
```

* Create a temporary work area to create the new app inside the docker container

```bash
$ mkdir temp
$ cd temp

$ cat > Dockerfile
FROM node:16.13.0-bullseye-slim

WORKDIR /usr/app

COPY ./entrypoint.sh .

EXPOSE 6006

ENTRYPOINT ["./entrypoint.sh"]

^D

$ cat > entrypoint.sh
#!/bin/bash

tail -f /dev/null

^D

$ chmod 755 entrypoint.sh
$ cd ..
```

* Lets create the docker-compose file

```bash
$ cat > docker-compose.yml
version: "3.9"

services:
  temp:
    build: temp
    volumes:
      - ./:/usr/src/app
^D
```

* Getting to the work area

```bash
$ docker-compose build
$ docker-compose up -d
$ docker-compose exec temp bash
root@f334a0468079:/usr/app#
```

* Create a react app and then install storybook

```bash
root@f334a0468079:/usr/app# npx create-react-app storybook
root@f334a0468079:/usr/app# cd storybook/

#install storybook to the newly created react app
root@f334a0468079:/usr/app/storybook# npx sb init
root@f334a0468079:/usr/app/storybook# cd ..

#Copy the newly generated app from workdir to the shared folder
root@f334a0468079:/usr/app# mv storybook /usr/src/app
root@f334a0468079:/usr/app# exit
```

* This is the app code base which we will be working in

```bash
$ tree -I node_modules storybook
storybook
├── Dockerfile
├── entrypoint.sh
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
├── README.md
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   ├── reportWebVitals.js
│   ├── setupTests.js
│   └── stories
│       ├── assets
│       │   ├── code-brackets.svg
│       │   ├── colors.svg
│       │   ├── comments.svg
│       │   ├── direction.svg
│       │   ├── flow.svg
│       │   ├── plugin.svg
│       │   ├── repo.svg
│       │   └── stackalt.svg
│       ├── button.css
│       ├── Button.jsx
│       ├── Button.stories.jsx
│       ├── header.css
│       ├── Header.jsx
│       ├── Header.stories.jsx
│       ├── Introduction.stories.mdx
│       ├── page.css
│       ├── Page.jsx
│       └── Page.stories.jsx
└── yarn.lock

4 directories, 37 files

```
* Now that we have a freshly built app folder ready, lets get started

```bash
#replace the content of docker-compose.yml with this:
$ cat > docker-compose.yml
version: "3.9"

services:
  storybook:
    build: storybook
    volumes:
      - ./storybook:/usr/app
    ports:
      - 6006:6006

^D

#Create Dockerfile in the new storybook folder
$ cat > storybook/Dockerfile
FROM node:16.13.0-bullseye-slim

WORKDIR /usr/app

COPY ./entrypoint.sh /usr/local/bin

EXPOSE 6006

ENTRYPOINT ["entrypoint.sh"]

^D

$ cat > storybook/entrypoint.sh
#!/bin/bash

yarn storybook

^D

$ cat > storybook/.dockerignore
node_modules

^D

$ chmod 755 storybook/entrypoint.sh

```

* Build and launch

```bash
$ docker-compose build
$ docker-compose up -d

```
Now go to <http://localhost:6006/>, and your storybook is ready!

![My fresh new storybook](/assets/2021-11-21-storybook-intro-and-setup.png)


* SEE ALSO
  * [For reference - Github repo with above code](https://github.com/technopreneurG/learn_node_reactapp)
  * [Storybook Documentation](https://storybook.js.org/docs/angular/get-started/install)
  * [TIL: WebDev: Node + ReactApp](/til/2020/01/11/node_reactapp.html)
