このページはJBoss、MySQLの組み合わせでミドルウェアのセットアップを行う際の手順をサンプルで記載しています。
実際に行う際は、各製品のドキュメントに従って実施してください。

## Java

    [root@www3225ug work]# yum install jdk-7u40-linux-x64.rpm
    Loaded plugins: fastestmirror, security
    Loading mirror speeds from cached hostfile
     * base: ftp.nara.wide.ad.jp
     * epel: ftp.iij.ad.jp
     * extras: ftp.nara.wide.ad.jp
     * updates: ftp.nara.wide.ad.jp
    Setting up Install Process
    Examining jdk-7u40-linux-x64.rpm: 2000:jdk-1.7.0_40-fcs.x86_64
    Marking jdk-7u40-linux-x64.rpm to be installed
    Resolving Dependencies
    --> Running transaction check
    ---> Package jdk.x86_64 2000:1.7.0_40-fcs will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    　（中略）

    Installed:
      jdk.x86_64 2000:1.7.0_40-fcs                                                                                                    
    
    Complete!
    
    [root@www3225ug work]# java -version
    java version "1.7.0_40"
    Java(TM) SE Runtime Environment (build 1.7.0_40-b43)
    Java HotSpot(TM) 64-Bit Server VM (build 24.0-b56, mixed mode)


### JBoss

    unzip jboss-as-7.1.1.Final.zip -d /usr/share/
    Archive:  jboss-as-7.1.1.Final.zip
       creating: /usr/share/jboss-as-7.1.1.Final/
       creating: /usr/share/jboss-as-7.1.1.Final/appclient/
       creating: /usr/share/jboss-as-7.1.1.Final/appclient/configuration/
       creating: /usr/share/jboss-as-7.1.1.Final/bin/
       creating: /usr/share/jboss-as-7.1.1.Final/bin/client/
       
      （中略）
      
      inflating: /usr/share/jboss-as-7.1.1.Final/standalone/configuration/mgmt-users.properties 
       creating: /usr/share/jboss-as-7.1.1.Final/domain/tmp/auth/
    [root@www3225ug work]#
    [root@www3225ug work]# adduser jboss
    [root@www3225ug work]# chown -fR jboss.jboss /usr/share/jboss-as-7.1.1.Final/
    [root@www3225ug work]# ls -al /usr/share/jboss-as-7.1.1.Final/
    合計 340
    drwxr-xr-x  10 jboss jboss   4096  3月 10 00:14 2012 .
    drwxr-xr-x. 87 root  root    4096 10月  7 05:30 2013 ..
    -rw-r--r--   1 jboss jboss  26530  3月 10 00:14 2012 LICENSE.txt
    -rw-r--r--   1 jboss jboss   2421  3月 10 00:14 2012 README.txt
    drwxr-xr-x   3 jboss jboss   4096  3月 10 00:14 2012 appclient
    drwxr-xr-x   4 jboss jboss   4096  3月 10 00:14 2012 bin
    drwxr-xr-x   4 jboss jboss   4096  3月 10 00:14 2012 bundles
    -rw-r--r--   1 jboss jboss   2451  3月 10 00:14 2012 copyright.txt
    drwxr-xr-x   4 jboss jboss   4096  3月 10 00:14 2012 docs
    drwxr-xr-x   5 jboss jboss   4096  3月 10 00:14 2012 domain
    -rw-r--r--   1 jboss jboss 266549  3月 10 00:14 2012 jboss-modules.jar
    drwxr-xr-x  13 jboss jboss   4096  3月 10 00:21 2012 modules
    drwxr-xr-x   6 jboss jboss   4096  3月 10 00:14 2012 standalone
    drwxr-xr-x   2 jboss jboss   4096  3月 10 00:14 2012 welcome-content
    [root@www3225ug work]# su jboss
    [jboss@www3225ug work]$ cd /usr/share/jboss-as-7.1.1.Final/bin
    [jboss@www3225ug bin]$ ls
    add-user.bat    appclient.conf.bat  domain.conf      jboss-cli.bat  jconsole.sh  run.sh               standalone.sh  wsconsume.sh
    add-user.sh     appclient.sh        domain.conf.bat  jboss-cli.sh   jdr.bat      standalone.bat       vault.bat      wsprovide.bat
    appclient.bat   client              domain.sh        jboss-cli.xml  jdr.sh       standalone.conf      vault.sh       wsprovide.sh
    appclient.conf  domain.bat          init.d           jconsole.bat   run.bat      standalone.conf.bat  wsconsume.bat
    [jboss@www3225ug bin]$ ./add-user.sh
    
    What type of user do you wish to add?
     a) Management User (mgmt-users.properties)
     b) Application User (application-users.properties)
    (a): a
    
    Enter the details of the new user to add.
    Realm (ManagementRealm) :
    Username : jboss
    Password :
    Re-enter Password :
    About to add user 'jboss' for realm 'ManagementRealm'
    Is this correct yes/no? yes
    Added user 'jboss' to file '/usr/share/jboss-as-7.1.1.Final/standalone/configuration/mgmt-users.properties'
    Added user 'jboss' to file '/usr/share/jboss-as-7.1.1.Final/domain/configuration/mgmt-users.properties'
    
    [jboss@www3225ug bin]$ ./standalone.sh -Djboss.bind.address=0.0.0.0 -Djboss.bind.address.management=0.0.0.0
    =========================================================================
    
      JBoss Bootstrap Environment
    
      JBOSS_HOME: /usr/share/jboss-as-7.1.1.Final
    
      JAVA: java
    
      JAVA_OPTS:  -server -XX:+UseCompressedOops -XX:+TieredCompilation -Xms64m -Xmx512m -XX:MaxPermSize=256m -Djava.net.preferIPv4Stack=true -Dorg.jboss.resolver.warning=true -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000 -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true -Djboss.server.default.config=standalone.xml
    
    =========================================================================
    

### MySQL

            
        [root@www3225ug ~]# wget -q http://dev.mysql.com/get/Downloads/MySQL-5.6/MySQL-client-5.6.14-1.el6.x86_64.rpm/from/http://cdn.mysql.com/ http://dev.mysql.com/get/Downloads/MySQL-5.6/MySQL-shared-compat-5.6.14-1.el6.x86_64.rpm/from/http://cdn.mysql.com/ http://dev.mysql.com/get/Downloads/MySQL-5.6/MySQL-server-5.6.14-1.el6.x86_64.rpm/from/http://cdn.mysql.com/ http://dev.mysql.com/get/Downloads/MySQL-5.6/MySQL-devel-5.6.14-1.el6.x86_64.rpm/from/http://cdn.mysql.com/ http://dev.mysql.com/get/Downloads/MySQL-5.6/MySQL-shared-5.6.14-1.el6.x86_64.rpm/from/http://cdn.mysql.com/
        [root@www3225ug work]# ls
        MySQL-client-5.6.14-1.el6.x86_64.rpm  MySQL-server-5.6.14-1.el6.x86_64.rpm  MySQL-shared-compat-5.6.14-1.el6.x86_64.rpm
        MySQL-devel-5.6.14-1.el6.x86_64.rpm   MySQL-shared-5.6.14-1.el6.x86_64.rpm
        [root@www3225ug work]# yum install MySQL-{client,devel,server,shared-compat}-5.6.14-1.el6.x86_64.rpm
        Loaded plugins: fastestmirror, security
        Loading mirror speeds from cached hostfile
        epel/metalink                                                                                               | 5.4 kB     00:00    
         * base: ftp.nara.wide.ad.jp
         * epel: ftp.iij.ad.jp
         * extras: ftp.nara.wide.ad.jp
         * updates: ftp.nara.wide.ad.jp
        
        （中略）
        
        
        
        A RANDOM PASSWORD HAS BEEN SET FOR THE MySQL root USER !
        You will find that password in '/root/.mysql_secret'.
        
        You must change that password on your first connect,
        no other statement but 'SET PASSWORD' will be accepted.
        See the manual for the semantics of the 'password expired' flag.
        
        Also, the account for the anonymous user has been removed.
        
        In addition, you can run:
        
          /usr/bin/mysql_secure_installation
        
        which will also give you the option of removing the test database.
        This is strongly recommended for production servers.
        
        See the manual for more instructions.
        
        Please report any problems with the /usr/bin/mysqlbug script!
        
        The latest information about MySQL is available on the web at
        
          http://www.mysql.com
        
        Support MySQL by buying support/licenses at http://shop.mysql.com
        
        New default config file was created as /usr/my.cnf and
        will be used by default by the server when you start it.
        You may edit this file to change server settings
        
        WARNING: Default config file /etc/my.cnf exists on the system
        This file will be read by default by the MySQL server
        If you do not want to use this, either remove it, or use the
        --defaults-file argument to mysqld_safe when starting the server
        
        
        
        （中略）
        
        
        Installed:
          MySQL-client.x86_64 0:5.6.14-1.el6              MySQL-devel.x86_64 0:5.6.14-1.el6       MySQL-server.x86_64 0:5.6.14-1.el6     
          MySQL-shared-compat.x86_64 0:5.6.14-1.el6     
        
        Replaced:
          mysql-libs.x86_64 0:5.1.67-1.el6_3                                                                                              
        
        Complete!
        [root@www3225ug work]# yum install MySQL-shared-5.6.14-1.el6.x86_64.rpm
        
        
        （中略）
        
        Total size: 8.4 M
        Installed size: 8.4 M
        Is this ok [y/N]: y
        Downloading Packages:
        Running rpm_check_debug
        Running Transaction Test
        Transaction Test Succeeded
        Running Transaction
          Installing : MySQL-shared-5.6.14-1.el6.x86_64                                                                                1/1
          Verifying  : MySQL-shared-5.6.14-1.el6.x86_64                                                                                1/1
        
        Installed:
          MySQL-shared.x86_64 0:5.6.14-1.el6                                                                                              
        
        Complete!
        [root@www3225ug work]#
