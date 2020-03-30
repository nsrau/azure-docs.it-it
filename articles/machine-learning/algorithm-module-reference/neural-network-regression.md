---
title: 'Regressione di rete neurale: riferimento al moduloNeural Network Regression: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Regressione di rete neurale in Azure Machine Learning per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456099"
---
# <a name="neural-network-regression-module"></a>Modulo di regressione della rete neurale

*Crea un modello di regressione usando un algoritmo di rete neurale*  
  
 Categoria: Machine Learning / Inizializza modello / Regressione
  
## <a name="module-overview"></a>Panoramica del modulo  

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
  
 Anche se le reti neurali sono ampiamente note per l'uso di problemi complessi di formazione e modellazione, ad esempio riconoscimento di immagini, sono facilmente soggetti a problemi di regressione. Qualsiasi classe di modelli statistici può definirsi rete neurale se usa pesi adattivi e può definire in maniera approssimativa funzioni non lineari dei relativi input. Pertanto la regressione della rete neurale è adatta a problemi in cui un modello di regressione più tradizionale non può contenere una soluzione.
  
 La regressione della rete neurale è un metodo di apprendimento supervisionato e pertanto richiede un set di *dati con tag,* che include una colonna label. Poiché un modello di regressione effettua stime su un valore numerico, la colonna di etichetta deve essere un tipo di dati numerico.  
  
 È possibile eseguire il training del modello fornendo il modello e il set di dati con tag come input per il training [del modello](./train-model.md). Il modello sottoposto a training può quindi essere utilizzato per stimare i valori per i nuovi esempi di input.  
  
## <a name="configure-neural-network-regression"></a>Configurare la regressione della rete neuraleConfigure Neural Network Regression 

Le reti neurali possono essere ampiamente personalizzate. In questa sezione viene descritto come creare un modello utilizzando due metodi:This section describes how to create a model using two methods:
  
+ [Creare un modello di rete neurale usando l'architettura predefinitaCreate a neural network model using the default architecture](#bkmk_DefaultArchitecture)  
  
    Se si accetta l'architettura di rete neurale predefinita, utilizzare il riquadro **Proprietà** per impostare i parametri che controllano il comportamento della rete neurale, ad esempio il numero di nodi nel livello nascosto, la velocità di apprendimento e la normalizzazione.

    Iniziare da qui se non si ha familiarità con le reti neurali. Il modulo supporta molte personalizzazioni, nonché l'ottimizzazione del modello, senza una conoscenza approfondita delle reti neurali. 

+ Definire un'architettura personalizzata per una rete neuraleDefine a custom architecture for a neural network 

    Utilizzare questa opzione se si desidera aggiungere livelli nascosti aggiuntivi o personalizzare completamente l'architettura di rete, le relative connessioni e le funzioni di attivazione.
    
    Questa opzione è migliore se si ha già un po' di familiarità con le reti neurali. Per definire l'architettura di rete, è possibile utilizzare il linguaggio Net.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Creare un modello di rete neurale usando l'architettura predefinitaCreate a neural network model using the default architecture

1.  Aggiungere il modulo **Regressione di rete neurale** alla pipeline nella finestra di progettazione. Questo modulo è disponibili in **Machine Learning**, **Initialize**, nella categoria **Regressione.** 
  
2. Indicare come si desidera eseguire il training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: Scegliere questa opzione se si conosce già come si desidera configurare il modello.

    -   **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.   

3.  In **Specifica layer nascosto**, selezionare **Maiuscole/minuscole connesse**. Questa opzione consente di creare un modello usando l'architettura di rete neurale predefinita, che per un modello di regressione della rete neurale, ha questi attributi:This option creates a model using the default neural network architecture, which for a neural network regression model, has these attributes:  
  
    + La rete ha esattamente un livello nascosto.
    + Il livello di output è completamente connesso al livello nascosto e il livello nascosto è completamente connesso al livello di input.
    + Il numero di nodi nel livello nascosto può essere impostato dall'utente (il valore predefinito è 100).  
  
    Poiché il numero di nodi nel layer di input è determinato dal numero di feature nei dati di training, in un modello di regressione può essere presente un solo nodo nel layer di output.  
  
4. Per **Numero di nodi nascosti**digitare il numero di nodi nascosti. L'impostazione predefinita è un livello nascosto con 100 nodi. (Questa opzione non è disponibile se si definisce un'architettura personalizzata utilizzando Net .)
  
5.  Per **Velocità di apprendimento**, digitare un valore che definisce il passaggio eseguito a ogni iterazione, prima della correzione. Un valore superiore per la velocità di apprendimento può provocare una convergenza più rapida del modello, ma può eccedere i valori minimi locali.

6.  Per **Numero di iterazioni di apprendimento**, specificare il numero massimo di volte in cui l'algoritmo elabora i case di training.


8.  Per **Momentum**, digitare un valore da applicare durante l'apprendimento come peso sui nodi delle iterazioni precedenti.

10. Selezionare l'opzione **Shuffle examples**per modificare l'ordine dei casi tra le iterazioni. Se si deseleziona questa opzione, i casi vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.
  
11. Per **Valore di serie con numero casuale**, è possibile digitare facoltativamente un valore da utilizzare come valore di serie. Specificare un valore di seeding è utile quando si vuole garantire la ripetibilità tra le esecuzioni della stessa pipeline.
  
13. Collegare un set di dati di formazione e uno dei moduli di [formazione:](module-reference.md) 
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare Train [Model](./train-model.md).  
  
   
14. Inviare la pipeline.  

## <a name="results"></a>Risultati

Al termine dell'allenamento:

- Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 