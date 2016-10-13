# phpPgAdmin - Cloud Foundry Ready

This version is a real fork from phpPgAdmin.
It can be use with service provided by [Dingo PostgreSQL](https://github.com/dingotiles/dingo-postgresql-release), [postgresql-cf-service-broker](https://github.com/cloudfoundry-community/postgresql-cf-service-broker), and others.

It use [cf-helper-php](https://github.com/cloudfoundry-community/cf-helper-php) to auto-binding postgresql service on phpPgAdmin.

## Installation & usage

Just 5 steps:

 1. Download the zip file from here: [https://github.com/cloudfoundry-community/phppgadmin-cf/archive/cf-ready.zip](https://github.com/cloudfoundry-community/phppgadmin-cf/archive/cf-ready.zip).
 2. Unzip it
 3. Go inside the unzipped folder and run `cf push --no-start -n phppgadmin-SPACENAME`
 4. If needed create a user-provided service to connect to an existing remote pg instance (e.g. ``cf cups ccdb-pg -p '{"uri":"postgres://192.168.131.8:5524/ccdb"}'`` )
 5. Bind your postgresql service with `cf bs phppgadmin-cfready <service_name>` and repeat for all postgresql services you want in your phpPgAdmin
 6. Restage the service with `cf restage phppgadmin-cfready` 
 7. Connect to phpPgAdmin through its HTTP route, and select the db instance to connect to from the list, and specify associated login/password of the pgdb to access

## Combo

Bonus command to deploy the app, discover and bind all `"postgresql"` tagged service instances in the current space to the `phppgadmin-cfready` application, using `cf curl`, [`jq`](https://stedolan.github.io/jq/download/), and `xargs`:

```
git clone https://github.com/cloudfoundry-community/phppgadmin-cf -b cf-ready
cd phppgadmin-cf
appname=phppgadmin
space_guid=$(cat ~/.cf/config.json| jq -r ".SpaceFields.GUID")
space_name=$(cat ~/.cf/config.json| jq -r ".SpaceFields.Name")
org_name=$(cat ~/.cf/config.json| jq -r ".OrganizationFields.Name")
app_hostname="phppgadmin-${org_name}-${space_name}"
cf push ${appname} --no-start -n ${app_hostname}
cf curl /v2/services | jq -r ".resources[] | select(.entity.tags | contains([\"postgresql\"])) | .entity.service_plans_url" | xargs -L1 cf curl | jq -r ".resources[].entity.service_instances_url" | awk "{print \$1\"\\\\?q=space_guid:${space_guid}\"}" | xargs -L1 cf curl | jq -r ".resources[].entity.name" | xargs -L1 cf bind-service ${appname}
cf restart ${appname}
```

Yeah, `cf curl`, `jq` and `xargs` are pretty fantastic when combined together.

To dump all the credentials for all the bound databases, try:

```
cf curl /v2/apps\?q=name:${appname} | jq -r ".resources[0].entity.service_bindings_url" | xargs -L1 cf curl | jq -r ".resources[].entity.credentials"
```

## FAQ

I'm getting the following error message, although the submitted credentials are correct: 
> Attempt to connect with invalid server parameter, possibly someone is trying to hack your system

Try closing your HTTP session (clear your cookies or use a private browsing window) to flush your PHP session. It might be your previous attempts to log in are interferring.
