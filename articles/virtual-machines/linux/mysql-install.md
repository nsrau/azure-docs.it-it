---
title: Configurare MySQL su una macchina virtuale Linux in Azure| Documentazione Microsoft
description: Informazioni su come installare lo stack MySQL in una macchina virtuale Linux (Ubuntu o famiglia Red Hat OS) in Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158431"
---
# <a name="how-to-install-mysql-on-azure"></a>Come installare MySQL in Azure
In questo articolo verrà illustrato come installare e configurare MySQL in una macchina virtuale Azure che esegue Linux.


> [!NOTE]
> Per poter completare questa esercitazione, è necessario disporre già di una macchina virtuale di Microsoft Azure che esegue Linux. Prima di continuare, vedere l’[esercitazione relativa alle macchine virtuali Linux di Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per creare e impostare una macchina virtuale Linux con `mysqlnode` come nome della macchina virtuale e `azureuser` come utente.
> 
> 

[In questo caso, usare la porta 3306 come porta di MySQL].  

In questo articolo, come esempio, per installare MySQL5.6 verrà utilizzato il pacchetto repository. In realtà, MySQL5.6 presenta maggiori miglioramenti in termini di prestazioni rispetto a MySQL5.5.  Ulteriori informazioni sono disponibili [qui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Installare MySQL5.6 in Ubuntu
Si userà una VM Linux che esegue Ubuntu.


### <a name="install-mysql"></a>Installare MySQL

Installare MySQL Server 5.6 passando al `root` utente:

```bash  
sudo su -
```

Installare mysql-server 5.6:

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Durante l'installazione, verrà visualizzata una finestra di dialogo per chiedere di impostare la password radice di MySQL riportata di seguito, ed è necessario impostare la password qui.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Immettere la password per confermare.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Accesso
  
Al termine dell'installazione del server MySQL, il servizio MySQL verrà avviato automaticamente. È possibile accedere al MySQL Server con il `root` utente, quindi immettere la password.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Gestire il servizio MySQL

Ottenere lo stato del servizio MySQL

```bash   
service mysql status
```
  
Avviare il servizio MySQL

```bash  
service mysql start
```
  
Arrestare il servizio MySQL

```bash  
service mysql stop
```
  
Riavviare il servizio MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Installare MySQL in sistemi operativi di Red Hat, CentOS, Oracle Linux
In questo caso si utilizzerà la VM Linux con CentOS oppure Oracle Linux.

### <a name="add-the-mysql-yum-repository"></a>Aggiungere il repository yum MySQL
    
Passare a `root` utente:

```bash  
sudo su -
```

Scaricare e installare il pacchetto di rilascio di MySQL:

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Abilitare il repository MySQL
Modificare il file per abilitare il repository MySQL per il download del pacchetto MySQL5.6.

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Aggiornare ogni valore del file come indicato di seguito:

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Installare MySQL 

Installare MySQL dal repository.

```bash  
yum install mysql-community-server
```
  
Il pacchetto RPM MySQL e tutti i pacchetti correlati verranno installati.


## <a name="manage-the-mysql-service"></a>Gestire il servizio MySQL
  
Controllare lo stato del servizio del server MySQL:

```bash  
service mysqld status\
```
  
Controllare se la porta predefinita del server MySQL è in esecuzione:

```bash  
netstat  –tunlp|grep 3306
```

Avviare il server MySQL:

```bash
service mysqld start
```

Arrestare il server MySQL:

```bash
service mysqld stop
```

Impostare MySQL da avviare quando l'avvio del sistema:

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Installare MySQL in SUSE Linux

In questa circostanza si utilizzerà una VM Linux con OpenSUSE.

### <a name="download-and-install-mysql-server"></a>Scaricare e installare il server MySQL
  
Passare all’utente `root` mediante il comando seguente:  

```bash  
sudo su -
```
  
Scaricare e installare il pacchetto MySQL:

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Gestire il servizio MySQL
  
Controllare lo stato del server MySQL:

```bash  
rcmysql status
```
  
Controllare se la porta predefinita del server MySQL:

```bash  
netstat  –tunlp|grep 3306
```

Avviare il server MySQL:

```bash
rcmysql start
```

Arrestare il server MySQL:

```bash
rcmysql stop
```

Impostare MySQL da avviare quando l'avvio del sistema:

```bash
insserv mysql
```

## <a name="next-step"></a>Passaggio successivo
Per altre informazioni, vedere la [MySQL](https://www.mysql.com/) sito Web.

