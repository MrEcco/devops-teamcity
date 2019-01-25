# Restoring from backup

You can create backup of previous installition by this link http://teamcity/admin/admin.html?item=backup (dont forget to change link for you) and after complete, download you backup.

Restoring can be useful for upgrade TeamCity.

## docker-compose

Past your backup to root of this repository. At this moment you have empty database (see make clean). Follow this instructions:

1. Open **docker-compose.yml** file.
2. Uncomment this strings:
```bash
teamcity-server:
-------- <cut> --------
# entrypoint:
# - "sleep"
# - "36000"
-------- <cut> --------
volumes:
-------- <cut> --------
# - "./:/temporary:ro"
```
3. Comment all teamcity-agents-X (where X is number) and their configurations in this file.
4. Create **database.properties** file in root of this repository:
```
# Postgres configuration
connectionProperties.user=teamcity
connectionProperties.password=teamcity
connectionProperties.autosave=conservative
connectionUrl=jdbc\:postgresql\://teamcity-pgsql:5432/teamcity
```
6. Start docker-compose script:
```bash
make start
```
7. Enter to docker image and run commands:
```bash
docker exec -it teamcity-server /bin/bash
# Wait to enter to container
# Download JDBC plugin
mkdir -p /data/teamcity_server/datadir/lib/jdbc
cd /data/teamcity_server/datadir/lib/jdbc
apt-get update -y && apt-get install wget -y
# Find latest version in https://jdbc.postgresql.org/download.html#current
wget https://jdbc.postgresql.org/download/postgresql-42.2.5.jar
# Run restoration script
cd /opt/teamcity/bin
./maintainDB.sh restore \
   -A /data/teamcity_server/datadir \
   -F /temporary/TeamCity_Backup_20190117_114401.zip \
   -T /temporary/database.properties
# And look successful restoring files and database
exit  # Exit from docker image
```
8. Stop docker-compose script:
```bash
make stop
```
9. Now recover **docker-compose.yml** file. Revert stage 2 and stage 3 (uncomment agents, comment "./:/temporary:ro" volume and custom entrypoint).
10. Start docker-compose script:
```bash
make start
```
11. Go to http://teamcity/ (dont forget to change link for you) and complete recovering. You may be need to recover password (see below how to do it)

## Kubernetes

Past your backup to root of this repository. At this moment you have empty database (see make clean). Follow this instructions:

1. Open **deployment.yml** file.
2. Uncomment this strings:
```bash
-------- <cut> --------
containers:
- name: teamcity
image: jetbrains/teamcity-server:2018.2.1
# command:
# - 'sleep'
# - '36000'
-------- <cut> --------
```
3. Create **database.properties** file in root of this repository:
```
# Postgres configuration
connectionProperties.user=teamcity
connectionProperties.password=teamcity
connectionProperties.autosave=conservative
connectionUrl=jdbc\:postgresql\://teamcity-pgsql:5432/teamcity
```
4. Create/apply deployment, service and required volumes, copy files into pod and exec into pod:
```bash
kubectl create -f volumes.yml
kubectl create -f service.yml # Check service type before
kubectl create -f deployment.yml
# kubectl apply -f deployment.yml
kubectl cp TeamCity_Backup_XXXXXXXXXXXXXXXXXXXX.zip teamcity-server:/TeamCity_Backup.zip
kubectl cp database.properties teamcity-server:/database.properties
kubectl exec -it teamcity-server-<TAB> /bin/bash
# Wait to enter to container
```
5. Run commands in pod:
```bash
# Download JDBC plugin
mkdir -p /data/teamcity_server/datadir/lib/jdbc
cd /data/teamcity_server/datadir/lib/jdbc
apt-get update -y && apt-get install wget -y
# Find latest version in https://jdbc.postgresql.org/download.html#current
wget https://jdbc.postgresql.org/download/postgresql-42.2.5.jar
# Run restoration script
cd /opt/teamcity/bin
./maintainDB.sh restore \
   -A /data/teamcity_server/datadir \
   -F /TeamCity_Backup.zip \
   -T /database.properties
exit
```
6. Now recover **deployment.yml** file. Revert stage 2 (uncomment commands).
7. Apply deployment and start TeamCity server:
```bash
kubectl apply -f deployment.yml
# ...and wait to start this
```
8. Go to http://teamcity/ (dont forget to change link for you) and complete recovering. You may be need to recover password (see below how to do it).
9. After check workability and backup completes, lets deploy agents:
```bash
kubectl create -f agents-configmap.yml
kubectl create -f statefulset.yml
kubectl scale --replicas=3 statefulset/teamcity-agents
# Wait 10 minutes (for installitions, benchmarks and other bootstrap)
```
10. Now you can authorize agents in TeamCity server.

## Reference links

1. TeamCity conffiguration for Postgres (with 11.1 compatible)
https://confluence.jetbrains.com/display/TCD10/Setting+up+an+External+Database#SettingupanExternalDatabase-PostgreSQL
2. TeamCity backing up
https://confluence.jetbrains.com/display/TCD10/TeamCity+Data+Backup
3. TeamCity Backup restoring
https://confluence.jetbrains.com/display/TCD10/Restoring+TeamCity+Data+from+Backup
4. Kuberenetes API Reference
https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.13/
5. Docker-compose Reference
https://docs.docker.com/compose/compose-file/
