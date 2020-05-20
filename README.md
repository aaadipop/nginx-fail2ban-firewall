# nginx-fail2ban-firewall
A simple one liner firewall that blocks bad bots trying to login to wordpress. Check 'More details' link for using in other scenarios. 

## The line:

```awk '{ if($9 ~ "login" && int($10) == 200 && $8 == "POST" && int($7) == 443) print $1, $6, $9, $10}' /var/log/nginx/access.log | sort -k1 -k2 | uniq -c | awk 'int($1) > 10 {print $2, $1}'```

## Log format:
```log_format compressed '$remote_addr $realip_remote_addr $time_local $server_name $host $server_port $request_method $uri $status';```

## Wrapped to crontab:
cat /nginx_fail2ban_firewall.sh \
```awk '{ if($9 ~ "wp-login" && int($10) == 200 && $8 == "POST" && int($7) == 443) print $1, $6, $9, $10}' /var/log/nginx/access.log | sort -k1 -k2 | uniq -c | awk 'int($1) > 10 {print $2}' | grep -P '^\d{1,3}(\.\d{1,3}){3}\s' |  while read line; do fail2ban-client set nginx-http-auth banip "$line"; done```

cat /etc/crontab \
```* * * * * root /root/nginx_fail2ban_firewall.sh >> /dev/null 2>&1```

## More details:
https://iservit.ro/tutorial/one-line-firewall-for-nginx-w-fail2ban/
