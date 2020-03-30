---
title: 'Rete neurale a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Rete neurale a due classi in Azure Machine Learning per creare un modello di rete neurale che può essere usato per stimare una destinazione con solo due valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477392"
---
# <a name="two-class-neural-network-module"></a>Modulo di rete neurale a due classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di rete neurale da usare per stimare una destinazione contenente solo due valori.

La classificazione tramite reti neurali è un metodo di apprendimento supervisionato e pertanto richiede un set di *dati con tag,* che include una colonna label. Ad esempio, è possibile usare questo modello di rete neurale per stimare i risultati binari, ad esempio se un paziente ha o meno una determinata malattia o se è probabile che una macchina si guasti entro un intervallo di tempo specificato.  

Dopo aver definito il modello, eseguire il training fornendo un set di dati con tag e il modello come input per il training [del modello](./train-model.md). Il modello sottoposto a training può quindi essere utilizzato per stimare i valori per i nuovi input.

### <a name="more-about-neural-networks"></a>Ulteriori informazioni sulle reti neurali

Una rete neurale è un insieme di livelli interconnessi. Gli ingressi sono il primo livello e sono collegati a un livello di output da un grafico aciclico composto da bordi e nodi ponderati.

Tra i livelli di input e output è possibile inserire più livelli nascosti. Con uno solo o più livelli nascosti, è possibile eseguire facilmente la maggior parte delle attività di stima. Tuttavia, recenti ricerche hanno dimostrato che le reti neurali profonde (DNN) con molti livelli possono essere efficaci in attività complesse come il riconoscimento di immagini o vocali. I livelli successivi vengono utilizzati per modellare livelli crescenti di profondità semantica.

La relazione tra input e output viene appresa dal training della rete neurale sui dati di input. La direzione del grafico procede dagli input attraverso il livello nascosto e al livello di output. Tutti i nodi di un livello sono collegati dai bordi ponderati ai nodi nel livello successivo.

Per calcolare l'output della rete per un particolare input, viene calcolato un valore in corrispondenza di ogni nodo nei livelli nascosti e nel livello di output. Il valore viene impostato calcolando la somma ponderata dei valori dei nodi del layer precedente. Una funzione di attivazione viene quindi applicata a tale somma ponderata.
  
## <a name="how-to-configure"></a>Modalità di configurazione

1.  Aggiungere il modulo **Rete neurale** a due classi alla pipeline. Questo modulo è disponibili in **Machine Learning**, **Initialize**, nella categoria **Classificazione.**  
  
2.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: Scegliere questa opzione se si conosce già come si desidera configurare il modello.

    -   **Intervallo parametri**: se non si è sicuri dei parametri migliori, è possibile trovare i parametri ottimali utilizzando il modulo [Ottimizzazione dei peridi modello.](tune-model-hyperparameters.md) Si fornisce un certo intervallo di valori e il trainer scorre più combinazioni delle impostazioni per determinare la combinazione di valori che produce il risultato migliore.  

3.  Per **Specifica layer nascosto**, selezionare il tipo di architettura di rete da creare.  
  
    -   **Caso completamente connesso**: utilizza l'architettura di rete neurale predefinita, definita per le reti neurali a due classi come segue:
  
        -   Ha uno strato nascosto.
  
        -   Il livello di output è completamente connesso al livello nascosto e il livello nascosto è completamente connesso al livello di input.
  
        -   Il numero di nodi nel layer di input è uguale al numero di feature nei dati di training.
  
        -   Il numero di nodi nel livello nascosto viene impostato dall'utente. Il valore predefinito è 100.
  
        -   Il numero di nodi è uguale al numero di classi. Per una rete neurale a due classi, ciò significa che tutti gli input devono essere mappati a uno dei due nodi nel livello di output.

5.  Per **Velocità di apprendimento**, definire le dimensioni del passaggio eseguito a ogni iterazione, prima della correzione. Un valore superiore per la velocità di apprendimento può provocare una convergenza più rapida del modello, ma può eccedere i valori minimi locali.

6.  Per **Numero di iterazioni di apprendimento**, specificare il numero massimo di volte in cui l'algoritmo deve elaborare i casi di training.

7.  Per **Diametro dei pesi**di apprendimento iniziale , specificare i pesi del nodo all'inizio del processo di apprendimento.

8.  Per **Lo slancio**, specificare un peso da applicare durante l'apprendimento ai nodi delle iterazioni precedenti  

10. Selezionare l'opzione **Esempi casuali** per riordinare i casi tra le iterazioni. Se si deseleziona questa opzione, i casi vengono elaborati esattamente nello stesso ordine ogni volta che si esegue la pipeline.
  
11. Per **Valore di serie con numero casuale**, digitare un valore da utilizzare come valore di serie.
  
     Specificare un valore di seeding è utile quando si vuole garantire la ripetibilità tra le esecuzioni della stessa pipeline.  In caso contrario, un valore di clock di sistema viene utilizzato come valore di errore, che può causare risultati leggermente diversi ogni volta che si esegue la pipeline.
  
13. Aggiungere un set di dati con tag alla pipeline e connettere uno dei moduli di [training.](module-reference.md)  
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](train-model.md)  
  
14. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per il punteggio, aggiungere il modulo Modello di **punteggio** a una pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
