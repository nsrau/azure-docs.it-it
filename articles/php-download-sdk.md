<properties
	pageTitle="Scaricare Azure SDK per PHP"
	description="Informazioni su come scaricare e installare Azure SDK per PHP."
	documentationCenter="php"
	services=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="tomfitz"/>

#Scaricare Azure SDK per PHP

## Panoramica

Azure SDK per PHP include componenti che consentono di sviluppare, distribuire e gestire applicazioni PHP per Azure. In particolare, Azure SDK per PHP include i seguenti componenti:

* **Librerie client PHP per Azure**. Queste librerie di classi offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud.  
* **Interfaccia della riga di comando di Azure per Mac, Linux e Windows** Un insieme di comandi che permette la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali di Azure. L’interfaccia della riga di comando di Azure funziona su qualsiasi piattaforma, incluse le piattaforme Mac, Linux e Windows.
* **Azure PowerShell (solo Windows)**. Questo insieme di cmdlet di PowerShell consente la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali.
* **Emulatori di Azure (solo Windows)**. Gli emulatori di calcolo e archiviazione sono emulatori locali di servizi cloud e di gestione dati che consentono di testare un'applicazione in locale. Gli emulatori di Azure funzionano solo su Windows.

Nella sezione seguente viene indicato come eseguire il download e l'installazione dei componenti sopra descritti.

Le istruzioni fornite in questo argomento presuppongono che [PHP][install-php] sia installato nel computer in uso.

> [AZURE.NOTE]È necessario disporre di PHP 5.3 o versione successiva per usare le librerie client PHP per Azure.

##Librerie client PHP per Azure

Le librerie client PHP per Azure offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud, da qualsiasi sistema operativo. Possono essere installate tramite Composer, gli strumenti di gestione dei pacchetti PEAR oppure manualmente.

Per informazioni su come utilizzare le librerie client PHP per Azure, vedere [Come utilizzare il servizio BLOB][blob-service], [Come utilizzare il servizio tabelle][table-service] e [Come utilizzare il servizio di accodamento][queue-service].

###Installazione tramite Composer

1. [Installare Git][install-git].


	> [AZURE.NOTE]In Windows sarà inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.

2. Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:

        {
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "require": {
                "pear-pear.php.net/mail_mime" : "*",
                "pear-pear.php.net/http_request2" : "*",
                "pear-pear.php.net/mail_mimedecode" : "*",
                "microsoft/windowsazure": "*"
            }
        }

3. Scaricare **[composer.phar][composer-phar]** nella radice del progetto.

4. Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

		php composer.phar install

###Installazione come pacchetto PEAR

Per installare le librerie client PHP per Azure come pacchetto PEAR, eseguire la procedura seguente:

1. [Installare PEAR][install-pear].
2. Configurare il canale PEAR di Azure:

		pear channel-discover pear.windowsazure.com
3. Installare il pacchetto PEAR:

		pear install pear.windowsazure.com/WindowsAzure-0.4.0

Al termine dell'installazione, è possibile fare riferimento alle librerie di classi dall'applicazione.

###Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, attenersi alla procedura seguente:

1. Scaricare un archivio ZIP contenente le librerie da [GitHub][php-sdk-github]. In alternativa, dopo il fork dell'archivio, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.

	> [AZURE.NOTE]Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) e [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Il modo consigliato per risolvere queste dipendenze è installare tali pacchetti utilizzando [PEAR package manager](http://pear.php.net/manual/en/installation.php).

2. Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione e fare riferimento alle classi dall'applicazione.

##Azure PowerShell ed emulatori di Azure

Azure PowerShell è un insieme di cmdlet di PowerShell per la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali. Gli emulatori di Azure sono emulatori di servizi cloud e di gestione dati che consentono di verificare un'applicazione in locale. Questi componenti sono supportati solo in Windows.

Il metodo consigliato per installare Azure PowerShell e gli emulatori di Azure è tramite l'[Installazione guidata piattaforma Web Microsoft][download-wpi]. Si noti che è inoltre possibile scegliere di installare altri componenti di sviluppo, come PHP, SQL Server, i driver Microsoft per SQL Server per PHP e WebMatrix.

Per informazioni sull'utilizzo di Azure PowerShell, vedere [Come utilizzare Azure PowerShell][powershell-tools].

##Interfaccia della riga di comando di Azure

Un’interfaccia della riga di comando di Azure è un insieme di comandi per la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali. Per informazioni sull'installazione di Azure CLI, vedere [Installare Azure CLI](xplat-cli-install.md).

## Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[pear-net]: http://pear.php.net/
[http-request2-package]: http://pear.php.net/package/HTTP_Request2
[mail-mimedecode-package]: http://pear.php.net/package/Mail_mimeDecode
[mail-mime-package]: http://pear.php.net/package/Mail_mime
[install-pear]: http://pear.php.net/manual/en/installation.getting.php
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=Oct15_HO3-->