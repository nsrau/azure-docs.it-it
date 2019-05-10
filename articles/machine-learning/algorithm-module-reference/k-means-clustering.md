---
title: 'Clustering K-Means: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di Clustering K-Means nel servizio di Azure Machine Learning per eseguire il training di modelli di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7e9b7c8f2cf86245322679198b84b50d2c5edce8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464676"
---
# <a name="module-k-means-clustering"></a>Modulo: Clustering K-means

Questo articolo descrive come usare il *Clustering K-Means* modulo in Azure Machine Learning Studio per creare un modello di clustering K-means senza training. 
 
K-means è uno dei più semplici e più noto *senza supervisione* algoritmi di apprendimento automatico. È possibile usare l'algoritmo per un'ampia gamma di attività di machine learning, ad esempio: 

* [Rilevare dati anomali](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Clustering di documenti di testo.
* L'analisi dei set di dati prima di usare altri metodi di classificazione o regressione. 

Per creare un modello di clustering, è:

* Aggiungere questo modulo nell'esperimento.
* Connettere un set di dati.
* Impostare i parametri, ad esempio il numero di cluster previsto, la metrica di distanza da utilizzare nella creazione di cluster e così via. 
  
Dopo aver configurato gli iperparametri di modulo, si connette il modello senza training per il [Train Clustering Model](train-clustering-model.md). Poiché l'algoritmo K-means è un metodo di apprendimento non supervisionato, una colonna di etichetta è facoltativa. 

+ Se i dati includono un'etichetta, è possibile usare i valori delle etichette per guidare selezione dei cluster e ottimizzare il modello. 

+ Se i dati non dispongono di alcuna etichetta, l'algoritmo crea i cluster che rappresenta le categorie possibili, basate esclusivamente sui dati.  

##  <a name="understand-k-means-clustering"></a>Le funzioni di clustering K-means
 
In generale, il clustering utilizza tecniche iterative per raggruppare i casi in un set di dati in cluster che possiedono caratteristiche simili. Questi raggruppamenti sono utili per l'esplorazione dei dati, che identifica le anomalie nei dati e alla fine per l'esecuzione di stime. I modelli di clustering possono anche identificare le relazioni in un set di dati che è non possibile derivare logicamente dall'osservazione di esplorazione o semplice. Per questi motivi, clustering viene spesso usato nelle fasi iniziali di attività, di machine learning per esplorare i dati e individuare correlazioni impreviste.  
  
 Quando si configura un modello di clustering utilizzando il metodo K-means, è necessario specificare un numero di destinazione *k* che indica il numero della *centroidi* desiderati nel modello. Il centro è un punto che è rappresentativo di ogni cluster. L'algoritmo K-means assegna ogni punto dati in ingresso a uno dei cluster, riducendo al minimo la somma dei quadrati all'interno di cluster. 
 
Durante l'elaborazione dati di training, l'algoritmo K-means inizia con un set iniziale di centroidi scelti in modo casuale. Centroidi fungono da punti di partenza per i cluster, e si applica l'algoritmo di Lloyd per perfezionare in modo iterativo le posizioni. L'algoritmo K-means interrompe la creazione e il perfezionamento dei cluster quando soddisfa uno o più delle seguenti condizioni:  
  
-   I centroidi si stabilizzano, vale a dire che le assegnazioni del cluster per i singoli punti non cambiano più e l'algoritmo è giunto a una soluzione.  
  
-   L'algoritmo ha completato l'esecuzione del numero specificato di iterazioni.  
  
 Dopo aver completato la fase di training, si utilizza il [assegna dati ai cluster](assign-data-to-clusters.md) module per assegnare nuovi casi a uno dei cluster che è stato individuato tramite l'algoritmo K-means. Assegnazione di cluster è eseguire calcolando la distanza tra il nuovo case e il centro di ogni cluster. Ogni nuovo case viene assegnata al cluster con il centroide più vicino.  

## <a name="configure-the-k-means-clustering-module"></a>Configurare il modulo di Clustering K-Means
  
1.  Aggiungere il **Clustering K-Means** modulo nell'esperimento.  
  
2.  Per specificare come si desidera che il modello di esecuzione del training, selezionare la **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Se si conoscono esattamente i parametri da usare nel modello di clustering, è possibile definire un set specifico di valori come argomenti.  
  
3.  Per la **numero di Centroidi**, digitare il numero di cluster che devono iniziare con l'algoritmo.  
  
     Il modello non è garantito per produrre esattamente questo numero di cluster. L'algoritmo inizia con questo numero di punti dati ed esegue l'iterazione per trovare la configurazione ottimale.  
  
4.  La proprietà **inizializzazione** viene usato per specificare l'algoritmo utilizzato per definire la configurazione iniziale del cluster.  
  
    -   **First N**: Un numero iniziale di punti dati venga scelto dal set di dati e usato come medie iniziali. 
    
         Questo metodo viene chiamato anche il *metodo di Forgy*.  
  
    -   **Random**: L'algoritmo inserisce in modo casuale un punto dati in un cluster e quindi calcola la media iniziale come centroide del cluster in modo casuale punti assegnati. 

         Questo metodo viene chiamato anche il *partizione casuale* (metodo).  
  
    -   **K-Means + +**: Questo è il metodo predefinito per l'inizializzazione di cluster.  
  
         Il **K-means + +** algoritmo è stato proposto nel 2007 da David Arthur e Sergei Vassilvitskii per evitare un clustering per l'algoritmo K-means standard. **K-means + +** migliora K-Means standard usando un metodo diverso per la scelta di centri dei cluster iniziali.  
  
    
5.  Per **seed numeri Random**, facoltativamente, digitare un valore da usare come valore di inizializzazione per l'inizializzazione del cluster. Questo valore può avere un impatto significativo sulla selezione del cluster.  
  
6.  Per la **metrica**, scegliere la funzione da utilizzare per misurare la distanza tra vettori di cluster o tra i nuovi punti dati e il centroide scelto in modo casuale. Azure Machine Learning supporta le metriche di distanza del cluster seguenti:  
  
    -   **Euclidean**: La distanza euclidea viene comunemente usata come una misura della dispersione dei cluster per il clustering K-means. Questa metrica è preferibile perché riduce al minimo la distanza media tra i punti e i centroidi.
  
7.  Per la **iterazioni**, digitare il numero di volte in cui l'algoritmo deve scorrere i dati di training prima che consente di finalizzare la selezione di centroidi.  
  
     È possibile modificare questo parametro per la precisione di bilanciamento rispetto al tempo di training.  
  
8.  Per la **etichetta di modalità di assegnazione**, scegliere un'opzione che specifica come una colonna di etichetta, se è presente nel set di dati, debba essere gestita.  
  
     Poiché il clustering K-means è un apprendimento senza supervisione di metodo, le etichette sono facoltative. Tuttavia, se il set di dati ha già una colonna di etichetta, è possibile usare tali valori come guida per la selezione dei cluster, oppure è possibile specificare che i valori ignorati.  
  
    -   **Ignorare la colonna etichetta**: I valori nella colonna di etichetta vengono ignorati e non vengono utilizzati nella compilazione del modello.
  
    -   **Immettere i valori mancanti**: I valori della colonna etichetta sono usati come funzionalità per la creazione di cluster. Se tutte le righe manca un'etichetta, il valore è attribuito con altre funzionalità.  
  
    -   **Sovrascrivere dal più vicino al centro**: I valori della colonna etichetta vengono sostituiti con valori di etichetta stimata, usando l'etichetta del punto più vicino al centro corrente.  

8.  Selezionare il **funzioni vengono normalizzate** opzione se si desidera normalizzare le funzionalità prima del training.
  
     Se si applica la normalizzazione, prima del training, i punti dati vengono normalizzati a `[0,1]` da MinMaxNormalizer.

10. Il training del modello.  
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, aggiungere un set di dati con tag e il training del modello utilizzando il [Train Clustering Model](train-clustering-model.md) modulo.  
  
### <a name="results"></a>Risultati

Dopo aver completato la configurazione e di training del modello, è necessario un modello che è possibile usare per generare punteggi. Tuttavia, esistono diversi modi per il training del modello e diversi modi per visualizzare e usare i risultati: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Acquisire uno snapshot del modello nell'area di lavoro

Se è stata usata la [Train Clustering Model](train-clustering-model.md) modulo:

1. Fare doppio clic il **Train Clustering Model** modulo.

2. Selezionare **Trained model**, quindi selezionare **Save as Trained Model**.

Il modello salvato rappresenta i dati di training al momento che è stato salvato il modello. Se si aggiornano in un secondo momento i dati di training usati nell'esperimento, non verrà aggiornato il modello salvato. 

#### <a name="see-the-clustering-result-dataset"></a>Vedere il set di risultati del clustering 

Se è stata usata la [Train Clustering Model](train-clustering-model.md) modulo:

1. Fare doppio clic il **Train Clustering Model** modulo.

2. Selezionare **set di dati risultati**, quindi selezionare **Visualize**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Suggerimenti per la generazione di migliore modello di clustering  

È noto che il *SEED* processo che viene usato durante il clustering può influire in modo significativo il modello. Il seeding, significa che la posizione iniziale di punti in centroidi potenziali.
 
Ad esempio, se il set di dati contiene molti outlier e viene scelto un outlier per effettuare il seeding dei cluster, altri punti dati non adatta bene con tale cluster e il cluster potrebbe essere un singleton. Vale a dire, potrebbe prevede un solo punto.  
  
È possibile evitare questo problema in due modi:  
  
-   Modificare il numero di centroidi e provare a più valori di inizializzazione.  
  
-   Creare più modelli, variare la metrica o l'iterazione di informazioni.  
  
In generale, con i modelli di clustering, è possibile che una determinata configurazione comporterà un set di cluster ottimizzato in locale. In altre parole, il set di cluster che viene restituito dal modello adatta solo i punti dati correnti e non generalizzabile ad altri dati. Se si usa un'altra configurazione iniziale, il metodo K-means potrebbe trovare una configurazione diversa, addirittura superiore. 
