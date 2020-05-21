---
title: Adding Circle CI/CD to your React Project
desc: An minimal example to quickly setup CI/CD to your project
---

It's really a pain to have to maintain multiple `env` files, login to your server all the time and then deploy.

Following is a simple method to integrate Circle CI into your workflow and ease your daily deployments.

1. First make sure to login to Circle CI and add your project in the Circle CI dashboard. 
2. To start with create a `.circleci/config.yml` file in your local directory.
3. This is your config which will be used to create [jobs](https://circleci.com/docs/2.0/jobs-steps/#jobs-overview), [workflows](https://circleci.com/docs/2.0/workflows/), run other scripts etc.
4. What we are trying to achieve here? On merging a pull request in to lets say `develop` branch, a workflow will executed.
5. For doing so we first specify the Circle `version` in our `config.yml` which is `2` for my case. You can use a later one if available.
6. Next step is to specify the environment in which the Circle CI Container will run, here `circleci/node:11.0.0` (11 is the node version).
7. Jobs are something which are later on used by workflows to run, in a step by step process. Here we name our job as `build_prod` and specify `docker` with an image of a node version 11. The node version could be the one which your project wants to use.
8. Next, specify the `working_directory` where you want circleci to build the project. In my case I've specified `~/repo`.
9. Steps in a job are literally what the name suggests, used to prepare the image to perform future tasks.
10. Hence the first step is [`checkout`](https://circleci.com/docs/2.0/configuration-reference/#checkout) step which we specified in the `working_directory`, the next step is pasting the ssh key fingerprint value which you got while you added the private RSA key from your server in the circleci dashboard. ([click here](https://circleci.com/docs/2.0/configuration-reference/#add_ssh_keys) for a more detailed document)
11. Next we run the `npm install` command and finally our `deploy.sh` script, which for me takes care of copying the build files from the circleci image to the server and extracting the same. For your reference I have copied that below. (Scroll down)
12. While our job is complete now, we specify a workflow on the branch we finally want to run the above job. To do so we specify the job name and the filter for the branch name.
13. While you are all set, you can merge the PR first to specified branch in your workflow. If everything went well for you then from the next merge all circleci builds should dpeloy onto your server.

```
# Javascript Node CircleCI 2.0 configuration file
#
# Check https://circleci.com/docs/2.0/language-javascript/ for more details
#
version: 2
# environment
environment:
   SERVER_IP_ADDRESS: 34.193.XX.XXX // Your IP Address
   APP: app_name

jobs:
  build_prod:
    docker:
      - image: circleci/node:11.0.0

    working_directory: ~/repo

    steps:
      - checkout

      - add_ssh_keys:
          fingerprints:
            - "e0:90:ab:1d:36:ad:9f:90:bb:a3:9d:65:3f:51:ea:60"

      - run: 
          shell: /bin/bash
          name: Installing Dependencies
          command: npm install
      - run: 
          shell: /bin/bash
          name: Running Deployment scripts
          command: ./deploy.sh
# Not a mandatory step, only if you want to cache your dependencies
      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}


workflows:
  version: 2
  build_deploy:
      jobs:
        - build_prod:
            filters:
              branches:
                only:
                  - master
```

For your reference:

`build.sh`
```
CI=false npm run build
rm repo.tar.gz
tar czf repo.tar.gz build
scp -o StrictHostKeyChecking=no repo.tar.gz name@domain.com:/home/name/repo/
ssh name@domain.com << EOF
    nvm use 11
    cd repo
    forever stop server.js
    rm -rf build
    tar -xvf repo.tar.gz
    forever start server.js
EOF
```
