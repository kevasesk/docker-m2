### Hiscolumn M2 Docker

The repository based on https://github.com/markshust/docker-magento

##### For new dev instance

```bash
# Clone the docker repository:
git clone https://github.com/kevasesk/docker-m2.git .

# Remove existing src directory:
rm -rf src

# Clone existing source code:
git clone ... src

# Create a DNS host entry for the site:
echo "127.0.0.1 ::1 magento.local" | sudo tee -a /etc/hosts


# GNU Linux

sed -i 's/- ${MAGENTO_MEDIA}/#- ${MAGENTO_MEDIA}/g' docker-compose.yml

# BSD/macOS
sed -i '' 's/- ${MAGENTO_MEDIA}/#- ${MAGENTO_MEDIA}/g' docker-compose.yml


# update paths to shared media folder
vim .env

# Start some containers, copy files ot them and then restart the containers:
docker-compose up -d

chmod +x bin/*

#For new project

composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition .

bin/magento setup:install --base-url=https://magento.local --db-host=db --db-name=magento --db-user=magento --db-password=magento --admin-firstname=admin --admin-lastname=admin --admin-email=admin@admin.com --admin-user=eugenesm --admin-password=eugenesm1029 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --elasticsearch-host=elasticsearch

cp src/nginx.conf.sample src/nginx.conf.dev

bin/magento setup:upgrade

'Magento_TwoFactorAuth' => 0,

# copy default magento dev env.php
cp src/app/etc/env.php.docker src/app/etc/env.php
cp src/wp/wp-config.php.docker src/wp/wp-config.php

#For new 
mkdir src

#for exists
bin/copytocontainer --all ## Initial copy will take a few minutes...

# Downgrade composer to v1
bin/root composer self-update --1

# Update permissions
bin/fixowns
bin/fixperm

# Install composer dependencies, then copy artifacts back to the host (for debugging purposes):

bin/composer install
bin/copyfromcontainer vendor
bin/copyfromcontainer app
bin/copyfromcontainer nginx.conf.sample


# Import existing database: (if needed)
#sed 's/\sDEFINER=`[^`]*`@`[^`]*`//g' -i files/backup.sql

bin/clinotty mysql -hdb -uroot -pmagento magento < files/db.sql

# Import app-specific environment settings:
bin/magento app:config:import

#set local base URL
bin/magento config:set web/secure/base_url https://magento.local/
bin/magento config:set web/unsecure/base_url https://magento.local/
bin/magento config:set smtp/general/enabled 0
bin/magento config:set googletagmanager/general/active 0
bin/magento config:set mailchimp/general/active 0

bin/setup-ssl magento.local

# GNU Linux
sed -i 's/#- ${MAGENTO_MEDIA}/- ${MAGENTO_MEDIA}/g' docker-compose.yml

# BSD/macOS
sed -i '' 's/#- ${MAGENTO_MEDIA}/- ${MAGENTO_MEDIA}/g' docker-compose.yml

bin/restart

open https://magento.local
```
