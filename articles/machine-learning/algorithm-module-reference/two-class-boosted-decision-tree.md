---
title: 'Albero decisionale potenziato a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Albero delle decisioni potenziato a due classi in Azure Machine Learning per creare un modello di apprendimento automatico basato sull'algoritmo degli alberi delle decisioni potenziato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920740"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modulo Albero decisionale potenziato a due classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di apprendimento automatico basato sull'algoritmo degli alberi delle decisioni potenziato. 

Un albero decisionale potenziato è un metodo di apprendimento dell'insieme in cui il secondo albero corregge gli errori del primo albero, il terzo albero corregge gli errori del primo e del secondo albero e così via.  Le previsioni si basano sull'intero insieme di alberi insieme che fa la previsione.
  
In genere, se configurati correttamente, gli alberi delle decisioni potenziati sono i metodi più semplici con cui ottenere prestazioni migliori su un'ampia gamma di attività di apprendimento automatico. Tuttavia, sono anche uno degli studenti più ad alta intensità di memoria, e l'implementazione corrente contiene tutto in memoria. Pertanto, un modello di albero delle decisioni potenziato potrebbe non essere in grado di elaborare i set di dati di grandi dimensioni che alcuni allievi lineari possono gestire.

## <a name="how-to-configure"></a>Modalità di configurazione

Questo modulo crea un modello di classificazione non sottoposto a training. Poiché la classificazione è un metodo di apprendimento supervisionato, per eseguire il training del modello, è necessario un set di *dati con tag* che include una colonna label con un valore per tutte le righe.

È possibile eseguire il training di questo tipo di modello utilizzando [Train Model](././train-model.md). 

1.  In Azure Machine Learning aggiungere il modulo **Albero delle decisioni potenziato** alla pipeline.
  
2.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**
  
    + **Parametro singolo**: se si conosce la configurazione del modello, è possibile fornire un set specifico di valori come argomenti.
  
    + **Intervallo parametri**: se non si è sicuri dei parametri migliori, è possibile trovare i parametri ottimali utilizzando il modulo [Ottimizzazione dei peridi modello.](tune-model-hyperparameters.md) Si fornisce un certo intervallo di valori e il trainer scorre più combinazioni delle impostazioni per determinare la combinazione di valori che produce il risultato migliore.
  
3.  Per **Numero massimo di foglie per albero**, indicare il numero massimo di nodi terminali (foglie) che è possibile creare in qualsiasi albero.
  
     Aumentando questo valore, potenzialmente si aumentano le dimensioni dell'albero e si ottiene una maggiore precisione. Possono tuttavia verificarsi casi di overfitting e tempi di training più lunghi.
  
4.  Per **Numero minimo di campioni per nodo foglia**, indicare il numero di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.  
  
     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.
  
5.  Per **Tasso di apprendimento**, digitare un numero compreso tra 0 e 1 che definisce la dimensione del passaggio durante l'apprendimento.  
  
     La velocità di apprendimento determina la velocità o la lentezza con cui lo studente converge sulla soluzione ottimale. Se la dimensione del passo è troppo grande, si potrebbe superare la soluzione ottimale. Se la dimensione del passo è troppo piccola, la formazione richiede più tempo per convergere sulla soluzione migliore.
  
6.  Per **Numero di alberi costruiti**, indicare il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma saranno necessari tempi di training maggiori.
  
     Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. se si desidera visualizzare o stampare un singolo albero, impostare il valore su 1. Tuttavia, quando si esegue questa operazione, viene prodotto un solo albero (l'albero con il set iniziale di parametri) e non vengono eseguite ulteriori iterazioni.
  
7.  Per **Valore di serie con numero casuale**, digitare facoltativamente un numero intero non negativo da utilizzare come valore di serie casuale. La specifica di un valore di serie garantisce la riproducibilità tra le esecuzioni con gli stessi dati e parametri.  
  
     Il valore di inizializzazione casuale è impostato per impostazione predefinita su 0, il che significa che il valore di inizializzazione iniziale viene ottenuto dall'orologio di sistema.  Le esecuzioni successive che utilizzano un seme casuale possono avere risultati diversi.
  

9. Eseguire il training del modello.
  
    + Se si imposta **la modalità Crea trainer** su **Parametro singolo**, collegare un set di dati con tag e il modulo Train [Model.](./train-model.md)  
   
## <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per il punteggio, aggiungere il modulo Modello di **punteggio** a una pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 