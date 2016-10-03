<properties
	pageTitle="Passaggio 3: Creare un nuovo esperimento di Machine Learning | Microsoft Azure"
	description="Passaggio 3 della procedura dettagliata Sviluppare una soluzione predittiva: Creare un nuovo esperimento di formazione in Azure Machine Learning Studio."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>  

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016" 
	ms.author="garye"/>


# Passaggio 3 della procedura dettagliata: Creare un nuovo esperimento di Machine Learning di Azure

Questo è il terzo passaggio della procedura dettagliata [Sviluppare una soluzione di analisi predittiva in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creare un'area di lavoro di Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3.	**Creare un nuovo esperimento**
4.	[Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Distribuire il servizio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

----------

Il passaggio successivo di questa procedura dettagliata consiste nel creare un nuovo esperimento in Machine Learning Studio che usi il set di dati che è stato caricato.

1.	In Studio fare clic su **+NEW** nella parte inferiore della finestra.
2.	Selezionare **EXPERIMENT** e quindi selezionare "Blank Experiment". Selezionare il nome dell'esperimento predefinito nella parte superiore dell'area di disegno e denominarlo in modo significativo.

	> [AZURE.TIP] È buona abitudine compilare i campi **Summary** e **Description** dell'esperimento nel riquadro **Properties**. Queste proprietà offrono la possibilità di documentare l'esperimento, in modo che chiunque in seguito lo esamini sia in grado di comprendere gli obiettivi e la metodologia.

3.	Nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento, espandere **Set di dati salvati**.
4.	Trovare il set di dati creato in **My Datasets** e trascinarlo nell'area di disegno. È possibile trovare il set di dati anche immettendone il nome nella casella **Cerca** sopra alla tavolozza.

## Preparare i dati

È possibile visualizzare le prime 100 righe di dati e alcune informazioni statistiche per l'intero set di dati facendo clic sulla porta di output del set di dati (il circoletto in basso) e selezionando **Visualize**.

Poiché il file di dati non presentava intestazioni di colonna, Studio ha assegnato intestazioni generiche (Col1, Col2, *e così via*). Per la creazione dei modelli non sono indispensabili intestazioni di colonna precise, ma semplificano l'uso dei dati nell'esperimento. Inoltre, quando il modello verrà pubblicato in un servizio Web, le intestazioni aiuteranno gli utenti del servizio a identificare le varie colonne.

È possibile aggiungere intestazioni di colonna utilizzando il modulo [Edit Metadata][edit-metadata]. Si utilizza il modulo [Edit Metadata][edit-metadata] per modificare i metadati associati a un set di dati. In questo caso, può fornire nomi più descrittivi per le intestazioni di colonna.

Per usare [Edit Metadata][edit-metadata] è necessario innanzitutto specificare le colonne da modificare (in questo caso, tutte), quindi specificare l'azione da eseguire su tali colonne (in questo caso, modificarne le intestazioni).

1.	Nella tavolozza dei moduli digitare "metadati" nella casella **Cerca**. Nell'elenco dei moduli verrà visualizzato il modulo [Edit Metadata][edit-metadata].
2.	Trascinare il modulo [Edit Metadata][edit-metadata] nell'area di disegno e rilasciarlo sotto il set di dati precedentemente aggiunto.
3.	Connettere il set di dati a [Edit Metadata][edit-metadata]\: fare clic sulla porta di output del set di dati (il circoletto in fondo al set di dati), trascinarla sulla porta di input di [Edit Metadata][edit-metadata] (il circoletto in cima al modulo), quindi rilasciare il pulsante del mouse. Il set di dati e il modulo rimarranno connessi anche se uno di essi viene spostato in un altra posizione nell'area di disegno.

    L'esperimento avrà ora un aspetto analogo al seguente:

    ![Aggiunta di Edit Metadata][2]
    
    Il punto esclamativo rosso indica che le proprietà di questo modulo non sono ancora state configurate. Ce ne occuperemo subito.
    
    > [AZURE.TIP] È possibile aggiungere un commento a un modulo facendo doppio clic sul modulo e immettendo del testo. In tal modo sarà possibile individuare subito l'operazione eseguita dal modulo nell'esperimento. In questo caso, fare doppio clic sul modulo [Edit Metadata][edit-metadata] e digitare il commento "Add column headings". Fare clic in un punto qualsiasi dell'area di disegno per chiudere la casella di testo. Fare clic sulla freccia giù del modulo per visualizzare il commento.

4.	Selezionare [Edit Metadata][edit-metadata] quindi, nel riquadro **Properties** a destra dell'area di disegno, fare clic su **Launch column selector**.
5.	Nella finestra di dialogo **Select columns** (Seleziona colonne) selezionare tutte le righe in **Available Columns** (Colonne disponibili)e fare clic su > per spostarle in **Selected Columns** (Colonne selezionate). La finestra di dialogo dovrebbe essere simile alla seguente: ![Selettore di colonna con tutte le colonne selezionate][4]
7.	Fare clic sul segno di spunta **OK**.
8.	Nel riquadro **Properties** cercare il parametro **New column names**. In questo campo immettere un elenco di nomi per le 21 colonne nel set di dati, separati da virgole e nell'ordine delle colonne. È possibile ottenere i nomi di colonna dalla documentazione relativa ai set di dati disponibile sul sito Web UCI, oppure, per praticità, è possibile copiare e incollare l'elenco seguente:

		  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Il riquadro Proprietà avrà l'aspetto seguente:

    ![Proprietà per Edit Metadata][1]

> [AZURE.TIP] Se si vuole verificare le intestazioni di colonna, eseguire l'esperimento (fare clic su **RUN** (ESEGUI) sotto l'area di disegno dell'esperimento). Al termine dell'esecuzione, verrà visualizzato un segno di spunta in [Edit Metadata][edit-metadata], fare clic sulla porta di output del modulo [Edit Metadata][edit-metadata] e selezionare **Visualize** (Visualizza). È possibile visualizzare l'output di ogni modulo nello stesso modo in cui si visualizza lo stato dei dati nel corso dell'esperimento.

## Creazione di set di dati di training e di test

Il prossimo passaggio dell'esperimento consiste nel generare set di dati separati che verranno usati per le attività di training e di test del modello.

Per eseguire questa operazione, viene impiegato il modulo [Split Data][split].

1.	Trovare il modulo [Split Data][split], trascinarlo nell'area di disegno, quindi connetterlo all'ultimo modulo [Edit Metadata][edit-metadata].
2.	Per impostazione predefinita, il rapporto di suddivisione è impostato su 0,5 e il parametro **Suddivisione casuale** è impostato. Questo significa che una metà casuale dei dati verrà inviata all'output attraverso una porta del modulo [Split Data][split] e l'altra metà attraverso l'altro. È possibile regolare queste impostazioni, così come il parametro **Random seed** per modificare la suddivisione tra dati di training e dati di test. Per questo esempio i parametri verranno lasciati inalterati.
	> [AZURE.TIP] La proprietà **Fraction of rows in the first output dataset** determina la quantità di dati da inviare alla porta di output sinistra. Se ad esempio si imposta il rapporto su 0,7, il 70% dei dati verrà inviato alla porta sinistra e il 30% alla porta destra.
3. Fare doppio clic sul modulo [Split Data][split] e immettere il commento, "Training/testing data split 50%".

È possibile usare gli output del modulo [Split Data][split] in qualsiasi modo si preferisca, ma in questo caso si sceglie di usare l'output sinistro per i dati di training e quello destro per i dati di test.

Come anche menzionato nel sito Web UCI, il costo di un'errata classificazione di un elevato rischio di credito come basso è 5 volte più alto del costo di un'errata classificazione di un basso rischio di credito come alto. Tenendo conto di questo, verrà generato un novo set di dati che rifletta questa funzione di costo. Nel nuovo set di dati ogni esempio a rischio elevato viene replicato 5 volte, mentre ogni esempio a basso rischio non viene replicato.

Ciò è ottenibile usando il codice R:

1.	Selezionare e trascinare il modulo [Execute R Script][execute-r-script] sull'area di disegno dagli esperimenti e connettere la porta di output sinistra del modulo [Split Data][split] alla prima porta di input ("Dataset1") del modulo [Execute R Script][execute-r-script].
2. Fare doppio clic sul modulo [Execute R Script][execute-r-script] e immettere il commento "Set cost adjustment".
2.	Nel riquadro **Properties** eliminare il testo predefinito nel parametro **R Script** e immettere lo script seguente:

		  dataset1 <- maml.mapInputPort(1)
		  data.set<-dataset1[dataset1[,21]==1,]
		  pos<-dataset1[dataset1[,21]==2,]
		  for (i in 1:5) data.set<-rbind(data.set,pos)
		  maml.mapOutputPort("data.set")


È necessario eseguire la stessa operazione di replica per ogni output del modulo [Split Data][split] in modo che i dati di training e di test abbiano la stessa rettifica del costo.

1.	Fare clic con il pulsante destro del mouse sul modulo [Esecuzione script R][execute-r-script] e selezionare **Copia**.
2.	Fare clic con il pulsante destro del mouse nell'area di disegno dell'esperimento, quindi selezionare **Incolla**.
3.	Connettere la prima porta di input di questo modulo [Execute R Script][execute-r-script] alla porta di output destra del modulo [Split Data][split].

> [AZURE.TIP] La copia del modulo Esecuzione script R contiene lo stesso script contenuto nel modulo originale. Quando si copia e incolla un modulo sull'area di disegno, la copia conserva tutte le proprietà dell'originale.

L'esperimento avrà ora un aspetto analogo al seguente:

![Adding Split module and R scripts][3]

Per altre informazioni sull'uso di script R negli esperimenti, vedere [Estendere l'esperimento con R](machine-learning-extend-your-experiment-with-r.md).

**Passaggio successivo: [Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->  
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0921_2016-->