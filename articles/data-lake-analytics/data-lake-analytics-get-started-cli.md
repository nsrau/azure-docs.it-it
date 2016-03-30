<properties 
   pageTitle="Introduzione ad Azure Data Lake Analytics con l’interfaccia della riga di comando di Azure | Microsoft Azure" 
   description="Informazioni su come usare l’interfaccia della riga di comando di Azure per creare un account di Archivio Data Lake, creare un processo di Data Lake Analytics mediante U-SQL e inviare il processo." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/10/2016"
   ms.author="jgao"/>

# Esercitazione: Introduzione ad Azure Data Lake Analytics con l’interfaccia della riga di comandi (CLI) di Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Informazioni su come usare l’interfaccia della riga di comando di Azure per creare account di Azure Data Lake Analytics, definire processi di Data Lake Analytics in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi ad account di Data Lake Analytics. Per altre informazioni su Analisi Data Lake, vedere [Panoramica di Analisi Data Lake di Azure](data-lake-analytics-overview.md).

In questa esercitazione si svilupperà un processo che legge un file di valori separati da tabulazioni (TSV) e lo converte in un file di valori separati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede nella parte superiore di questa sezione.

**Processo di Data Lake Analytics di base:**

![Analisi Data Lake di Azure - Diagramma di flusso del processo](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Creare un account di Analisi Data Lake.
2. Preparare i dati di origine. I processi di Analisi Data Lake possono leggere dati da account di Archivio Data Lake di Azure o da account di archiviazione BLOB di Azure.   
3. Sviluppare uno script U-SQL.
4. Inviare un processo (script U-SQL) all'account di Analisi Data Lake. Il processo legge i dati di origine, li elabora come indicato nello script U-SQL e quindi salva l'output in un account di Archivio Data Lake o in un account di archiviazione BLOB.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Interfaccia della riga di comando di Azure**. Vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).
	- Scaricare e installare gli [Strumenti di Azure CLI](https://github.com/MicrosoftBigData/AzureDataLake/releases) **pre-release** per completare questa demo.
- **Autenticazione**, utilizzando il comando seguente:

		azure login
	Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere l'argomento relativo alla [connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).
- **Passare alla modalità Gestione risorse di Azure**, usando il comando seguente:

		azure config mode arm
		
## Creare un account di Analisi Data Lake

È necessario disporre di un account di Data Lake Analytics prima di poter eseguire qualsiasi processo. Per creare un account di Data Lake Analytics, è necessario specificare quanto segue:

- **Gruppo di risorse di Azure**: è necessario creare un account di Data Lake Analytics all'interno di un gruppo di risorse di Azure. [Gestione risorse di Azure](../resource-group-overview.md) consente di lavorare con le risorse dell'applicazione come gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata.  

	Per elencare i gruppi di risorse nella sottoscrizione:
    
    	azure group list 
    
	Per creare un nuovo gruppo di risorse:

		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome dell'account di Data Lake Analytics**
- **Posizione**: uno dei data center di Azure che supporta Data Lake Analytics.
- **Account di Data Lake predefinito**: ogni account di Data Lake dispone di un account di Data Lake predefinito.

	Per elencare gli account Data Lake esistenti:
	
		azure datalake store account list

	Per creare un nuovo account di Data Lake:

		azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

	> [AZURE.NOTE] Il nome dell'account di Data Lake deve contenere solo lettere minuscole e numeri.



**Per creare un account di Analisi Data Lake**

		azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

		azure datalake analytics account list
		azure datalake analytics account show "<Data Lake Analytics Account Name>"			

![Account di visualizzazione di Data Lake Analytics](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Il nome dell'account di Data Lake Analytics deve contenere solo lettere minuscole e numeri.


## Caricare dati nell’Archivio Data Lake

In questa esercitazione si eseguirà l'elaborazione di alcuni log di ricerca. Il log di ricerca può essere archiviato in Archivio Data Lake o in un'archiviazione BLOB di Azure.

Il portale di Azure fornisce un’interfaccia utente per copiare alcuni file di dati di esempio nell'account di Data Lake predefinito, tra cui anche un file di log di ricerca. Vedere [Preparare i dati di origine](data-lake-analytics-get-started-portal.md#prepare-source-data) per caricare i dati nell'account Archivio Data Lake predefinito.

Per caricare i file utilizzando cli, utilizzare il comando seguente:

  	azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
  	azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics può inoltre accedere all'archivio BLOB di Azure. Per caricare i dati nell'archiviazione BLOB di Azure, vedere [Uso dell’interfaccia della riga di comando di Azure con Archiviazione di Azure](../storage/storage-azure-cli.md).

## Inviare processi di Data Lake Analytics

I processi di Data Lake Analtyics vengono scritti nel linguaggio U-SQL. Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Per creare uno script per il processo di Data Lake Analytics**

- Creare un file di testo contenente il seguente script U-SQL e salvare il file di testo nella workstation in uso:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Questo script U-SQL legge il file dei dati di origine mediante **Extractors.Tsv()** e quindi crea un file con estensione csv mediante **Outputters.Csv()**.
    
    Non modificare i due percorsi, a meno che il file di origine non sia stato copiato in una posizione diversa. Data Lake Analytics creerà la cartella di output, se non esiste già.
	
	Risulta più semplice usare i percorsi relativi dei file archiviati negli account predefiniti di Data Lake, ma è possibile usare anche percorsi assoluti. Ad esempio
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati. La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Non sono attualmente supportate autorizzazioni di accesso a contenitori pubblici o a contenitori BLOB di Azure con BLOB pubblici.

	
**Per inviare il processo**


	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Per elencare i processi, ottenere i dettagli dei processi e annullare i processi, è possibile utilizzare i comandi seguenti:

  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
  	azure datalake analytics job list "<Data Lake Analytics Account Name>"
	azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Dopo il completamento del processo, è possibile usare i cmdlet seguenti per visualizzare l'elenco di file e scaricare il file:
	
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
	azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
	azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## Vedere anche

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Analisi Data Lake di Azure](data-lake-analytics-u-sql-get-started.md).
- Per informazioni sulle attività di gestione, vedere [Gestire Analisi di Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0323_2016-->