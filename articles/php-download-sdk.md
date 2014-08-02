<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="" />

Download di Azure SDK per PHP
=============================

Azure SDK per PHP include componenti che consentono di sviluppare, distribuire e gestire applicazioni PHP per Azure. In particolare, Azure SDK per PHP include i seguenti componenti:

-   **Librerie client PHP per Azure**. Queste librerie di classi offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud.
-   **Strumenti da riga di comando di Azure per Mac e Linux** Questo insieme di strumenti da riga di comando consente la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali di Azure. Gli strumenti funzionano su qualsiasi piattaforma, incluse le piattaforme Mac, Linux e Windows.
-   **Azure PowerShell (solo Windows)**. Questo insieme di cmdlet di PowerShell consente la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali.
-   **Emulatori di Azure (solo Windows)**. Gli emulatori di calcolo e archiviazione sono emulatori locali di servizi cloud e di gestione dati che consentono di testare un'applicazione in locale. Gli emulatori di Azure funzionano solo su Windows.

Nella sezione seguente viene indicato come eseguire il download e l'installazione dei componenti sopra descritti.

Le istruzioni fornite in questo argomento presuppongono che [PHP](http://www.php.net/manual/en/install.php) sia installato nel computer in uso.

> [WACOM.NOTE] È necessario disporre di PHP 5.3 o versione successiva per utilizzare le librerie client PHP per Azure.

Librerie client PHP per Azure
-----------------------------

Le librerie client PHP per Azure offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud, da qualsiasi sistema operativo. Possono essere installate tramite Composer, gli strumenti di gestione dei pacchetti PEAR oppure manualmente.

Per informazioni su come utilizzare le librerie client PHP per Azure, vedere [Come utilizzare il servizio BLOB](http://go.microsoft.com/fwlink/?LinkId=252714), [Come utilizzare il servizio tabelle](http://go.microsoft.com/fwlink/?LinkId=252715) e [Come utilizzare il servizio di accodamento](http://go.microsoft.com/fwlink/?LinkId=252716).

### Installazione tramite Composer

1.  [Installare Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).

    > [WACOM.NOTE] In Windows sarà inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.

2.  Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:

         {
             "require": {
                 "microsoft/windowsazure": "*"
             },         
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "minimum-stability": "dev"
         }

3.  Scaricare **[composer.phar](http://getcomposer.org/composer.phar)** nella radice del progetto.

4.  Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

		php composer.phar install

### Installazione come pacchetto PEAR

Per installare le librerie client PHP per Azure come pacchetto PEAR, eseguire la procedura seguente:

1.  [Installare PEAR](http://pear.php.net/manual/en/installation.getting.php).
2.  Configurare il canale PEAR di Azure:

		pear channel-discover pear.windowsazure.com

3.  Installare il pacchetto PEAR:

		pear install pear.windowsazure.com/WindowsAzure-0.3.1

Al termine dell'installazione, è possibile fare riferimento alle librerie di classi dall'applicazione.

### Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, eseguire la procedura seguente:

1.  Scaricare un archivio ZIP contenente le librerie da [GitHub](http://go.microsoft.com/fwlink/?LinkId=252719). In alternativa, dopo il fork dell'archivio, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.

    > [WACOM.NOTE] Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR [HTTP\_Request2](http://pear.php.net/package/HTTP_Request2), [Mail\_mime](http://pear.php.net/package/Mail_mime) e [Mail\_mimeDecode](http://pear.php.net/package/Mail_mimeDecode). Il modo consigliato per risolvere queste dipendenze è installare tali pacchetti utilizzando [PEAR package manager](http://pear.php.net/manual/en/installation.php).

2.  Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione e fare riferimento alle classi dall'applicazione.

Azure PowerShell ed emulatori di Azure
--------------------------------------

Azure PowerShell è un insieme di cmdlet di PowerShell per la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali. Gli emulatori di Azure sono emulatori di servizi cloud e di gestione dati che consentono di verificare un'applicazione in locale. Questi componenti sono supportati solo in Windows.

Il metodo consigliato per installare Azure PowerShell e gli emulatori di Azure è tramite l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/?LinkId=253447). Si noti che è inoltre possibile scegliere di installare altri componenti di sviluppo, come PHP, SQL Server, i driver Microsoft per SQL Server per PHP e WebMatrix.

Per informazioni sull'utilizzo di Azure PowerShell, vedere [Come utilizzare Azure PowerShell](http://go.microsoft.com/fwlink/?LinkId=252718).

Strumenti da riga di comando di Azure per Mac e Linux
-----------------------------------------------------

Gli strumenti da riga di comando di Azure per Mac e Linux sono un insieme di strumenti da riga di comando per la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali di Azure. Nell'elenco seguente viene descritto come installare questi strumenti in base al sistema operativo in uso:

-   **Mac**: scaricare il programma di installazione di Azure SDK all'indirizzo: <http://go.microsoft.com/fwlink/?LinkId=252249>. Aprire il file con estensione pkg scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

-   **Linux**: installare la versione più recente di [Node.js](http://nodejs.org/) (vedere [Install Node.js via Package Manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)), quindi eseguire il comando seguente:

		npm install azure-cli -g

    > [WACOM.NOTE] Per l'esecuzione di questo comando potrebbero essere necessari privilegi elevati: `sudo npm install azure-cli -g`

Per informazioni sull'utilizzo degli strumenti da riga di comando di Azure per Mac e Linux, vedere [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux](http://go.microsoft.com/fwlink/?LinkId=252717).

