<properties 
	pageTitle="Monitorare e gestire Data factory di Azure con il portale di anteprima di Azure" 
	description="Informazioni su come usare il portale di gestione di Azure per monitorare e gestire le istanze di Data factory di Azure create." 
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
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Monitorare Data factory di Azure con il portale di anteprima di Azure
In questo articolo vengono descritti vari scenari per l'utilizzo del portale Azure Preview per monitorare e gestire la Factory di dati di Azure.

## <a name="AllDataFactories"></a> Visualizzare tutte le factory di dati in una sottoscrizione di Azure

- Accesso a per il [portale Azure Preview][azure-preview-portal].
- Fare clic su **Sfoglia** hub a sinistra e fare clic su **factory dati**.  

	![Sfoglia hub -> factory di dati][image-data-factory-browse-datafactories]

	Se non viene visualizzato **factory dati**, fare clic su **tutto** e quindi fare clic su **dati factorries** nel **Sfoglia** blade.

	![Sfoglia hub -> tutto][image-data-factory-browse-everything]

- Tutte le factory di dati in dovrebbe essere visualizzato il **factory dati** blade.

	![Blade factory di dati][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Visualizzare i dettagli relativi a una factory di dati

Per visualizzare i dettagli relativi a un'istanza di Data factory, eseguire una delle operazioni seguenti:


- Fare clic su una factory di dati nel **factory dati** blade illustrato in precedenza.
- Fare clic sul collegamento per la factory di dati sul **Startboard**. **Startboard** è blade vedere all'accesso al portale di anteprima di Azure. Se è stata selezionata **aggiungere alla schermata iniziale** durante la creazione di una factory di dati (opzione predefinita), si noterà che la factory di dati per collegare nello Startboard come illustrato nell'immagine seguente. In questo esempio, **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** dati factory collegamenti sono disponibili nel **Startboard**.


![Factory di dati dalla schermata iniziale][image-data-factory-datafactory-from-startboard]

In entrambi i casi, verrà visualizzato il **dati FACTORY** blade per la factory di dati selezionato, come illustrato nella figura seguente.

 ![Dati Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Visualizzazione Diagramma della factory di dati
Nel **dati FACTORY** blade per la factory di dati, fare clic su **diagramma** tessera per visualizzare la vista diagramma della factory di dati.

![Vista diagramma Factory di dati][image-data-factory-diagram-view]
 
### Aprire una pipeline nella vista diagramma
È possibile visualizzare tutte le attività in una pipeline facendo la pipeline nella vista diagramma, **Apri Pipeline**. Si noterà che le attività nella pipeline e DataSet di input e output per le attività. ![Aprire pipeline](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Fare clic su **Data factory** in tale breadcrumb nell'angolo superiore sinistro per tornare alla visualizzazione Diagramma. Visualizza tutte le pipeline. In questo esempio è stata creata solo una singola pipeline.

## <a name="DataFactoryLinkedServices"></a> Visualizzare i servizi collegati in una factory di dati
Nel **dati FACTORY** blade per la factory di dati, fare clic su **servizi collegati** tessera per visualizzare tutti i servizi collegati in un elenco.

![Blade servizi collegati][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Visualizzare i dettagli relativi a un servizio collegato
Nel **servizi collegati** blade, scegliere il servizio collegato dall'elenco per visualizzare i dettagli su di esso.

![Blade collegato del servizio][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Visualizza set di dati in una factory di dati 
Nel **dati FACTORY** blade per la factory di dati, fare clic su **set di dati** tessera per visualizzare tutte le tabelle nell'ambiente di produzione di dati.

![Blade di set di dati][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Visualizzare i dettagli relativi a un set di dati
Fare clic sul set di dati nell'elenco dei set di dati del pannello SET DI DATI per visualizzarne i dettagli. Una tabella è un set di dati rettangolare che contiene uno schema. È l'unico tipo di set di dati supportato a questo punto.

![Tabella Blade][image-data-factory-table]

Nel **tabella** blade sopra entrambi **aggiornato di recente sezioni** e **Impossibile recentemente sezioni** gli elenchi vengono ordinati in base la **ora ultimo aggiornamento**. I tempi di aggiornamento di una sezione viene modificato nelle situazioni seguenti.

-  È aggiornare lo stato della sezione manualmente, ad esempio, utilizzando il **Set AzureDataFactorySliceStatus** (o) facendo clic su **eseguire** sul **sezione** blade della sezione.
-  La sezione Cambia stato a causa di un'esecuzione (ad esempio, una sequenza di avvio, un'esecuzione terminata e non è riuscita, un'esecuzione terminata e ha avuto esito positivo, ecc).
 
	
Per visualizzare le sezioni di dati ordinati in base alle ore di inizio e fine sezione invece, fare clic su **sezioni di dati (per ora slice)** riquadro.
 
![Sezioni di dati dalla sezione tempo][DataSlicesBySliceTime]

Fare clic sul titolo degli elenchi o **... (puntini di sospensione)** Per visualizzare l'elenco di intervalli di dimensioni maggiore.

![Tutte le sezioni di una tabella][image-data-factory-all-slices]

Nel **sezioni di dati** blade, fare clic sui **filtro** pulsante per visualizzare il **filtro** blade che consente di **filtro** sezioni per vedere le sezioni specifiche che si desidera esaminare. Verrà visualizzato il blade simile al seguente quando si fa clic su **filtro** sul **sezioni di dati** blade con sezioni **ordinati in base all'ora di aggiornamento**.

![Filtro Blade][image-data-factory-filter-blade]

Il **filtro** blade consente di filtrare in base a **ora dell'ultimo aggiornamento** e **sezionare stato**. Nella tabella seguente vengono descritti tutti gli stati di sezione e la relativa descrizione.
 
Stato di sezione | Descrizione
------------ | ------------
PendingExecution | L'elaborazione dati non è stata ancora avviata.
InProgress | L'elaborazione dati è in corso.
Ready | L'elaborazione dati è stata completata e la sezione di dati è pronta.
Failed | L'esecuzione che genera la sezione non è riuscita.
Skip | L'elaborazione delle sezione è stata ignorata.
Retry | Nuovo tentativo dell'esecuzione che genera la sezione.
Timed Out | Timeout dell'elaborazione dati della sezione.
PendingValidation | La sezione di dati è in attesa di convalida in base ai criteri di convalida prima di poter essere elaborata.
RetryValidation | Nuovo tentativo in corso la convalida della sezione.
FailedValidation | La convalida della sezione non è riuscita.
LongRetry | Una sezione è in questo stato se LongRetry è specificato nel JSON della tabella e i normali tentativi per la sezione non sono riusciti.
ValidationInProgress | La convalida della sezione (basata sui criteri definiti nel JSON della tabella) è in esecuzione.

Quando si fa clic **filtro** sul **sezioni di dati** blade con sezioni **il tempo di intervallo vengono ordinati**, verrà visualizzato un tipo diverso di **filtro** blade.

![Filtro Blade 2][image-data-factory-filter-blade-2]


Quando si avvia il **filtro** blade, il **a** campo viene impostato automaticamente l'ora più recente (arrotondato) per limitare il numero di record restituiti. Il **da** viene impostato automaticamente anche. È possibile modificare il **da** data selezionando il **calendario** pulsante. Il **a** data verrà modificata automaticamente quando si modifica il **da** Data.

È possibile fare clic **precedente**/ * * Avanti * * pulsanti da visualizzare sezioni nella precedente periodo/periodo successivo. L'intervallo di tempo per **precedente** e **Avanti** pulsanti sono impostato in base alla frequenza dell'intervallo e intervallo, come illustrato nella tabella seguente.

Frequenza | Intervallo di valori di intervallo | Blocco di tempo risultante
----------| -------------------- | --------------------
Minuto | 1-4 | 6 ore
Minuto | 5-29 | 1 giorno
Minuto | 30-180 | 7 giorni
Minuto | 180 + | 28 giorni (approssimativo. mese)
Ora | 1-3 | 7 giorni
Ora | 4-11 | 28 giorni (approssimativo. mese)
Ora | 12-72 | 182 giorni (approssimativi. 6 mesi)
Ora | 73 + | 1 anno
Giorno | 1-6 | 1 anno
Giorno | 7-20 | 5 anni
Giorno | 21 + | 10 anni
Settimana | 1-3 | 5 anni
Settimana | 4 + | 10 anni
Mese | qualsiasi | 10 anni
 
Ad esempio, se si definisce **frequenza** come **ora** e **intervallo** di **2**, scegliendo la **Avanti**/ * * precedente * * i pulsanti Sposta l'intervallo di tempo **7 giorni** in entrambe le direzioni. Questa logica si applica al panello Filtro quando si visualizzano tutte le sezioni/le sezioni recenti/le sezioni problematiche.




## <a name="DataFactorySlice"></a> Visualizzare i dettagli relativi a una sezione
Fare clic su una sezione nell'elenco di intervalli sia sul **tabella** blade o **sezioni di dati** blade per visualizzare i dettagli su tale sezione.

![Sezione di dati][image-data-factory-dataslice]

Se non è inclusa nella sezione il **Pronto** stato, è possibile visualizzare le sezioni a monte che non si è pronti e bloccano l'intervallo corrente da eseguito nel **sezioni a monte che non sono pronte** elenco.

### <a name="DataFactoryActivtyRuns"></a> Visualizza che tutte le attività viene eseguita per una sezione
Una sezione può contenere più esecuzioni. Ad esempio, se una sezione non riesce, il servizio può riprovare alcune volte. È anche possibile rieseguire una sezione contenente tutti i tentativi non riusciti. È possibile visualizzare tutte le attività viene eseguito sul * * blade dati sezione * * nell'elenco nella parte inferiore.

## <a name="DataFactoryActivtyRunDetails"></a> Visualizzare i dettagli relativi a un'attività in esecuzione
Fare clic su un'attività dall'elenco di esecuzioni di eseguirvi la **sezione di dati** blade per visualizzare i dettagli sull'attività in esecuzione.

![Dettagli di esecuzione di attività][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Operazioni lente - eventi nell'ultima settimana
Nel **dati FACTORY** blade (o home page) per la factory di dati, fare clic su **eventi durante la settimana scorsa** in **operazioni** obiettivo per visualizzare gli eventi nell'ultima settimana. In questo modo è possibile avere una visualizzazione generale delle operazioni eseguite dall'istanza di Data factory nella settimana precedente. È anche possibile risolvere eventuali errori con lo spostamento o l'elaborazione dei dati.

![Eventi di Factory di dati][image-data-factory-events]


## Vedere anche

Articolo | Descrizione
------ | ---------------
[Monitoraggio e gestione di Azure dati Factory tramite PowerShell][monitor-manage-using-powershell] | In questo articolo viene descritto come monitorare una Factory di dati di Azure mediante i cmdlet PowerShell di Azure. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


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
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=GIT-SubDir--> 