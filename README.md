# docker-ghost-template
Docker composition of Ghost blog with Node, NGINX proxy, database, etc.

## How to Use It

0. Clone project to your filesystem.
0. Edit environment settings in docker-compose.yml
    - blog domain
    - database settings
    - mail server settings
0. Run from within your Linux environment or Docker Toolbox environment: 
    0. docker-compose build
    0. docker-compose up
0. If using Docker Toolbox, look up the host IP address with: docker-machine ip default
0. On Linux, you'll just use "localhost" or "127.0.0.1".
0. Put that IP address in your browser and you'll see your new blog.
0. Go to http://THAT-IP-ADDRESS/admin to set up your blog.
0. Once you've made your admin account using that wizard, go back to: http://THAT-IP-ADDRESS/admin
0. Log in and enjoy.

## Ghost Content Directory

The directory ghost/content will be populated with 4 additional directories the first time you start the container.

Ghost will create these directories:

- apps - for future use when ghost supports apps
- data - sqlite db
- images - for uploaded images
- themes

Once the directories have been created, you are free to commit the files to version control in your clone.

## How to back up your database

0. Run "docker-compose ps" to get a list of running containers.
0. Locate the name of the mysql container.
0. Run this command to get the container's internal IP: docker inspect --format='{{.NetworkSettings.IPAddress}}' THAT_CONTAINER-NAME
0. In your favorite database GUI tool (like Navicat), create a new connection via SSH tunnel to the host machine
0. Use the internal IP address and database user and password to connect to database once SSH tunnel is established to host.
0. You'll have access to the data so you can view data and run backups.

## Todo List:

- fix timing issues: 
    - MySQL port 3306 is available before it's ready to receive requests.
- add S3 support - https://www.npmjs.com/package/ghost-s3-file-store
- add data volume for MySQL
- add NGINX SSL termination

## The Stack

- Node.js
    - Ghost blog software
    - Added Node module for supporting uploads to S3 so images are not saved to filesystem.
- NGINX
    - proxying port 80 calls to the Node web server on port 2368
- MySQL database
    - using UTFMB4 encoding (MySQL's UTF8 implementation was limited. UTFMB4 includes Emoji)

## Security

- Only NGINX's ports are exposed at host level.
- Other ports are available only from inside host and linked containers.