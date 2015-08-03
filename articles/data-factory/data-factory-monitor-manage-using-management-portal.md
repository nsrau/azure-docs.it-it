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
Questo articolo illustra diversi scenari per l'uso del portale di anteprima di Azure per monitorare e gestire Data factory di Azure.

## <a name="AllDataFactories"></a> Visualizzare tutte le data factory in una sottoscrizione di Azure

- Accedere al [portale di anteprima di Azure][azure-preview-portal].
- Fare clic sull'hub **SFOGLIA** a sinistra, quindi su **Data factory**.  

	![Hub SFOGLIA -> Data factory][image-data-factory-browse-datafactories]

	Se **Data factory** non viene visualizzato, fare clic su **Tutto** e quindi su **Data factory** nel pannello **Sfoglia**.

	![Hub SFOGLIA -> Tutto][image-data-factory-browse-everything]

- Dovrebbero essere visualizzate tutte le data factory presenti nel pannello **Data factory**.

	![Pannello Data factory][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Visualizzare i dettagli relativi a una data factory

Per visualizzare i dettagli relativi a un'istanza di Data factory, eseguire una delle operazioni seguenti:


- Fare clic su una data factory nel pannello **Data factory** illustrato sopra.
- Fare clic sul collegamento per la data factory nella **Schermata iniziale**. La **Schermata iniziale** è il pannello visualizzato quando si accede al portale di anteprima di Azure. Se durante la creazione di una data factory è stata selezionata l'opzione **Aggiungi alla Schermata iniziale** (opzione predefinita), il collegamento della data factory dovrebbe essere visualizzato nella Schermata iniziale, come mostrato nell'immagine seguente. In questo esempio sono disponibili i collegamenti delle data factory **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** nella **Schermata iniziale**.


![Data factory dalla Schermata iniziale][image-data-factory-datafactory-from-startboard]

In entrambi i casi verrà visualizzato il pannello **DATA FACTORY** per la data factory selezionata, come mostrato nell'immagine seguente.

 ![Home page di Data factory][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Visualizzare la vista diagramma della data factory
Nel pannello **DATA FACTORY** per la data factory fare clic sul riquadro **Diagramma** per visualizzare la vista diagramma della data factory.

![Vista diagramma di Data factory][image-data-factory-diagram-view]
 
### Aprire una pipeline nella vista diagramma
È possibile visualizzare tutte le attività di una pipeline facendo clic con il pulsante destro del mouse sulla pipeline nella vista diagramma e scegliendo **Apri pipeline**. Dovrebbero essere visualizzate le attività della pipeline, oltre ai set di dati di input e output per le attività. ![Apri pipeline](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Fare clic su **Data factory** sulla barra di navigazione nell'angolo superiore sinistro per tornare alla vista diagramma in cui sono visualizzate tutte le pipeline. In questo esempio è stata creata una sola pipeline.

## <a name="DataFactoryLinkedServices"></a> Visualizzare i servizi collegati in una data factory
Nel pannello **DATA FACTORY** per la data factory fare clic sul riquadro **Servizi collegati** per visualizzare tutti i servizi collegati in un elenco.

![Pannello Servizi collegati][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Visualizzare i dettagli relativi a un servizio collegato
Nel pannello **SERVIZI COLLEGATI** fare clic sul servizio collegato nell'elenco per visualizzarne i dettagli.

![Pannello Servizio collegato][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Visualizzare set di dati in una data factory 
Nel pannello **DATA FACTORY** per la data factory fare clic sul riquadro **Set di dati** per visualizzare tutte le tabelle nella data factory.

![Pannello Set di dati][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Visualizzare i dettagli relativi a un set di dati
Fare clic sul set di dati nell'elenco dei set di dati del pannello SET DI DATI per visualizzarne i dettagli. Una tabella è un set di dati rettangolare che contiene uno schema. È l'unico tipo di set di dati supportato a questo punto.

![Pannello Tabella][image-data-factory-table]

Nel pannello **TABELLA** precedente gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ordinati in base a **ORA ULTIMO AGGIORNAMENTO**. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.

-  Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
-  Lo stato della sezione cambia a causa di un'esecuzione, ad esempio un'esecuzione avviata, un'esecuzione terminata con errore, un'esecuzione terminata correttamente e così via.
 
	
Per visualizzare invece le sezioni di dati ordinate in base alle ore di inizio/fine, fare clic sul riquadro **Sezioni dati (in base all'ora della sezione)**.
 
![Sezioni dati in base all'ora della sezione][DataSlicesBySliceTime]

Fare clic sul titolo degli elenchi oppure su **... (puntini di sospensione)** per visualizzare un elenco di sezioni più ampio.

![Tutte le sezioni di una tabella][image-data-factory-all-slices]

Nel pannello **Sezioni dati** fare clic sul pulsante **Filtro** per visualizzare il pannello **Filtro** che consente di **filtrare** le sezioni e visualizzare quelle da esaminare. Quando si fa clic su **Filtro** nel pannello **Sezioni dati** con sezioni **ordinate in base all'ora di aggiornamento**, verrà visualizzato un pannello simile al seguente.

![Pannello Filtro][image-data-factory-filter-blade]

Il pannello **Filtro** permette di applicare filtri in base ai valori di **Ora ultimo aggiornamento** e **Stato sezione**. La tabella seguente illustra tutti gli stati delle sezioni e le rispettive descrizioni.
 
Stato sezione | Descrizione
------------ | ------------
PendingExecution | L'elaborazione dati non è stata ancora avviata.
InProgress | L'elaborazione dati è in corso.
Ready | L'elaborazione dati è stata completata e la sezione di dati è pronta.
Failed | L'esecuzione che genera la sezione non è riuscita.
Skip | L'elaborazione delle sezione è stata ignorata.
Retry | Nuovo tentativo dell'esecuzione che genera la sezione.
Timed Out | Timeout dell'elaborazione dati della sezione.
PendingValidation | La sezione di dati è in attesa di convalida in base ai criteri di convalida prima di poter essere elaborata.
RetryValidation | È in corso un nuovo tentativo di convalida della sezione.
FailedValidation | La convalida della sezione non è riuscita.
LongRetry | Una sezione è in questo stato se LongRetry è specificato nel JSON della tabella e i normali tentativi per la sezione non sono riusciti.
ValidationInProgress | La convalida della sezione (basata sui criteri definiti nel JSON della tabella) è in esecuzione.

Quando si fa clic su **Filtro** nel pannello **Sezioni dati** con sezioni **ordinate in base all'ora della sezione**, verrà visualizzato un pannello **Filtro** di tipo diverso.

![Pannello Filtro 2][image-data-factory-filter-blade-2]


Quando si apre il pannello **Filtro**, il campo **A** viene impostato automaticamente sull'ora più recente (arrotondata) per limitare il numero di record restituiti. Anche il campo **Da** viene impostato automaticamente. È possibile modificare la data **Da** facendo clic sul pulsante **Calendario**. La data **A** verrà modificata automaticamente quando si modifica la data **Da**.

È possibile fare clic sui pulsanti **Indietro**/**Avanti** per visualizzare le sezioni nel periodo precedente o successivo. L'intervallo di tempo per **Indietro** e **Avanti** è impostato in base alla frequenza e all'intervallo della sezione, come illustrato nella tabella seguente.

Frequenza | Intervallo di valori di intervallo | Blocco di tempo risultante
----------| -------------------- | --------------------
Minuto | 1-4 | 6 ore
Minuto | 5-29 | 1 giorno
Minuto | 30-180 | 7 giorni
Minuto | 180+ | 28 giorni (all'incirca un mese)
Ora | 1-3 | 7 giorni
Ora | 4-11 | 28 giorni (all'incirca un mese)
Ora | 12-72 | 182 giorni (all'incirca 6 mesi)
Ora | 73+ | 1 anno
Giorno | 1-6 | 1 anno
Giorno | 7-20 | 5 anni
Giorno | 21+ | 10 anni
Settimana | 1-3 | 5 anni
Settimana | 4+ | 10 anni
Mese | qualsiasi | 10 anni
 
Ad esempio, se si definisce l'oggetto **frequency** come **Hour** e l'oggetto **interval** di **2**, facendo clic sui pulsanti **Avanti**/**Indietro** si sposterà l'intervallo di tempo di **7 giorni** in una delle due direzioni. Questa logica si applica al pannello Filtro quando si visualizzano tutte le sezioni/le sezioni recenti/le sezioni problematiche.




## <a name="DataFactorySlice"></a> Visualizzare i dettagli relativi a una sezione
Fare clic su una sezione nell'elenco di sezioni nel pannello **TABELLA** o nel pannello **Sezioni dati** per visualizzarne i dettagli.

![Sezione dati][image-data-factory-dataslice]

Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.

### <a name="DataFactoryActivtyRuns"></a> Visualizzare tutte le esecuzioni di attività per una sezione
Una sezione può contenere più esecuzioni. Ad esempio, se una sezione non riesce, il servizio può riprovare alcune volte. È anche possibile rieseguire una sezione contenente tutti i tentativi non riusciti. È possibile visualizzare tutte le esecuzioni di attività nel pannello **Sezione dati** nell'elenco in basso.

## <a name="DataFactoryActivtyRunDetails"></a> Visualizzare i dettagli relativi a un'esecuzione di attività
Fare clic su un'esecuzione di attività nell'elenco di esecuzioni del pannello **Sezione dati** per visualizzarne i dettagli.

![Dettagli esecuzione attività][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Sezione Operazioni - Eventi nella settimana precedente
Nel pannello **DATA FACTORY** o nella home page della data factory fare clic su **Eventi nella settimana precedente** nella sezione **Operazioni** per visualizzare gli eventi della settimana precedente. In questo modo è possibile avere una visualizzazione generale delle operazioni eseguite dall'istanza di Data factory nella settimana precedente. È anche possibile risolvere eventuali errori con lo spostamento o l'elaborazione dei dati.

![Eventi di Data factory][image-data-factory-events]


## Vedere anche

Articolo | Descrizione
------ | ---------------
[Monitorare e gestire Data factory di Azure con PowerShell][monitor-manage-using-powershell] | Questo articolo descrive come monitorare Data factory di Azure con i cmdlet di Azure PowerShell. 


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

<!---HONumber=July15_HO4-->