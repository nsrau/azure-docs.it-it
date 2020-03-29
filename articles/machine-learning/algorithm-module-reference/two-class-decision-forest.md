---
title: 'Foresta decisionale a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Foresta decisionale a due classi in Azure Machine Learning per creare un modello di apprendimento automatico basato sull'algoritmo delle foreste decisionali.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916711"
---
# <a name="two-class-decision-forest-module"></a>Modulo Foresta decisionale a due classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di apprendimento automatico basato sull'algoritmo delle foreste decisionali.  

Le foreste decisionali sono modelli di ensemble veloci e supervisionati. Questo modulo è una buona scelta se si desidera prevedere un obiettivo con un massimo di due risultati. 

## <a name="understanding-decision-forests"></a>Informazioni sulle foreste decisionali

Questo algoritmo della foresta decisionale è un metodo di apprendimento dell'insieme destinato alle attività di classificazione. I metodi Ensemble si basano sul principio generale che, invece di basarsi su un singolo modello, è possibile ottenere risultati migliori e un modello più generalizzato creando più modelli correlati e combinandoli in qualche modo. In genere, i modelli di insieme offrono una copertura e un'accuratezza migliori rispetto a singoli alberi delle decisioni. 

Ci sono molti modi per creare singoli modelli e combinarli in un insieme. Questa particolare implementazione di una foresta decisionale funziona costruendo più alberi delle decisioni e quindi **votando** sulla classe di output più popolare. La votazione è uno dei metodi più noti per generare risultati in un modello di ensemble. 

+ Vengono creati molti singoli alberi di classificazione, utilizzando l'intero set di dati, ma punti di partenza diversi (in genere randomizzati). Ciò differisce dall'approccio della foresta casuale, in cui i singoli alberi delle decisioni potrebbero utilizzare solo una parte casuale dei dati o delle funzionalità.
+ Ogni albero nell'albero della foresta decisionale restituisce un istogramma di frequenza non normalizzato delle etichette. 
+ Il processo di aggregazione somma questi istogrammi e normalizza il risultato per ottenere le "probabilità" per ogni etichetta. 
+ Gli alberi che hanno un'elevata fiducia nella previsione avranno un peso maggiore nella decisione finale dell'ensemble.

Gli alberi delle decisioni in generale presentano molti vantaggi per le attività di classificazione:Decision trees in in general have many advantages for classification tasks:
  
- Possono catturare i confini delle decisioni non lineari.
- È possibile eseguire il training e prevedere grandi quantità di dati, in quanto sono efficienti nel calcolo e nell'utilizzo della memoria.
- La selezione delle funzionalità è integrata nei processi di formazione e classificazione.  
- Gli alberi possono contenere dati rumorosi e molte caratteristiche.  
- Sono modelli non parametrici, il che significa che possono gestire i dati con distribuzioni diverse. 

Tuttavia, semplici alberi delle decisioni possono essere sovraadattati ai dati e sono meno generalizzabili degli insiemi di alberi.

Per ulteriori informazioni, vedere [Strutture decisionali](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Modalità di configurazione
  
1.  Aggiungere il modulo **Foresta decisionale** a due classi alla pipeline in Azure Machine Learning e aprire il riquadro **Proprietà** del modulo. 

    È possibile trovare il modulo in **Machine Learning**. Espandere **Initialize**, quindi **Classificazione**.  
  
2.  In **Metodo di ricampionamento**, scegliere il metodo utilizzato per creare i singoli alberi.  È possibile scegliere tra **Bagging** o **Replicate**.  
  
    -   **Bagging**: Bagging è anche chiamato *bootstrap aggregating*. In questo metodo, ogni albero viene coltivato in un nuovo campione, creato campionando casualmente il set di dati originale con la sostituzione fino a quando non si dispone di un set di dati delle dimensioni dell'originale.  
  
         Gli output dei modelli vengono combinati *votando*, che è una forma di aggregazione. Ogni albero in una foresta decisionale di classificazione restituisce un istogramma di frequenza non normalizzato di etichette. L'aggregazione è quella di sommare questi istogrammi e normalizzare per ottenere le "probabilità" per ogni etichetta. In questo modo, gli alberi che hanno un'elevata fiducia nella previsione avranno un peso maggiore nella decisione finale dell'ensemble.  
  
         Per ulteriori informazioni, vedere la voce di Wikipedia per l'aggregazione del bootstrap.  
  
    -   **Replica**: nella replica, ogni albero viene addestrato sugli stessi dati di input. La determinazione del predicato diviso viene utilizzata per ogni nodo della struttura ad albero rimane casuale e gli alberi saranno diversi.   
  
3.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: se si conosce la configurazione del modello, è possibile fornire un set specifico di valori come argomenti.

    -   **Intervallo parametri**: se non si è sicuri dei parametri migliori, è possibile trovare i parametri ottimali utilizzando il modulo [Ottimizzazione dei peridi modello.](tune-model-hyperparameters.md) Si fornisce un certo intervallo di valori e il trainer scorre più combinazioni delle impostazioni per determinare la combinazione di valori che produce il risultato migliore.
  
4.  Per **Numero di alberi delle decisioni**, digitare il numero massimo di alberi delle decisioni che è possibile creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una copertura migliore, ma il tempo di allenamento aumenta.  
  
    > [!NOTE]
    >  Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1. Tuttavia, è possibile produrre un solo albero (l'albero con il set iniziale di parametri) e non vengono eseguite ulteriori iterazioni.
  
5.  In **Profondità massima degli alberi delle decisioni**digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio però di overfitting e di aumento dei tempi di training.
  
6.  Per **Numero di divisioni casuali per nodo**, digitare il numero di divisioni da utilizzare durante la compilazione di ogni nodo della struttura ad albero. Una *divisione* significa che le funzionalità in ogni livello dell'albero (nodo) vengono suddivise in modo casuale.
  
7.  Per **Numero minimo di campioni per nodo foglia**, indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.
  
     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.  
  
8.  Selezionare l'opzione **Consenti valori sconosciuti per le entità geografiche di categoria** per creare un gruppo per i valori sconosciuti nei set di training o di convalida. Il modello potrebbe essere meno preciso per i valori noti, ma può fornire stime migliori per i nuovi valori (sconosciuti). 

     Se si deseleziona questa opzione, il modello può accettare solo i valori contenuti nei dati di training.
  
9. Allegare un set di dati con etichetta e uno dei moduli di [training:](module-reference.md)  
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](./train-model.md)  
    
## <a name="results"></a>Risultati

Al termine dell'allenamento:

+ Per salvare un'istantanea del modello sottoposto a training, selezionare la scheda **Uscite** nel pannello destro del modulo **Modulo del modello.** Selezionare l'icona **Registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per il punteggio, aggiungere il modulo Modello di **punteggio** a una pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 