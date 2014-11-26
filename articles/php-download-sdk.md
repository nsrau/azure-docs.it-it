<properties title="Download the Azure SDK for PHP" pageTitle="Download the Azure SDK for PHP" metaKeywords="" description="Learn how to download and install the Azure SDK for PHP." documentationCenter="PHP" services="" solutions="web" authors="robmcm" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Download di Azure SDK per PHP

Azure SDK per PHP include componenti che consentono di sviluppare, distribuire e gestire applicazioni PHP per Azure. In particolare, Azure SDK per PHP include i seguenti componenti:

-   **Librerie client PHP per Azure**. Queste librerie di classi offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud.
-   **Strumenti da riga di comando di Azure per Mac e Linux**. Questo insieme di strumenti da riga di comando permette la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali di Azure. Gli strumenti funzionano su qualsiasi piattaforma, incluse le piattaforme Mac, Linux e Windows.
-   **Azure PowerShell (solo Windows)**. Questo insieme di cmdlet di PowerShell consente la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali.
-   **Emulatori di Azure (solo Windows)**. Gli emulatori di calcolo e archiviazione sono emulatori locali di servizi cloud e di gestione dati che consentono di testare un'applicazione in locale. Gli emulatori di Azure funzionano solo su Windows.

Nella sezione seguente viene indicato come eseguire il download e l'installazione dei componenti sopra descritti.

Le istruzioni fornite in questo argomento presuppongono che [PHP][PHP] sia installato nel computer in uso.

> [WACOM.NOTE]
> È necessario disporre di PHP 5.3 o versione successiva per usare le librerie client PHP per Azure.

## Librerie client PHP per Azure

Le librerie client PHP per Azure offrono un'interfaccia per accedere alle funzionalità di Azure, ad esempio i servizi di gestione dati e i servizi cloud, da qualsiasi sistema operativo. Possono essere installate tramite Composer, gli strumenti di gestione dei pacchetti PEAR oppure manualmente.

Per informazioni su come usare le librerie client PHP per Azure, vedere [Come usare il servizio BLOB][Come usare il servizio BLOB], [Come usare il servizio tabelle][Come usare il servizio tabelle] e [Come usare il servizio di accodamento][Come usare il servizio di accodamento].

### Installazione tramite Composer

1.  [Installare Git][Installare Git].

    > [WACOM.NOTE]
    > In Windows sarà inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.

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

3.  Scaricare **[composer.phar][composer.phar]** nella radice del progetto.

4.  Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

        php composer.phar install

### Installazione come pacchetto PEAR

Per installare le librerie client PHP per Azure come pacchetto PEAR, eseguire la procedura seguente:

1.  [Installare PEAR][Installare PEAR].
2.  Configurare il canale PEAR di Azure:

        pear channel-discover pear.windowsazure.com

3.  Installare il pacchetto PEAR:

        pear install pear.windowsazure.com/WindowsAzure-0.3.1

Al termine dell'installazione, è possibile fare riferimento alle librerie di classi dall'applicazione.

### Installare manualmente

Per scaricare e installare manualmente le librerie client PHP per Azure, attenersi alla procedura seguente:

1.  Scaricare un archivio ZIP contenente le librerie da [GitHub][GitHub]. In alternativa, dopo il fork del repository, clonare quest'ultimo nel computer locale. Per quest'ultima opzione è necessario un account GitHub e un'installazione locale di Git.

    > [WACOM.NOTE]
    > Le librerie client PHP per Azure hanno dipendenze dai pacchetti PEAR [HTTP\_Request2][HTTP\_Request2], [Mail\_mime][Mail\_mime] e [Mail\_mimeDecode][Mail\_mimeDecode]. Il modo consigliato per risolvere queste dipendenze è installare tali pacchetti usando [PEAR package manager][PEAR package manager].

2.  Copiare la directory `WindowsAzure` dell'archivio scaricato nella struttura di directory dell'applicazione e fare riferimento alle classi dall'applicazione.

## Azure PowerShell ed emulatori di Azure

Azure PowerShell è un insieme di cmdlet di PowerShell per la distribuzione e la gestione di servizi di Azure, come servizi cloud e macchine virtuali. Gli emulatori di Azure sono emulatori di servizi cloud e di gestione dati che consentono di verificare un'applicazione in locale. Questi componenti sono supportati solo in Windows.

Il metodo consigliato per installare Azure PowerShell e gli emulatori di Azure è tramite l'[Installazione guidata piattaforma Web Microsoft][Installazione guidata piattaforma Web Microsoft]. Si noti che è inoltre possibile scegliere di installare altri componenti di sviluppo, come PHP, SQL Server, i driver Microsoft per SQL Server per PHP e WebMatrix.

Per informazioni sull'utilizzo di Azure PowerShell, vedere [Come usare Azure PowerShell][Come usare Azure PowerShell].

## Strumenti da riga di comando di Azure per Mac e Linux

Gli strumenti da riga di comando di Azure per Mac e Linux sono un insieme di strumenti da riga di comando per la distribuzione e la gestione di servizi di Azure, come Siti Web e Macchine virtuali di Azure. Nell'elenco seguente viene descritto come installare questi strumenti in base al sistema operativo in uso:

-   **Mac**: scaricare il programma di installazione di Azure SDK all'indirizzo: [][]<http://go.microsoft.com/fwlink/?LinkId=252249></a>. Aprire il file con estensione pkg scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

-   **Linux**: installare la versione più recente di [Node.js][Node.js] (vedere [Install Node.js via Package Manager][Install Node.js via Package Manager]), quindi eseguire il comando seguente:

        npm install azure-cli -g

    > [WACOM.NOTE]
    > Per l'esecuzione di questo comando potrebbero essere necessari privilegi elevati: `sudo npm install azure-cli -g`

Per informazioni sull'utilizzo degli strumenti da riga di comando di Azure per Mac e Linux, vedere [Come usare gli strumenti da riga di comando di Azure per Mac e Linux][Come usare gli strumenti da riga di comando di Azure per Mac e Linux].

  [PHP]: http://www.php.net/manual/en/install.php
  [Come usare il servizio BLOB]: http://go.microsoft.com/fwlink/?LinkId=252714
  [Come usare il servizio tabelle]: http://go.microsoft.com/fwlink/?LinkId=252715
  [Come usare il servizio di accodamento]: http://go.microsoft.com/fwlink/?LinkId=252716
  [Installare Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [Installare PEAR]: http://pear.php.net/manual/en/installation.getting.php
  [GitHub]: http://go.microsoft.com/fwlink/?LinkId=252719
  [HTTP\_Request2]: http://pear.php.net/package/HTTP_Request2
  [Mail\_mime]: http://pear.php.net/package/Mail_mime
  [Mail\_mimeDecode]: http://pear.php.net/package/Mail_mimeDecode
  [PEAR package manager]: http://pear.php.net/manual/en/installation.php
  [Installazione guidata piattaforma Web Microsoft]: http://go.microsoft.com/fwlink/?LinkId=253447
  [Come usare Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=252718
  []: http://go.microsoft.com/fwlink/?LinkId=252249
  [Node.js]: http://nodejs.org/
  [Install Node.js via Package Manager]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
  [Come usare gli strumenti da riga di comando di Azure per Mac e Linux]: http://go.microsoft.com/fwlink/?LinkId=252717
