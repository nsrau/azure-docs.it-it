---
title: Introduzione ad Azure Data Lake Analytics con il portale di Azure | Documentazione Microsoft
description: 'Informazioni su come usare il portale di Azure per creare un account Data Lake Analytics, creare un processo di Data Lake Analytics con U-SQL e inviare il processo. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 64c5869f3e66c249fefa9af228fe1b33974cf293
ms.lasthandoff: 04/25/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Esercitazione: Introduzione ad Azure Data Lake Analytics con il portale di Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare il portale di Azure per creare account Azure Data Lake Analytics, definire processi in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi al servizio Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

In questa esercitazione si svilupperà un processo che legge un file di valori separati da tabulazioni (TSV) e lo converte in un file di valori separati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede disponibili nella parte superiore di questa sezione. Dopo il completamento del primo processo, è possibile iniziare a scrivere trasformazioni di dati più complesse con U-SQL.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione sono necessari gli elementi seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake
È necessario disporre di un account di Data Lake Analytics prima di poter eseguire qualsiasi processo.

Ogni account di Data Lake Analytics presenta una dipendenza sull'account di Azure Data Lake Store.  Questo account viene definito account di Data Lake Store predefinito.  È possibile creare l'account di Archivio Data Lake anticipatamente o quando si crea l'account di Analisi Data Lake. In questa esercitazione si creerà l'account di Archivio Data Lake contestualmente all'account di Analisi Data Lake.

**Creare un account di Data Lake Analytics**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo**, su **Intelligence e analisi** e quindi su **Data Lake Analytics**.
3. Digitare o selezionare i valori seguenti:

    ![Pannello del portale di Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

   * **Nome**: il nome dell'account di Data Lake Analytics deve contenere solo lettere minuscole e numeri.
   * **Sottoscrizione**: scegliere la sottoscrizione di Azure usata per l'account di Analytics.
   * **Gruppo di risorse**. Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo. Gestione risorse di Azure consente di lavorare con le risorse dell'applicazione come gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md).
   * **Località**. Selezionare un data center di Azure per l'account di Data Lake Analytics.
   * **Data Lake Store**: fare clic su *Configurare le impostazioni necessarie*. Seguire le istruzioni per creare un nuovo account di Data Lake Store o selezionarne uno esistente. Ogni account di Data Lake Analytics ha un account di Data Lake Store dipendente. L'account di Data Lake Analytics e l'account di Data Lake Store dipendente devono trovarsi nello stesso data center di Azure.
4. Selezionare il piano tariffario  
5. Fare clic su **Crea**. Verrà di nuovo visualizzata la schermata iniziale del portale in cui sarà presente un nuovo riquadro che indica "Distribuzione di Azure Data Lake Analytics". Il processo di distribuzione richiederà alcuni minuti per la creazione di un account Data Lake Analytics. Al termine del processo, il portale aprirà l'account in un nuovo pannello.

Dopo aver creato un account di Analisi Data Lake, è possibile aggiungere altri account di Archivio Data Lake e account di Archiviazione di Azure. Per istruzioni, vedere la sezione relativa alla [gestione delle origini dati degli account di Analisi Data Lake](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

## <a name="prepare-source-data"></a>Preparare i dati di origine
In questa esercitazione verranno elaborati log di ricerca.  Il log di ricerca può essere archiviato in Data Lake Store o in un archivio BLOB di Azure.

Il portale di Azure offre un'interfaccia utente per la copia di file di dati di esempio nell'account Data Lake Store predefinito, tra cui anche un file di log di ricerca.

**Copiare file di dati di esempio**

1. Aprire l'account Data Lake Analytics dal [portale di Azure](https://portal.azure.com).  Vedere [Gestire gli account Data Lake Analytics](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account) per creare un account e quindi aprirlo nel portale.
2. Espandere il riquadro **Informazioni di base** e quindi fare clic su **Esplora script di esempio**. Verrà visualizzato un altro pannello denominato **Script di esempio**.

    ![Script di esempio del portale di Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-sample-scripts.png)
3. Fare clic su **Mancano i dati di esempio** per copiare i file di dati di esempio. Al termine, il portale visualizzerà il messaggio **I dati di esempio sono stati aggiornati**.
4. Nella parte superiore del pannello dell'account di Analisi Data Lake fare clic su **Esplora dati** .

    ![Analisi Data Lake di Azure - Pulsante Esplora dati](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Vengono aperti due panelli: **Esplora dati** e un pannello relativo all'account Data Lake Store predefinito.
5. Nel pannello relativo all'account Data Lake Store predefinito fare clic su **Esempi** per espandere la cartella e quindi su **Dati** per espandere la cartella. Verranno visualizzati i file e le cartelle seguenti:

   * AmbulanceData/
   * AdsLog.tsv
   * SearchLog.tsv
   * version.txt
   * WebLog.log

     In questa esercitazione si userà SearchLog.tsv.

Si programmeranno in pratica le applicazioni in modo che i dati vengano caricati o scritti in account di archiviazione collegati. Per caricare i file, vedere [Upload data to Data Lake Store](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) (Caricare dati in Data Lake Store) o [Upload data to Blob storage](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb) (Caricare dati nell'archivio BLOB).

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Creare e inviare processi di Analisi Data Lake
Dopo aver preparato i dati di origine, è possibile iniziare a sviluppare uno script U-SQL.  

**Per inviare un processo**

1. Nel pannello del portale relativo all'account di Analisi Data Lake fare clic su **Nuovo processo**.

    ![Analisi Data Lake di Azure - Pulsante Nuovo processo](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Se il pannello non è presente, vedere [Aprire un account di Analisi Data Lake dal portale](data-lake-analytics-manage-use-portal.md#access-adla-account).
2. Immettere il **Nome processo**e lo script U-SQL seguente:

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

    ![creare processi U-SQL con Analisi Data Lake di Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

    Questo script U-SQL legge il file di dati di origine tramite **Extractors.Tsv()** e quindi crea un file CSV tramite **Outputters.Csv()**.

    Non modificare i due percorsi, a meno che il file di origine non sia stato copiato in una posizione diversa.  Data Lake Analytics creerà la cartella di output, se non esiste già.  In questo caso si useranno semplici percorsi relativi.  

    Per i file archiviati negli account predefiniti di Data Lake risulta più semplice usare percorsi relativi, ma è possibile usare anche percorsi assoluti.  Ad esempio

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md) e [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348) (Riferimenti al linguaggio U-SQL).

1. Fare clic su **Invia processo** nel menu in alto.   
2. Attendere finché lo stato del processo non viene modificato in **Riuscito**. È possibile vedere che l'esecuzione del processo ha richiesto circa un minuto.

    In caso di esito negativo del processo, vedere [Monitorare e risolvere i problemi dei processi di Data Lake Analytics](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
3. Nella parte inferiore del pannello fare clic sulla scheda **Output** e quindi su **SearchLog-from-Data-Lake.csv**. Sarà possibile visualizzare in anteprima, scaricare, rinominare ed eliminare il file di output.

    ![Analisi Data Lake di Azure - Proprietà del file di output del processo](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)

## <a name="see-also"></a>Vedere anche
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
* Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).
* Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
* Per registrare informazioni di diagnostica, vedere [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)

