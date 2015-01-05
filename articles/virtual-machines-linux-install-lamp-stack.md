<properties urlDisplayName="Install LAMP stack" pageTitle="Installare lo stack LAMP in una macchina virtuale Linux" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/18/2014" ms.author="szark" />



#Installare lo stack LAMP in una macchina virtuale Linux in Azure

Uno stack LAMP è costituito dai diversi elementi seguenti:

- **L**inux: sistema operativo
- **A**pache: server Web
- **M**ySQL: server database
- **P**HP: linguaggio di programmazione


##Installazione su Ubuntu

È necessario che siano installati i pacchetti seguenti:

- `apache2`
- `mysql-server`
- `php5`
- `php5-mysql`

Dopo aver eseguito `apt-get update` per aggiornare l'elenco locale di pacchetti, è possibile installare tali pacchetti con un singolo comando `apt-get install`:

	# sudo apt-get update
	# sudo apt-get install apache2 mysql-server php5 php5-mysql

Dopo l'esecuzione del comando riportato sopra verrà chiesto di installare questi pacchetti e altre dipendenze.  Premere y e quindi INVIO per continuare e seguire eventuali altri comandi per impostare una password amministrativa per MySQL.

Verranno così installate le estensioni PHP minime obbligatorie per l'uso di PHP con MySQL. Eseguire il comando seguente per visualizzare altre estensioni PHP disponibili come pacchetti:

	# apt-cache search php5


##Installazione su CentOS e Oracle Linux

È necessario che siano installati i pacchetti seguenti:

- `httpd`
- `mysql`
- `mysql-server`
- `php`
- `php-mysql`

È possibile installare questi pacchetti con un singolo comando `yum install`:

	# sudo yum install httpd mysql mysql-server php php-mysql

Dopo l'esecuzione del comando riportato sopra verrà chiesto di installare questi pacchetti e altre dipendenze.  Premere y e quindi INVIO per continuare.

Verranno così installate le estensioni PHP minime obbligatorie per l'uso di PHP con MySQL. Eseguire il comando seguente per visualizzare altre estensioni PHP disponibili come pacchetti:

	# yum search php


## Installazione su SUSE Linux Enterprise Server

È necessario che siano installati i pacchetti seguenti:

- apache2
- mysql
- apache2-mod_php53
- php53-mysql

È possibile installare questi pacchetti con un singolo comando `zypper install`:

	# sudo zypper install apache2 mysql apache2-mod_php53 php53-mysql

Dopo l'esecuzione del comando riportato sopra verrà chiesto di installare questi pacchetti e altre dipendenze.  Premere y e quindi INVIO per continuare.

Verranno così installate le estensioni PHP minime obbligatorie per l'uso di PHP con MySQL. Eseguire il comando seguente per visualizzare altre estensioni PHP disponibili come pacchetti:

	# zypper search php


Configurazione
----------

1. Configurare **Apache**

	- Avviare il comando seguente per verificare se il server Web Apache è stato avviato:

		- Ubuntu e SLES: `sudo service apache2 restart`

		- CentOS e Oracle: `sudo service httpd restart`

	- Per impostazione predefinita, Apache è in attesa sulla porta 80. Per accedere al server Apache in remoto, può essere necessario aprire un endpoint.  Per istruzioni più dettagliate, vedere la documentazione relativa alla [configurazione degli endpoint](http://azure.microsoft.com/it-it/documentation/articles/virtual-machines-set-up-endpoints/).

	- A questo punto, è possibile verificare se Apache è ora in esecuzione e se rende disponibili contenuti. Puntare il browser a `http://[MYSERVICE].cloudapp.net`, dove **[MYSERVICE]** è il nome del servizio cloud in cui si trovano le macchine virtuali. In alcune distribuzioni è possibile che si riceva un messaggio da una pagina Web predefinita con la conferma del funzionamento del server. In altre è possibile che venga visualizzata una pagina Web più complessa con collegamenti a pagine di documentazione e contenuti aggiuntivi per la configurazione del server Apache.

2. Configurare **MySQL**

	- Si noti che in Ubuntu questo passaggio non è necessario, in quanto la richiesta di una password `root` di MySQL viene visualizzata al momento dell'installazione del pacchetto mysql-server.

	- Nelle altre distribuzioni, impostare la password radice per MySQL eseguendo il comando seguente:

			# mysqladmin -u root -p password yourpassword

	- È quindi possibile gestire MySQL usando le utilità `mysql` o `mysqladmin`.


##Ulteriori informazioni

Sono disponibili molte risorse relative alla configurazione di uno stack LAMP su Ubuntu.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

<!--HONumber=35.1-->
