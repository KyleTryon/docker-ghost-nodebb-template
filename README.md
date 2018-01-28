# docker-ghost-nodebb-template
Docker composition of Ghost blog and NodeBB forum with Node, NGINX proxy with SSL termination, Redis, etc.

## Software Used

**Docker** version 1.10.3

**docker-compose** version 1.6.2

## The Stack

- Node.js
    - Ghost blog software with S3 storage https://www.npmjs.com/package/ghost-s3-service
    - Nodebb forum software
- NGINX
    - proxying port 80 calls to the Node web server on port 2368
- Redis database

## Inherited Image Versions

- Ghost: 0.11.0
- NodeBB: xxx
- NGINX: 1.10
- Redis: xxx

## What You'll Need

- A server somewhere, like Amazon EC2 or Google Compute Cloud
- A domain name.
- An SSL certificate and key for your domain.
- Access to your domain's DNS.
- An account with an email sending service (SMTP service) (Mailgun, Sendgrid, etc.). I'm using Mailgun, which is free for low volume.
- An Amazon S3 bucket.
- An Amazon CloudFront distribution set up to serve your S3 bucket.

## How to Use It

0. Clone project to your server's filesystem.
0. Edit environment settings in docker-compose.yml
    - blog domain
    - database settings - in 2 places - under ghost, and under mysql.
    - mail server settings
    - S3 bucket and S3 info
    - CloudFront domain
0. Add your SSL certificate and key to nginx/volumes/ssl/ (there are placeholders there)
0. Set the blog domain (server_name) in nginx/copy/default.conf (must match the common name in your SSL certificate)
0. Run from within your Linux environment or Docker Toolbox environment: 
    0. docker-compose build
    0. docker-compose up -d  (you can remove the -d if you want to see logs, then Ctrl+C to stop all containers)
0. If using Docker Toolbox, look up the host IP address with: docker-machine ip default
0. On Linux, you'll just use "localhost" or "127.0.0.1".
0. Switch your domain's DNS to point to the address. Go to the domain in our browser and you'll see your new blog.
0. Go to https://YOUR_DOMAIN/admin to set up your blog.
0. Once you've made your admin account using that wizard, go back to: https://YOUR_DOMAIN/admin
0. Log in and enjoy.

## Ghost Content Directory

The directory ghost/volumes/content will be populated with 5 directories and a file the first time you start the container.

Ghost will create these directories:

- apps - for future use when ghost supports apps
- data - would normally hold a SQLite db, but won't for us since we're using MySQL
- images - for uploaded images, but will stay empty since we'll use S3 for storage
- storage - created in our Dockerfile for S3 image support
- themes - You can add more themes here. Default is Casper theme.
- index.js - It's a symlink. Don't edit or remove it.

## Theme

The default theme is Casper.  I have a minor fork of this theme here: https://github.com/jwasham/casper-startup-next-door

It includes Disqus support, so all you have to do is change the identifiers to your blog's Disqus ID and domain and you're good to go. See the README in that repo.

### To install another theme

Stop docker-compose with:

docker-compose stop

Copy the new theme to docker-ghost-template/ghost/volumes/content/themes/
so that your theme folder sits next to the casper folder in the themes directory

Now run:
docker-compose up -d

Log in to the Ghost admin, go to Settings > General, and at the bottom is the Theme dropdown. Select your theme and click Save.

### How does that work?

The ghost/volumes/content directory (on docker host machine) gets mounted inside your ghost container at /var/lib/ghost when the container runs. See the docker-compose.yml to see where this volume mount is happening.

## Security

- Only NGINX's ports (80, 443) are exposed at host level.
- Other ports are available only from inside host and linked containers.

## To Upgrade from an Earlier Ghost version

Docker containers are meant to be disposable, so you'll need to export your Ghost data first.

0. Export your data: https://help.ghost.org/hc/en-us/articles/224112927-Import-Export-Data
0. Set up a new server with the repo.
0. Set it up as directed.
0. Create your admin account via the web-based wizard.
0. Delete the starter post.
0. Go to Labs and import your data.

That's it!

