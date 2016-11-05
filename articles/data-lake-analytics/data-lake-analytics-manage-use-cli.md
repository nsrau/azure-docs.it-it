---
title: Gestire Azure Data Lake Analytics mediante l’interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come gestire gli account, le origini dati, i processi e gli utenti di Data Lake Analytics tramite l’interfaccia della riga di comando di Azure
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Gestire Azure Data Lake Analytics mediante l’interfaccia della riga di comando (CLI) di Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics tramite Azure. Per visualizzare l'argomento relativo alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Interfaccia della riga di comando di Azure**. Vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
  * Scaricare e installare gli [Strumenti di Azure CLI](https://github.com/MicrosoftBigData/AzureDataLake/releases) **pre-release** per completare questa demo.
* **Autenticazione**, utilizzando il comando seguente:
  
        azure login
    Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).
* **Passare alla modalità Gestione risorse di Azure**, usando il comando seguente:
  
        azure config mode arm

**Per elencare i comandi Data Lake Store e Data Lake Analytics :**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## Gestire account
Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. A differenza di Azure HDInsight, un account di Analisi non è soggetto ad alcun pagamento fino a quando il processo non è in esecuzione. Il pagamento, infatti, viene richiesto solo per la durata di esecuzione di un processo. Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

### Creare account
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### Aggiornare account
Il seguente comando aggiorna le proprietà di un account Data Lake Analytics esistente

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### Elencare gli account
Elencare gli account di Data Lake Analytics

    azure datalake analytics account list

Elencare gli account di Data Lake Analytics all'interno di un gruppo di risorse specifico

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Ottenere i dettagli di un account specifico di Data Lake Analytics

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### Eliminare gli account di Data Lake Analytics
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## Gestire le origini dati degli account
Analisi Data Lake supporta attualmente le seguenti origini dati:

* [Archivio Data Lake di Azure](../data-lake-store/data-lake-store-overview.md)
* [Archiviazione di Azure](../storage/storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di archiviazione di Azure Data Lake come account di archiviazione predefinito. L'account di archiviazione ADL predefinito viene usato per archiviare i metadati dei processi e i log di controllo dei processi. Dopo aver creato un account di Analytics, è possibile aggiungere altri account di archiviazione di Data Lake e/o account di archiviazione di Azure.

### Cercare l'account di archiviazione ADL predefinito
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

Il valore è elencato in properties:datalakeStoreAccount:name.

### Aggiungere altri account di archiviazione BLOB di Azure
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> Sono supportati solo nomi brevi di archiviazione BLOB. Non utilizzare FQDN, ad esempio "myblob.blob.core.windows.net".
> 
> 

### Aggiungere altri account di Data Lake Store
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] è un parametro facoltativo per indicare se il Data Lake da aggiungere è l'account Data Lake predefinito.

### Aggiornare l'origine dati esistente
Per impostare un account Archivio Data Lake esistente come impostazione predefinita:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

Per aggiornare una chiave di account di archiviazione BLOB esistente:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### Elencare le origini dati:
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Origine dati dell'elenco Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### Eliminare origini dati:
Per eliminare un account Archivio Data Lake:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Per eliminare un account di archiviazione BLOB:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## Gestire i processi
È necessario disporre di un account di Data Lake Analytics prima di poter creare un processo. Per altre informazioni, vedere [Gestire gli account di Analisi Data Lake](#manage-accounts).

### Elencare i processi
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Origine dati dell'elenco Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### Ottenere i dettagli dei processi
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### Inviare i processi
> [!NOTE]
> La priorità predefinita di un processo è 1000 e il livello predefinito di parallelismo per un processo è 1.
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### Annullare i processi
Utilizzare il comando list per cercare l'id del processo e quindi utilizzare cancel per annullare il processo.

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## Gestire il catalogo
Il catalogo di U-SQL viene usato per definire la struttura dei dati e del codice in modo da poterli condividere mediante U-SQL. Il catalogo consente di ottenere le migliori prestazioni possibili con i dati in Azure Data Lake. Per altre informazioni, vedere la pagina di [Usare il catalogo di U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### Elencare gli elementi del catalogo
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

I tipi includono database, schema, assembly, origine dati esterna, tabella, funzione con valori di tabella o statistiche tabelle.

### Creare il segreto del catalogo
    azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### Modificare il segreto del catalogo
      azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### Eliminare il segreto del catalogo
    azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## Utilizzare i gruppi ARM
Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../resource-group-overview.md).

Un servizio di Analisi Data Lake può includere i componenti seguenti:

* Account di Azure Data Lake Analytics
* Account di archiviazione predefinito obbligatorio di Azure Data Lake
* Account di archiviazione aggiuntivi di Azure Data Lake
* Account di archiviazione aggiuntivi di Azure

È possibile creare tutti questi componenti in un unico gruppo ARM per semplificarne la gestione.

![Analisi Data Lake di Azure - Account e archiviazione](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di Analisi Data Lake e gli account di archiviazione dipendenti devono trovarsi nello stesso data center di Azure, mentre il gruppo ARM può trovarsi anche in un data center diverso.

## Vedere anche
* [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Gestire Analisi Data Lake di Azure tramite il portale di Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0914_2016-->