---
title: 'Regressione della rete neurale: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione della rete neurale in Azure Machine Learning per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d5cc0f06e9fb95894df30af1322d47337f51e314
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466000"
---
# <a name="neural-network-regression-module"></a>Modulo di regressione della rete neurale

*Crea un modello di regressione usando un algoritmo di rete neurale*  
  
 Categoria: Machine Learning/inizializzare il modello/regressione
  
## <a name="module-overview"></a>Panoramica del modulo  

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
  
 Sebbene le reti neurali siano ampiamente note per l'uso nell'apprendimento avanzato e la modellazione di problemi complessi, come il riconoscimento delle immagini, sono facilmente adattabili ai problemi di regressione. Qualsiasi classe di modelli statistici può essere definita una rete neurale se usa pesi adattivi e può approssimarsi a funzioni non lineari degli input. Quindi, la regressione della rete neurale è adatta ai problemi in cui un modello di regressione più tradizionale non può adattarsi a una soluzione.
  
 La regressione della rete neurale è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag*, che include una colonna di etichetta. Poiché un modello di regressione stima un valore numerico, la colonna di etichetta deve essere un tipo di dati numerico.  
  
 Per eseguire il training del modello, è possibile fornire il modello e il set di dati con tag come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.  
  
## <a name="configure-neural-network-regression"></a>Configurare la regressione della rete neurale 

È possibile personalizzare ampiamente le reti neurali. Questa sezione descrive come creare un modello usando due metodi:
  
+ [Creare un modello di rete neurale usando l'architettura predefinita](#bkmk_DefaultArchitecture)  
  
    Se si accetta l'architettura di rete neurale predefinita, utilizzare il riquadro **Proprietà** per impostare i parametri che controllano il comportamento della rete neurale, ad esempio il numero di nodi nel livello nascosto, la velocità di apprendimento e la normalizzazione.

    Se non si ha familiarità con le reti neurali, iniziare da qui. Il modulo supporta molte personalizzazioni, nonché l'ottimizzazione del modello, senza una conoscenza approfondita delle reti neurali. 

+ Definire un'architettura personalizzata per una rete neurale 

    Utilizzare questa opzione se si desidera aggiungere ulteriori livelli nascosti oppure personalizzare completamente l'architettura di rete, le relative connessioni e le funzioni di attivazione.
    
    Questa opzione è consigliata se si ha già una certa familiarità con le reti neurali. Usare il linguaggio NET # per definire l'architettura di rete.  

##  <a name="bkmk_DefaultArchitecture"></a>Creare un modello di rete neurale usando l'architettura predefinita

1.  Aggiungere il modulo di **regressione della rete neurale** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **regressione** . 
  
2. Indicare come si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: scegliere questa opzione se si conosce già il modo in cui si vuole configurare il modello.  

3.  In **specifica livello nascosto**selezionare **case con connessione completa**. Questa opzione consente di creare un modello utilizzando l'architettura di rete neurale predefinita, per un modello di regressione della rete neurale, con questi attributi:  
  
    + La rete ha esattamente un livello nascosto.
    + Il livello di output è completamente connesso al livello nascosto e il livello nascosto è completamente connesso al livello di input.
    + Il numero di nodi nel livello nascosto può essere impostato dall'utente (il valore predefinito è 100).  
  
    Poiché il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training, in un modello di regressione può essere presente un solo nodo nel livello di output.  
  
4. Per **numero di nodi nascosti**, digitare il numero di nodi nascosti. Il valore predefinito è un livello nascosto con nodi 100. Questa opzione non è disponibile se si definisce un'architettura personalizzata usando NET #.
  
5.  Per la **velocità di apprendimento**, digitare un valore che definisce il passaggio effettuato a ogni iterazione, prima della correzione. Un valore più grande per la velocità di apprendimento può comportare una maggiore velocità di convergenza del modello, ma è possibile che venga superata la minima locale.

6.  Per **numero di iterazioni di apprendimento**specificare il numero massimo di volte in cui l'algoritmo elabora i case di training.

7.  Per * * il diametro iniziale dei pesi di apprendimento, digitare un valore che determina i pesi del nodo all'inizio del processo di apprendimento.

8.  Per **il momento**, digitare un valore da applicare durante l'apprendimento come peso nei nodi delle iterazioni precedenti.

10. Selezionare l'opzione, **esempi shuffle**, per modificare l'ordine dei case tra le iterazioni. Se si deseleziona questa opzione, i case vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.
  
11. Per il valore di **inizializzazione del numero casuale**, è possibile digitare facoltativamente un valore da usare come valore di inizializzazione. La specifica di un valore di inizializzazione è utile quando si desidera garantire la ripetibilità tra le esecuzioni della stessa pipeline.
  
13. Connettere un set di dati di training e uno dei [moduli di training](module-reference.md): 
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare [Train Model](./train-model.md).  
  
   
14. Eseguire la pipeline.  

## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare un riepilogo dei parametri del modello, insieme ai pesi della funzionalità appresi dal training e ad altri parametri della rete neurale, fare clic con il pulsante destro del mouse sull'output di [Train Model](./train-model.md)e selezionare **Visualize (Visualizza**).  

+ Per salvare uno snapshot del modello con training, fare clic con il pulsante destro del mouse sull'output del **modello** sottoposto a training e selezionare **Salva come modello con training**. Questo modello non viene aggiornato in esecuzioni successive della stessa pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 