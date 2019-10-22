---
title: 'Clustering K-means: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di clustering K-means nel servizio Azure Machine Learning per eseguire il training di modelli di clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: bb39a57f565a542e5106b7ba14e58bc39041f53d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693104"
---
# <a name="module-k-means-clustering"></a>Modulo: clustering K-means

Questo articolo descrive come usare il modulo di *clustering k-means* in Azure Machine Learning Studio per creare un modello di clustering k-means non sottoposto a training. 
 
K-means è uno dei più semplici e noti algoritmi di apprendimento senza *supervisione* . È possibile usare l'algoritmo per un'ampia gamma di attività di Machine Learning, ad esempio: 

* [Rilevamento di dati anomali](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Clustering di documenti di testo.
* Analisi dei set di impostazioni prima di utilizzare altri metodi di classificazione o regressione. 

Per creare un modello di clustering, è necessario:

* Aggiungere questo modulo alla pipeline.
* Connettere un set di dati.
* Impostare i parametri, ad esempio il numero di cluster previsti, la metrica di distanza da usare per la creazione dei cluster e così via. 
  
Dopo aver configurato gli iperparametri del modulo, connettere il modello senza training al [modello Train clustering](train-clustering-model.md). Poiché l'algoritmo K-means è un metodo di apprendimento non supervisionato, una colonna Label è facoltativa. 

+ Se i dati includono un'etichetta, è possibile usare i valori delle etichette per guidare la selezione dei cluster e ottimizzare il modello. 

+ Se i dati non hanno etichetta, l'algoritmo crea cluster che rappresentano le categorie possibili, esclusivamente in base ai dati.  

##  <a name="understand-k-means-clustering"></a>Comprendere il clustering K-means
 
In generale, il clustering usa tecniche iterative per raggruppare i case di un set di dati in cluster con caratteristiche simili. Questi raggruppamenti sono utili per esplorare i dati, identificare le anomalie nei dati e infine per eseguire stime. I modelli di clustering consentono inoltre di identificare le relazioni in un set di dati che potrebbero non essere derivate logicamente tramite esplorazione o semplice osservazione. Per questi motivi, il clustering viene spesso usato nelle fasi iniziali delle attività di Machine Learning, per esplorare i dati e individuare correlazioni impreviste.  
  
 Quando si configura un modello di clustering utilizzando il metodo K-means, è necessario specificare un numero di destinazione *k* che indichi il numero di *centroidi* desiderati nel modello. Il baricentro è un punto rappresentativo di ogni cluster. L'algoritmo K-means assegna ogni punto dati in ingresso a uno dei cluster riducendo al minimo la somma dei quadrati all'interno del cluster. 
 
Quando elabora i dati di training, l'algoritmo K-means inizia con un set iniziale di centroidi scelti in modo casuale. Centroidi vengono usati come punti di partenza per i cluster e applicano l'algoritmo di Lloyd per perfezionare in modo iterativo le rispettive posizioni. L'algoritmo K-means interrompe la compilazione e il perfezionamento dei cluster quando soddisfa una o più di queste condizioni:  
  
-   Il centroidi si stabilizza, ovvero le assegnazioni del cluster per i singoli punti non cambiano più e l'algoritmo è convergente in una soluzione.  
  
-   L'algoritmo ha completato l'esecuzione del numero specificato di iterazioni.  
  
 Dopo aver completato la fase di training, è possibile usare il modulo [assign data to Clusters](assign-data-to-clusters.md) per assegnare nuovi case a uno dei cluster trovati usando l'algoritmo K-means. Per eseguire l'assegnazione del cluster, calcolare la distanza tra il nuovo case e il baricentro di ogni cluster. Ogni nuovo case viene assegnato al cluster con il baricentro più vicino.  

## <a name="configure-the-k-means-clustering-module"></a>Configurare il modulo di clustering K-means
  
1.  Aggiungere il modulo di **clustering K-means** alla pipeline.  
  
2.  Per specificare il modo in cui si desidera eseguire il training del modello, selezionare l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: se si conoscono i parametri esatti da usare nel modello di clustering, è possibile specificare come argomenti un set specifico di valori.  
  
3.  Per **numero di centroidi**, digitare il numero di cluster per i quali si vuole iniziare l'algoritmo.  
  
     Non è garantito che il modello produca esattamente questo numero di cluster. L'algoritmo inizia con questo numero di punti dati e scorre per trovare la configurazione ottimale.  
  
4.  L' **inizializzazione** delle proprietà viene utilizzata per specificare l'algoritmo utilizzato per definire la configurazione iniziale del cluster.  
  
    -   **First N**: un numero iniziale di punti dati viene scelto dal set di dati e usato come mezzo iniziale. 
    
         Questo metodo viene chiamato anche *metodo false*.  
  
    -   **Random**: l'algoritmo inserisce in modo casuale un punto dati in un cluster e quindi calcola il valore medio iniziale per essere il baricentro dei punti assegnati in modo casuale del cluster. 

         Questo metodo viene chiamato anche metodo di *partizione casuale* .  
  
    -   **K-means + +** : questo è il metodo predefinito per l'inizializzazione dei cluster.  
  
         L'algoritmo **k-means + +** è stato proposto in 2007 da David Arthur e Sergei vassilvitski per evitare un clustering insufficiente nell'algoritmo k-means standard. **K-means + +** migliora la modalità k standard usando un metodo diverso per la scelta dei centri cluster iniziali.  
  
    
5.  Per il valore di inizializzazione **numerico casuale**, digitare facoltativamente un valore da usare come valore di inizializzazione per l'inizializzazione del cluster. Questo valore può avere un effetto significativo sulla selezione del cluster.  
  
6.  Per **metrica**, scegliere la funzione da usare per misurare la distanza tra i vettori del cluster o tra i nuovi punti dati e il baricentro scelto in modo casuale. Azure Machine Learning supporta le metriche di distanza del cluster seguenti:  
  
    -   **Euclideo**: la distanza euclidea viene comunemente usata come misura della dispersione del cluster per il clustering K-means. Questa metrica è preferibile perché riduce al minimo la distanza media tra punti e centroidi.
  
7.  Per le **iterazioni**, digitare il numero di volte in cui l'algoritmo deve eseguire l'iterazione sui dati di training prima di finalizzare la selezione di centroidi.  
  
     È possibile modificare questo parametro per bilanciare l'accuratezza rispetto al tempo di training.  
  
8.  Per la **modalità assegnazione etichetta**, scegliere un'opzione che specifichi il modo in cui deve essere gestita una colonna di etichetta, se è presente nel set di dati.  
  
     Poiché il clustering K-means è un metodo di apprendimento automatico non supervisionato, le etichette sono facoltative. Tuttavia, se il set di dati contiene già una colonna di etichetta, è possibile usare tali valori per guidare la selezione dei cluster oppure è possibile specificare che i valori vengano ignorati.  
  
    -   **Ignora colonna etichetta**: i valori nella colonna etichetta vengono ignorati e non vengono utilizzati nella compilazione del modello.
  
    -   **Fill Missing Values**: i valori della colonna Label vengono usati come funzionalità per facilitare la compilazione dei cluster. Se nelle righe manca un'etichetta, il valore viene imputato utilizzando altre funzionalità.  
  
    -   **Sovrascrivi dal più vicino al centro**: i valori delle colonne dell'etichetta vengono sostituiti con i valori delle etichette stimati, usando l'etichetta del punto più vicino al baricentro corrente.  

8.  Selezionare l'opzione **normalizza funzionalità** se si vuole normalizzare le funzionalità prima del training.
  
     Se si applica la normalizzazione, prima del training, i punti dati vengono normalizzati per `[0,1]` da MinMaxNormalizer.

10. Eseguire il training del modello.  
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, aggiungere un set di dati con tag ed eseguire il training del modello usando il modulo [Train clustering Model](train-clustering-model.md) .  
  
### <a name="results"></a>Risultati

Una volta completata la configurazione e il training del modello, si dispone di un modello che è possibile utilizzare per generare i punteggi. Tuttavia, esistono diversi modi per eseguire il training del modello e diversi modi per visualizzare e utilizzare i risultati: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Acquisire uno snapshot del modello nell'area di lavoro

Se è stato usato il modulo [Train clustering Model](train-clustering-model.md) :

1. Fare clic con il pulsante destro del mouse sul modulo **Train clustering Model** .

2. Selezionare **modello**sottoposto a training e quindi selezionare **Salva come modello con training**.

Il modello salvato rappresenta i dati di training nel momento in cui è stato salvato il modello. Se successivamente si aggiornano i dati di training usati nella pipeline, il modello salvato non viene aggiornato. 

#### <a name="see-the-clustering-result-dataset"></a>Vedere il set di dati dei risultati del clustering 

Se è stato usato il modulo [Train clustering Model](train-clustering-model.md) :

1. Fare clic con il pulsante destro del mouse sul modulo **Train clustering Model** .

2. Selezionare **results DataSet**, quindi selezionare **Visualize**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Suggerimenti per la generazione del modello di clustering migliore  

È noto che il processo di *seeding* utilizzato durante il clustering può influire in modo significativo sul modello. Il seeding indica la posizione iniziale dei punti in centroidi potenziali.
 
Se, ad esempio, il set di dati contiene molti outlier e viene scelto un outlier per eseguire il seeding dei cluster, nessun altro punto dati si adatterà correttamente a tale cluster e il cluster potrebbe essere un singleton. Ovvero, potrebbe avere un solo punto.  
  
È possibile evitare questo problema in due modi:  
  
-   Modificare il numero di centroidi e provare a usare più valori di inizializzazione.  
  
-   Creazione di più modelli, variazione della metrica o iterazione.  
  
In generale, con i modelli di clustering, è possibile che una determinata configurazione provochi un set di cluster ottimizzato localmente. In altre parole, il set di cluster restituito dal modello si adatta solo ai punti dati correnti e non è generalizzabile con altri dati. Se si usa una configurazione iniziale diversa, il metodo K-means potrebbe trovare una configurazione diversa, forse superiore. 
