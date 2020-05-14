# demojenkins2
second task MLOPS

Hello Readers! Today I'm creating a pipeline of 5 jobs in Jenkins as a task given in the MLOPS training under Mr. Vimal Daga of LinuxWorld Informatics Pvt.Ltd.

This practical will be you can say a part of a real product development . While Developing any real world product , the team is always worried about the downtime of the product , whether it be a website , app or any product . We as humans can't monitor 24x7 our product.

So, we need something which can automatically detect if something goes wrong and make it working . And here for automation I'm using Jenkins which will automatically set up server according to the code pushed on github and keeps monitoring the server . Hope you'll enjoy!

##So, here is the working of each job:

> job1: pulls the code from github to our os

> job2: checks for the type of code pulled and according starts a container for the code

> job3: It will check if our app is working or not

> job4: sends email to the team if the app is crashed

> job5: if a container fails it restarts the container


Here , In this task our jenkins will be running in a container and creates new containers according to requirements on the base os .

The base os here used is Redhat.

So, let's start....

First of all we need to create a Jenkins image. So here is the Dockerfile to create jenkins image that we will use to create jenkins container.

Your Dockerfile should looks like this :
<img src="">


Save this Dockerfile and build an image from it using:

docker build -t imagename .

Thus the image is built , now next step is to launch the Jenkins container :

for that use :

docker run -it --name jenki -p 8989:8080 imagename

Here I am using port no. 8989 .

You can check if your container is running using :

docker ps 

on base os
No alt text provided for this image

Thus if you go to your baseos url with specified port you'll see this page:
No alt text provided for this image

the default username is admin and password can be found at location /var/lib/jenkins/secrets/initialAdminPassword

Thus you'll see dashboard
No alt text provided for this image

Now create a new job (job1)
No alt text provided for this image

and use following configuration to pull repo from github (we have already done this in my previous article):
No alt text provided for this image
No alt text provided for this image

here # sudo cp -r * /web will copy the code from github to your jenkins container at location /web

and # sudo scp /web root@192.168.43.154:/web

will copy it to your base os's location /web

I have used two steps here ,however you can also directly copy to the baseos using ssh

Here , the configuration for first job is complete , now for second job, configuration looks somewhat like this
No alt text provided for this image
No alt text provided for this image

Here currently , i am deploying for only webserver , but soon i'll be updating my article for variable type of codes , we just need an if else loop in bash script to do so


for third job we are checking if our app is working fine or not , so configuration looks like this:
No alt text provided for this image
No alt text provided for this image

Here we are curling to the web server from our base os , if the webserver is working fine curl will return a true and exit code 0 will be returned and vice versa.

Now coming to job4 which will send us the email if job is working fine or not ( that's an interesting one )

for that we need to setup some things.

First thing is on our os where jenkins is running go to the location

/etc/sysconfig/

There in jenkins file add these to JENKINS_JAVA_OPTIONS :
No alt text provided for this image

You can copy from here:

JENKINS_JAVA_OPTIONS="-Djava.awt.headless=true -Dmail.smtp.starttls.enable=true -Dmail.smtp.ssl.protocols=TLSv1.2"

Now in you Jenkins configurations, pass SMTP server and your gmail credentials:
No alt text provided for this image


Now configure job4 like this :
No alt text provided for this image
No alt text provided for this image

Thus if any error occurs in the web server , an automated message will be sent to the recipient's email address.

Now , the last job i.e. job5 is to check for the proper working of the app , if the app crashes it'll automatically restart the app. For this I've used "build periodically" option , which will check for the app's proper working every minute
No alt text provided for this image
No alt text provided for this image

That's all set , Now to visualize the flow of job we can use the build pipeline plugin in jenkins , so go to manage plugin option and install build pipeline plugin from there
No alt text provided for this image

then click on new view option on the dashboard to create a view



No alt text provided for this image


enter any view name of your choice and click on Build pipeline view
No alt text provided for this image


select an initial job (job1 in this case)
No alt text provided for this image

and click on ok

Thus now you'll see an option to view the pipeline will be there , click on it and you'll see the pipeline view of all the jobs interconnected:
No alt text provided for this image


