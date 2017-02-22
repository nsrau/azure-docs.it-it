---
title: Trasmettere i dati da Analisi di flusso a Data Lake Store | Azure
description: Usare Analisi di flusso di Azure per trasmettere i dati in Archivio Azure Data Lake
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/05/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4ecf4f8594f7a274bec231fb74c4caa22c3cc354
ms.openlocfilehash: b5f2ae124ca3276e15e0d1f75d655ec346bf8ee8


---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Trasmettere i dati dal BLOB di archiviazione di Azure ad Archivio Data Lake usando Analisi di flusso di Azure
In questo articolo viene descritto come usare Archivio Azure Data Lake come output per un processo di Analisi di flusso di Azure. Questo articolo illustra uno scenario semplice in cui i dati vengono letti da un BLOB di Archiviazione di Azure (input) e scritti in Archivio Data Lake (output).

> [!NOTE]
> Attualmente la creazione e la configurazione di output di Archivio Data Lake per l'Analisi di flusso è supportata solo nel [portale di Azure classico](https://manage.windowsazure.com). Di conseguenza, alcune parti di questa esercitazione useranno il portale di Azure classico.
>
>

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account di archiviazione di Azure**. Per l'input dei dati per un processo di Analisi di flusso viene usato un contenitore BLOB da questo account. Per questa esercitazione, si supponga di disporre di un account di archiviazione **storageforasa** e di un contenitore incluso nell'account denominato **storageforasacontainer**. Dopo aver creato il contenitore, caricare un file di dati di esempio. 
  
* **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md). Si supponga di avere un account Data Lake denominato **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso
Iniziare creando un processo di Analisi di flusso che include un'origine di input e una destinazione di output. Per questa esercitazione, l'origine è un contenitore BLOB di Azure e la destinazione è Archivio Data Lake.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro sinistro, fare clic su **Processi di Analisi di flusso**, quindi fare clic su **Aggiungi**.

    ![Creare un processo di Analisi di flusso](./media/data-lake-store-stream-analytics/create.job.png "Creare un processo di Analisi di flusso")

    > [!NOTE]
    > Assicurarsi di creare il processo nella stessa area dell'account di archiviazione per non incorrere in costi aggiuntivi per lo spostamento dei dati tra le aree.
    >

## <a name="create-a-blob-input-for-the-job"></a>Creare un input BLOB per il processo

1. Aprire la pagina per il processo di Analisi di flusso, nel riquadro sinistro fare clic sulla scheda **Input** e quindi fare clic su **Aggiungi**.

    ![Aggiungere un input al processo](./media/data-lake-store-stream-analytics/create.input.1.png "Aggiungere un input al processo")

2. Nel pannello **Nuovo input** specificare i valori seguenti.

    ![Aggiungere un input al processo](./media/data-lake-store-stream-analytics/create.input.2.png "Aggiungere un input al processo")

    * In **Alias dell'input** inserire un nome univoco per l'input del processo.
    * Per **Tipo di origine** selezionare **Flusso dati**.
    * Per **Origine** selezionare **Archiviazione BLOB**.
    * Per **Sottoscrizione** selezionare **Usa l'archiviazione BLOB della sottoscrizione corrente**.
    * Per **Account di archiviazione** selezionare l'account di archiviazione creato come parte dei prerequisiti. 
    * Per **Contenitore** selezionare il contenitore creato nell'account di archiviazione selezionato.
    * In **Formato di serializzazione eventi** scegliere **CSV**.
    * Per **Delimitatore **selezionare **scheda**.
    * Per **Codifica** selezionare **UTF-8**.

    Fare clic su **Crea**. Il portale ora aggiunge l'input e verifica la connessione allo stesso.


## <a name="create-a-data-lake-store-output-for-the-job"></a>Creare un output di Archivio Data Lake per il processo

1. Aprire la pagina per il processo di Analisi di flusso, fare clic sulla scheda **Output** e quindi fare clic su **Aggiungi**.

    ![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.1.png "Aggiungere un output al processo")

2. Nel pannello **Nuovo output** specificare i valori seguenti.

    ![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.2.png "Aggiungere un output al processo")

    * In **Alias dell'output** inserire un nome univoco per l'output del processo. È un nome descrittivo usato nelle query per indirizzare l'output delle query ad Archivio Data Lake in uso.
    * Per **Sink** selezionare **Data Lkae Store**.
    * Verrà richiesto di autorizzare l'accesso all'account Data Lake Store. Fare clic su **Autorizza**.

3. Nel pannello **Nuovo output** continuare a specificare i valori seguenti.

    ![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.3.png "Aggiungere un output al processo")

    * Per **Nome account** selezionare l'account Data Lake Store già creato a cui si desidera inviare l'output del processo.
    * In **Schema prefisso percorso** immettere un percorso di file usato per scrivere i file nell'account Data Lake Store specificato.
    * Per **Formato data**, se nel percorso di prefisso viene usato un token di data, è possibile selezionare il formato della data in cui sono organizzati i file.
    * Per **Formato ora**, se nel percorso di prefisso viene usato un token di ora, specificare il formato dell'ora in cui sono organizzati i file.
    * In **Formato di serializzazione eventi** scegliere **CSV**.
    * Per **Delimitatore **selezionare **scheda**.
    * Per **Codifica** selezionare **UTF-8**.
    
    Fare clic su **Crea**. Il portale ora aggiunge l'output e verifica la connessione allo stesso.
    
## <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

1. Per eseguire un processo di Analisi di flusso, è necessario eseguire una query dalla scheda **Query**. Per questa esercitazione, è possibile eseguire la query di esempio sostituendo i segnaposto con gli alias di input e output del processo, come illustrato nella schermata seguente.

    ![Eseguire query](./media/data-lake-store-stream-analytics/run.query.png "Eseguire query")

2. Fare clic su **Salva** nella parte superiore dello schermo, quindi sulla scheda **Panoramica** e su **Avvia**. Nella finestra di dialogo selezionare **Ora personalizzata**, quindi selezionare la data e l'ora correnti.

    ![Impostare l'ora del processo](./media/data-lake-store-stream-analytics/run.query.2.png "Impostare l'ora del processo")

    Fare clic su **Avvia** per avviare il processo. L'avvio del processo può richiedere un paio di minuti.

3. Per attivare la raccolta dei dati dal BLOB da parte del processo, copiare un file dati campione nel contenitore BLOB. È possibile ottenere un file di dati di esempio dal [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Per questa esercitazione verrà copiato il file **vehicle1_09142014.csv**. È possibile usare vari tipi di client, ad esempio [Azure Storage Explorer](http://storageexplorer.com/), per caricare i dati in un contenitore BLOB.

4. Nella scheda **Panoramica**, in **Monitoraggio** è possibile visualizzare come sono stati elaborati i dati.

    ![Monitorare il processo](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorare il processo")

5. Infine, è possibile verificare la disponibilità dei dati di output del processo nell'account Data Lake Store. 

    ![Verificare l'output](./media/data-lake-store-stream-analytics/run.query.4.png "Verificare l'output")

    Nel riquadro Esplora dati l'output viene scritto in un percorso di cartella come specificato nelle impostazioni di output di Data Lake Store (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Vedere anche
* [Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jan17_HO1-->


