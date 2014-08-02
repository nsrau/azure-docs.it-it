<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Installazione dello stack LAMP in una macchina virtuale Linux in Azure
======================================================================

Uno stack LAMP è costituito dai diversi elementi seguenti:

-   **L**inux: sistema operativo
-   **A**pache: server Web
-   **M**ySQL: server database
-   **P**HP: linguaggio di programmazione

Installazione su Ubuntu
-----------------------

È necessario che siano installati i pacchetti seguenti:

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`
-   `libapache2-mod-auth-mysql`
-   `libapache2-mod-php5`
-   `php5-xsl`
-   `php5-gd`
-   `php-pear`

È possibile eseguire queste installazioni mediante un singolo comando `apt-get install`:

    apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear

Installazione su CentOS
-----------------------

È necessario che siano installati i pacchetti seguenti:

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

È possibile eseguire queste installazioni mediante un singolo comando `yum install`:

    yum install httpd mysql mysql-server php-php-mysql

Configurazione
--------------

1.  Configurare **Apache**.

    1.  Sarà necessario riavviare il server Web Apache. Eseguire il comando seguente:

             sudo /etc/init.d/apache2 restart

    2.  Verificare che l'installazione sia in esecuzione. Inserire nel browser l'indirizzo seguente: <http://localhost>. Dovrebbe essere visualizzato "It works!".

2.  Configurare **MySQL**.
    1.  Configurare la password radice per MySQL eseguendo il comando seguente:

             mysqladmin -u root -p password yourpassword

    2.  Accedere alla console utilizzando `mysql` o uno dei client MySQL disponibili.

3.  Configurare **PHP**.

    1.  Abilitare il modulo PHP di Apache eseguendo il comando seguente:

             sudo a2enmod php5

    2.  Riavviare Apache eseguendo il comando seguente:

             sudo service apache2 restart

Ulteriori informazioni
----------------------

Sono disponibili molte risorse relative alla configurazione di uno stack LAMP su Ubuntu.

-   <https://help.ubuntu.com/community/ApacheMySQLPHP>
-   <http://fedorasolved.org/server-solutions/lamp-stack>

