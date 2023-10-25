# Georga Docker

Docker Setup for Geographical Resources & Groups App.

## Prerequisites

Install Python and add user to group:

    sudo apt-get install git python python3-yaml
    sudo usermod -aG docker $USER
    newgrp docker

To install Docker, we recommend the current method you find on https://docs.docker.com/compose/install/ . Just trying ``sudo apt-get install docker docker-compose`` may lead to problems as the latest compose.yaml format may not be supported.

Optionally install GnuPG to sign your git commits (recommended):

    sudo apt-get -y install gnupg

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

Configure submodules to track the branch and being pulled along with the main repo:

    git submodule foreach 'git checkout main'
    git config recurse.submodules true

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

    docker compose run --rm server bash -c "./manage.py loaddata georga/fixtures/*"

If you don't want to load the demo data, create and activate a Django superuser instead:

    docker compose run --rm server bash
    > ./manage.py createsuperuser
    > from georga.models import Person
    > person = Person.objects.get(email="my@email.address")
    > person.is_active = True
    > person.save()

## Run

Run all docker services:

    docker compose up

## Develop

Open django graphql interface:

    xdg-open georga.test/graphql

Open django admin interface:

    xdg-open georga.test/admin

Open react client interface:

    xdg-open georga.test:3000

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
       # if you made database changes yourself, enter this: docker compose run --rm server ./manage.py makemigrations
       docker compose run --rm server ./manage.py migrate
       docker compose run --rm server bash -c "./manage.py loaddata georga/fixtures/*"

5. Run tests and commit changes to submodules:

       git add server
       git commit -m "updates submodules"
       git push

## Test

Run django tests:

    docker compose run --rm server ./manage.py test
    docker compose run --rm server ./manage.py test \
        --verbosity 2 --failfast --timing --keepdb --parallel auto
