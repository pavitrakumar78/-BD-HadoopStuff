#Setting up java on your Ubuntu

You need to have Oracle JRE/DJK installed on your system.
To check, type:
```
java -version
```

If you already have Oracle JDK installed, you can skip reading this file, go directly to HadoopSetup[here]
If you see OpenJDK, then follow the instruction in this file to uninstall and setup OracleJDK/JRE.

1.Remove OpenDJK and create a folder to install OracleJDK/JRE.
```
sudo apt-get purge openjdk-\*
sudo mkdir -p /usr/local/java
```

2.Check if your system is 32-Bit or  64-Bit
```
uname -a
```
If you see x86_64 anywhere after executing the above command, then it is a 64-Bit system.

3.Download the required files from the link given below:  
[JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
[JRE](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)

4.Copy the donwloaded files into java directory we created.
```
cd ~/Downloads
sudo cp -r jre-8u25-linux-x64.tar.gz /usr/local/java
sudo cp -r jdk-8u25-linux-x64.tar.gz /usr/local/java
cd /usr/local/java
```

(change the file name according to the version you are downloading)

5.Unpack and install the files
```
sudo tar xvzf jre-8u25-linux-x64.tar.gz
sudo tar xvzf jdk-8u25-linux-x64.tar.gz
```

Now, we need to set the system PATH vairable
```
sudo vi /etc/profile
```
Append/Modify the following to the file:
```
JAVA_HOME=/usr/local/java/jre1.8.0_25
PATH=$PATH:$HOME/bin:$JAVA_HOME/bin
export JAVA_HOME
export PATH

JAVA_HOME=/usr/local/java/jdk1.8.0_25
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME
export JRE_HOME
export PATH
```
For mac just add:
```
export JAVA_HOME=$(/usr/libexec/java_home)
```
Now we need to tell the system where the java files are located.
For Java:
```
sudo update-alternatives --install "/usr/bin/java" "java" "/usr/local/java/jdk1.8.0_25/bin/java" 1
```
For Java Compiler:
```
sudo update-alternatives --install "/usr/bin/javac" "javac" "/usr/local/java/jdk1.8.0_25/bin/javac" 1
```
For Java webstart:
```
sudo update-alternatives --install "/usr/bin/javaws" "javaws" "/usr/local/java/jdk1.8.0_25/bin/javaws" 1
```

Making it the default java
```
sudo update-alternatives --set java /usr/local/java/jdk1.8.0_25/bin/java
sudo update-alternatives --set javac /usr/local/java/jdk1.8.0_25/bin/java
sudo update-alternatives --set javaws /usr/local/java/jdk1.8.0_25/bin/java
```

Now reload the path and restart OS(this step applies for both ubuntu and mac).
```
source /etc/profile
```

You have now installed the required java for Hadoop setup, after you have completed all the steps given to check if you
have installed it correctly, enter the following command:
```
java -version
```
You will now see Java(TM) SE Runtime Environment.
Now continue to this file [link] to setup Hadoop.
