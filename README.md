## Base Django docker

This docker image contains the following packages:

 * nginx
 * uwsgi
 * Django 1.5.1
 * sshd
 * new relic
 * supervisord

Please note that it's just a base image, not production ready! In order to have
something usable you'll have to:

 * Create your own Dockerfile and use andresriancho/django-uwsgi-nginx-ssh as it's base
 
 * Set your New Relic license key for server monitoring:
    ```
    RUN nrsysmond-config --set license_key=...
    ```
  
 * Add your SSH keys to be able to login to the docker when it's running, the
 user you'll use for SSH is called `docker`:
    ```
    RUN ssh-import-id gh:andresriancho -o /home/docker/.ssh/authorized_keys
    ```

 * Set the root password to `su -`:
    ```
    $ openssl passwd -1 your-password
    ```
    And then replace the hash here:
    ```
    RUN echo 'root:hash' | chpasswd -e
    ```

 * ADD the Django application code to `/home/docker/code`
    ```
    RUN cp myapp/wsgi.py wsgi.py
    ```
    
 * Move your Django application `wsgi.py` file to the `/code/` directory
 
 * Place your static and media files in `/home/docker/code/static/` and
  `/home/docker/code/media/` respectively.

## Source
The source for this image can be found [here](https://github.com/andresriancho/django-uwsgi-nginx-ssh),
I'm not using automated builds since they are really slow, instead I'm building
the images at CircleCI and then pushing them to the registry.
  
## Pushing a new version
```
docker build -t andresriancho/django-uwsgi-nginx-ssh . 
docker pull andresriancho/django-uwsgi-nginx-ssh
```