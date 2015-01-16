<properties title="Monitor and manage Azure Data Factory using Azure Preview Portal" pageTitle="Monitorare e gestire Data factory di Azure con il portale di anteprima di Azure" description="Informazioni su come usare il portale di gestione di Azure per monitorare e gestire le istanze di Data factory di Azure create." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Monitorare Data factory di Azure con il portale di anteprima di Azure

- [Visualizzare tutte le istanze di Data factory in una sottoscrizione di Azure](#AllDataFactories)
- [Visualizzare i dettagli relativi a un'istanza di Data factory](#DataFactoryDetails)
- [Visualizzare la vista diagramma di un'istanza di Data factory](#DataFactoryDiagram)
- [Visualizzare i servizi collegati in un'istanza di Data factory](#DataFactoryLinkedServices)
- [Visualizzare i dettagli relativi a un servizio collegato](#DataFactoryLinkedService) 
- [Visualizzare set di dati in un'istanza di Data factory](#DataFactoryDatasets)
- [Visualizzare i dettagli relativi a un set di dati](#DataFactoryDataset)
- [Visualizzare i dettagli relativi a una sezione](#DataFactorySlice) 
- [Visualizzare tutte le esecuzioni di attività per una sezione](#DataFactoryActivtyRuns) 
- [Visualizzare i dettagli relativi a un'esecuzione attività](#DataFactoryActivtyRunDetails)
- [Sezione Operazioni - Eventi nella settimana precedente](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Visualizzare tutte le istanze di Data factory in una sottoscrizione di Azure

- Accedere al [portale di anteprima di Azure][azure-preview-portal].
- Fare clic sull'hub **SFOGLIA** a sinistra, quindi su **Istanze di Data factory**.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	Se **Istanze di Data factory** non viene visualizzato, fare clic su **Tutto**, quindi su **Istanze di Data factory** nel pannello **Sfoglia**.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- Verranno visualizzate tutte le istanze di Data factory presenti nel pannello **Istanze di Data factory**.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Visualizzare i dettagli relativi a un'istanza di Data factory

Per visualizzare i dettagli relativi a un'istanza di Data factory, eseguire una delle operazioni seguenti: 


- Fare clic su un'istanza di Data factory nel pannello **Istanze di Data factory** mostrato sopra.
- Fare clic sul collegamento dell'istanza di Data factory nella **Schermata iniziale**. La **Schermata iniziale** è il pannello visualizzato quando si accede al portale di anteprima di Azure. Se durante la creazione di un'istanza di Data factory è stata selezionata l'opzione **Aggiungi alla Schermata iniziale** (opzione predefinita), il collegamento dell'istanza di Data factory verrà visualizzato nella Schermata iniziale come mostrato nell'immagine seguente. In questo esempio sono disponibili i collegamenti delle istanze di Data factory **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** nella **Schermata iniziale**.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

In entrambi i casi verrà visualizzato il pannello **DATA FACTORY** per l'istanza di Data factory selezionata come mostrato nell'immagine seguente. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Visualizzare la vista diagramma dell'istanza di Data factory
Nel pannello **DATA FACTORY** per l'istanza di Data factory fare clic sul riquadro **Diagramma** per visualizzare la vista diagramma dell'istanza di Data factory. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Visualizzare i servizi collegati in un'istanza di Data factory
Nel pannello **DATA FACTORY** per l'istanza di Data factory fare clic sul riquadro **Servizi collegati** per visualizzare tutti i servizi collegati in un elenco. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Visualizzare i dettagli relativi a un servizio collegato
Nel pannello **SERVIZI COLLEGATI** fare clic sul servizio collegato nell'elenco per visualizzarne i dettagli. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Visualizzare set di dati in un'istanza di Data factory 
Nel pannello **DATA FACTORY** per l'istanza di Data factory fare clic sul riquadro **Set di dati** per visualizzare tutte le tabelle nell'istanza di Data factory.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  Visualizzare i dettagli relativi a un set di dati
Fare clic sul set di dati nell'elenco dei set di dati del pannello SET DI DATI per visualizzarne i dettagli. Una tabella è un set di dati rettangolare che contiene uno schema. È l'unico tipo di set di dati supportato a questo punto. 

![Table Blade][image-data-factory-table]

Nel pannello **TABELLA** mostrato sopra sono visibili gli elenchi **Sezioni recenti** e **Sezioni problematiche**. Fare clic su **... (puntini di sospensione)** per visualizzare tute le sezioni. 

![All Slices of a Table][image-data-factory-all-slices]

Nel pannello **Sezioni di dati** è possibile usare un filtro per visualizzare le sezioni specifiche da esaminare.


## <a name="DataFactorySlice"></a> Visualizzare i dettagli relativi a una sezione
Fare clic su una sezione nell'elenco di sezioni nel pannello **TABELLA** o nel pannello **Sezioni di dati** per visualizzarne i dettagli. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> Visualizzare tutte le esecuzioni di attività per una sezione
Una sezione può contenere più esecuzioni. Ad esempio, se una sezione non riesce, il servizio può riprovare alcune volte. È anche possibile rieseguire una sezione contenente tutti i tentativi non riusciti. È possibile visualizzare tutte le esecuzioni di attività nel pannello **Sezione di dati** nell'elenco in basso. 

## <a name="DataFactoryActivtyRunDetails"></a>  Visualizzare i dettagli relativi a un'esecuzione attività
Fare clic su un'esecuzione attività nell'elenco di esecuzioni del pannello **Sezione di dati** per visualizzarne i dettagli. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Sezione Operazioni - Eventi nella settimana precedente
Nel pannello **DATA FACTORY** (o nella home page) dell'istanza di Data factory fare clic su **Eventi nella settimana precedente** nella sezione **Operazioni** per visualizzare gli eventi della settimana precedente. In questo modo è possibile avere una visualizzazione generale delle operazioni eseguite dall'istanza di Data factory nella settimana precedente. È anche possibile risolvere eventuali errori con lo spostamento o l'elaborazione dei dati. 

![ Data Factory Events][image-data-factory-events]


## Vedere anche

Articolo | Descrizione
------ | ---------------
[Monitorare e gestire Data factory di Azure con PowerShell][monitor-manage-using-powershell] | Questo articolo descrive come monitorare Data factory di Azure con i cmdlet di Azure PowerShell. 
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure.
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 
[Informazioni di riferimento per i cmdlet di Data factory di Azure][cmdlet-reference] | Questo contenuto di riferimento include i dettagli su tutti i **cmdlet di Data factory**.


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
