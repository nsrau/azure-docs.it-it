---
title: Configurare MySQL su una macchina virtuale Linux in Azure| Documentazione Microsoft
description: Informazioni su come installare lo stack MySQL in una macchina virtuale Linux (Ubuntu o famiglia RedHat OS) in Azure.
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
ms.openlocfilehash: 0ee70bda954cf0a193d43b5b47702e7b2c37844d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-mysql-on-azure"></a>Come installare MySQL in Azure
In questo articolo verrà illustrato come installare e configurare MySQL in una macchina virtuale Azure che esegue Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Installare MySQL nella macchina virtuale.
> [!NOTE]
> Per poter completare questa esercitazione, è necessario disporre già di una macchina virtuale di Microsoft Azure che esegue Linux. Prima di continuare, vedere l’[esercitazione relativa alle macchine virtuali Linux di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per creare e impostare una macchina virtuale Linux con `mysqlnode` come nome della macchina virtuale e `azureuser` come utente.
> 
> 

[In questo caso, usare la porta 3306 come porta di MySQL].  

Connettersi tramite putty alla macchina virtuale Linux creata. Se è la prima volta che si usa una macchina virtuale Linux di Azure, vedere come usare putty per connettersi a una macchina virtuale Linux facendo clic [qui](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

In questo articolo, come esempio, per installare MySQL5.6 verrà utilizzato il pacchetto repository. In realtà, MySQL5.6 presenta maggiori miglioramenti in termini di prestazioni rispetto a MySQL5.5.  Ulteriori informazioni sono disponibili [qui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Come installare MySQL5.6 in Ubuntu
Di seguito si utilizzerà una VM Linux con Ubuntu da Azure.

* Passaggio 1: Installare lo switch MySQL Server 5.6 per l’utente `root`:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Installare mysql-server 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Durante l'installazione, verrà visualizzata una finestra di dialogo pop up in cui viene chiesto di impostare la password radice di MySQL riportata di seguito ed è necessario impostare la password.
  
    ![immagine](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Immettere la password per confermare.

    ![immagine](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Passaggio 2: Accedere al server MySQL
  
    Al termine dell'installazione del server MySQL, il servizio MySQL verrà avviato automaticamente. È possibile accedere al server MySQL con l’utente `root` .
    Utilizzare il seguente comando per accedere e immettere la password.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Passaggio 3: Gestire il servizio MySQL in esecuzione
  
    (a) Ottenere lo stato del servizio MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) Avviare il servizio MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) Interrompere il servizio MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) Riavviare il servizio MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Come installare MySQL nella famiglia di sistemi operativi Redhat, come CentOS oppure Oracle Linux
In questo caso si utilizzerà la VM Linux con CentOS oppure Oracle Linux.

* Passaggio 1: Aggiungere lo switch del repository Yum MySQL all’utente `root`:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Scaricare e installare il pacchetto di rilascio di MySQL:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Passaggio 2: Modificare il file per abilitare il repository MySQL per il download del pacchetto MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Aggiornare ogni valore del file come indicato di seguito:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Passaggio 3: Installare MySQL dal repository MySQL:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Il pacchetto RPM MySQL e tutti i pacchetti correlati verranno installati.
* Passaggio 4: Gestire il servizio MySQL in esecuzione
  
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


### <a name="how-to-install-mysql-on-suse-linux"></a>Come installare MySQL in Suse Linux
In questa circostanza si utilizzerà una VM Linux con OpenSUSE.

* Passaggio 1: Scaricare e installare MySQL Server
  
    Passare all’utente `root` mediante il comando seguente:  
  
           #sudo su -
  
    Scaricare e installare il pacchetto MySQL:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Passaggio 2: Gestire il servizio MySQL in esecuzione
  
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

### <a name="next-step"></a>Passaggio successivo
Ulteriori utilizzi e informazioni su MySQL sono disponibili [qui](https://www.mysql.com/).

