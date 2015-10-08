# -*- mode: ruby -*-
# vi: set ft=ruby :

exo_api_key = ENV['EXO_API_KEY']
exo_sec_key = ENV['EXO_SECRET_KEY']
exo_sos_endpoint = "https://sos.exo.io"

s3cfg = "
[default]
host_base = sos.exo.io
host_bucket = %(bucket)s.sos.exo.io
access_key = #{exo_api_key}
secret_key = #{exo_sec_key}
use_https = True
signature_v2 = True"

pgbucket = "pg_backup"
pgpassword = 'an_awesome_password'

Vagrant.configure(2) do |config|

  config.vm.box = "boxcutter/ubuntu1404"
  
  # Install PostgreSQL
  config.vm.provision :shell, inline: "echo 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main' > /etc/apt/sources.list.d/pgdg.list"
  config.vm.provision :shell, inline: "wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -"
  config.vm.provision :shell, inline: "apt-get update"
  config.vm.provision :shell, inline: "apt-get install -y postgresql-9.3 pgadmin3"
  
  # Create a DB, a table and populate it
  config.vm.provision :shell, inline: "sudo -u postgres psql -c 'CREATE DATABASE test_exo_bucket'"
  config.vm.provision :shell, inline: "sudo -u postgres psql -c 'CREATE TABLE demo (company text, location text)' -d 'test_exo_bucket'"
  config.vm.provision :shell, inline: "sudo -u postgres psql -c \"INSERT INTO demo VALUES ('Apple','Cupertino');\" -d 'test_exo_bucket'"
  config.vm.provision :shell, inline: "sudo -u postgres psql -c \"INSERT INTO demo VALUES ('Google','Mountain View');\" -d 'test_exo_bucket'"
  config.vm.provision :shell, inline: "sudo -u postgres psql -c \"INSERT INTO demo VALUES ('Microsoft','Seattle');\" -d 'test_exo_bucket'"
  
  # Set password
  config.vm.provision :shell, inline: "sudo -u postgres psql -c \"ALTER USER Postgres WITH PASSWORD '#{pgpassword}'\" -d 'test_exo_bucket'"
  
  # Install S3CMD
  config.vm.provision :shell, inline: "apt-get install -y s3cmd"
  
  # Create S3CMD config file
  config.vm.provision :shell, inline: "echo '#{s3cfg}' > /root/.s3cfg"
  
  # Create a Bucket to store the backup
  config.vm.provision :shell, inline: "s3cmd mb s3://#{pgbucket}"
  
  # Backup the Database (Dump)
  config.vm.provision :shell, inline: "PGPASSWORD='#{pgpassword}' pg_dump -Fc --no-acl -h localhost -U postgres test_exo_bucket > backup.dump"
  
  # Backup the DB dump to Exoscale SOS (add --encrypt to encrypt)
  config.vm.provision :shell, inline: "s3cmd put backup.dump s3://#{pgbucket} " 
  
  # Delete the dump from the server
  config.vm.provision :shell, inline: "rm -f backup.dump"  
  
  
end
