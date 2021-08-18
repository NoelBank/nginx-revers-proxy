## How to Setup Nginx as Reverse Proxy ⚡️

### Inital Setup (NGINX) 📟

Install 
```
sudo apt-get update
sudo apt-get install nginx
```

Start and Check Nginx
```
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
```
now in browser should nothing happen 😭! We have to install certbot!

Wait we need password protection for our page! 🤯

```
sudo apt-get update
sudo apt-get install apache2-utils


sudo htpasswd -c /etc/nginx/.htpasswd [username]

sudo htpasswd /etc/nginx/.htpasswd testuser

cat /etc/nginx/.htpasswd

```

*Output*
```
[username]:$apr1$lzxsIfXG$tmCvCfb49vpP.....
testuser:$apr1$p1E9MeAf$kiAhneUwr.......
```

Yes! 👏🏻 we finished first setup!

### Initial Setup (Certbot) 📩

Install Certbot
```
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Create the first Cert
```
# create cert
sudo certbot --nginx
# - [username].neoskop.dev
# - do the register stuff...
```

Create the Certs for the Subdomains
```
sudo certbot run -d storybook.[username].neoskop.dev
sudo certbot run -d magnolia.[username].neoskop.dev
```
Yes! 👏🏻 we finished the second setup!


### Edit NGINX Config

`sudo nano /etc/nginx/sites-available/default`

Search for the first server_name! it's like `[username].neoskop.dev` here u have to remove the Config part:

```
        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
```

after u done this we can go ahed with this ⚠️

```
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        # This Part is for Password Protection
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }

    # only for frontend
    location /assets {
        proxy_pass http://localhost:3000/assets;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```

After you done this! You are Pro with *NGINX* Repate the Step with follwing `server_names`!
 - `storybook.[username].neoskop.dev`
 - `magnolia.[username].neoskop.dev`


### Additional Setup

Custom Error Pages if Proxy is unavailable:
Place it in ngnix config!

```
    error_page 500 503 504 401 404 /error.html;
    location /error.html {
        root /var/www/html;
    }
```

After this u have to create a `error.html` in the `/var/www/html` folder. Feel free to make some crazy shit!

My Template: [Link to Codepen](https://codepen.io/noelbank/pen/LYyKWRb)


Congratulations, you are now a nginx pro! 🎉🎉🎉🎉
