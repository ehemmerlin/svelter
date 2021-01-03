<p align="center">
  <img src="https://github.com/ehemmerlin/svelter/raw/main/images/logo.png" width="882" alt="Svelte with Docker">
  <p align="center">
    <strong>For full documentation, visit <a href="https://medium.com/@erichemmerlin/svelte-in-docker-deployed-to-netlify-d0054aabc454">this Medium post</a>.</strong>
  </p>
</p>

# Svelte in Docker deployed to Netlify
Develop Svelte in Docker without Node.js installed on your host machine and deploy our application to Netlify to benefit from its built-in continuous deployment feature.

## Svelte
Download and extract the official Svelte template: https://github.com/sveltejs/template/archive/master.zip

## Docker
Add this Dockerfile at the root of the Svelte tmeplate directory.

```
FROM node:14-alpine

WORKDIR /app

COPY . .
RUN npm install
RUN npm run build
RUN npm prune --production # Remove dev dependencies

EXPOSE 5000
ENV HOST=0.0.0.0

CMD [ "npm", "start" ]
```

Add also this .gitignore file at the same location.
```
.git
.gitignore
.dockerignore
Dockerfile
node_modules
README.md
```

## Docker Compose
Add this docker-compose.yml file at the root of the Svelte template directory.

```
version: "3.4"
services:
  violeta:
    image: node:14-alpine
    volumes:
      - type: bind
        source: ./
        target: /app
    working_dir: /app
    command: npm run dev
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=development
      - PORT=5000
      - HOST=0.0.0.0
```

## Development
Build the image then install the dependencies and start the application.
```
> docker build -t app . 
> docker-compose run --rm app npm install
> docker-compose up
```
Open http://localhost:5000 to see your Svelte application running in a Docker container.

## Deployment
### Netlify
Go to https://www.netlify.com/ and create a free account.

Go to https://app.netlify.com/start and select the source of you code (gitlab, github, bitbucket) then select the repository of your dockerized Svelte application.

Set the branch (to master for example), the build command to "npm run build" and the publish directory to "public" and click on "Deploy Site".

Visit your newly deployed Svelte application. Netlify will deploy it after each push to this branch.