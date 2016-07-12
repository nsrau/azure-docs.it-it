<properties 	
	pageTitle="Azure Data Factory: esempi" 
	description="Fornisce informazioni dettagliate sugli esempi inclusi nel servizio Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/23/2016" 
	ms.author="spelluru"/>

# Azure Data Factory: esempi

## Esempi in GitHub
Il [repository Azure-DataFactory di GitHub](https://github.com/azure/azure-datafactory) include alcuni esempi che permettono di acquisire rapidamente familiarità con il servizio Azure Data Factory (o) modificare gli script e usarlo nella propria applicazione. La cartella Samples\\JSON include frammenti di codice JSON per scenari comuni.

| Esempio | Descrizione |
| :----- | :---------- | 
| [Procedura dettagliata di Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | Questo esempio fornisce una procedura dettagliata end-to-end per l'elaborazione dei file di log mediante Azure Data Factory allo scopo di trasformare i dati da file di log in informazioni. <br/><br/>In questa procedura dettagliata la pipeline di Data Factory raccoglie log di esempio, elabora e arricchisce i dati dei log con dati di riferimento e trasforma i dati per valutare l'efficacia di una campagna di marketing lanciata di recente. |
| [Esempi JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | Questo esempio fornisce esempi JSON relativi a scenari comuni. | 
| [Esempio relativo all'unità di download dei dati HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Questo esempio mostra il download di dati da un endpoint HTTP nell'archiviazione BLOB di Azure mediante un'attività .NET personalizzata. |
| [Esempio di attività .NET di passaggio tra AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Questo esempio consente di creare un'attività .NET personalizzata non vincolata alle versioni assembly usate dal servizio di avvio di Azure Data Factory (ad esempio WindowsAzure.Storage versione 4.3.0, Newtonsoft.Json versione 6.0.x e così via). |
| [Esecuzione di script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) | Questo esempio include l'attività personalizzata di Data Factory che può essere usata per richiamare RScript.exe. Questo esempio funziona soltanto con il cluster HDInsight dell'utente (non con quello su richiesta) in cui è già installato R. |
| [Richiamare processi Spark in cluster Hadoop di HDInsight](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | Questo esempio illustra come usare l'attività MapReduce per richiamare un programma Spark. Il programma Spark si limita a copiare i dati da un contenitore BLOB di Azure a un altro. |
| [Analisi Twitter mediante un'attività batch di Azure Machine Learning per l'assegnazione dei punteggi](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | Questo esempio illustra come usare AzureMLBatchScoringActivity per richiamare un modello di Azure Machine Learning che esegue analisi del sentiment Twitter, assegnazione dei punteggi, stime e così via. |
| [Analisi Twitter mediante un'attività personalizzata](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) | Questo esempio illustra come usare un'attività .NET personalizzata per richiamare un modello di Azure Machine Learning che esegue analisi del sentiment Twitter, assegnazione dei punteggi, stime e così via. |
| [Pipeline con parametri per Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | Questo esempio fornisce un codice C# end-to-end per la distribuzione di N pipeline per l'assegnazione dei punteggi e la ripetizione di ciascuno con un parametro di area differente. L'elenco delle aree proviene da un file parameters.txt incluso nell'esempio. | 
| [Aggiornamento dei dati di riferimento per i processi di Analisi di flusso di Azure](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) | Questo esempio illustra come sfruttare insieme le potenzialità di Azure Data Factory e Analisi di flusso di Azure per eseguire le query con dati di riferimento e configurare l'aggiornamento dei dati di riferimento in base a una pianificazione. |
| [Pipeline ibrida con Hortonworks Hadoop locale](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | Questo esempio usa un cluster Hadoop locale come computer di destinazione per l'esecuzione di processi in Data Factory in modo analogo all'aggiunta di altre destinazioni di calcolo quali un cluster Hadoop basato su HDInsight nel cloud. |
| [Strumento di conversione JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | Questo strumento consente di convertire documenti JSON da una versione precedente a 2015-07-01-preview (impostazione predefinita) o a una versione successiva. |  
| [File di input di esempio U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) | Si tratta di un file di esempio usato da un'attività di U-SQL. | 



## Esempi nel portale di Azure
È possibile usare il riquadro **Pipeline di esempio** nella home page della data factory per distribuire le pipeline di esempio e le entità associate (set di dati e servizi collegati) nella data factory.

1. Creare una nuova data factory o aprire una data factory esistente. Per informazioni su come creare una data factory, vedere [Introduzione a Data factory di Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory).
2. Nel pannello **DATA FACTORY** per la data factory, fare clic sul riquadro **Pipeline di esempio**.

	![Riquadro Pipeline di esempio](./media/data-factory-samples/SamplePipelinesTile.png)

2. Nel pannello **Pipeline di esempio**, fare clic sull'**esempio** da distribuire.
	
	![Pannello Pipeline di esempio](./media/data-factory-samples/SampleTile.png)

3. Specificare le impostazioni di configurazione per l'esempio, ad esempio il nome dell'account di archiviazione di Azure e la chiave dell'account, il nome del server di Azure SQL, il database, l'ID utente, la password e così via.

	![Pannello Esempio](./media/data-factory-samples/SampleBlade.png)

4. Dopo avere specificato le impostazioni di configurazione, fare clic su **Crea** per creare/distribuire le pipeline di esempio e i servizi collegati o le tabelle usati dalle pipeline.
5. Lo stato della distribuzione sarà visualizzato sul riquadro dell'esempio selezionato in precedenza nel pannello **Pipeline di esempio**.

	![Stato della distribuzione](./media/data-factory-samples/DeploymentStatus.png)

6. Quando viene visualizzato il messaggio **Distribuzione riuscita** nel riquadro relativo all'esempio, chiudere il pannello **Pipeline di esempio**.
5. Nel pannello **DATA FACTORY** si potrà notare che i servizi collegati, i set di dati e le pipeline vengono aggiunti alla data factory.

	![Pannello Data factory](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## Esempi in Visual Studio

### Prerequisiti

È necessario disporre dei seguenti prodotti installati nel computer in uso:

- Visual Studio 2013 o Visual Studio 2015
- Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](https://azure.microsoft.com/downloads/) e fare clic su **VS 2013** o **VS 2015** nella sezione **.NET**.
- Scaricare il plug-in Data Factory di Azure più recente per Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [2015 VS](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Se si usa Visual Studio 2013, è anche possibile aggiornare il plug-in nel modo seguente: nel menu fare clic su **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Strumenti di Microsoft Azure Data Factory per Visual Studio** -> **Aggiorna**.

### Usare Modelli di Data Factory

1. Fare clic su **File** nel menu, scegliere **New** (Nuovo) e quindi fare clic su **Project** (Progetto).
2. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura:
	1. Selezionare **DataFactory** in **Templates** (Modelli).
	2. Selezionare **Data Factory Templates** (Modelli di Data Factory) nel riquadro di destra.
	3. Immettere un **nome** per il progetto.
	4. Selezionare una **località** per il progetto.
	5. Fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Nella finestra di dialogo **Data Factory Templates** (Modelli di Data Factory) selezionare il modello di esempio dalla sezione **Use-Case Templates** (Modelli di casi d'uso) e fare clic su **Avanti**. I passaggi seguenti forniscono istruzioni dettagliate per l'uso del modello **Customer Profiling** (Profiling cliente). I passaggi sono simili ad altri esempi.

	![Finestra di dialogo Modelli di Data Factory](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
7. Nella finestra di dialogo **Data Factory Configuration** (Configurazione di Data Factory) fare clic su **Next** (Avanti) nella pagina **Data Factory Basics** (Informazioni di base di Data Factory).
8. Nella pagina **Configure data factory** (Configura data factory) seguire questa procedura:
	1. Selezionare **Create New Data Factory** (Crea nuova data factory) ai fini di questa procedura dettagliata. In alternativa, selezionare **Use existing data factory** (Usa data factory esistente).
	2. Immettere un **nome** per la data factory.
	3. Selezionare la **sottoscrizione di Azure** in cui creare la data factory.
	4. Selezionare il **gruppo di risorse** per la data factory.
	5. Selezionare **West US** (Stati Uniti occidentali), **East US** (Stati Uniti orientali) o **North Europe** (Europa settentrionale) per l'**area**.
	6. Fare clic su **Next** (Avanti).
9. Nella pagina **Configura archivi dati** specificare un **database SQL di Azure** e un **account di archiviazione di Azure** esistenti oppure crearne di nuovi, quindi scegliere Avanti.
10. Nella pagina **Configure data stores** (Configura calcolo) selezionare le impostazioni predefinite e fare clic su **Next** (Avanti).
11. Nella pagina **Summary** (Riepilogo) esaminare tutte le impostazioni e fare clic su **Next** (Avanti).
12. Nella pagina **Deployment Status** (Stato distribuzione) attendere finché non viene completata la distribuzione e fare clic su **Finish** (Fine).
13. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**.
19. Se viene visualizzato **Accedere al proprio account Microsoft** nella finestra di dialogo immettere le credenziali per l'account che dispone di sottoscrizione di Azure e fare clic su **accedi**.
20. Verrà visualizzata la finestra di dialogo seguente:

	![Finestra di dialogo Pubblica](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. Nella pagina **Configure data factory** (Configura data factory) procedere come segue:
	1. Confermare l'opzione **Use existing data factory** (Usa data factory esistente).
	2. Selezionare la stessa **data factory** del modello precedente.
	6. Fare clic su **Next** (Avanti) per passare alla pagina **Publish Items** (Pubblica elementi). Premere **TAB** per uscire dal campo Name (Nome) se il pulsante **Next** (Avanti) è disabilitato.
23. Nella pagina **Publish Items** (Pubblica elementi) assicurarsi che tutte le data factory siano selezionate e fare clic su **Next** (Avanti) per passare alla pagina **Summary** (Riepilogo).
24. Esaminare il riepilogo e fare clic su **Next** (Avanti) per avviare il processo di distribuzione e visualizzare **Deployment Status** (Stato della distribuzione).
25. Nella pagina **Deployment Status** (Stato della distribuzione), è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Finish (Fine) dopo il termine della distribuzione.

Per informazioni dettagliate sull'uso di Visual Studio per creare entità di Data Factory e pubblicarle in Azure, vedere [Creare la prima data factory di Azure con Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=AcomDC_0629_2016-->