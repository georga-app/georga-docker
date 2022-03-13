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

Build docker images:

    docker compose build

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

Pull all changes in this repo

    git pull

Pull all changes in the submodules

    git submodule update --remote --merge

If `requirements.txt` has changed, rebuild docker images:

    docker compose build

If database structure has changed, delete database:

    sudo rm -rf volumes/database

Run tests and commit changes to submodules:

    git add server
    git commit -m "updates submodules"
    git push
