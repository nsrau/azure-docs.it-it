<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Esempio di Machine Learning: Stima del rischio di credito | Azure" description="Un esperimento di Azure Machine Learning di esempio per lo sviluppo di un modello di classificazione binario che consente di stimare se un richiedente è a basso o ad alto rischio di credito." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Esempio di Machine Learning di Azure: Stima del rischio di credito 

>[AZURE.NOTE]
>L'[esperimento di esempio] e il [set di dati di esempio] associati a questo modello sono disponibili in ML Studio. Per informazioni dettagliate, vedere di seguito.
[Esperimento di esempio]: #sample-experiment
[Set di dati di esempio]: #sample-dataset

*Per una procedura dettagliata sulla creazione e l'uso di una versione semplificata di questo esperimento, vedere [Sviluppare una soluzione predittiva con Machine Learning di Azure](http://azure.microsoft.com/it-it/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).*

Lo scopo di questo esperimento è prevedere il rischio sul credito in base alle informazioni fornite in una richiesta di credito. La stima è un valore binario: basso rischio o alto rischio. 


<!-- Removed until the Training and Scoring parts are fixed
This example is divided into 3 sample experiments:

- Development Experiment - for experimenting with different models
- Training Experiment - to train the one chosen model
- Scoring Experiment - to set up a web service using the trained model
-->

<!-- Removed because we added a section at the bottom describing the dataset
##Dataset Description

The experiment uses the UCI Statlog (German Credit Card) dataset which can be found here: 
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>. 
Viene usato il file german.data di questo sito Web.

Il set di dati classifica le persone, descritte da un set di attributi, come rischi di credito alti o bassi. Ogni esempio rappresenta una persona. Sono presenti 20 variabili, sia numeriche che relative alle categorie, nonché un'etichetta binaria (il valore del rischio di credito). Le voci che rappresentano un rischio di credito elevato hanno l'etichetta 2, quelle che rappresentano un rischio di credito basso hanno l'etichetta 1. Classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti.
-->

##Esperimento di sviluppo

Il set di dati originale è in formato separato da spazi. Il set di dati è stato convertito in formato CSV e caricato in ML Studio. Questa trasformazione può essere eseguita con Powershell: 

	cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

O usando il comando sed di Unix:

	sed 's/ /,/g' german.data > german.csv

Si inizia usando l'**editor di metadati** per aggiungere nomi di colonna per sostituire i nomi di colonna predefiniti del set di dati con nomi più significativi presi dalla descrizione del set di dati nel sito UCI. I nuovi nomi di colonna sono separati da virgola nel campo **Nome nuova colonna** dell'**editor di metadati**.

Verranno ora generati training set e di test che saranno usati per sviluppare il modello di stima del rischio. Il set di dati originale è stato suddiviso in training set e set di test delle stesse dimensioni usando il modulo **Suddivisione** con **Rapporto tra prime righe di output e input** impostato su 0,5.
 
Poiché classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti rispetto a classificare erroneamente un cliente come ad alto rischio mentre è a basso rischio, verrà generato un nuovo set di dati che riflette questa funzione di costo. Nel nuovo set di dati, ogni esempio ad alto rischio viene replicato 5 volte, mentre gli esempi a basso rischio vengono mantenuti invariati. La suddivisione dei set di dati di training e di test viene eseguita prima di questa replica, per evitare la presenza dello stesso esempio in entrambi i set. 

La replica viene eseguita dal codice R seguente, che viene eseguito usando il modulo **Esecuzione script R**:

	dataset1 <- maml.mapInputPort(1)
	data.set<-dataset1[dataset1[,21]==1,]
	pos<-dataset1[dataset1[,21]==2,]
	for (i in 1:5) data.set<-rbind(data.set,pos)
	maml.mapOutputPort("data.set")

Nell'esperimento vengono confrontati due approcci alla generazione di modelli: training sul set di dati originale e training sul set di dati replicato. In entrambi gli approcci, per restare allineati alla funzione di costo del problema si eseguono i test sul set di test con replica. Il flusso di lavoro finale per la suddivisione e la replica è illustrato di seguito. In questo flusso di lavoro l'output di sinistra del modulo **Suddivisione** è un training set e l'output di destra è un set di test. Notare che il training set viene usato successivamente sia con che senza **Esecuzione script R**, vale a dire con e senza replica.

![Splitting training and test data][screen1]
 
Oltre a controllare l'effetto della replica degli esempi nel training set, vengono confrontate le prestazioni di due algoritmi: Macchina a vettori di supporto (SVM) e albero delle decisioni con boosting. In questo modo vengono efficacemente generati 4 modelli:

- SVM, sottoposto a training con i dati originali
- SVM, sottoposto a training con i dati replicati
- Albero delle decisioni con boosting, sottoposto a training con i dati originali
- Albero delle decisioni con boosting, sottoposto a training con i dati replicati

Gli alberi delle decisioni con boosting funzionano bene con qualsiasi tipo di funzionalità. Poiché tuttavia il modulo SVM genera un classificatore lineare, il modello che genera ha l'errore di test migliore quando gli elementi hanno la stessa scala. Per convertire tutti gli elementi nella stessa scala si usa il modulo **Trasforma dati scalando** con una trasformazione tanh. Questa trasformazione converte tutti gli elementi numerici nell'intervallo [0,1]. Notare che gli elementi numerici vengono convertiti dal modulo SVM in elementi di categoria e quindi in elementi 0/1 binari, quindi non è necessario trasformare manualmente gli elementi stringa. 

L'algoritmo di apprendimento viene inizializzato usando il modulo **Macchina a vettori di supporto a due classi** o il modulo **Albero delle decisioni con boosting a due classi** e quindi usando il modulo **Esegui training modello**per creare il modello effettivo. Questi modelli vengono usati dai moduli **Calcola punteggio** per produrre punteggi degli esempi di test. Di seguito è illustrato un flusso di lavoro di esempio che combina questi moduli e usa SVM e il training set replicato. Notare che **Esegui training modello** è connesso al training set, mentre **Calcola punteggio** è connesso al set di test.

![Training and scoring a model][screen2]

Nella fase di valutazione dell'esperimento si calcola l'accuratezza di ognuno dei 4 modelli precedenti. A questo scopo si usa il modulo **Valuta modello**. Notare che questo modulo calcola l'accuratezza solo quando tutti gli esempi hanno lo stesso costo di classificazione non corretta. Tuttavia, poiché in precedenza sono stati replicati gli esempi positivi, l'accuratezza calcolata da **Valuta modello** tiene conto del costo ed è 

![Accuracy computation][formula]

dove *n+* e *n-* sono i numeri degli esempi positivi e negativi nel set di dati originale, mentre *e+* e *e-* sono i numeri degli esempi positivi e negativi classificati in modo errato nel set di dati originale.

Il modulo **Valuta modello** confronta due modelli con punteggio assegnato, dunque viene usato un modulo **Valuta modello** per confrontare i due modelli SVM e uno per confrontare i due modelli albero delle decisioni con boosting. I due moduli verranno combinati in una tabella per visualizzate tutti e quattro i risultati. **Valuta modello** produce una tabella con un'unica riga che contiene vari tipi di metrica. Per combinare tutti i risultati in un'unica tabella verrà usato il modulo **Aggiungi righe**. Nella tabella verranno quindi inserite annotazioni sull'accuratezza dei 4 moduli usando uno script R nel modulo **Esecuzione script R**, in cui verranno inseriti manualmente i nomi delle righe della tabella finale. Infine verranno rimosse le colonne con metrica non pertinente usando il modulo **Colonne progetto**. 

I risultati finali dell'esperimento, ottenuti facendo clic con il pulsante destro del mouse sull'output **Set di dati risultati** di **Colonne progetto** sono:

![Results][results] 

dove la prima colonna è il nome dell'algoritmo di Machine Learning usato per generare un modello, la seconda colonna indica il tipo di training set e la terza colonna è l'accuratezza sensibile al costo. In questo esperimento, il modello SVM, che usa il set di dati di training replicato, offre l'accuratezza migliore.

<!-- Removed until the Training and Scoring parts are fixed
##Training Experiment

The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model. 

Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.

![Azure storage parameters][screen3] 

##Scoring Experiment

The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model. 

After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**. 

After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.

When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.

![Web service ready for production][screen4] 
-->

## Esperimento di esempio

L'esperimento di esempio seguente associato a questo modello è disponibile in ML Studio nella sezione **ESPERIMENTI** della scheda **ESEMPI**.

> **Esperimento di esempio - German Credit - Sviluppo**


## Set di dati di esempio

L'esempio di set di dati seguente usato per questo esperimento è disponibile in ML Studio nella tavolozza dei moduli in **Set di dati salvati**.

###Set di dati UCI German Credit Card
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]



[screen1]:./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
[screen2]:./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
[formula]:./media/machine-learning-sample-credit-risk-prediction/formula.jpg
[results]:./media/machine-learning-sample-credit-risk-prediction/results.jpg
[screen3]:./media/machine-learning-sample-credit-risk-prediction/screen3.jpg
[screen4]:./media/machine-learning-sample-credit-risk-prediction/screen4.jpg
