---
layout: post
title: "WebDev: Node + ReactApp"
tags: [Web, Node, ReactApp]
date: 2020-01-11 10:45:38 +0530
created_at: 2020-01-11 10:45:38 +0530
updated_at: 2020-01-13 00:43:23 +0530
categories: til
---


Create React App
===
create-react-app is a tool to help setup a modern single-page React application.

<br/>
> Create React App is an officially supported way to create single-page React applications. It offers a modern build setup with no configuration.
>
> <https://create-react-app.dev/docs/getting-started>
<br/>

* Create a learning area to get started

```bash
# Create folder
$ mkdir learn_node_reactapp
$ cd learn_node_reactapp/
```

* We will use docker for creating the dev environment

```bash
# Create these two files for building the docker containers
$ cat > Dockerfile 
FROM node:13.1.0-stretch

WORKDIR /usr/app

COPY ./entrypoint.sh .

EXPOSE 3000

ENTRYPOINT ["./entrypoint.sh"]
^D

$ cat > entrypoint.sh 
#!/bin/bash

tail -f /dev/null
^D


# Build the customised docker image for development
$ docker build -t learn_node_reactapp .

# Run the container
$ docker run --name LEARN_NODE_REACTAPP --rm -dt \
		-v $(pwd):/usr/app -p 3005:3005 learn_node_reactapp:latest

# Get into the running container
$ docker exec -it LEARN_NODE_REACTAPP bash
root@09e5ec3d64e3:/usr/app#
```

* Create the app

```bash
# Create the app named my-react-app1
root@09e5ec3d64e3:/usr/app# npx create-react-app my-react-app1

# Show the files created
$ cd my-react-app1
$ tree -I node_modules .
.
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
│   ├── serviceWorker.js
│   └── setupTests.js
└── yarn.lock

2 directories, 17 files
```

* To run the application

```bash
root@09e5ec3d64e3:/usr/app# cd my-react-app1

root@09e5ec3d64e3:/usr/app/my-react-app1# yarn start
```

Now go to http://localhost:3000 in your browser to view it working!

* For running any unit tests

```
root@09e5ec3d64e3:/usr/app/my-react-app1# yarn test
```

* To build a production ready app

```
root@09e5ec3d64e3:/usr/app/my-react-app1# yarn build
```
Copy the build folder content and deploy it in production
