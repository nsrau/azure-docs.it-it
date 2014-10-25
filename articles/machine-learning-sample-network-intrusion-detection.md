<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning Sample: Network intrusion detection | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Esempio di Azure Machine Learning: rilevamento intrusione di rete

<em>L'esperimento di esempio associato a questo modello in ML Studio è disponibile nella sezione **ESPERIMENTI** nella scheda **ESEMPI**. Il nome dell'esperimento è:</em>

    Sample Experiment - Network Intrusion Detection - Development

## Descrizione del problema

La demo usa varie funzionalità di rete per rilevare le attività di rete che fanno parte di un'intrusione o di un attacco. Questo problema di classificazione binaria non è troppo difficile perché le funzionalità sono abbastanza predittive di una classe e le classi sono abbastanza bilanciate. Poiché le diverse funzionalità fanno parte dell'etichetta, è importante rimuovere tutte le parti dell'etichetta prima di eseguire il training di un classificatore.

## Dati

1.  I dati tratti da KDD cup 1999 includono set di dati di training e di testing. Le accuratezze di classificazione per il set di dati di training sono in genere maggiori di quelle del set di dati di test poiché le nuove intrusioni di rete vengono introdotte nel set di test. Il set di dati di training contiene circa 126K righe e 43 colonne, comprese le etichette. Poiché tre colonne fanno parte delle informazioni sulle etichette, ci sono 40 colonne disponibili per il training del modello. Queste 40 colonne sono principalmente numeriche con alcune funzioni stringa o categoriali. I dati di test contengono circa 22.5K esempi di test (con le stesse 43 colonne nei dati di training).
2.  I dati sono stati caricati in un BLOB pubblico in modo da potervi accedere facilmente. AzureML fornisce moduli lettore per la lettura di dati da diverse risorse di archiviazione come i BLOB con pochi semplici clic senza dover scrivere righe di codice.

## Modello

1.  Il primo passaggio nel modello è condizionare i dati per la successiva classificazione. La colonna "Classe" contiene le etichette da predire. Queste etichette sono "normali" (nessuna intrusione) o rappresentano il nome di un attacco. Alcuni attacchi non hanno molti esempi nei dati di training ed esistono nuovi attacchi nel set di dati di test, pertanto queste etichette multiclasse vengono convertite in etichette di classe binarie per rendere il problema fattibile. Lo studio fornisce un modulo incorporato per semplificare questo passaggio di pre-elaborazione. Il modulo dei valori indicatori viene usato per binarizzare le etichette di classe e quindi il modulo delle colonne di progetto viene usato per selezionare questa colonna di etichette di classe binarizzate (esclusa la colonna di etichette di classe originali).
2.  Come parte dello sviluppo di questo esperimento, verrà analizzata la selezione/correlazione delle funzionalità dato che molte funzionalità sono altamente correlate all'etichetta. Ciò è comune nei set di dati sintetici. Cloud ML fornisce questa visualizzazione in pochi semplici clic. A tale scopo, selezionare l'opzione di visualizzazione nell'output del modulo di progetto e fare clic sull'intestazione di una delle funzionalità per avviare il visualizzatore e quindi selezionare l'etichetta di classe nell'elenco a discesa di confronto.
3.  Poiché un numero limitato di funzionalità è altamente correlato all'etichetta, verranno testate le combinazioni di classificatori lineari e non lineari con e senza la selezione di funzionalità per determinare il modello appropriato. Questo confronto viene ottenuto mediante il modulo Valuta modello.

## Risultati

1.  Le prestazioni del classificatore non lineare (albero di decisione con boosting) sono risultate leggermente migliori rispetto a quelle del classificatore lineare (regressione logistica):

![][]

Ora verrà eseguito il confronto con un albero di decisione con boosting con la selezione delle funzionalità. Le prestazioni della classificazione sono abbastanza simili, ma l'albero di decisione con boosting in tutte le funzionalità è leggermente migliore e verrà usato per il flusso di lavoro per l'assegnazione di punteggi. Si noti che il valore elevato dell'area sottesa alla curva (AUC) per questa classificazione è tipico per tale set di dati.

![][1]

<!-- Removed until this part is fixed ## Operationalization ##   We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio:   1. First step is to save the learned model (by right clicking on the classifier module output)  1. Now create a new experiment and search for saved model and drop it in the panel for new experiment  1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment  1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service  1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio    -->

  []: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
  [1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
