---
title: 'Rete neurale a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo a due classi Neural Network nel servizio Azure Machine Learning per creare un modello di rete neurale che può essere usato per stimare una destinazione con solo due valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8f38a7b7086e5023eb63e94363301ac5277f7e7c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693605"
---
# <a name="two-class-neural-network-module"></a>Modulo di rete neurale a due classi

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di rete neurale che può essere usato per stimare una destinazione con solo due valori.

La classificazione con reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag*, che include una colonna di etichetta. Ad esempio, è possibile usare questo modello di rete neurale per stimare i risultati binari, ad esempio se un paziente ha una determinata malattia o se un computer potrebbe non riuscire entro un intervallo di tempo specificato.  

Dopo aver definito il modello, eseguirne il training fornendo un set di dati con tag e il modello come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi input.

### <a name="more-about-neural-networks"></a>Altre informazioni sulle reti neurali

Una rete neurale è un set di livelli interconnessi. Gli input sono il primo livello e sono connessi a un livello di output da un grafico aciclici costituito da bordi ponderati e nodi.

Tra i livelli di input e di output è possibile inserire più livelli nascosti. La maggior parte delle attività predittive può essere eseguita facilmente con solo uno o pochi livelli nascosti. Tuttavia, la ricerca recente ha dimostrato che le reti neurali profonde (DNN) con molti livelli possono essere efficaci in attività complesse, ad esempio il riconoscimento vocale o di immagine. I livelli successivi vengono usati per modellare livelli crescenti di profondità semantica.

La relazione tra input e output viene appresa dal training della rete neurale sui dati di input. La direzione del grafico prosegue dagli input attraverso il livello nascosto e il livello di output. Tutti i nodi di un livello sono connessi con i bordi ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un input specifico, viene calcolato un valore in ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del livello precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.
  
## <a name="how-to-configure"></a>Come configurare

1.  Aggiungere il modulo a **due classi Neural Network** alla pipeline. È possibile trovare questo modulo in **Machine Learning**, **Initialize**, nella categoria **classificazione** .  
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: scegliere questa opzione se si conosce già il modo in cui si vuole configurare il modello.  

3.  Per **specifica del livello nascosto**, selezionare il tipo di architettura di rete da creare.  
  
    -   **Caso completamente connesso**: usa l'architettura di rete neurale predefinita, definita per le reti neurali a due classi come indicato di seguito:
  
        -   Dispone di un livello nascosto.
  
        -   Il livello di output è completamente connesso al livello nascosto e il livello nascosto è completamente connesso al livello di input.
  
        -   Il numero di nodi nel livello di input è uguale al numero di funzioni nei dati di training.
  
        -   Il numero di nodi nel livello nascosto viene impostato dall'utente. Il valore predefinito è 100.
  
        -   Il numero di nodi è uguale al numero di classi. Per una rete neurale a due classi, questo significa che tutti gli input devono essere mappati a uno dei due nodi nel livello di output.

5.  Per la **velocità di apprendimento**, definire le dimensioni del passaggio effettuato a ogni iterazione, prima della correzione. Un valore più grande per la velocità di apprendimento può comportare una maggiore velocità di convergenza del modello, ma è possibile che venga superata la minima locale.

6.  Per **numero di iterazioni di apprendimento**specificare il numero massimo di volte in cui l'algoritmo deve elaborare i case di training.

7.  Per **il diametro iniziale dei pesi di apprendimento**, specificare i pesi del nodo all'inizio del processo di apprendimento.

8.  Per **il momento**, specificare un peso da applicare durante l'apprendimento ai nodi dalle iterazioni precedenti  

10. Selezionare l'opzione **esempi shuffle** per mescolare i casi tra le iterazioni. Se si deseleziona questa opzione, i case vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.
  
11. Per il valore di **inizializzazione numerico casuale**, digitare un valore da usare come valore di inizializzazione.
  
     La specifica di un valore di inizializzazione è utile quando si desidera garantire la ripetibilità tra le esecuzioni della stessa pipeline.  In caso contrario, viene usato un valore clock di sistema come valore di inizializzazione, che può causare risultati leggermente diversi ogni volta che si esegue la pipeline.
  
13. Aggiungere un set di dati con tag alla pipeline e connettere uno dei [moduli di training](module-reference.md).  
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .  
  
14. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare un riepilogo dei parametri del modello, insieme ai pesi della funzionalità appresi dal training e ad altri parametri della rete neurale, fare clic con il pulsante destro del mouse sull'output di [Train Model](./train-model.md)e selezionare **Visualize (Visualizza**).  

+ Per salvare uno snapshot del modello con training, fare clic con il pulsante destro del mouse sull'output del **modello** sottoposto a training e selezionare **Salva come modello con training**. Questo modello non viene aggiornato in esecuzioni successive della stessa pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
