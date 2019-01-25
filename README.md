# DevOps TeamCity

This is just ready-to-use simple-deploy TeamCity server docker-compose script and/or Kubernetes clusterisation manifests. In addition, it have some helpful guides. Enjoy it for free! :)

This repository refer to [Jetbrains TeamCity](https://jetbrains.ru/products/teamcity/), [Docker](https://www.docker.com/) and [Kubernates](https://kubernetes.io/) projects. Don"t forget to say thanks to these projects!

Project references:
https://www.jetbrains.com/teamcity/
https://github.com/docker/docker-ce
https://github.com/docker/compose
https://github.com/kubernetes/kubernetes

## Common tasks

We have **Makefile** for many common tasks like start, stop, clean and many other. **Makefile** use is preferred for many operations. Look it like how-it-work description.

### docker-compose

```bash
make start # Start TeamCity server
make stop # Stop TeamCity server
make clean # Clean persistences for start it and like a first time later
make configure # Set folder permissions for volumes
```

### Kubernetes

```bash
# If you have ready DB - just skip it
kubectl create -f postgres/statefulset.yml # Start database special for TeamCity server.
kubectl create -f postgres/service.yml # Make it avaliable for TeamCity server
# Check volumes manifest for correct storageclass before apply
kubectl create -f volumes.yml # TeamCity Server persistences
kubectl create -f agents-configmap.yml # Bootstrap script and custom config files
kubectl create -f deployment.yml # TeamCity Server
kubectl create -f service.yml # Make it avaliable from external
kubectl create -f statefulset.yml # Start TeamCity agents
# Totally comfortable for scaling agents count
kubectl scale --replicas=3 statefulset/teamcity-agents # Scale to 3 agents
# If you need - you just can connect to every TeamCity agent pods and run own commands
```

## First run

1. After startup, go to http://localhost/ (this link may be other in custom docker/kubernetes installitions, use it smart).
2. On step where you must define database:
**DATABASE=teamcity-pgsql:5432**
**PASSWORD=teamcity** # Dont forget to change it for production
**USERNAME=teamcity**
4. After, just follow the instructions and enter to TeamCity Server.

## Restoring from backup

Moved to [here](BACKUP_RESTORING.md)

## Password recovery

Moved to [here](PASSWD_RECOVERY.md)

## Security tips

We use ONLY official docker images, downloads links and links to references. Always check information which you found in global network. Use reference links for any information which you publish for simplify checking.

## Reference links

1. TeamCity installition concepts
https://confluence.jetbrains.com/display/TCD18/Installing+and+Configuring+the+TeamCity+Server
2. TeamCity conffiguration for Postgres (JDBC 42.2.5 is compatible with 11.1 PostgreSQL DB)
https://confluence.jetbrains.com/display/TCD10/Setting+up+an+External+Database#SettingupanExternalDatabase-PostgreSQL
3. Kuberenetes API Reference
https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.13/
4. Docker-compose Reference
https://docs.docker.com/compose/compose-file/
