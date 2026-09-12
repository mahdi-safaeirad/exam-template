# Scenario 1

Use one heading for each problem.
Write what was wrong and how you fixed it.
Paste the config you changed (only the changed part).
Paste the commands you used.
Write every step you tried, even guesses.
English is better. Persian is OK.

## Problem 1: DNS not working

What was wrong:
`/etc/resolv.conf` was a symlink to systemd-resolved stub, but `systemd-resolved` service was inactive (dead) and disabled. Because of that, apt and any hostname resolution failed.

How I fixed it:
Removed the broken symlink and wrote static DNS servers.

Config I changed (only the changed part):
```
nameserver 8.8.8.8
nameserver 1.1.1.1
```

Commands I used:
```
ls -l /etc/resolv.conf
systemctl status systemd-resolved --no-pager
sudo rm /etc/resolv.conf
sudo bash -c 'echo -e "nameserver 8.8.8.8\nnameserver 1.1.1.1" > /etc/resolv.conf'
ping google.com
```

## Problem 2: docker-compose not installed

What was wrong:
`docker compose` command was not available. Tried to install via apt but failed earlier because of DNS.

How I fixed it:
After fixing DNS, installed the package.

Commands I used:
```
sudo apt update
sudo apt install -y docker-compose
docker-compose version
```

## Problem 3: Containers not running + network isolation

What was wrong:
No containers were running. When I started them, backend could not resolve hostname `db` because backend and db were on different Docker networks (`nginx-backend-net` vs `backend-db-net`).

How I fixed it:
Added both networks to the backend service so it can talk to db and also to nginx.

Config I changed (only the changed part):
```yaml
  backend:
    ...
    networks:
      - nginx-backend-net
      - backend-db-net
```

Commands I used:
```
cd /opt/service-catalog
docker-compose ps -a
cat docker-compose.yml
docker-compose up -d --build
docker-compose logs
```

## Problem 4: Nginx 502 Bad Gateway

What was wrong:
Nginx was configured to proxy to a wrong upstream: `http://backend-api:8080`.  
Real service name is `backend` and it listens on port `5000`.

How I fixed it:
Corrected the `proxy_pass` directive.

Config I changed (only the changed part):
```nginx
location / {
    proxy_pass http://backend:5000;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

Commands I used:
```
cat nginx/nginx.conf
cat > /opt/service-catalog/nginx/nginx.conf << 'EOF'
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;

    server {
        listen 80;
        server_name _;

        location / {
            proxy_pass http://backend:5000;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
EOF
docker-compose restart nginx
curl -v http://localhost/graph
```


# Extra problems

Write side problems here. For example: your laptop, a wrong config change, or internet.
Write how much time each one took.

For example:
+ Weak Internet connection (10 min)

