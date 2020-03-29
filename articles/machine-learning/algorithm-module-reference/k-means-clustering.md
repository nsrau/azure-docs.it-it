---
title: 'Clustering K-means: Riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo K-Means Clustering in Azure Machine Learning per eseguire il training dei modelli di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 9606768288cc74afc24491149eb471944f45e2dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921165"
---
# <a name="module-k-means-clustering"></a>Modulo: K-Means Clustering

Questo articolo descrive come usare il modulo *K-Means Clustering* nella finestra di progettazione di Azure Machine Learning (anteprima) per creare un modello di clustering K-means non addestrato. 
 
K-means è uno degli algoritmi di apprendimento non *supervisionati* più semplici e conosciuti. È possibile usare l'algoritmo per un'ampia gamma di attività di apprendimento automatico, ad esempio:You can use the algorithm for a variety of machine learning tasks, such as: 

* [Rilevamento di dati anomali](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Clustering di documenti di testo.
* Analisi dei set di dati prima di utilizzare altri metodi di classificazione o regressione. 

Per creare un modello di clustering, è necessario:To create a clustering model, you:

* Aggiungere questo modulo alla pipeline.
* Connettere un set di dati.
* Impostare i parametri, ad esempio il numero di cluster previsti, la metrica della distanza da utilizzare nella creazione dei cluster e così via. 
  
Dopo aver configurato gli iperparametri del modulo, è possibile connettere il modello non sottoposto a training al modello di [clustering del](train-clustering-model.md)treno . Poiché l'algoritmo K-means è un metodo di apprendimento senza supervisione, una colonna label è facoltativa. 

+ Se i dati includono un'etichetta, è possibile utilizzare i valori dell'etichetta per guidare la selezione dei cluster e ottimizzare il modello. 

+ Se i dati non hanno etichetta, l'algoritmo crea cluster che rappresentano possibili categorie, basate esclusivamente sui dati.  

##  <a name="understand-k-means-clustering"></a>Comprendere il clustering K-mezzi
 
In generale, il clustering utilizza tecniche iterative per raggruppare i casi in un set di dati in cluster che possiedono caratteristiche simili. Questi raggruppamenti sono utili per esplorare i dati, identificare le anomalie nei dati ed eventualmente per eseguire stime. I modelli di clustering consentono inoltre di identificare le relazioni in un set di dati che potrebbe non derivare logicamente mediante l'esplorazione o l'osservazione semplice. Per questi motivi, il clustering viene spesso usato nelle prime fasi delle attività di apprendimento automatico, per esplorare i dati e individuare correlazioni impreviste.  
  
 Quando si configura un modello di clustering utilizzando il metodo K-means, è necessario specificare un numero di destinazione *k* che indica il numero di *centriidi* desiderato nel modello. Il centroide è un punto rappresentativo di ogni cluster. L'algoritmo K-means assegna ogni punto dati in ingresso a uno dei cluster riducendo al minimo la somma all'interno del cluster di quadrati. 
 
Quando elabora i dati di training, l'algoritmo K-means inizia con un set iniziale di centriidi scelti casualmente. I Centroidi fungono da punti di partenza per i cluster e applicano l'algoritmo Di Lloyd per perfezionare in modo iterativo le loro posizioni. L'algoritmo K-means interrompe la creazione e la raffinazione dei cluster quando soddisfa una o più di queste condizioni:  
  
-   I centriidi si stabilizzano, il che significa che le assegnazioni del cluster per i singoli punti non cambiano più e l'algoritmo è convergente in una soluzione.  
  
-   L'algoritmo ha completato l'esecuzione del numero specificato di iterazioni.  
  
 Dopo aver completato la fase di training, utilizzare il modulo [Assegna dati ai cluster](assign-data-to-clusters.md) per assegnare nuovi casi a uno dei cluster rilevati utilizzando l'algoritmo K-means. L'assegnazione del cluster viene eseguita calcolando la distanza tra il nuovo caso e il centroide di ogni cluster. Ogni nuovo caso viene assegnato al cluster con il centroide più vicino.  

## <a name="configure-the-k-means-clustering-module"></a>Configurare il modulo K-Means Clustering
  
1.  Aggiungere il modulo **K-Means Clustering** alla pipeline.  
  
2.  Per specificare la modalità di training del modello, selezionare l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: se si conoscono i parametri esatti che si desidera utilizzare nel modello di clustering, è possibile fornire un set specifico di valori come argomenti.  
  
3.  Per **Numero di centriidi**, digitare il numero di cluster con cui si desidera iniziare l'algoritmo.  
  
     Il modello non è garantito per produrre esattamente questo numero di cluster. L'algoritmo inizia con questo numero di punti dati e itera per trovare la configurazione ottimale.  
  
4.  Le proprietà **Inizializzazione** vengono utilizzate per specificare l'algoritmo utilizzato per definire la configurazione iniziale del cluster.  
  
    -   **Prima N**: Alcuni punti dati iniziali vengono scelti dal set di dati e utilizzati come mezzi iniziali. 
    
         Questo metodo viene anche chiamato *metodo Forgy*.  
  
    -   **Casuale**: L'algoritmo inserisce casualmente un punto dati in un cluster e quindi calcola la media iniziale per essere il centroide dei punti assegnati casualmente del cluster. 

         Questo metodo viene anche chiamato metodo *di partizione casuale.*  
  
    -   **K-Means:** questo è il metodo predefinito per l'inizializzazione dei cluster.  
  
         L'algoritmo **K-means** è stato proposto nel 2007 da David Arthur e Sergei Vassilvitskii per evitare un clustering inadeguato dall'algoritmo K-means standard. **K-mezzi- migliora** su K-mezzi standard utilizzando un metodo diverso per la scelta dei centri cluster iniziali.  
  
    
5.  Per **Valore di inizializzazione con numero casuale**, digitare facoltativamente un valore da utilizzare come valore di inizializzazione per l'inizializzazione del cluster. Questo valore può avere un effetto significativo sulla selezione del cluster.  
  
6.  Per **Metrica**, scegliere la funzione da utilizzare per misurare la distanza tra i vettori del cluster o tra i nuovi punti dati e il centroide scelto casualmente. Azure Machine Learning supporta le metriche seguenti per la distanza del cluster:Azure Machine Learning supports the following cluster distance metrics:  
  
    -   **Euclideo**: La distanza euclidea è comunemente usata come misura della dispersione dei cluster per il clustering K-mezzi. Questa metrica è preferibile perché riduce al minimo la distanza media tra i punti e i centroidi.
  
7.  Per **Iterazioni**, digitare il numero di volte in cui l'algoritmo deve eseguire l'iterazione dei dati di training prima di finalizzare la selezione dei centriidi.  
  
     È possibile regolare questo parametro per bilanciare la precisione rispetto al tempo di allenamento.  
  
8.  Per **Assegna modalità etichetta**, scegliere un'opzione che specifichi come gestire una colonna etichetta, se presente nel set di dati.  
  
     Poiché il clustering K-means è un metodo di apprendimento automatico senza supervisione, le etichette sono facoltative. Tuttavia, se il set di dati include già una colonna label, è possibile usare tali valori per guidare la selezione dei cluster oppure specificare che i valori vengano ignorati.  
  
    -   **Ignora colonna etichetta**: I valori nella colonna etichetta vengono ignorati e non vengono utilizzati nella creazione del modello.
  
    -   **Riempi valori mancanti**: I valori delle colonne dell'etichetta vengono utilizzati come funzionalità per facilitare la creazione dei cluster. Se in alcune righe manca un'etichetta, il valore viene imputato utilizzando altre funzionalità.  
  
    -   **Sovrascrivi dal più vicino al centro**: I valori della colonna etichetta vengono sostituiti con i valori di etichetta previsti, utilizzando l'etichetta del punto più vicina al centroide corrente.  

8.  Selezionare l'opzione **Normalizza funzionalità** se si desidera normalizzare le funzioni prima dell'allenamento.
  
     Se si applica la normalizzazione, prima del `[0,1]` training, i punti dati vengono normalizzati da MinMaxNormalizer.If you apply normalization, before training, the data points are normalized to by MinMaxNormalizer.

10. Eseguire il training del modello.  
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, aggiungere un set di dati con tag ed eseguire il training del modello utilizzando il modulo Train [Clustering Model.](train-clustering-model.md)  
  
## <a name="results"></a>Risultati

Dopo aver completato la configurazione e il training del modello, si dispone di un modello che è possibile usare per generare punteggi. Tuttavia, esistono diversi modi per eseguire il training del modello e diversi modi per visualizzare e usare i risultati:However, there are multiple ways to train the model, and multiple ways to view and use the results: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Acquisire uno snapshot del modello nell'area di lavoro

Se è stato utilizzato il modulo [Train Clustering Model:](train-clustering-model.md)

1. Selezionare il modulo **Train Clustering Model** e aprire il pannello di destra.

2. Selezionare la scheda **Output.** Selezionare l'icona **Registra set di dati** per salvare una copia del modello sottoposto a training.

Il modello salvato rappresenta i dati di training al momento del salvataggio del modello. Se in seguito si aggiornano i dati di training usati nella pipeline, il modello salvato non viene aggiornato. 

### <a name="see-the-clustering-result-dataset"></a>Vedere il set di dati dei risultati del clusteringSee the clustering result dataset 

Se è stato utilizzato il modulo [Train Clustering Model:](train-clustering-model.md)

1. Fare clic con il pulsante destro del mouse sul modulo **Train Clustering Model (Modello di clustering treno).**

2. Selezionare **Visualizza**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Suggerimenti per la generazione del modello di clustering migliore  

È noto che il processo di *seeding* utilizzato durante il clustering può influire in modo significativo sul modello. Seeding significa il posizionamento iniziale dei punti nei potenziali centriidi.
 
Ad esempio, se il set di dati contiene molti outlier e viene scelto un outlier per eseguire il seeding dei cluster, nessun altro punto dati si adatterebbe bene a tale cluster e il cluster potrebbe essere un singleton. Cioè, potrebbe avere solo un punto.  
  
È possibile evitare questo problema in un paio di modi:You can avoid this problem in a couple of ways:  
  
-   Modificare il numero di centriide e provare più valori di serie.  
  
-   Crea più modelli, variando la metrica o iterando di più.  
  
In generale, con i modelli di clustering, è possibile che una determinata configurazione si traduca in un set di cluster ottimizzato localmente. In altre parole, il set di cluster restituito dal modello si adatta solo ai punti dati correnti e non è generalizzabile per altri dati. Se si utilizza una configurazione iniziale diversa, il metodo K-means potrebbe trovare una configurazione diversa, superiore. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
