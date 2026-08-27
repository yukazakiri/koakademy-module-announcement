# KoAkademy Announcement Module

School-wide announcements for administrators and portal users.

## Requirements

- KoAkademy 1.20 or newer
- PHP 8.5
- Laravel 13, Filament 5, and nwidart/laravel-modules 13
- A host application that uses the KoAkademy module loader

The package is distributed through the public [KoAkademy Composer
repository](https://yukazakiri.github.io/koakademy-modules). The Marketplace
catalog does not install PHP code into a running container; Composer and the
application image do that.

## Installation

Run the following commands from the host application's repository root:

    composer config repositories.koakademy composer https://yukazakiri.github.io/koakademy-modules
    composer require koakademy/announcement:^1.0
    php artisan migrate --force
    php artisan optimize:clear

If the host already has the koakademy Composer repository configured, only the
composer require command is needed. Commit composer.json and composer.lock; do
not install the package only inside one running replica.

### Docker, Swarm, Dokploy, and Kubernetes

Install the package during the application image build, then build the normal
frontend assets:

    composer install --no-dev --prefer-dist --optimize-autoloader
    npm ci
    npm run build

Run the migration once as a release job or one-off task, clear the application
cache, and restart or roll every application worker. For Docker Compose this
is normally docker compose build followed by the platform's migration task.
For Swarm or Dokploy, push the new image to the configured registry and
redeploy the service with that image. For Kubernetes, run the migration as a
release Job and roll the Deployment. All replicas must use the same
composer.lock and image.

### Enable the module

1. Sign in as a super administrator.
2. Open **Administrators → System → Marketplace**, or visit
   /administrators/module-marketplace.
3. Enable **Announcement**.
4. Restart/redeploy the application so every worker reads the new module
   status.

Fresh installations initialize optional modules disabled. Upgrades preserve
the existing modules_statuses.json choices, so an already-enabled Announcement
module stays enabled.

## Using the admin panel

The custom admin workspace is available at
/administrators/announcements and appears as **Announcements** in the
administrator navigation. The Filament resource is also available at
/admin/announcements under **Communications**.

Create an announcement, choose its audience and publication window, save it,
and verify it from the relevant portal or login page. Use the resource's
edit/delete actions to maintain an existing announcement. The module's
policies and existing KoAkademy permissions still apply.

## API

Authenticated API consumers can use the versioned endpoint:

    /api/v1/announcements

Use the host application's Sanctum credentials and follow the normal API
permission rules.

## Upgrading

After releasing a newer module tag, update the package in the host repository,
rebuild the image, run migrations, and redeploy. Refreshing Marketplace alone
only refreshes catalog metadata.

## License

AGPL-3.0-or-later
