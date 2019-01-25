# Password recovery

This can be helpful after upgrade or backup restoring. Or just after loose/stole password.

1. Go to http://teamcity/?super=1. You see only one input field for token.
2. Go to you container/pod:
```bash
# For docker-compose method:
docker exec -it teamcity-server /bin/bash
# For kubernetes method:
kubectl exec -it teamcity-server-<TAB> /bin/bash
```
3. Check logs for token. It will be long digits string:
```bash
cat /opt/teamcity/logs/teamcity-server.log | egrep "[0-9]{8,}"
```
4. Enter found token to browser.
5. Lets do that you need!

## Tips

This sign-in method is not recommended for permanent use. Use it only if you cannot enter with original account.

## Reference links

1. TeamCity superuser
https://confluence.jetbrains.com/display/TCD18/Super+User
