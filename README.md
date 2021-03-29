# How to apply patches in weblogic 10.3.6 version and upt 12.1.1
Weblogic Smart Update utility (bsu)  is used to apply patches in 10.3.6 and 12.1.1 version of weblogic.

Weblogic Smart Update utility (bsu) lies in $MW_HOME/utils/bsu/

BSU Utility stands for BEA Smart update utility

Now how to apply patches in weblogic in 10.3.6

## 0.Stop the weblogic server

### Export needed PATH variables
    
    export MW_HOME=/u01/app/oracle/product/fmw11g
    export WLS_HOME=$MW_HOME/wlserver_10.3
    export WL_HOME=$WLS_HOME
    export JAVA_HOME=/usr/lib/jvm/java-1.6.0-openjdk-1.6.0.41.x86_64
    export PATH=$JAVA_HOME/bin:$PATH
    

## 1.First unzip the patch to {MW_HOME}/utils/bsu/

### For Unix/Linux
    ```
    [oracle@XXXXXXX tmp]$  unzip p26519424_1036_Generic.zip -d $MW_HOME/utils/bsu/cache_dir
    ```

    *Note: You must make sure that the target directory for unzip has required write and executable permissions
     for “user” with which the component being patched is installed.

    *Note down the patch ID by reading the read me

> Description:
> ============
> Oracle WebLogic Sever overlay patch for 10.3.6.0.190416 which requires WLS 10.3.6.0.190416 PSU (Patch Number: >29204678 , Patch ID :**FMJJ**) in the environment

## 2.Navigate to the {MW_HOME}/utils/bsu directory and Apply the patch.

### For Unix/Linux
    ```
    $ cd $MW_HOME/utils/bsu
    $ [oracle@XXXXXXX bsu]$ ./bsu.sh -install -patch_download_dir=$MW_HOME/utils/bsu/cache_dir -patchlist=FMJJ -prod_dir=$WLS_HOME -log=/tmp/weblogic_patching.log
    Exception in thread "Main Thread" java.lang.OutOfMemoryError
    Solution : Edit the bsu.cmd and increase the JVM to something like below.
    -Xms1024m –Xmx2048m
    [oracle@XXXXXXX bsu]$ ./bsu.sh -install -patch_download_dir=$MW_HOME/utils/bsu/cache_dir -patchlist=FMJJ -prod_dir=$WLS_HOME -log=/tmp/weblogic_patching.log
    Checking for conflicts...
    No conflict(s) detected

    Installing Patch ID: FMJJ
    ..
    Result: Success

    ```
### For Windows
    ```
    bsu.cmd -install -patch_download_dir=${MW_HOME}/utils/bsu/cache_dir -patchlist=<id> -prod_dir=$MW_HOME\wlserver_10.3
    ```

## 3.If you get conflicts, you may have to remove previous patches, before attempting to apply the patch again.
    ```
    [oracle@xxxxxxxxx bsu]$ ./bsu.sh -install -patch_download_dir=$MW_HOME/utils/bsu/cache_dir -patchlist=FMJJ -prod_dir=$WLS_HOME -log=/tmp/weblogic_patching.log
    Checking for conflicts........
    Conflict(s) detected - resolve conflict condition and execute patch installation again
    Conflict condition details follow:
    Patch FMJJ is mutually exclusive and cannot coexist with patch(es): B25A
    [oracle@FCISCOSTBAPP1 bsu]$

    [oracle@xxxxxxxx bsu]# ./bsu.sh -remove -patchlist=B25A -prod_dir=$WLS_HOME -log=/tmp/weblogic_patching.log
    Checking for conflicts......
    No conflict(s) detected

    Removing Patch ID: B25A.
    Result: Success


    [oracle@xxxxxxxxx bsu]# ./bsu.sh -install -patch_download_dir=/u01/app/oracle/Middleware/utils/bsu/cache_dir -patchlist=FMJJ -prod_dir=/u01/app/oracle/Middleware/wlserver_10.3/ -log=/tmp/weblogic_patching.log
    Checking for conflicts......
    No conflict(s) detected

    Installing Patch ID: FMJJ..
    Result: Success
    ```

## 4. Check Version is Updated

### Source CLASSPATH values
    ```
    [oracle@xxxxxx bsu]$ . $WLS_HOME/server/bin/setWLSEnv.sh
    CLASSPATH=/u01/app/oracle/product/fmw11g/patch_wls1036/profiles/default/sys_manifest_classpath/weblogic_patch.jar:/u01/app/oracle/product/fmw11g/patch_ocp371/profiles/default/sys_manifest_classpath/weblogic_patch.jar:/u01/app/oracle/jdk1.8.0_45/lib/tools.jar:/u01/app/oracle/product/fmw11g/wlserver_10.3/server/lib/weblogic_sp.jar:/u01/app/oracle/product/fmw11g/wlserver_10.3/server/lib/weblogic.jar:/u01/app/oracle/product/fmw11g/modules/features/weblogic.server.modules_10.3.6.0.jar:/u01/app/oracle/product/fmw11g/wlserver_10.3/server/lib/webservices.jar:/u01/app/oracle/product/fmw11g/modules/org.apache.ant_1.7.1/lib/ant-all.jar:/u01/app/oracle/product/fmw11g/modules/net.sf.antcontrib_1.1.0.0_1-0b2/lib/ant-contrib.jar:

    PATH=/u01/app/oracle/product/fmw11g/wlserver_10.3/server/bin:/u01/app/oracle/product/fmw11g/modules/org.apache.ant_1.7.1/bin:/u01/app/oracle/jdk1.8.0_45/jre/bin:/u01/app/oracle/jdk1.8.0_45/bin:/usr/lib/jvm/              java-1.6.0-openjdk-1.6.0.41.x86_64/bin:/usr/lib/jvm/java-1.6.0-openjdk-1.6.0.41.x86_64/bin:/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin

    Your environment has been set.
    ```

### Check version is set
    ```
    [oracle@xxxxx bsu]$ java weblogic.version

    WebLogic Server 10.3.6.0.171017 PSU Patch for BUG26519424 TUE SEP 12 18:34:42 IST 2017
    WebLogic Server 10.3.6.0  Tue Nov 15 08:52:36 PST 2011 1441050

    Use 'weblogic.version -verbose' to get subsystem information

    Use 'weblogic.utils.Versions' to get version information for all modules
    [oracle@xxxxxx bsu]$
    ```

    