# Exoscale: Vagrant PostgreSQL Backup

This is a quick Vagrantfile to test how to backup a PostgreSQL DB to a bucket on Exoscale Simple Object Storage

### Usage

1. Install Vagrant (+ Virtualbox or VMWare Fusion)
2. Setup your API Key and API Secret in your Environment
3. Clone this repo
4. `cd exoscale_vagrant_pg_backup`
3. `vagrant up`

After a few minutes, you should have a bucket with the DB dump store inside.