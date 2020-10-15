---
title: 'Foresta delle decisioni Two-Class: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Two-Class Decision Forest in Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo delle foreste delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: 9870b5366ac629fe1b10cfde15a58b85c7c6fbc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907710"
---
# <a name="two-class-decision-forest-module"></a>Modulo della foresta delle decisioni Two-Class

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo delle foreste delle decisioni.  

Le foreste delle decisioni sono modelli di ensemble veloci e con supervisione. Questo modulo è una scelta ottimale se si desidera stimare una destinazione con un massimo di due risultati. 

## <a name="understanding-decision-forests"></a>Informazioni sulle foreste delle decisioni

Questo algoritmo di foresta delle decisioni è un metodo di apprendimento dell'insieme destinato alle attività di classificazione. I metodi Ensemble sono basati sul principio generale che invece di basarsi su un singolo modello, è possibile ottenere risultati migliori e un modello più generalizzato creando più modelli correlati e combinando tali modelli in qualche modo. In genere, i modelli di insieme offrono una copertura e un'accuratezza migliori rispetto a singoli alberi delle decisioni. 

Esistono diversi modi per creare singoli modelli e combinarli in un insieme. Questa particolare implementazione di una foresta delle decisioni funziona creando più alberi delle decisioni e **votando** la classe di output più comune. Il voto è uno dei metodi più noti per la generazione di risultati in un modello di insieme. 

+ Vengono creati molti singoli alberi di classificazione, usando l'intero set di dati, ma punti di partenza diversi, in genere casuali. Questo approccio è diverso da quello della foresta casuale, in cui i singoli alberi delle decisioni possono utilizzare solo una parte casuale dei dati o delle funzionalità.
+ Ogni albero nell'albero della foresta delle decisioni restituisce un istogramma di frequenza non normalizzato di etichette. 
+ Il processo di aggregazione Somma questi istogrammi e normalizza il risultato per ottenere le "probabilità" per ciascuna etichetta. 
+ Gli alberi con attendibilità di stima elevata avranno un peso maggiore nella decisione finale dell'insieme.

Gli alberi delle decisioni in generale presentano molti vantaggi per le attività di classificazione:
  
- Possono acquisire i limiti di decisione non lineari.
- È possibile eseguire il training e la stima in molti dati, perché sono efficienti nell'utilizzo del calcolo e della memoria.
- La selezione delle funzioni è integrata nei processi di training e classificazione.  
- Gli alberi possono contenere dati rumorosi e molte funzionalità.  
- Sono modelli non parametrici, ovvero possono gestire i dati con distribuzioni diverse. 

Tuttavia, gli alberi delle decisioni semplici possono overfitting sui dati e sono meno generalizzabili rispetto all'albero.

Per altre informazioni, vedere [foreste delle decisioni](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Modalità di configurazione
  
1.  Aggiungere il modulo **Two-Class Decision Forest** alla pipeline in Azure Machine Learning e aprire il riquadro delle **Proprietà** del modulo. 

    Il modulo è reperibile in **Machine Learning**. Espandere **Initialize**e quindi **Classification**.  
  
2.  Per il **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra l' **insaccamento** o la **replica**.  
  
    -   **Insaccamento**: l'insaccamento viene chiamato anche *aggregazione bootstrap*. In questo metodo ogni albero viene ampliato in un nuovo esempio, creato eseguendo il campionamento casuale del set di dati originale con sostituzione fino a quando non si dispone di un set di dati di dimensioni originali.  
  
         Gli output dei modelli vengono combinati *votando*, ovvero una forma di aggregazione. Ogni albero in una foresta delle decisioni di classificazione restituisce un istogramma di frequenza non normalizzato di etichette. L'aggregazione consiste nel sommare questi istogrammi e normalizzare per ottenere le "probabilità" per ciascuna etichetta. In questo modo, gli alberi con attendibilità di stima elevata avranno un peso maggiore nella decisione finale dell'insieme.  
  
         Per ulteriori informazioni, vedere la voce Wikipedia per l'aggregazione bootstrap.  
  
    -   **Replicate**: nella replica ogni albero viene sottoposto a training esattamente sugli stessi dati di input. La determinazione del predicato Split utilizzato per ogni nodo della struttura ad albero rimane casuale e gli alberi saranno diversi.   
  
3.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: se si sa come si desidera configurare il modello, è possibile fornire un set di valori specifico come argomenti.

    -   **Intervallo**di parametri: se non si è certi dei parametri migliori, è possibile trovare i parametri ottimali usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) . Viene fornito un intervallo di valori e il trainer scorre più combinazioni di impostazioni per determinare la combinazione di valori che produce il risultato migliore.
  
4.  Per **numero di alberi delle decisioni**, digitare il numero massimo di alberi delle decisioni che è possibile creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma aumenta il tempo di training.  
  
    > [!NOTE]
    >  Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1. Tuttavia, è possibile produrre solo un albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.
  
5.  Per la **profondità massima degli alberi delle decisioni**, digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero potrebbe aumentare la precisione, con il rischio però di overfitting e di aumento dei tempi di training.
  
  
7.  Per il **numero minimo di campioni per nodo foglia**, indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.
  
     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque casi che soddisfano le stesse condizioni.  
  
8.  Selezionare l'opzione **Consenti valori sconosciuti per le funzionalità categoriche** per creare un gruppo per i valori sconosciuti nei set di training o di convalida. Il modello può essere meno preciso per i valori noti, ma può fornire stime migliori per i valori nuovi (sconosciuti). 

     Se si deseleziona questa opzione, il modello può accettare solo i valori contenuti nei dati di training.
  
9. Alleghi un set di dati con etichetta ed Esegui il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.  
    
## <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per l'assegnazione dei punteggi, aggiungere il modulo **Score Model** a una pipeline.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 