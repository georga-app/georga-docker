# Georga Docker

Docker Setup for Geographical Ressources & Groups App.

## Install

Clone repository and submodules:

    git clone --recursive https://github.com/georga-app/georga-docker
    cd georga-docker

Configure git user name/email/signing (optional):

    git config user.name "<NAME>"
    git submodule foreach git config user.name "<NAME>"
    git config user.email "<EMAIL>"
    git submodule foreach git config user.email "<EMAIL>"
    git config user.signingkey "<GPGKEYID>"
    git submodule foreach git config user.signingkey "<GPGKEYID>"
    git config commit.gpgsign true
    git submodule foreach git config commit.gpgsign true

Configure `/etc/hosts`:

    127.0.0.1   georga.test

Copy `.env` example file:

    cp .env.example .env

Create JWT certs in `./server/keys/`:

    ./server/scripts/generate_jwt_certs.sh

Build docker images:

    docker compose build

Migrate database:

    docker compose run --rm server ./manage.py migrate

Load demo data:

    docker compose run --rm server bash -c "./manage.py loaddata georga/initial_data/*"

## Run

Run all docker services:

    docker compose up

## Develop

Open django graphql interface:

    xdg-open georga.test/graphql

Open mailhog:

    xdg-open georga.test:8025

Delete database:

    docker compose down
    sudo rm -rf volumes/database

## Update

1. Pull changes in this repo

    git pull

2. Pull changes in the submodules

    git submodule update --remote --merge

3. If `requirements.txt` has changed, rebuild docker images:

    docker compose build

4. If database structure has changed, delete database and reload demo data:

    docker compose down
    sudo rm -rf volumes/database
    docker compose run --rm server ./manage.py migrate
    docker compose run --rm server bash -c "./manage.py loaddata georga/initial_data/*"

5. Run tests and commit changes to submodules:

    git add server
    git commit -m "updates submodules"
    git push
