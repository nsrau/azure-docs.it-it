<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Passaggio 3: Creare un nuovo esperimento di Machine Learning | Azure" description="Passaggio 3: Creazione di un nuovo esperimento di formazione in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/02/2014" ms.author="garye" />


Questo è il terzo passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Machine Learning di Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creare un'area di lavoro ML][create-workspace]
2.	[Caricare i dati esistenti][upload-data]
3.	**Creare un nuovo esperimento**
4.	[Addestrare e valutare i modelli][train-models]
5.	[Pubblicare il servizio Web][publish]
6.	[Accedere al servizio Web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Passaggio 3: Creare un nuovo esperimento di Machine Learning di Azure

È necessario creare un nuovo esperimento in ML Studio che usi il set di dati che è stato caricato.  

1.	In ML Studio fare clic su **+NUOVO** nella parte inferiore della finestra.
2.	Selezionare **ESPERIMENTO**.
3.	Nella tavolozza dei moduli a sinistra dell'area di disegno dell'esperimento espandere **Set di dati salvati**.
4.	Individuare il set di dati creato e trascinarlo nell'area di disegno. È possibile trovare il set di dati anche immettendone il nome nella casella **Cerca** sopra la tavolozza.  

##Preparare i dati
È possibile visualizzare le prime 100 righe di dati e alcune informazioni statistiche per l'intero set di dati facendo clic con il pulsante destro del mouse sulla porta di output del set di dati e selezionando **Visualizza**. Si noti che ML Studio ha già identificato il tipo di dati per ogni colonna. Sono anche state assegnate intestazioni generiche alle colonne, perché il file di dati non ne presentava alcuna.  

Le intestazioni di colonna non sono indispensabili ma semplificheranno l'uso dei dati nel modello. Inoltre, quando il modello verrà pubblicato in un servizio Web, le intestazioni aiuteranno gli utenti del servizio a identificare le varie colonne.  

È possibile aggiungere intestazioni di colonna usando il modulo **Editor metadati**.  

1.	Nella tavolozza dei moduli digitare "metadati" nella casella **Cerca**. Nell'elenco dei moduli verrà visualizzato il modulo **Editor metadati**.
2.	Trascinare il modulo **Editor metadati** nell'area di disegno e rilasciarlo sotto il set di dati.
3.	Connettere il set di dati all'**Editor metadati**: fare clic sulla porta di output del set di dati, trascinarla sulla porta di input di **Editor metadati**, quindi rilasciare il pulsante del mouse. Il set di dati e il modulo rimarranno connessi anche se uno di essi viene spostato in un altra posizione nell'area di disegno.
4.	Nel riquadro **Proprietà** a destra dell'area di disegno fare clic su **Avvia selettore colonne**.
5.	Verificare che nel campo **Inizia con** sia selezionato "Tutte le colonne", rimuovere le eventuali righe e fare clic su **OK**. Questo indicherà a **Editor metadati** di operare su tutte le colonne di dati.
6.	Per il parametro **Nuovo nome colonna** immettere un elenco di nomi per le 21 colonne nel set di dati, separati da virgole e nell'ordine delle colonne. È possibile ottenere i nomi di colonna dalla documentazione relativa ai set di dati disponibile sul sito Web UCI, oppure, per praticità è possibile copiare e incollare i nomi seguenti:  

	Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

Il riquadro Proprietà avrà l'aspetto seguente:

![Properties for Metadata Editor][1] 

>Suggerimento - Per verificare le intestazioni di colonna, eseguire l'esperimento (fare clic su **ESEGUI** sotto l'area di disegno dell'esperimento), fare clic con il pulsante destro del mouse sulla porta di output del modulo **Editor metadati** e selezionare **Visualizza**. È possibile visualizzare l'output di ogni modulo nello stesso modo in cui si visualizza lo stato dei dati nel corso dell'esperimento.

L'esperimento avrà ora un aspetto analogo al seguente:  

![Adding Metadata Editor][2]
 
##Creare set di dati di training e di test
Il prossimo passaggio dell'esperimento consiste nel generare set di dati separati che verranno usati per le attività di training e di test del modello. Per questa operazione viene usato il modulo **Dividi**.  

1.	Individuare il modulo **Dividi**, trascinarlo nell'area di disegno e connetterlo all'ultimo modulo **Editor metadati**.
2.	Per impostazione predefinita, il rapporto di suddivisione è impostato su 0,5 e il parametro **Suddivisione casuale** è impostato. Questo significa che una metà casuale dei dati verrà inviata all'output attraverso una porta del modulo **Dividi** e l'altra metà attraverso l'altra. È possibile regolare queste impostazioni, così come il parametro **Valore di inizializzazione casuale** per modificare la suddivisione tra dati di training e dati di assegnazione del punteggio. Per questo esempio i parametri verranno lasciati inalterati.
	>Suggerimento - Il rapporto di suddivisione determina in sostanza la quantità di dati che viene inviata all'output attraverso la relativa porta. Se ad esempio si imposta il rapporto su 0,7, il 70% dei dati verrà inviato alla porta sinistra e il 30% alla porta destra.  
	
È possibile usare gli output del modulo **Dividi** in qualsiasi modo si preferisca, in questo caso si sceglierà di usare l'output sinistro per i dati di training e quello destro per i dati di assegnazione del punteggio.  

Come anche menzionato nel sito Web UCI, il costo di un'errata classificazione di un elevato rischio di credito come basso è 5 volte più alto del costo di un'errata classificazione di un basso rischio di credito come alto. Tenendo conto di questo, verrà generato un novo set di dati che rifletta questa funzione di costo. Nel nuovo set di dati ogni esempio elevato viene replicato 5 volte, mentre ogni esempio basso non viene replicato.   

Ciò è ottenibile usando il codice R:  

1.	Individuare e trascinare il modulo **Esecuzione script R** nell'area di disegno dell'esperimento e connetterlo alla porta di output sinistra del modulo **Dividi**.
2.	Nel riquadro **Proprietà** eliminare il testo predefinito nel parametro **Script** e immettere lo script seguente: 

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


È necessario eseguire la stessa operazione di replica per ogni output del modulo **Dividi** in modo che i dati di training e di assegnazione del punteggio abbiano la stessa regolazione di costo.

1.	Fare clic con il pulsante destro del mouse sul modulo **Esecuzione script R** e selezionare **Copia**.
2.	Fare clic con il pulsante destro del mouse sull'area di disegno dell'esperimento e selezionare **Incolla**.
3.	Connettere questo modulo **Esecuzione script R** alla porta di output destra del modulo **Dividi**.  

>Suggerimento: la copia del modulo Esecuzione script R contiene lo stesso script contenuto nel modulo originale. Quando si copia e incolla un modulo sull'area di disegno, la copia conserva tutte le proprietà dell'originale.  
>
L'esperimento avrà ora un aspetto analogo al seguente:
 
![Adding Split module and R scripts][3]

**Passaggi successivi: [Addestrare e valutare i modelli][train-models]**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
