# Drupal 8 Docker development environment

[![CircleCI](https://circleci.com/gh/adrian-gheorghe/docker-drupal.svg?style=svg)](https://circleci.com/gh/adrian-gheorghe/docker-drupal)

Drupal 8 local development environment

## Setup
Requires https://github.com/adrian-gheorghe/docker-setup for the docker-compose setup to function correctly

### Structure
- One MySQL container. Local volume set to /var/lib/mysql for database persistence
- One PHP Apache Drupal container. The PHP image has build arguments in order to customize the PHP version, Drupal version
- One PHP Install container. This container waits for the database to be ready and runs the drupal installation with default values. Also uses build arguments.

### Usage
- Clone repository where you want to set up a drupal 8 installation
- Change services php and install build arguments to suit your needs
- run docker-compose up -d or docker-compose up -d --build

### URLs
- http://drupal.localhost/
- http://adminer.drupal.localhost/