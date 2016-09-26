<properties
   pageTitle="Trasmettere i dati da Analisi di flusso ad Archivio Data Lake | Azure"
   description="Usare Analisi di flusso di Azure per trasmettere i dati in Archivio Azure Data Lake"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>  

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>  

# Trasmettere i dati dal BLOB di archiviazione di Azure ad Archivio Data Lake usando Analisi di flusso di Azure

In questo articolo viene descritto come usare Archivio Azure Data Lake come output per un processo di Analisi di flusso di Azure. Questo articolo illustra uno scenario semplice in cui i dati vengono letti da un BLOB di Archiviazione di Azure (input) e scritti in Archivio Data Lake (output).

>[AZURE.NOTE] Attualmente la creazione e la configurazione di output di Archivio Data Lake per l'Analisi di flusso è supportata solo nel [portale di Azure classico](https://manage.windowsazure.com). Di conseguenza, alcune parti di questa esercitazione useranno il portale di Azure classico.

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).

- **Account di archiviazione di Azure**. Per l'input dei dati per un processo di Analisi di flusso viene usato un contenitore BLOB da questo account. Per questa esercitazione, si supponga di creare un account di archiviazione **datalakestoreasa** e un contenitore incluso nell'account denominato **datalakestoreasacontainer**. Dopo aver creato il contenitore, caricare un file di dati di esempio. È possibile ottenere un file di dati di esempio dal [repository Git di Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). È possibile usare vari tipi di client, ad esempio [Azure Storage Explorer](http://storageexplorer.com/), per caricare i dati in un contenitore BLOB.

	>[AZURE.NOTE] Se si crea l'account dal portale di Azure, assicurarsi che venga creato con il modello di distribuzione **classico**. Questo modello garantisce che l'account di archiviazione sia accessibile dal portale di Azure classico, ovvero il portale che verrà usato per creare un processo di Analisi di flusso. Per istruzioni su come creare un account di archiviazione dal portale di Azure usando la distribuzione classica, vedere [Creare un account di archiviazione di Azure](../storage/storage-create-storage-account/#create-a-storage-account).
	>
	> In alternativa, l'account di archiviazione può essere creato dal portale di Azure classico.

- **Account di Archivio Azure Data Lake**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md).


## Creare un processo di Analisi di flusso

Iniziare creando un processo di Analisi di flusso che include un'origine di input e una destinazione di output. Per questa esercitazione, l'origine è un contenitore BLOB di Azure e la destinazione è Archivio Data Lake.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).

2. Dalla sezione in basso a sinistra della schermata fare clic su **Nuovo**, **Servizi dati**, **Analisi di flusso**, **Creazione rapida**. Specificare i valori seguenti, quindi fare clic su **Crea processo di Analisi di flusso**.

	![Creare un processo di Analisi di flusso](./media/data-lake-store-stream-analytics/create.job.png "Creare un processo di Analisi di flusso.")

## Creare un input BLOB per il processo

1. Aprire la pagina per il processo di Analisi di flusso, fare clic sulla scheda **Input** e quindi fare clic su **Aggiungere un input** per avviare una procedura guidata.

2. Nella pagina **Aggiungere un input al processo** selezionare **Flusso dati**, quindi fare clic sulla freccia Avanti.

	![Aggiungere un input al processo](./media/data-lake-store-stream-analytics/create.input.1.png "Aggiungere un input al processo")

3. Nella pagina **Aggiungere un flusso dati al processo** selezionare **Archivio BLOB**, quindi fare clic sulla freccia Avanti.

	![Aggiungere un flusso dei dati al processo](./media/data-lake-store-stream-analytics/create.input.2.png "Aggiungere un flusso dei dati al processo")

4. Nella pagina **Impostazioni archivio BLOB** specificare i dettagli per l'archivio BLOB usato come origine dati di input.

	![Specificare le impostazioni dell'archivio BLOB](./media/data-lake-store-stream-analytics/create.input.3.png "Specificare le impostazioni dell'archivio BLOB")

	* **Immettere un alias di input**. Si tratta di un nome univoco specificato per l'input del processo.
	* **Selezionare un account di archiviazione**. Verificare che l'account di archiviazione si trovi nella stessa area del processo di Analisi di flusso per non incorrere in costi aggiuntivi per lo spostamento dei dati tra le aree.
	* **Specificare un contenitore di archiviazione**. È possibile creare un nuovo contenitore o selezionarne uno esistente.

	Fare clic sulla freccia Avanti.

5. Nella pagina **Impostazioni di serializzazione** impostare il formato di serializzazione, ad esempio **CSV**, il delimitatore, ad esempio **tabulazione**, la codifica, ad esempio **UTF8**, quindi fare clic sul segno di spunta.

	![Specificare le impostazioni di serializzazione](./media/data-lake-store-stream-analytics/create.input.4.png "Specificare le impostazioni di serializzazione")

6. Dopo aver completato la procedura guidata, l'input BLOB verrà aggiunto nella scheda **Input** e nella colonna **Diagnosi** dovrebbe essere visualizzato **OK**. È anche possibile testare in modo esplicito la connessione all'input usando il pulsante **Test connessione** nella parte inferiore della schermata.

## Creare un output di Archivio Data Lake per il processo

1. Aprire la pagina per il processo di Analisi di flusso, fare clic sulla scheda **Output** e quindi fare clic su **Aggiungere un output** per avviare una procedura guidata.

2. Nella pagina **Aggiungere un output al processo** selezionare **Archivio Data Lake**, quindi fare clic sulla freccia Avanti.

	![Aggiungere un output al processo](./media/data-lake-store-stream-analytics/create.output.1.png "Aggiungere un output al processo")

3. Nella pagina **Autorizza connessione**, se è già stato creato un account Archivio Data Lake, fare clic su **Autorizza ora**. In caso contrario, fare clic su **Iscriversi adesso** per creare un nuovo account. Per questa esercitazione, si supponga di aver già creato un account Archivio Data Lake (come indicato nel prerequisito). L'autorizzazione viene eseguita automaticamente con le credenziali per l'accesso al portale di Azure classico.

	![Autorizzare Archivio Data Lake](./media/data-lake-store-stream-analytics/create.output.2.png "Autorizzare Archivio Data Lake")

4. Nella pagina delle **impostazioni di Archivio Data Lake** immettere le informazioni come illustrato nella schermata riportata di seguito.

	![Specificare le impostazioni di Archivio Data Lake](./media/data-lake-store-stream-analytics/create.output.3.png "Specificare le impostazioni di Archivio Data Lake")

	* **Immettere un alias di output**. Si tratta di un nome univoco specificato per l'output del processo.
	* **Specificare un account Archivio Data Lake**. Questo account dovrebbe essere già stato creato, come indicato nel prerequisito.
	* **Specificare uno schema prefisso percorso**. Questo schema è necessario per identificare i file di output che vengono scritti in Archivio Data Lake dal processo di Analisi di flusso. Poiché i titoli degli output scritti dal processo sono in formato GUID, l'inclusione di un prefisso consente di identificare l'output scritto. Per includere un indicatore di data e ora come parte del prefisso, verificare che `{date}/{time}` sia incluso nello schema prefisso. Se si include questo indicatore, i campi **Formato data** e **Formato ora** sono abilitati ed è possibile selezionare il formato desiderato.

	Fare clic sulla freccia Avanti.

5. Nella pagina **Impostazioni di serializzazione** impostare il formato di serializzazione, ad esempio **CSV**, il delimitatore, ad esempio **tabulazione**, la codifica, ad esempio **UTF8**, quindi fare clic sul segno di spunta.

	![Specificare il formato di output](./media/data-lake-store-stream-analytics/create.output.4.png "Specificare il formato di output")

6. Dopo aver completato la procedura guidata, l'output di Archivio Data Lake verrà aggiunto nella scheda **Output** e nella colonna **Diagnosi** dovrebbe essere visualizzato **OK**. È anche possibile testare in modo esplicito la connessione all'output usando il pulsante **Test connessione** nella parte inferiore della schermata.

## Eseguire il processo di Analisi di flusso

Per eseguire un processo di Analisi di flusso, è necessario eseguire una query dalla scheda Query. Per questa esercitazione, è possibile eseguire la query di esempio sostituendo i segnaposto con gli alias di input e output del processo, come illustrato nella schermata seguente.

![Esegui query](./media/data-lake-store-stream-analytics/run.query.png "Esegui query")

Fare clic su **Salva** nella parte inferiore della schermata e quindi fare clic su **Start**. Nella finestra di dialogo selezionare **Ora personalizzata**, quindi selezionare una data passata, ad esempio **1/1/2016**. Fare clic sul segno di spunta per avviare il processo. L'avvio del processo può richiedere un paio di minuti.

![Impostare l'ora del processo](./media/data-lake-store-stream-analytics/run.query.2.png "Impostare l'ora del processo")

Dopo l'avvio del processo, fare clic sulla scheda **Monitoraggio** per visualizzare la modalità di elaborazione dei dati.

![Monitorare il processo](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorare il processo")

Infine, è possibile usare il [portale di Azure](https://portal.azure.com) per aprire l'account di Archivio Data Lake e verificare se i dati sono stati scritti correttamente nell'account.

![Verificare l'output](./media/data-lake-store-stream-analytics/run.query.4.png "Verificare l'output")

Nel riquadro Data Explorer l'output viene scritto in una cartella come specificato nelle impostazioni di output dell'Archivio Data Lake (`streamanalytics/job/output/{date}/{time}`).

## Vedere anche

* [Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->