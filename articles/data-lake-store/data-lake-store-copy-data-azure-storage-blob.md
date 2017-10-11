---
title: Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake| Microsoft Docs
description: Usare lo strumento AdlCopy per copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ee8e282feacfb6b5db2f746b468fe2cd26cb0a52
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake
> [!div class="op_single_selector"]
> * [Con DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Con AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Store fornisce uno strumento da riga di comando, [AdlCopy](http://aka.ms/downloadadlcopy), per copiare dati dalle origini seguenti:

* Dal BLOB di Archiviazione di Azure a Data Lake Store. Non è possibile usare lo strumento AdlCopy per copiare dati da Archivio Data Lake ai BLOB di Archiviazione di Azure.
* Tra due account di Azure Data Lake Store.

È anche possibile usare lo strumento AdlCopy in due modi diversi:

* **Autonomo**, in cui lo strumento usa le risorse di Archivio Data Lake per eseguire l'attività.
* **Tramite un account di Analisi Data Lake**, in cui le unità assegnate all'account di Analisi Data Lake vengono usate per eseguire l'operazione di copia. È consigliabile scegliere questa opzione se si intende eseguire le attività di copia in modo prevedibile.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **BLOB di Archiviazione di Azure** con alcuni dati.
* **Un account di Archivio Data Lake di Azure**. Per istruzioni su come crearne uno, vedere [Introduzione ad Archivio Data Lake di Azure](data-lake-store-get-started-portal.md)
* **Un account di Azure Data Lake Analytics (facoltativo)**. Per istruzioni su come creare un account di Data Lake Store, vedere [Introduzione ad Azure Data Lake Analytics con il portale di Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
* **Lo strumento AdlCopy**. Installare lo strumento AdlCopy da [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintassi dello strumento AdlCopy
Usare la sintassi seguente per usare lo strumento AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern

I parametri nella sintassi sono descritti di seguito:

| Opzione | Descrizione |
| --- | --- |
| Sorgente |Specifica il percorso dei dati di origine nel BLOB di Archiviazione di Azure. L'origine può essere un contenitore BLOB, un BLOB o un altro account Data Lake Store. |
| Dest |Specifica la destinazione di Archivio Data Lake in cui eseguire la copia. |
| SourceKey |Specifica la chiave di accesso alle risorse di archiviazione per l'origine BLOB di Archiviazione di Azure. La chiave è necessaria solo se l'origine è un contenitore BLOB o un BLOB. |
| Account |**Facoltativo**. Scegliere questa opzione se si vuole usare l'account di Analisi Azure Data Lake per eseguire il processo di copia. Se si usa l'opzione /Account nella sintassi ma non si specifica un account di Analisi Data Lake, AdlCopy usa un account predefinito per eseguire il processo. Inoltre, se si sceglie questa opzione, è necessario aggiungere l'origine (BLOB di Archiviazione di Azure) e la destinazione (Archivio Azure Data Lake) come origini dati per l'account di Analisi Data Lake. |
| Unità |Specifica il numero di unità di Analisi Data Lake che verranno usate per il processo di copia. Questa opzione è obbligatoria se si usa l'opzione **/Account** per specificare l'account di Analisi Data Lake. |
| Modello |Specifica un modello regex che indica quali BLOB o file copiare. AdlCopy usa la corrispondenza tra maiuscole e minuscole. La copia di tutti gli elementi è il criterio predefinito usato quando non viene specificato alcun modello. Non è consentito specificare più criteri file. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Usare AdlCopy (come file autonomo) per copiare i dati da un BLOB di Archiviazione di Azure
1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.
2. Eseguire il comando seguente per copiare un BLOB specifico dal contenitore di origine a un'istanza di Archivio Data Lake:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    ad esempio:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE] La sintassi precedente specifica il file da copiare in una cartella dell'account Data Lake Store. Se il nome della cartella specificato non esiste, lo strumento AdlCopy crea una cartella.

    Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account Azure Data Lake Store. L'output visualizzato sarà simile al seguente:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. È inoltre possibile copiare tutti i BLOB da un contenitore all'account di Archivio Data Lake tramite il comando seguente:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    ad esempio:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Quando si esegue una copia da un account di Archiviazione BLOB di Azure, è possibile che alcune operazioni siano limitate nell'ambito dell'archiviazione BLOB. In questo caso le prestazioni del processo di copia diminuiranno. Per altre informazioni sui limiti di Archiviazione BLOB di Azure, vedere i limiti di Archiviazione di Azure in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Usare AdlCopy (come file autonomo) per copiare i dati da un altro account di Data Lake Store
È anche possibile usare AdlCopy per copiare dati tra due account Data Lake Store.

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.
2. Eseguire il comando seguente per copiare un file specifico da un account Data Lake Store a un altro.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    ad esempio:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > La sintassi precedente specifica il file da copiare in una cartella dell'account Data Lake Store di destinazione. Se il nome della cartella specificato non esiste, lo strumento AdlCopy crea una cartella.
   >
   >

    Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account Azure Data Lake Store. L'output visualizzato sarà simile al seguente:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Il comando seguente consente di copiare tutti i file da una cartella specifica dell'account Data Lake Store di origine in una cartella dell'account Data Lake Store di destinazione.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Quando si usa AdlCopy come strumento autonomo, la copia viene eseguita su risorse gestite di Azure condivise. Le prestazioni che è possibile ottenere in questo ambiente dipendono dal carico del sistema e dalle risorse disponibili. Questa modalità è più adatta ai trasferimenti di piccole dimensioni eseguiti ad hoc. Quando si usa AdlCopy come strumento autonomo, non è necessario impostare alcun parametro.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Usare AdlCopy (con un account Data Lake Analytics) per copiare i dati
È anche possibile usare l'account di Analisi Data Lake per eseguire il processo AdlCopy per copiare dati da BLOB di Archiviazione di Azure ad Archivio Data Lake. In genere si sceglie questa opzione quando i dati da spostare sono nell'ordine di gigabyte e terabyte e si vuole una velocità effettiva delle prestazioni migliore e prevedibile.

Per usare l'account Data Lake Analytics con AdlCopy per eseguire la copia da un BLOB di Archiviazione di Azure, l'origine (BLOB di Archiviazione di Azure) deve essere aggiunta come origine dati per l'account Data Lake Analytics. Per istruzioni sull'aggiunta di origini dati aggiuntive all'account di Data Lake Analytics, vedere [Gestire Analisi Data Lake tramite il portale di Azure](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se si esegue la copia da un account Azure Data Lake Store come origine tramite un account Data Lake Analytics, non è necessario associare l'account Data Lake Store all'account Data Lake Analytics. È necessario associare l'archivio di origine con l'account Data Lake Analytics solo quando l'origine è un account di Archiviazione di Azure.
>
>

Eseguire il comando seguente per copiare da un BLOB di Archiviazione di Azure a un account Data Lake Store tramite un account Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

ad esempio:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Analogamente, eseguire il comando seguente per copiare da un BLOB di Archiviazione di Azure a un account Data Lake Store tramite un account Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Quando si copiano dati nell'ordine di terabyte, l'uso di AdlCopy con il proprio account di Azure Data Lake Analytics consente di ottenere prestazioni migliori e più prevedibili. Il parametro da impostare è il numero di unità di Azure Data Lake Analytics da usare per il processo di copia. L'aumento del numero di unità si traduce in un aumento delle prestazioni del processo di copia. Per ogni file da copiare, è possibile usare al massimo un'unità. Se si specificano più unità rispetto al numero di file da copiare, le prestazioni non aumentano.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Usare AdlCopy per copiare i dati usando la corrispondenza dei modelli
Questa sezione descrive come usare AdlCopy per copiare i dati da un'origine (nell'esempio di seguito un BLOB di Archiviazione di Azure) a un account Data Lake Store di destinazione tramite la corrispondenza dei modelli. Ad esempio, è possibile usare la procedura seguente per copiare tutti i file con estensione csv dal BLOB di origine alla destinazione.

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.
2. Eseguire il comando seguente per copiare tutti i file con estensione *.csv da un BLOB specifico del contenitore di origine a Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    ad esempio:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fatturazione
* Se si usa lo strumento AdlCopy in modalità autonoma, verranno fatturati i costi di uscita per lo spostamento dei dati, se l'account di origine di Archiviazione di Azure non si trova nella stessa area di Archivio Data Lake.
* Se si usa lo strumento AdlCopy con l'account di Analisi Data Lake, verranno applicati i [Prezzi di Analisi Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) standard.

## <a name="considerations-for-using-adlcopy"></a>Considerazioni sull'uso di AdlCopy
* AdlCopy (per la versione 1.0.5) supporta la copia dei dati da origini che collettivamente contengono migliaia di file e cartelle. Tuttavia, in caso di problemi durante la copia di un set di dati grande, è possibile distribuire file e cartelle in sottocartelle diverse e usare il percorso per tali sottocartelle come origine.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerazioni sulle prestazioni per l'uso di AdlCopy

AdlCopy supporta la copia dei dati che contengono migliaia di file e cartelle. Se si verificano problemi con la copia di un set di dati di grandi dimensioni, è comunque possibile distribuire file e cartelle in sottocartelle più piccole. AdlCopy è stato creato per copie ad hoc. Se si tenta di copiare dati su base periodica, è consigliabile usare [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) che consente gestire completamente le operazioni di copia.

## <a name="release-notes"></a>Note sulla versione
* 1.0.13 - Se si copiano dati nello stesso account Azure Data Lake Store tramite più comandi adlcopy, non è più necessario immettere di nuovo le credenziali per ogni esecuzione. Adlcopy memorizza ora nella cache queste informazioni tra le diverse esecuzioni.

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

