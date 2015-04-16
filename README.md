phpPgAdmin Cloud Foundry Ready
=============================

This version is a real fork from phpPgAdmin.
It can be use with service provided by [postgresql-cf-service-broker](https://github.com/cloudfoundry-community/postgresql-cf-service-broker).
It use [cf-helper-php](https://github.com/cloudfoundry-community/cf-helper-php) to auto-binding postgresql service on phpPgAdmin.

Installation
============
Just 5 steps:

 1. Download the zip file from here: [https://github.com/cloudfoundry-community/phppgadmin-cf/archive/cf-ready.zip](https://github.com/cloudfoundry-community/phppgadmin-cf/archive/cf-ready.zip).
 2. Unzip it
 3. Go inside the unzipped folder and run `cf push`
 4. Bind your postgresql service with `cf bs phppgadmin-cfready <service_name>` and repeat for all postgresql services you want in your phpPgAdmin
 5. Restage the service with `cf restage phppgadmin-cfready` and you're done
