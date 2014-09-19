## Base Django docker

This docker image contains the following packages:

 * nginx
 * uwsgi
 * Django 1.5.1
 * sshd
 * new relic
 * supervisord

Please note that it't just a base image, not production ready! In order to have
something usable you'll have to:

 * Create your own Dockerfile and use andresriancho/django-uwsgi-nginx-ssh as it's base
 
 * Set your New Relic license key for server monitoring:
    ```bash
    RUN nrsysmond-config --set license_key=...
    ```
  
 * Add your SSH keys to be able to login to the docker when it's running, the
 user you'll use for SSH is called `docker`:
    ```bash
    RUN ssh-import-id gh:andresriancho -o /home/docker/.ssh/authorized_keys
    ```

 * Set the root password to `su -`:
    ```console
    $ openssl passwd -1 your-password
    ```
    And then replace the hash here:
    ```bash
    RUN echo 'root:hash' | chpasswd -e
    ```

 * ADD the Django application code to `/home/docker/code`
    ```bash
    RUN cp myapp/wsgi.py wsgi.py
    ```
    
 * Move your Django application `wsgi.py` file to the `/code/` directory
 
 * Place your static and media files in `/home/docker/code/static/` and
  `/home/docker/code/media/` respectively.