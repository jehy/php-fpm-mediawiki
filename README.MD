# PHP-FPM docker images for mediawiki

Updating a really old mediawiki is a pain and should be done in several steps, according to diagram on https://www.mediawiki.org/wiki/Compatibility.

Also

> Since Version 1.36, MediaWiki only commits to supporting upgrades from two LTS releases ago (see phab:T259771).
> Upgrades from older versions of MediaWiki will have to be performed in multiple steps.
> This means that if you want to upgrade to 1.41 from 1.34 or earlier, you'll first have to upgrade your 1.34 wiki to 1.35 (or 1.39),
> and, from 1.35 (or 1.39), you'll be able to upgrade to 1.41.

Also, basic docker images don't work because they dont contain needed PHP extensions.

And building a docker image for old php versions is a bit complex since many repos and certificates already stopped existing.

I've gone through it and wanted to share docker images for php-fpm that can help to update from 1.23 to 1.41 version.

You are free to use my docker compose, nginx and mysql docker files, or any another you like.
I used Mysql 5.7 because it is compatible with all wiki versions I saw.

How update can be done:

1. Use docker compose or pull image according to your wiki version, for example `ghcr.io/jehy/php-fpm-mediawiki_1.35:latest`
2. Make sure everything is alright
3. Update php-fpm to next version, for example `ghcr.io/jehy/php-fpm-mediawiki_1.39:latest`
4. Copy new wiki files to your web server
5. Run maintenance update script
6. Repeat 2-5 until you have latest version :)

# Provided packages:

* `ghcr.io/jehy/php-fpm-mediawiki_1.35:latest`
* `ghcr.io/jehy/php-fpm-mediawiki_1.39:latest`
* `ghcr.io/jehy/php-fpm-mediawiki_1.41:latest`


# Useful scripts

```bash
# export sql backup
docker exec mysqlContainerName /usr/bin/mysqldump -u userName --password=Password dbName > backup.sql

# import SQL backup
cat backup.sql | docker exec -i mysqlContainerName mysql -u userName --password=Password dbName

# rebuild images
docker exec -i phpContainerName php /var/www/public_html/maintenance/rebuildImages.php

# enter container shell
docker exec -it phpContainerName sh

#run update script inside container
cd /var/www/public_html/maintenance/
php ./update.php

# reinstall composer deps which are broken in gzip inside container
cd /var/www/public_html/
rm ./vendor/* -rf
composer install
```
