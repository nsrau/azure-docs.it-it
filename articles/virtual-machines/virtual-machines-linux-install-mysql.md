<properties
	pageTitle="Impostare MySQL su una macchina virtuale Linux | Microsoft Azure"
	description="Informazioni su come installare lo stack MySQL in una macchina virtuale Linux (Ubuntu o famiglia RedHat OS) in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="mingzhan"/>


#Come installare MySQL in Azure


In questo articolo verrà illustrato come installare e configurare MySQL in una macchina virtuale Azure che esegue Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##Installare MySQL nella macchina virtuale.

> [AZURE.NOTE]Per poter completare questa esercitazione, è necessario disporre già di una macchina virtuale di Microsoft Azure che esegue Linux. Prima di procedere, vedere l’[esercitazione relativa alle macchine virtuali Linux di Azure](virtual-machines-linux-tutorial.md) per creare e impostare una macchina virtuale Linux con `mysqlnode` come nome macchina virtuale e `azureuser` come utente.

[In questo caso, usare la porta 3306 come porta di MySQL].

Connettersi tramite putty alla macchina virtuale Linux creata. Se è la prima volta che si usa una macchina virtuale Linux di Azure, vedere come usare putty per connettersi a una macchina virtuale Linux facendo clic [qui](virtual-machines-linux-use-ssh-key.md).

In questo articolo, come esempio, per installare MySQL5.6 verrà utilizzato il pacchetto repository. In realtà, MySQL5.6 presenta maggiori miglioramenti in termini di prestazioni rispetto a MySQL5.5. Ulteriori informazioni sono disponibili [qui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###Come installare MySQL5.6 in Ubuntu
Di seguito si utilizzerà una VM Linux con Ubuntu da Azure.

- Passaggio 1: Installare lo switch MySQL Server 5.6 per l’utente `root`:

            #[azureuser@mysqlnode:~]sudo su -

    Installare mysql-server 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Durante l'installazione, verrà visualizzata una finestra di dialogo pop up in cui viene chiesto di impostare la password radice di MySQL riportata di seguito ed è necessario impostare la password.

    ![immagine](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p1.png)


    Immettere la password per confermare.

    ![immagine](./media/virtual-machines-linux-install-mysql/virtual-machines-linux-install-mysql-p2.png)

- Passaggio 2: Accedere al server MySQL

    Al termine dell'installazione del server MySQL, il servizio MySQL verrà avviato automaticamente. È possibile accedere al server MySQL con l’utente `root`. Utilizzare il seguente comando per accedere e immettere la password.

             #[root@mysqlnode ~]# mysql -uroot -p

- Passaggio 3: Gestire il servizio MySQL in esecuzione

    (a) Ottenere lo stato del servizio MySQL

             #[root@mysqlnode ~]# service mysql status

    (b) Avviare il servizio MySQL

             #[root@mysqlnode ~]# service mysql start

    (c) Interrompere il servizio MySQL

             #[root@mysqlnode ~]# service mysql stop

    (d) Riavviare il servizio MySQL

             #[root@mysqlnode ~]# service mysql restart


###Come installare MySQL nella famiglia di sistemi operativi Redhat, come CentOS oppure Oracle Linux
In questo caso si utilizzerà la VM Linux con CentOS oppure Oracle Linux.

- Passaggio 1: Aggiungere lo switch del repository Yum MySQL all’utente `root`:

            #[azureuser@mysqlnode:~]sudo su -

    Scaricare e installare il pacchetto di rilascio di MySQL:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Passaggio 2: Modificare il file per abilitare il repository MySQL per il download del pacchetto MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Aggiornare ogni valore del file come indicato di seguito:

        # *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Passaggio 3: Installare MySQL dal repository MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    Il pacchetto RPM MySQL e tutti i pacchetti correlati verranno installati.

- Passaggio 4: Gestire il servizio MySQL in esecuzione

    (a) Verificare lo stato di servizio del server MySQL:

           #[root@mysqlnode ~]#service mysqld status

    (b) Verificare se la porta predefinita del server MySQL è in esecuzione:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) Avviare il server MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) Interrompere il server MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) Impostare MySQL in modo che si avvii all’avvio del sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


###Come installare MySQL in Suse Linux
In questa circostanza si utilizzerà una VM Linux con OpenSUSE.

- Passaggio 1: Scaricare e installare MySQL Server

    Passare all’utente `root` mediante il comando seguente:

           #sudo su -

    Scaricare e installare il pacchetto MySQL:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Passaggio 2: Gestire il servizio MySQL in esecuzione

    (a) Verificare lo stato del server MySQL:

           #[root@mysqlnode ~]# rcmysql status

    (b) Verificare se la porta predefinita del server MySQL è in esecuzione:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) Avviare il server MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) Interrompere il server MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) Impostare MySQL in modo che si avvii all’avvio del sistema:

           #[root@mysqlnode ~]# insserv mysql

###Passaggio successivo
Ulteriori utilizzi e informazioni su MySQL sono disponibili [qui](https://www.mysql.com/).

<!---HONumber=Oct15_HO3-->