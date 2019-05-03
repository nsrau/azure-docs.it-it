---
title: 'Regressione rete neurale: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Neural Network Regression nel servizio Azure Machine Learning per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029311"
---
# <a name="neural-network-regression-module"></a>Modulo di regressione rete neurale

*Crea un modello di regressione usando un algoritmo neural network*  
  
 Categoria: Machine Learning / Initialize Model / Regression
  
## <a name="module-overview"></a>Panoramica del modulo  

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di regressione usando un algoritmo di rete neurale personalizzabile.
  
 Anche se le reti neurali sono ampiamente note per deep learning e modellazione di problemi complessi, ad esempio riconoscimento di immagini, sono facilmente soggetti a problemi di regressione. Qualsiasi classe di modelli statistici può definirsi rete neurale Se Usa pesi adattivi e può approssimativa funzioni non lineari dei relativi input. In questo modo la regressione rete neurale è adatta a problemi in cui un modello di regressione più tradizionale non può essere contenuta una soluzione.
  
 Regressione rete neurale è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag*, che include una colonna di etichetta. Poiché un modello di regressione viene stimato un valore numerico, la colonna di etichetta deve essere un tipo di dati numerici.  
  
 È possibile eseguirne il training, fornendo come input per il modello e il set di dati con tag [Train Model](./train-model.md). Il modello con training può quindi utilizzabile per stimare i valori per i nuovi esempi di input.  
  
## <a name="configure-neural-network-regression"></a>Configurare la regressione rete neurale 

Le reti neurali possono essere ampiamente personalizzate. Questa sezione descrive come creare un modello usando due metodi:
  
+ [Creare un modello di rete neurale tramite l'architettura predefinita](#bkmk_DefaultArchitecture)  
  
    Se si accetta l'architettura di rete neurale predefinito, usare il **proprietà** riquadro per impostare i parametri che controllano il comportamento della rete neurale, ad esempio il numero di nodi nel livello nascosto, velocità di apprendimento delle normalizzazione.

    Iniziare da qui se si ha familiarità con le reti neurali. Il modulo supporta numerose personalizzazioni, come pure sintonizzazione dei modelli, senza una conoscenza approfondita delle reti neurali. 

+ Definire un'architettura personalizzata per una rete neurale 

    Usare questa opzione se si desidera aggiungere livelli nascosti aggiuntivi o personalizzare completamente le funzioni di architettura, le connessioni e attivazione di rete.
    
    Questa opzione è consigliata se si ha una certa familiarità con le reti neurali. Utilizzare il linguaggio Net # per definire l'architettura di rete.  

##  <a name="bkmk_DefaultArchitecture"></a> Creare un modello di rete neurale tramite l'architettura predefinita
  
1.  Aggiungere il **Neural Network Regression** modulo all'esperimento nell'interfaccia. È possibile trovare questo modulo sotto **Machine Learning**, **inizializzare**, nelle **regressione** categoria. 
  
2. Indicare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Scegliere questa opzione se si conosce già la modalità con cui si desidera configurare il modello.  

3.  Nelle **specifica del livello nascosto**, selezionare **completamente connesso case**. Questa opzione Crea un modello usando l'architettura di rete neurale predefinito, che per un modello di regressione rete neurale, dispone di questi attributi:  
  
    + La rete ha un solo livello nascosto.
    + Il livello di output è completamente connesso al livello nascosto e livello nascosto è completamente connesso al livello di input.
    + Il numero di nodi nel livello nascosto può essere impostato dall'utente (valore predefinito è 100).  
  
    Poiché il numero di nodi nel livello di input è determinato dal numero di funzioni nei dati di training, in un modello di regressione può esistere un solo nodo nel livello di output.  
  
4. Per la **numero di nodi nascosti**, digitare il numero di nodi nascosti. Il valore predefinito è un livello nascosto con 100 nodi. (Questa opzione non è disponibile se si definisce un'architettura personalizzata tramite Net #.)
  
5.  Per la **velocità di apprendimento**, digitare un valore che definisce il passaggio eseguito a ogni iterazione, prima della correzione. Un valore maggiore per la velocità di apprendimento può causare il modello di convergenza più rapida, ma può eccedere i valori minimi locali.

6.  Per la **numero di iterazioni di apprendimento**, specificare il numero massimo di volte in cui l'algoritmo elabora i case di training.

7.  Per * * di apprendimento iniziale di ne vengono ponderati diametro, digitare un valore che determina i pesi dei nodi all'inizio del processo di apprendimento.

8.  Per la **il momentum**, digitare un valore da applicare durante l'apprendimento, come un peso nei nodi dalle iterazioni precedenti.

10. Selezionare l'opzione **Shuffle esempi**, per modificare l'ordine dei casi tra due iterazioni. Se si deseleziona questa opzione, casi vengono elaborati nello stesso ordine ogni volta che si esegue l'esperimento.
  
11. Per la **seed numeri Random**, è possibile digitare un valore da usare come valore di inizializzazione. Specificare un valore di inizializzazione valore è utile quando si desidera garantire la ripetibilità nelle esecuzioni dello stesso esperimento.
  
13. Connettere un set di dati di training e uno dei [moduli di training](module-reference.md): 
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare [Train Model](./train-model.md).  
  
   
14. Eseguire l'esperimento.  

## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare un riepilogo dei parametri del modello, con la funzionalità di pesi appreso dal training e altri parametri della rete neurale, fare doppio clic sull'output del [Train Model](./train-model.md)e selezionare **Visualize**.  

+ Per salvare uno snapshot del modello con training, fare doppio clic il **Trained model** di output e selezionare **Salva come modello sottoposto a training**. Questo modello non viene aggiornato nelle esecuzioni successive dello stesso esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 