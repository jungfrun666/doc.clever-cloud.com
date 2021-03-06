---
title: Deploying Elixir apps
shortdesc: Elixir is a functional, concurrent, general-purpose programming language that runs on the Erlang virtual machine…
tags:
- elixir
- phoenix
- erlang
---

## Overview

Clever Cloud supports Elixir based applications.

## Create an Elixir application

In the Clever Cloud web console:
- under the organization of your choice, click new, then application. Select **Elixir**, then name your application and choose its deployment zone.

#### If you deploy a Phoenix application

- when prompted if you need an add-on, select **PostgreSQL**. Select your database size and name it.

## Configure your application

### Mandatory configuration

- Get your Elixir version with `$ elixir -v`, you will need it later

#### If you deploy a Phoenix application

- Edit the file `config/prod.secret.exs` to replace `System.get_env("DATABASE_URL")` with `System.get_env("POSTGRESQL_ADDON_URI")`.
- Generate a secret token with `$ mix phx.gen.secret`.

## Set production environment variables

In the envrionment variables section of your Elixir application, set

- **CC_ELIXIR_VERSION** to the value of `$ elixir -v` result. (Available versions as of today are `1.8`, `1.9` or `1.10`)

#### If you deploy a Phoenix application

- **SECRET_KEY_BASE** to the value obtained with `$ mix phx.gen.secret` previously
- **CC_PHOENIX_RUN_ECTO_MIGRATE** to `true` if you need to trigger the command `$ mix ecto.migrate`

## Git Deployment on Clever Cloud

You need git on your computer to deploy via this tool. Here is the official website of Git to get more information: git-scm.com

### Setting up your remotes

The "Information" page of your app gives you your git deployment URL. It looks like this:
`git+ssh://git@push.clever-cloud.com/<your_app_id>.git`. Copy it in your clipboard.

- Locally, under your code folder, type in `git init` to set up a new git repository or skip this step if you already have one.
- Add the deploy URL with `git remote add <name> <your-git-deployment-url>`.
- Add your files via `git add <files path>` and commit them via `git commit -m <your commit message>`.
- Now push your application on Clever Cloud with `git push <name> master`

## Build, deployment phases and custom configuration

Once you push your code to the Clever Cloud remote provided, the following commands are run:
```
$ mix deps.get
$ mix deps.compile
$ npm install
```
These commands will compile your dependencies at the root of your project folder. 
If you want to use another folder for `npm install`, specify it via the environment variable **CC_PHOENIX_ASSETS_DIR**.
To change the folder for the entire build / run process, you should use **APP_FOLDER** environment variable.

Then `$ mix compile` is run. If you want to override this behavior, you can set the environment variable **CC_MIX_BUILD_GOAL** to the value you desire.

At this point, there is the command `$ npm run deploy`.

Then `$ mix phx.digest` is run. You can override this one with the variable **CC_PHOENIX_DIGEST_GOAL**.

Finally, `$ mix phx.server` is invoked, and as always, you can override this behavior, either with **CC_RUN_COMMAND** where you have to specify the full command, or **CC_PHOENIX_SERVER_GOAL** where it will be a mix task by default.

Note: If you need to specify the timezone of your application, you can do it with the variable **TZ** set to the usual timezone format, for instance `Europe/Paris`.
