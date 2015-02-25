<properties title="Step 2: Upload existing data into an Azure Machine Learning experiment" pageTitle="Passaggio 2: Caricare dati in un esperimento di Machine Learning | Azure" description="Passaggio 2: Caricamento di dati pubblici esistenti in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="big-data" documentationCenter="" authors="garye" videoId="" scriptId="" manager="paulettm" editor="cgronlun" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/02/2014" ms.author="garye" />

Questo è il secondo passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Machine Learning di Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Creare un'area di lavoro ML][create-workspace]
2.	**Caricare i dati esistenti**
3.	[Creare un nuovo esperimento][create-new]
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

# Passaggio 2: Caricare dati esistenti in un esperimento di Machine Learning di Azure  

Per sviluppare un modello predittivo per il rischio del credito, verrà usato il set di dati "UCI Statlog (German Credit Data) Data Set" del repository di Machine Learning UCI, disponibile al seguente indirizzo:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Verrà usato il file denominato **german.data**. Scaricare questo file nel disco rigido locale.  

Questo set di dati contiene le righe di 20 variabili per 1000 clienti che in passato hanno fatto richiesta di un credito. Queste 20 variabili rappresentano il vettore della funzionalità del set di dati che fornisce le caratteristiche di identificazione di ogni richiedente. Una colonna aggiuntiva in ogni riga rappresenta il rischio del credito del richiedente. In questa colonna 700 richiedenti sono identificati come a basso rischio e 300 ad alto rischio.   

Il sito Web UCI fornisce una descrizione degli attributi del vettore delle funzionalità che includono informazioni finanziarie, storico dei crediti, stato di occupazione e dati personali. Per ogni richiedente è stata assegnata una valutazione in formato binario per indicare se è a basso o ad alto rischio.  

Questi dati verranno usati per creare un modello di analisi predittiva. Una volta completata questa operazione, il modello dovrebbe essere in grado di accettare informazioni per nuovi clienti e prevedere se sono a basso o ad alto rischio.  

Ma ecco un interessante risvolto. La descrizione del set di dati spiega che classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti per l'istituto di credito rispetto a classificare erroneamente un cliente come ad alto rischio mentre è a basso rischio. Un modo semplice per tenere conto di questo aspetto nell'esperimento consiste nel duplicare (5 volte) le voci che rappresentano un cliente con rischio di credito elevato. In tal modo, se il modello classifica erroneamente come basso un rischio alto, la classificazione errata sarà ripetuta 5 volte, una per ogni duplicato, e il costo di questo errore aumenterà nei risultati.  

## Convertire il formato del set di dati
Nel set di dati originale viene usato un formato con valori separati da spazi vuoti. Per il funzionamento ottimale di ML Studio è preferibile usare un file con valori separati da virgole (CSV), di conseguenza il set di dati verrà convertito sostituendo gli spazi con le virgole.  

È possibile effettuare questa operazione con il comando seguente di Windows PowerShell:   

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

Oppure anche con il comando sed di Unix:  

	sed 's/ /,/g' german.data > german.csv  

## Caricare il set di dati in ML Studio

Dopo aver convertito i dati in formato CSV, è necessario caricarli in ML Studio.  

1.	In ML Studio fare clic su **+NUOVO** nella parte inferiore della finestra.
2.	Selezionare **SET DI DATI**.
3.	Selezionare **DA FILE LOCALE**.
4.	Nella finestra di dialogo **Carica un nuovo dataset** fare clic su **Sfoglia** e individuare il file **german.csv** creato.
5.	Immettere un nome per il set di dati. Per questo esempio il nome sarà "UCI German Credit Card Data".
6.	Come tipo di dati selezionare "File CSV generico senza intestazione (.nh.csv)".
7.	Aggiungere un'eventuale descrizione.
8.	Fare clic su **OK**.  

![Upload the dataset][1]  

 
I dati vengono caricati in un modulo Set di dati che è possibile usare in un esperimento.

**Passaggi successivi: [Creare un nuovo esperimento][create-new]**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
