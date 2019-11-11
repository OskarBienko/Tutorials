# Setting up a Cluster on AWS with Putty and Apache Spark / Installing Jupyter



This tutorial is based on https://dzone.com/articles/apache-spark-setting-up-a-cluster-on-aws

### 1. Create AWS Instance (Select Ubuntu Server) and download PuTTY

### 2. SSH into your instance 

 Download your EC2 key pair file and convert it to ppk format in PuTTYGen and then lunch PuTTY.

 Type ubuntu@ and paste instance's public DNS:



![0](https://github.com/mrkjankowski/Tutorials/blob/Photos/0.png)

 Select your PPK file:

![](https://github.com/mrkjankowski/Tutorials/blob/Photos/1.png)

And click Open.

### 3. Download and install Anaconda

Type in terminal:

```
$ wget http://repo.continuum.io/archive/Anaconda3-4.1.1-Linux-x86_64.sh
$ bash Anaconda3–4.1.1-Linux-x86_64.sh
```

 Press Enter through the license agreements, then Enter **yes** to accept, then Enter again to accept the     default location. Then reset terminal.

### 4. Prepare Jupyter

```
$ jupyter notebook --generate-config
$ mkdir certs
$ cd certs
$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

```

 You’ll get asked some general questions after running that last line. Just fill them out with some general    information.  

Then we will use visual editor (vi) to edit the file. 

```
$ cd ~/.jupyter/
$ vi jupyter_notebook_config.py
```

Press "**i**" on your keyboard and then at the top of the file type:

```
c = get_config()
c.NotebookApp.certfile = u'/home/ubuntu/certs/mycert.pem' 
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False 
c.NotebookApp.port = 8888
```

 Then press **ESC** and type "**:wq**" to write and quit.

### 5.  Install Java 

```
$ cd
$ sudo apt-get -y install openjdk-8-jdk-headless
```

### 6. Install Apache Spark: 

```
$ mkdir ~/server
$ cd ~/server
$ wget http://ftp.man.poznan.pl/apache/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.7.tgz \
$ tar xvzf spark-2.4.4-bin-hadoop2.7.tgz
$ export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/
```

This is the end of installing the application.

### 7. Tunneling on Putty

Now we will create a tunnel to be able to open Jupyter and to get informations about our cluster in the browser.

Open PuTTY, type your instance's public DNS and browse your PPK file same as in the second point.

Then create tunnels:

![](https://github.com/mrkjankowski/Tutorials/blob/Photos/2.png)

![](https://github.com/mrkjankowski/Tutorials/blob/Photos/3.png)

Then click Open.

### 8. Starting the Jupyter

```
$ jupyter notebook
```

Startup takes some time. You should see something like this:

![](https://github.com/mrkjankowski/Tutorials/blob/Photos/5.png)

 Copy the selected URL, open your browser and paste URL. Then change "localhost:8888" to "localhost:8000".



### 9. Startup Master

Open new terminal in Jupyter and type:

```
$ cd ~/server
$  ./spark-2.4.4-bin-hadoop2.7/sbin/start-master.sh
```

 Navigate to port 8001 and you get a snapshot of the cluster:

![](https://github.com/mrkjankowski/Tutorials/blob/Photos/6.png) 

 The URL highlighted in red is the Spark URL for the Cluster. Copy it down as you will need it to start the slave. 

### 10. Slave Startup

```cd ~/server
$ cd ~/server
$ ./spark-2.4.4-bin-hadoop2.7/sbin/start-slave.sh spark://ip-172-31-22-200.us-west-1.compute.internal:7077
```

 And with that, your cluster should be functioning. Hit the status page again at port 8001 to check for it. Observe that you can see the slave under Workers, along with the number of core available and the memory. 

