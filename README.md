# Create-simple-custom-docker-image-&-push-to-docker-hub


We are creating an custom docker image with help of existing apache docker image.So the final image will be an httpd docker image with listener port 8080.

## Installation

If you need to download docker to the EC2 instance, then can follow this:

~~~sh
sudo yum install docker -y
sudo systemctl restart docker.service
sudo systemctl enable docker.service
sudo systemctl status docker.service
sudo usermod -a -G docker ec2-user
~~~

> Please exit from the instance and login back to reflect the above changes


## 1. Getting HTTPD conf file

First, we need to get an HTTPD docker image and have to fetch the httpd.conf file from the image. And we would use this .conf file for our custom docker image. You can use this link for getting [docker image](https://hub.docker.com/_/httpd)

Lauch an container webserver with the image httpd:alpine
~~~sh
docker container run --name webserver -d httpd:alpine
~~~

Copy the conf file
~~~sh
docker container cp webserver:/usr/local/apache2/conf/httpd.conf .
~~~

Then, we can stop & remove the container as well as the docker image
~~~sh
docker container stop webserver ; docker container rm webserver
docker image rm httpd:alpine
~~~

Now we have conf file with us, we can make our desired changes in this conf file and save it.
![image](https://user-images.githubusercontent.com/100773863/162553134-84f0b48c-6666-4ba9-89ad-db4b1e699bf0.png)

Here I'm changing the port of httpd from default 80 to 8080

~~~sh
vim httpd.conf 
# Listen 80 to Listen 8080
~~~


## 2. Adding sample template to instance

Here I'm using HTML template for EC2 instance, you can use this link for getting [sample HTMl templates](https://www.tooplate.com/) . 
Downloaded the template to /home/ec2-user/website/ location.

~~~sh
wget https://www.tooplate.com/zip-templates/2121_wave_cafe.zip ; unzip 2121_wave_cafe.zip ; mv 2121_wave_cafe website; rm -rf 2121_wave_cafe.zip
~~~

So, we have the site template in /home/ec2-user/website/


## 3. Move conf and template to my project directory

Now we create a directory for handling our project and we move these 2 files (website and conf) to that directory.

~~~sh
mkdir project/
cp -r website/ httpd.conf devops-project 
cd project/
ls -l project/
total 24
-rw-r--r-- 1 ec2-user ec2-user 20827 Apr  4 10:00 httpd.conf
drwxrwxr-x 6 ec2-user ec2-user   106 Apr  9 02:29 website
~~~

## 4. Create file for building image

Now we will create a file in project directory for building our custom docker image. We create file "Dockerfile" and we add these:

~~~sh
vim Dockerfile
~~~

then add,

~~~sh

FROM httpd:alpine
COPY ./website/ /usr/local/apache2/htdocs/
COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
CMD ["httpd-foreground"]

~~~


Let's build the image, 
>  Note: Ensure the build image command is running on the correct working directory (Project):

~~~sh
docker image build -t dockerimage:1 .
~~~



## 5. Pushing custom image to Docker

Now, let's add this custom docker image to our docker hub. First we need to sign up in [docker hub](https://hub.docker.com/) and then follow this:

~~~sh
docker login
~~~
Enter your credentials and you will get a login succeeded message/result once done




**We will rename the custom image (dockerimage:1) to desired one, here I rename it to:**

~~~sh
docker image tag dockerimage:1 <user_name>/dockertestimage:custom
docker image tag dockerimage:1 <user_name>/dockertestimage:latest
~~~

> Note: please use your docker hub username on here <user_name>



Now, we can push the image to docker hub

~~~sh
docker image push <user_name>/dockertestimage:custom
docker image push <user_name>/dockertestimage:latest
~~~

Now we can pull this image for creating docker container!

**To check: **

Launch a container from your newly created image

~~~sh
docker container run --rm --name test5 -d -p 80:8080 <user_name>/dockertestimage
~~~

Then load your intance public Ip on the web browser, 


## Conclusion

This is how an docker custom image is created and pushed to docker hub. Please contact me when you encounter any difficulty error while using this terrform code. Thank you and have a great day!


 ### ⚙️ Connect with Me
<p align="center">
<a href="https://www.linkedin.com/in/radin-lawrence-8b3270102/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>
<a href="mailto:radin.lawrence@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
