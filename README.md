# solaris-mariadb-nodejs

1. solaris setting

* sudo vi /etc/user_attr
`root::::type=normal`
vi /etc/ftpd/ftpusers #root<br>
vi /etc/ssh/sshd_config
`PermitRootLogin no -> yes`
svcadm restart ssh

1. maria db install

* mysql install
pkg install mysql-51<br>
svcs –a \| grep mysql 명령어를 통해 서비스 확인 \(disable\)<br>
svcadm enable mysql 명령어를 통해 서비스 활성화<br>
svcs –a \| grep mysql 통해 활성화 확인\(online\)<br>
mysql 명령어를 통해 mysql 접속 (exit 통해 빠져나오기)<br>
* maria db install
/database에 mariadb-10.0.11-solaris11-x86_64.tar.gz 폴더 복사<br>
cd /database<br>
tar –zxvf mariadb-10.0.11-solaris11-x86_64.tar.gz 명령어를 통해 압출 풀기<br>
ln –s mariadb-10.0.11-solaris11-x86_64 mariadb 명령어를 통해 심볼릭 만들기<br>
cd mariadb<br>
\./scripts/mysql\_install\_db –user=mysql 명령어를 통해 DB 설치<br>
chown –R root . 명령어를 통해 root 모든 권한 부여<br>
chown –R mysql data 명령어를 통해 mysql유저는 data권한만 부여<br>
cp /database/mariadb/support-files/my-huge.cnf /etc/my.cnf 명령어를 통해서 파일 복사<br>
cp /database/mariadb/support-files/mysql.server /etc/init.d/mysqld 명령어를 통해서 파일 복사<br>
vi /etc/profile<br>
PATH=$PATH:/database/mariadb<br>
PATH=$PATH:/database/mariadb/scripts 작성 후 :wq 종료<br>
source /etc/profile 파일 실행하기<br>
vi /etc/init.d/mysqld<br>
basedir=/database/mariadb<br>
datadir=/database/data 작성 후 :wq 종료<br>
./bin/mysqladmin –u root password ‘123’ 명령어를 통해 root 계정 비밀번호 변경하기<br>
cp /database/mariadb/support-files/mysql.server /etc/init.d/mysql.server 자동실행<br>
ln –s /database/mariadb/bin/mysql /usr/bin/mysql 심볼릭 생성<br>
cd /database/mariadb<br>
./bin/mysql_safe –user=mysql –defaults-file=/etc/my.cnf & 명령어로 마리아DB 실행<br>
mysql –u root –p 또는 mysql –u mysql 명령어 실행<br>

![image](https://user-images.githubusercontent.com/14309034/43109990-8543445a-8f24-11e8-83ca-9e150e17ac83.png)


1. node.js install
/root 에서 TRIBnode-64bit-8.11.3.0.pkg 복사<br>
pkgadd -d TRIBnode-64bit-8.11.3.0.pkg TRIBnode 명령어 실행<br>
vi /etc/profile<br>
PATH=$PATH:/opt/Node<br>
PATH=$PATH:/opt/Node/bin 작성 후 :wq<br>

![image](https://user-images.githubusercontent.com/14309034/43110037-b9c09c5a-8f24-11e8-9409-dcc26f54296f.png)

## reference page
[https://www.itfromallangles.com/2013/02/solaris-11-installing-mysql/](https://www.itfromallangles.com/2013/02/solaris-11-installing-mysql/)

<span class="colour" style="color: rgb(119, 119, 119);">Recently I have been spending some time learning about database technologies (Oracle Databases at Keste as well as MySQL on my own).  Part of this I have decided to carry over into my existing work with Solaris, and go through the installation process using the Image Packaging System which is in Solaris.  Now really the IPS does all the heavy lifting for us, but we still need to know how to utilize the package manager to get the desired result.</span>

<span class="colour" style="color: rgb(119, 119, 119);">**Searching for the MySQL Package**</span>
<span class="colour" style="color: rgb(119, 119, 119);">Now when we search using the below command, you will notice that we are using the parameter -r this tells it to use the remote repository in addition to the local repository, this allows us to find software that we do not have installed on the machine.</span>

|  |
| --- |
| `# pkg search -r mysql`<br>`INDEX       ACTION VALUE                                                                   PACKAGE`<br>`pkg.summary set    A MySQL database adapter for the Python programming language            pkg:/library/python-2/python-mysql-26@1.2.2-0.175.1.0.0.11.0`<br>`pkg.summary set    Apache Portable Runtime Utility (APR-util) 1.3 DBD Driver for MySQL 5.0 pkg:/library/apr-util-13/dbd-mysql@1.3.9-0.175.1.0.0.24.0`<br>`pkg.summary set    MySQL Database Management System (Base)                                 pkg:/database/mysql-common@0.5.11-0.175.1.0.0.24.0`<br>`pkg.summary set    MySQL extension module for PHP                                          pkg:/web/php-53/extension/php-mysql@5.3.14-0.175.1.0.0.24.0`<br>`pkg.summary set    MySQL extension module for PHP                                          pkg:/web/php-52/extension/php-mysql@5.2.17-0.175.1.0.0.24.0`<br>`pkg.summary set    MySQL 5.1 Database Management System                                    pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`pkg.summary set    MySQL 5.1 libraries                                                     pkg:/database/mysql-51/library@5.1.37-0.175.1.0.0.24.0`<br>`pkg.summary set    MySQL 5.1 tests                                                         pkg:/database/mysql-51/tests@5.1.37-0.175.1.0.0.24.0`<br>`basename    file   usr/mysql/5.1/bin/amd64/mysql                                           pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    file   usr/mysql/5.1/bin/mysql                                                 pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    file   usr/mysql/5.1/bin/sparcv9/mysql                                         pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`name        group  mysql                                                                   pkg:/database/mysql-common@0.5.11-0.175.1.0.0.24.0`<br>`basename    link   usr/bin/mysql                                                           pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`name        user   mysql                                                                   pkg:/database/mysql-common@0.5.11-0.175.1.0.0.24.0`<br>`basename    dir    etc/mysql                                                               pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql                                                               pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql/5.1/include/mysql                                             pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql/5.1/share/mysql                                               pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    var/mysql                                                               pkg:/database/mysql-51@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql                                                               pkg:/database/mysql-51/library@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql/5.1/lib/amd64/mysql                                           pkg:/database/mysql-51/library@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql/5.1/lib/mysql                                                 pkg:/database/mysql-51/library@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql/5.1/lib/sparcv9/mysql                                         pkg:/database/mysql-51/library@5.1.37-0.175.1.0.0.24.0`<br>`basename    dir    usr/mysql                                                               pkg:/database/mysql-51/tests@5.1.37-0.175.1.0.0.24.0` |

<span class="colour" style="color: rgb(119, 119, 119);">Now in the output we are looking for a pkg.summary which is the software we are looking for, in our case pkg:/database/mysql-51 or simply mysql-51.</span>
<span class="colour" style="color: rgb(119, 119, 119);">Once we think we have the right package I like to do a pkg info to make sure that it is what I expect, again here we want to look against remote repositories as well with the -r parameter.</span>

|  |
| --- |
| `# pkg info -r mysql-51`<br>`Name: database/mysql-51`<br>`Summary: MySQL 5.1 Database Management System`<br>`Category: Development/Databases`<br>`State: Not installed`<br>`Publisher: solaris`<br>`Version: 5.1.37`<br>`Build Release: 5.11`<br>`Branch: 0.175.1.0.0.24.0`<br>`Packaging Date: September  4, 2012 05:09:22 PM`<br>`Size: 147.23 MB`<br>`FMRI: [pkg://solaris/database/mysql-51@5.1.37](pkg://solaris/database/mysql-51@5.1.37),5.11-0.175.1.0.0.24.0:20120904T170922Z` |

<span class="colour" style="color: rgb(119, 119, 119);">**Install the MySQL Package**</span>
<span class="colour" style="color: rgb(119, 119, 119);">Here we can install MySQL 5.1 via the IPS repositories.</span>

|  |
| --- |
| `# pkg install mysql-51`<br>`Packages to install:  2`<br>`Create boot environment: No`<br>`Create backup boot environment: No`<br>`Services to change:  2`<br> <br>`DOWNLOAD                                PKGS         FILES    XFER (MB)   SPEED`<br>`Completed                                2/2       252/252    52.2/52.2 16.3M/s`<br> <br>`PHASE                                          ITEMS`<br>`Installing new actions                       343/343`<br>`Updating package state database                 Done`<br>`Updating image state                            Done`<br>`Creating fast lookup database                   Done` |

<span class="colour" style="color: rgb(119, 119, 119);">**Enable the MySQL Service**</span>
<span class="colour" style="color: rgb(119, 119, 119);">Now lets take a look at the service.  We can see that the service is installed but disabled.</span>

|  |
| --- |
| `# svcs -a | grep mysql`<br>`disabled       10:28:40 svc:/application/database/mysql:version_51` |

<span class="colour" style="color: rgb(119, 119, 119);">Enable the service.</span>

|  |
| --- |
| `# svcadm enable mysql`<br>`# svcs -a | grep mysql`<br>`online         10:30:26 svc:/application/database/mysql:version_51` |

<span class="colour" style="color: rgb(119, 119, 119);">**Connect to MySQL**</span>

|  |
| --- |
| `# mysql`<br>`Welcome to the MySQL monitor.  Commands end with ; or \g.`<br>`Your MySQL connection id is 1`<br>`Server version: 5.1.37 Source distribution`<br> <br>`Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.`<br> <br>`mysql&gt;` |

<span class="colour" style="color: rgb(119, 119, 119);">Please note this is not a secure MySQL configuration.  You will need to secure this before use.</span>

[https://mariadb.com/kb/en/library/installing-mariadb-binary-tarballs/](https://mariadb.com/kb/en/library/installing-mariadb-binary-tarballs/)

<span class="colour" style="color:rgb(51, 51, 51)">MariaDB Binary tarballs are named following the pattern: mariadb-VERSION-OS.tar.gz. Be sure to [download](http://downloads.mariadb.org/) the correct version for your machine.</span>

**Note:** Some binary tarballs are marked _'(GLIBC_2.14)'_ or _'(requires GLIBC_2.14+)'_. These binaries are built the same as the others, but on a newer build host, and they require GLIBC 2.14 or higher. Use the other binaries for machines with older versions of GLIBC installed. Run `ldd --version` to see which version is running on your distribution.
Others are marked _'systemd'_, which are for systems with `systemd` and GLIBC 2.19 or higher.
<span class="colour" style="color:rgb(51, 51, 51)">To install the [binaries](http://downloads.askmonty.org/), unpack the distribution into the directory of your choice and run the `[mysql\_install\_db](https://mariadb.com/kb/en/installing-system-tables-mysql_install_db/)` script.</span>
<span class="colour" style="color:rgb(51, 51, 51)">In the example below we install MariaDB in the `/usr/local/mysql` directory (this is the default location for MariaDB for many platforms). However any other directory should work too.</span>
<span class="colour" style="color:rgb(51, 51, 51)">We install the binary with a symlink to the original name. This is done so that you can easily change MariaDB versions just by moving the symlink to point to another directory.</span>
**NOTE:** For [MariaDB 5.1.32](https://mariadb.com/kb/en/mariadb-5132-release-notes/) **_only_** the line "`./scripts/mysql_install_db --user=mysql`" should be changed to "`./bin/mysql_install_db --user=mysql`"

### Ensure you use the correct my.cnf files

<span class="colour" style="color:rgb(51, 51, 51)">MariaDB searches for the configuration files '`/etc/my.cnf`' (on some systems '`/etc/mysql/my.cnf`') and '`~/.my.cnf`'. If you have an old `my.cnf` file (maybe from a system installation of MariaDB or MySQL) you need to take care that you don't accidentally use the old one with your new binary .tar installation.</span>
<span class="colour" style="color:rgb(51, 51, 51)">The normal solution for this is to ignore the `my.cnf` file in `/etc` when you use the programs in the tar file.</span>
<span class="colour" style="color:rgb(51, 51, 51)">This is done by [creating your own .my.cnf file](https://mariadb.com/kb/en/mysqld-startup-options/) in your home directory and telling `mysql_install_db`, [mysqld_safe](https://mariadb.com/kb/en/mysqld_safe/) and possibly [mysql \(the command-line client utility\)](https://mariadb.com/kb/en/mysql-client/) to **only** use this one with the option '`--defaults-file=~/.my.cnf`'. Note that this has to be first option for the above commands!</span>

### Installing MariaDB as root in /usr/local/mysql

<span class="colour" style="color:rgb(51, 51, 51)">If you have root access to the system, you probably want to install MariaDB under the user and group 'mysql' (to keep compatibility with MySQL installations):</span>

```
groupadd mysql
useradd -g mysql mysql
cd /usr/local
tar -zxvpf /path-to/mariadb-VERSION-OS.tar.gz
ln -s mariadb-VERSION-OS mysql
cd mysql
./scripts/mysql_install_db --user=mysql
chown -R root .
chown -R mysql data

```

<span class="colour" style="color:rgb(51, 51, 51)">The symlinking with `ln -s` is recommended as it makes it easy to install many MariaDB version at the same time (for easy testing, upgrading, downgrading etc).</span>
<span class="colour" style="color:rgb(51, 51, 51)">If you are installing MariaDB to replace MySQL, then you can leave out the call to `mysql_install_db`. Instead shut down MySQL. MariaDB should find the path to the data directory from your old `/etc/my.cnf` file (path may vary depending on your system).</span>
<span class="colour" style="color:rgb(51, 51, 51)">To start mysqld you should now do:</span>

```
./bin/mysqld_safe --user=mysql &
or
./bin/mysqld_safe --defaults-file=~/.my.cnf --user=mysql &

```

<span class="colour" style="color:rgb(51, 51, 51)">To test connection, modify your $PATH so you can invoke client such as [mysql](https://mariadb.com/kb/en/mysql-client/), [mysqldump](https://mariadb.com/kb/en/mysqldump/), etc.</span>

```
export PATH=$PATH:/usr/local/mysql/bin/

```

<span class="colour" style="color:rgb(51, 51, 51)">You may want to modify your .bashrc or .bash_profile to make it permanent.</span>

### Installing MariaDB as not root in any directory

<span class="colour" style="color:rgb(51, 51, 51)">Below, change /usr/local to the directory of your choice.</span>

```
cd /usr/local
gunzip < /path-to/mariadb-VERSION-OS.tar.gz | tar xf -
ln -s mariadb-VERSION-OS mysql
cd mysql
./scripts/mysql_install_db --defaults-file=~/.my.cnf

```

<span class="colour" style="color:rgb(51, 51, 51)">If you have problems with the above gunzip command line, you can instead, if you have gnu tar, do:</span>

```
tar xfz /path-to/mariadb-VERSION-OS.tar.gz

```

<span class="colour" style="color:rgb(51, 51, 51)">To start mysqld you should now do:</span>

```
./bin/mysqld_safe --defaults-file=~/.my.cnf &

```

### Auto start of mysqld

<span class="colour" style="color:rgb(51, 51, 51)">You can get mysqld (the MariaDB server) to autostart by copying the file `mysql.server` file to the right place.</span>

```
cp support-files/mysql.server /etc/init.d/mysql.server

```

<span class="colour" style="color:rgb(51, 51, 51)">The exact place depends on your system. The `mysql.server` file contains instructions of how to use and fine tune it.</span>

### Post installation

<span class="colour" style="color:rgb(51, 51, 51)">After this, remember to set proper passwords for all accounts accessible from untrusted sources, to avoid exposing the host to security risks! Also consider using the [mysql.server](https://mariadb.com/kb/en/mysqlserver/) to [start MariaDB automatically](https://mariadb.com/kb/en/starting-and-stopping-mariadb-automatically/) when your system boots.</span>
<span class="colour" style="color:rgb(51, 51, 51)">Our MariaDB binaries are similar to the Generic binaries available for the MySQL binary distribution. So for more options on using these binaries, the MySQL 5.5 manual entry on [installing generic binaries](http://docs.oracle.com/cd/E17952_01/refman-5.5-en/binary-installation.html) can be consulted.</span>
<span class="colour" style="color:rgb(51, 51, 51)">For details on the exact steps used to build the binaries, see the [compiling MariaDB section](https://mariadb.com/kb/en/compiling-mariadb-from-source/) of the KB.</span>

<span class="colour" style="color: rgb(0, 0, 0);">[http://itgameworld.tistory.com/63](http://itgameworld.tistory.com/63)</span>

<span class="colour" style="color: rgb(0, 0, 0);">**MariaDB 10.1 Binary 설치**</span>
<span class="colour" style="color: rgb(0, 0, 0);">컴파일보다 비교적 간단한 Binary 설치에 대해 정리해보겠습니다.</span>
<span class="colour" style="color: rgb(0, 0, 0);">Binary는 컴파일이 된 프로그램을 압축해놓은 상태로, 압축만 풀고 간단한 설정만으로도</span>
<span class="colour" style="color: rgb(0, 0, 0);">자신이 원하는 설정대로 조절이 가능하다는 장점이 있습니다.</span>
<span class="colour" style="color: rgb(0, 0, 0);">컴파일의 장점을 갖고 있으면서도 컴파일의 단점인 설치가 복잡하고 오래걸린다는 단점이 보완됩니다.</span>
<span class="colour" style="color: rgb(0, 0, 0);">**파일 다운로드**</span>
<span class="colour" style="color: rgb(0, 0, 0);">[https://mariadb.org/](https://mariadb.org/)</span>
<span class="colour" style="color: rgb(0, 0, 0);">![](https://t1.daumcdn.net/cfile/tistory/215B2E4058193B4529)</span>
<span class="colour" style="color: rgb(0, 0, 0);">![](https://t1.daumcdn.net/cfile/tistory/2403A34058193B4626)</span>
<span class="colour" style="color: rgb(0, 0, 0);">![](https://t1.daumcdn.net/cfile/tistory/2747FB4058193BC62F)</span>
**설치 및 링크 생성**
[root@localhost ~]# useradd mysql
[root@localhost ~]# cd /data
[root@localhost ~]# cd /mdata
[root@localhost ~]# chown mysql:mysql /mdata
[root@localhost data]# tar zxvf mariadb-10.1.18-linux-x86_64.tar.gz
[root@localhost data]# mv mariadb-10.1.18-linux-x86_64 /usr/local/
[root@localhost data]# chown mysql:mysql mariadb-10.1.18-linux-x86_64
[root@localhost data]# chown mysql:mysql mariadb
[root@localhost data]# cd /usr/local/
[root@localhost local]# ln -s mariadb-10.1.18-linux-x86_64 mariadb
**기타 설정**
[root@localhost local]# cp /usr/local/mariadb/support-files/<span class="colour" style="color: rgb(255, 0, 0);">my-huge.cnf <span class="colour" style="color: rgb(0, 0, 0);">/etc/my.cnf</span></span>
[root@localhost local]# cp /usr/local/mariadb/support-files/mysql.server /etc/init.d/mysqld
[root@localhost local]# vi /etc/profile
...
PATH=$PATH:/usr/local/mariadb/bin
PATH=$PATH:/usr/local/mariadb/scripts
[root@localhost local]# source /etc/profile
\[root@localhost local\]\# mysql\_install\_db \-\-basedir=/usr/local/mariadb \-\-datadir=/mdata \-\-defaults\-file=/etc/my\.cnf
[root@localhost local]# vi /etc/init.d/mysqld
...
46    basedir=<span class="colour" style="color: rgb(255, 0, 0);">/usr/local/mariadb</span>
47    datadir=<span class="colour" style="color: rgb(255, 0, 0);">/mdata</span>
...
[root@localhost local]# service mysqld start
Starting MySQL.161018 15:34:35 mysqld_safe Logging to '/mdata/localhost.localdomain.err'.
                                                           [  OK  ]
**비밀번호 변경 및 실행**
[root@localhost bin]# mysqladmin -u root password <span class="colour" style="color: rgb(255, 0, 0);">비밀번호</span>
<span class="colour" style="color: rgb(255, 0, 0);"><span class="colour" style="color: rgb(0, 0, 0);">[root@localhost bin]# mysql -u root -p</span></span>
<span class="colour" style="color: rgb(255, 0, 0);"><span class="colour" style="color: rgb(0, 0, 0);">Enter password:</span></span>

[http://lahuman.jabsiri.co.kr/189](http://lahuman.jabsiri.co.kr/189)

MariaDB를 binary tar.gz 파일로 설치 하자
<span class="colour" style="color: rgb(56, 56, 56);">일반적으로 설치 방법은 다음과 같이 3개의 방법이 있다.</span>

1. 패키지 관리 명령어를 이용한 설치
    * 데비안 계열의 **apt-get**
    * 레드햇 계열의 **yum**
2. 배포 파일을 이용한 설치
    * 데비안 계열의 **.deb**
    * 레드햇 계열의 **.rpm**
3. 바이너리 파일을 이용한 설치
    * 실행파일로 **tar.gz** 형태 제공

<span class="colour" style="color: rgb(56, 56, 56);">물론 모든 프로그램이 상위 3가지 버젼을 제공하지는 않는다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">오늘 정리할 내용은 이중 바이너리 파일을 이용한 설치이다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">먼저 바이너리 파일을 이용한 설치의 경우 설치 & 삭제가 쉽다는 장점이 있다.</span>

* 압축을 풀어서 놓은 디렉토리가 설치 위치가 된다.
* 해당 디렉토리를 삭제하면 끝!

<span class="colour" style="color: rgb(56, 56, 56);">하지만 다음과 같은 단점도 있다.</span>

* 의존성있는 라이브러리 설치 따로 진행 해야한다.

## 설치 환경

* OS : Ubuntu 16.04.3 LTS
* Mariadb Version : 10.2.12

## Mariadb Binary Download

<span class="colour" style="color: rgb(56, 56, 56);">Mariadb Binary Download URL : [https://downloads.mariadb.com/MariaDB/mariadb-10.2.12/bintar-linux-x86_64/](https://downloads.mariadb.com/MariaDB/mariadb-10.2.12/bintar-linux-x86_64/)</span>
<span class="colour" style="color: rgb(56, 56, 56);">**mariadb-10.2.12-linux-x86_64.tar.gz** 파일을 다운 받는다.</span>

## 설치 진행

<span class="colour" style="color: rgb(56, 56, 56);">[참고 자료 1](https://mariadb.com/kb/en/library/installing-mariadb-binary-tarballs/)를 참고 하여 설치를 진행 한다. **설치는 root 계정으로 한다.**</span>

```
groupadd mysql
useradd -g mysql mysql
cd /usr/local
tar -zxvpf /path-to/mariadb-VERSION-OS.tar.gz
ln -s mariadb-VERSION-OS mysql
cd mysql
./scripts/mysql_install_db --user=mysql
chown -R root .
chown -R mysql data

```

<span class="colour" style="color: rgb(56, 56, 56);">MariaDB 의존성 프로그램 설치</span>

```
sudo apt-get install libaio1 libaio-dev

```

## 설정

<span class="colour" style="color: rgb(56, 56, 56);">설치가 완료 되면 설정을 진행 한다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">설정을 하기 전에 my.cnf 파일을 정해야 한다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">mariadb 압축 해제된 디렉토리를 보면 support-files 디렉토리가 있다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">그 안을 보면 다음과 같은 설정 파일 들이 있다.</span>

* my-huge.cnf
* my-innodb-heavy-4G.cnf
* my-large.cnf
* my-medium.cnf
* my-small.cnf

<span class="colour" style="color: rgb(56, 56, 56);">사용하려는 환경에 맞는 cnf파일을 mysql 디렉토리로 my.cnf 이름으로 변경하여 복사한다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">이후 다음 명령어를 실행한다.</span>

```
./scripts/mysql_install_db --user=mysql --default-file=my.cnf

```

## 실행

<span class="colour" style="color: rgb(56, 56, 56);">설정이 완료 되면 이제 MariaDB를 실행 할 수 있다.</span>
<span class="colour" style="color: rgb(56, 56, 56);">실행 명령어</span>

```
./bin/mysqld_safe --datadir='./data' &

```

<span class="colour" style="color: rgb(56, 56, 56);">실행 이후, 가장 먼저 root의 비밀번호를 설정한다.</span>

```
 ./bin/mysqladmin -u root password 'New Password'

```

## 접속 테스트

<span class="colour" style="color: rgb(56, 56, 56);">접속 테스트 명령은 다음과 같다.</span>

```
 ./bin/mysql/ -uroot -p

```

##
[http://www.petertribble.co.uk/Solaris/node.html](http://www.petertribble.co.uk/Solaris/node.html)

Node
<span class="colour" style="color: rgb(0, 0, 0);">For convenience I've made available Solaris 10 packages (x86 only) of [node.js](http://nodejs.org/). Building it isn't overly taxing, but getting some of the prerequisites (such as a convenient compiler) may be a little tedious.</span>
<span class="colour" style="color: rgb(0, 0, 0);">For those interested, [build notes are here](http://www.petertribble.co.uk/Solaris/node-build.html).</span>
<span class="colour" style="color: rgb(0, 0, 0);">You may find SunOS binary tarballs available on [nodejs.org](http://nodejs.org/). They are for OpenSolaris/Illumos derivatives (SmartOS, specifically). If you're on Solaris 10 (or possibly 11), then unfortunately those binaries won't work for you but you can use the ones packaged below. If you're running OpenIndiana or Tribblix, then use the versions in their package repositories.</span>
<span class="colour" style="color: rgb(0, 0, 0);">Note that these builds are provided as-is. They have seen minimal testing, although `npm install` is known to work.</span>
<span class="colour" style="color: rgb(0, 0, 0);">**Node doesn't work on SPARC, these packages are for x86 systems.**</span>
<span class="colour" style="color: rgb(0, 0, 0);">**Note:** Recent builds are available as 32-bit or 64-bit packages. You only need one of the packages. All older packages were 32-bit only.</span>

## Node v6 LTS:

<span class="colour" style="color: rgb(0, 0, 0);">[32-bit DOWNLOAD](http://www.petertribble.co.uk/Solaris/TRIBnode-32bit-6.14.3.0.pkg.bz2) \| [64-bit DOWNLOAD](http://www.petertribble.co.uk/Solaris/TRIBnode-64bit-6.14.3.0.pkg.bz2)</span>
<span class="colour" style="color: rgb(0, 0, 0);">This includes [Node](http://nodejs.org/) 6.14.3, [cURL](http://curl.haxx.se/) 7.42.1. It's built with [GCC 4.8.5](http://gcc.gnu.org/) and includes the two shared libraries necessary for it to run. Uncompress the file with bunzip2, then the following as root:</span>

```
pkgadd -d TRIBnode-32bit-6.14.3.0.pkg TRIBnode

```

<span class="colour" style="color: rgb(0, 0, 0);">or</span>

```
pkgadd -d TRIBnode-64bit-6.14.3.0.pkg TRIBnode

```

<span class="colour" style="color: rgb(0, 0, 0);">and it will be installed into /opt/Node, and you just need to add /opt/Node/bin into your PATH.</span>
<span class="colour" style="color: rgb(0, 0, 0);">To upgrade from one version to the next, use `pkgrm TRIBnode` to remove the old version and install the new version as above.</span>
