---
title: 'Two-Class Neural Network: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class Neural Network nel servizio Azure Machine Learning per creare un modello di rete neurale utilizzabile per stimare una destinazione contenente solo due valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029221"
---
# <a name="two-class-neural-network-module"></a>Modulo Two-Class Neural Network

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di rete neurale utilizzabile per stimare una destinazione contenente solo due valori.

Classificazione con reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag*, che include una colonna di etichetta. Ad esempio, è possibile utilizzare questo modello di rete neurale per stimare risultati binari, ad esempio dispone di una determinata malattia o meno un incontro del paziente, o se un computer è potrebbe non riuscire in un intervallo di tempo specificato.  

Dopo aver definito il modello, eseguire il training, fornendo un set di dati con tag e il modello come input per [Train Model](./train-model.md). Il modello con training può quindi utilizzabile per stimare i valori per i nuovi input.

### <a name="more-about-neural-networks"></a>Informazioni sulle reti neurali

Una rete neurale è un set di livelli interconnessi. Gli input sono il primo livello e sono connessi a un livello di output da un grafo aciclico diretto, costituito da nodi e bordi ponderati.

Tra i livelli di input e outpui è possibile inserire più livelli nascosti. Più attività di stima può essere eseguita facilmente con solo una o più livelli nascosti. Tuttavia, ricerche recenti hanno evidenziato che le reti neurali profonde (DNN) con molti livelli possono risultare efficaci in attività complesse, quali il riconoscimento vocale o di immagini. I livelli successivi vengono usati per modellare livelli con maggiore profondità semantica.

La relazione tra input e output verrà acquisita dal training della rete neurale sui dati di input. La direzione del grafo procede dagli input attraverso il livello nascosto e livello di output. Tutti i nodi in un livello sono connessi tramite vertici ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un particolare input, viene calcolato un valore in ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del livello precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.
  
## <a name="how-to-configure"></a>Come configurare

1.  Aggiungere il **Two-Class Neural Network** modulo nell'esperimento. È possibile trovare questo modulo sotto **Machine Learning**, **inizializzare**, nelle **classificazione** categoria.  
  
2.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Scegliere questa opzione se si conosce già la modalità con cui si desidera configurare il modello.  

3.  Per la **nascosta specifica del livello**, selezionare il tipo di architettura di rete da creare.  
  
    -   **Completamente connesso case**: Usa l'architettura di rete neurale predefinito, definito come indicato di seguito per le reti neurali a due classi:
  
        -   Ha un livello nascosto.
  
        -   Il livello di output è completamente connesso al livello nascosto e livello nascosto è completamente connesso al livello di input.
  
        -   Il numero di nodi nel livello di input è uguale al numero di funzionalità nei dati di training.
  
        -   Il numero di nodi nel livello nascosto è impostato dall'utente. Il valore predefinito è 100.
  
        -   Il numero di nodi è uguale al numero di classi. Per una rete neurale a due classi, ciò significa che tutti gli input devono eseguire il mapping a uno dei due nodi nel livello di output.

5.  Per la **velocità di apprendimento**, definire le dimensioni del passaggio eseguito a ogni iterazione, prima della correzione. Un valore maggiore per la velocità di apprendimento può causare il modello di convergenza più rapida, ma può eccedere i valori minimi locali.

6.  Per la **numero di iterazioni di apprendimento**, specificare il numero massimo di volte in cui l'algoritmo deve elaborare i case di training.

7.  Per la **consente di ponderare l'apprendimento iniziale diametro**, specificare i pesi dei nodi all'inizio del processo di apprendimento.

8.  Per la **il momentum**, specificare un peso da applicare durante l'apprendimento ai nodi dalle iterazioni precedenti  

10. Selezionare il **Shuffle esempi** opzione per riprodurre casualmente casi tra due iterazioni. Se si deseleziona questa opzione, casi vengono elaborati nello stesso ordine ogni volta che si esegue l'esperimento.
  
11. Per la **seed numeri Random**, digitare un valore da usare come valore di inizializzazione.
  
     Specificare un valore di inizializzazione valore è utile quando si desidera garantire la ripetibilità nelle esecuzioni dello stesso esperimento.  In caso contrario, un valore dell'orologio di sistema viene utilizzato come valore di inizializzazione, che può causare risultati leggermente diversi ogni volta che si esegue l'esperimento.
  
13. Aggiungere un set di dati con tag all'esperimento e connettere tra il [moduli di training](module-reference.md).  
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](train-model.md) modulo.  
  
14. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare un riepilogo dei parametri del modello, con la funzionalità di pesi appreso dal training e altri parametri della rete neurale, fare doppio clic sull'output del [Train Model](./train-model.md)e selezionare **Visualize**.  

+ Per salvare uno snapshot del modello con training, fare doppio clic il **Trained model** di output e selezionare **Salva come modello sottoposto a training**. Questo modello non viene aggiornato nelle esecuzioni successive dello stesso esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 
