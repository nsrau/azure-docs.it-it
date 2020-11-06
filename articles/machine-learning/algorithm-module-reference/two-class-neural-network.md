---
title: 'Two-Class rete neurale: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Two-Class Neural Network in Azure Machine Learning per creare un classificatore binario.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421159"
---
# <a name="two-class-neural-network-module"></a>Modulo di rete neurale Two-Class

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per creare un modello di rete neurale da usare per stimare una destinazione contenente solo due valori.

La classificazione con reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un *set di dati con tag* , che include una colonna di etichetta. Ad esempio, è possibile usare questo modello di rete neurale per stimare i risultati binari, ad esempio se un paziente ha una determinata malattia o se un computer potrebbe non riuscire entro un intervallo di tempo specificato.  

Dopo aver definito il modello, eseguirne il training fornendo un set di dati con tag e il modello come input per il [training del modello](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi input.

### <a name="more-about-neural-networks"></a>Altre informazioni sulle reti neurali

Una rete neurale è un set di livelli interconnessi. Gli input sono il primo livello e sono connessi a un livello di output da un grafico aciclici costituito da bordi ponderati e nodi.

Tra i livelli di input e di output è possibile inserire più livelli nascosti. Con uno solo o più livelli nascosti, è possibile eseguire facilmente la maggior parte delle attività di stima. Tuttavia, la ricerca recente ha dimostrato che le reti neurali profonde (DNN) con molti livelli possono essere efficaci in attività complesse, ad esempio il riconoscimento vocale o di immagine. I livelli successivi vengono usati per modellare livelli crescenti di profondità semantica.

La relazione tra input e output viene appresa dal training della rete neurale sui dati di input. La direzione del grafico prosegue dagli input attraverso il livello nascosto e il livello di output. Tutti i nodi di un livello sono connessi con i bordi ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un input specifico, viene calcolato un valore in ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del livello precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.
  
## <a name="how-to-configure"></a>Modalità di configurazione

1.  Aggiungere il modulo a **due classi Neural Network** alla pipeline. È possibile trovare questo modulo in **Machine Learning** , **Initialize** , nella categoria **classificazione** .  
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro** : scegliere questa opzione se si conosce già il modo in cui si vuole configurare il modello.

    -   **Intervallo** di parametri: se non si è certi dei parametri migliori, è possibile trovare i parametri ottimali usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) . Viene fornito un intervallo di valori e il trainer scorre più combinazioni di impostazioni per determinare la combinazione di valori che produce il risultato migliore.  

3.  Per **specifica del livello nascosto** , selezionare il tipo di architettura di rete da creare.  
  
    -   **Caso completamente connesso** : usa l'architettura di rete neurale predefinita, definita per le reti neurali a due classi come indicato di seguito:
  
        -   Dispone di un livello nascosto.
  
        -   Il livello di output è completamente connesso al livello nascosto e il livello nascosto è completamente connesso al livello di input.
  
        -   Il numero di nodi nel livello di input è uguale al numero di funzioni nei dati di training.
  
        -   Il numero di nodi nel livello nascosto viene impostato dall'utente. Il valore predefinito è 100.
  
        -   Il numero di nodi è uguale al numero di classi. Per una rete neurale a due classi, questo significa che tutti gli input devono essere mappati a uno dei due nodi nel livello di output.

5.  Per la **velocità di apprendimento** , definire le dimensioni del passaggio effettuato a ogni iterazione, prima della correzione. Un valore superiore per la velocità di apprendimento può provocare una convergenza più rapida del modello, ma può eccedere i valori minimi locali.

6.  Per **numero di iterazioni di apprendimento** specificare il numero massimo di volte in cui l'algoritmo deve elaborare i case di training.

7.  Per **il diametro iniziale dei pesi di apprendimento** , specificare i pesi del nodo all'inizio del processo di apprendimento.

8.  Per **il momento** , specificare un peso da applicare durante l'apprendimento ai nodi dalle iterazioni precedenti  

10. Selezionare l'opzione **esempi shuffle** per mescolare i casi tra le iterazioni. Se si deseleziona questa opzione, i case vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.
  
11. Per il valore di **inizializzazione numerico casuale** , digitare un valore da usare come valore di inizializzazione.
  
     La specifica di un valore di inizializzazione è utile quando si desidera garantire la ripetibilità tra le esecuzioni della stessa pipeline.  In caso contrario, viene usato un valore clock di sistema come valore di inizializzazione, che può causare risultati leggermente diversi ogni volta che si esegue la pipeline.
  
13. Aggiungere un set di dati con etichetta alla pipeline ed eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro** , connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri** , connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.  
  
14. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per l'assegnazione dei punteggi, aggiungere il modulo **Score Model** a una pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
