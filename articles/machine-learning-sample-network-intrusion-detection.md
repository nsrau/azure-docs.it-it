<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Esempio di Machine Learning: Rilevamento intrusione di rete | Azure" description="Un esperimento di Azure Machine Learning di esempio che usa un modello di classificazione per determinare quali attività di rete costituiscono intrusioni nella rete." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Esempio di Machine Learning di Azure: Rilevamento intrusione di rete

>[AZURE.NOTE]
>L'[esperimento di esempio] e il [set di dati di esempio] associati a questo modello sono disponibili in ML Studio. Per informazioni dettagliate, vedere di seguito.
[Esperimento di esempio]: #sample-experiment
[Set di dati di esempio]: #sample-dataset


## Descrizione del problema ##

La demo usa varie funzionalità di rete per rilevare le attività di rete che fanno parte di un'intrusione o di un attacco. Questo problema di classificazione binaria non è troppo difficile perché le funzionalità sono abbastanza predittive di una classe e le classi sono abbastanza bilanciate.  Poiché le diverse funzionalità fanno parte dell'etichetta, è importante rimuovere tutte le parti dell'etichetta prima di eseguire il training di un classificatore. 

## Dati ##

1. I dati tratti da KDD cup 1999 includono set di dati di training e di testing.  Le accuratezze di classificazione per il set di dati di training sono in genere maggiori di quelle del set di dati di test poiché le nuove intrusioni di rete vengono introdotte nel set di test.  Il set di dati di training contiene circa 126K righe e 43 colonne, comprese le etichette.  Poiché tre colonne fanno parte delle informazioni sulle etichette, ci sono 40 colonne disponibili per il training del modello.  Queste 40 colonne sono principalmente numeriche con alcune funzioni stringa o categoriali. I dati di test contengono circa 22.5K esempi di test (con le stesse 43 colonne nei dati di training). 
1. I dati sono stati caricati in un BLOB pubblico in modo da potervi accedere facilmente. AzureML fornisce moduli lettore per la lettura di dati da diverse risorse di archiviazione come i BLOB con pochi semplici clic senza dover scrivere righe di codice. 

## Modello ##

1. Il primo passaggio nel modello è condizionare i dati per la successiva classificazione.  La colonna "Classe" contiene le etichette da predire.  Queste etichette sono "normali" (nessuna intrusione) o rappresentano il nome di un attacco.  Alcuni attacchi non hanno molti esempi nei dati di training ed esistono nuovi attacchi nel set di dati di test, pertanto queste etichette multiclasse vengono convertite in etichette di classe binarie per rendere il problema fattibile.  Lo studio fornisce un modulo incorporato per semplificare questo passaggio di pre-elaborazione. Il modulo dei valori indicatori viene usato per binarizzare le etichette di classe e quindi il modulo delle colonne di progetto viene usato per selezionare questa colonna di etichette di classe binarizzate (esclusa la colonna di etichette di classe originali).  
1. Come parte dello sviluppo di questo esperimento, verrà analizzata la selezione/correlazione delle funzionalità dato che molte funzionalità sono altamente correlate all'etichetta. Ciò è comune nei set di dati sintetici.  Cloud ML fornisce questa visualizzazione in pochi semplici clic. A tale scopo, selezionare l'opzione di visualizzazione nell'output del modulo di progetto e fare clic sull'intestazione di una delle funzionalità per avviare il visualizzatore e quindi selezionare l'etichetta di classe nell'elenco a discesa di confronto.
1. Poiché un numero limitato di funzionalità è altamente correlato all'etichetta, verranno testate le combinazioni di classificatori lineari e non lineari con e senza la selezione di funzionalità per determinare il modello appropriato.  Questo confronto viene ottenuto mediante il modulo Valuta modello.    

## Risultati ##

1. Le prestazioni del classificatore non lineare (albero delle decisioni con boosting) sono risultate leggermente migliori rispetto a quelle del classificatore lineare (regressione logistica): 

![][1]


Ora verrà eseguito il confronto con un albero delle decisioni con boosting con la selezione delle funzionalità. Le prestazioni della classificazione sono abbastanza simili, ma l'albero delle decisioni con boosting in tutte le funzionalità è leggermente migliore e verrà usato per il flusso di lavoro per l'assegnazione di punteggi.  Si noti che il valore elevato dell'area sottesa alla curva (AUC) per questa classificazione è tipico per tale set di dati. 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## Esperimento di esempio

L'esperimento di esempio seguente associato a questo modello è disponibile in ML Studio nella sezione **ESPERIMENTI** della scheda **ESEMPI**.

> **Esperimento di esempio - Rilevamento delle intrusioni di rete - Sviluppo**


## Set di dati di esempio

L'esempio di set di dati seguente usato per questo esperimento è disponibile in ML Studio nella tavolozza dei moduli in **Set di dati salvati**.

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
