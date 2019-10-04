---
title: 'Foresta delle decisioni a due classi: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Two-Class Decision Forest nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo delle foreste delle decisioni.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37a2ce77e438145219df9cb553d1881626e8a2c6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128390"
---
# <a name="two-class-decision-forest-module"></a>Modulo della foresta delle decisioni a due classi

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo delle foreste delle decisioni.  

Le foreste delle decisioni sono modelli di ensemble veloci e con supervisione. Questo modulo è una scelta ottimale se si desidera stimare una destinazione con un massimo di due risultati. 

## <a name="understanding-decision-forests"></a>Informazioni sulle foreste delle decisioni

Questo algoritmo di foresta delle decisioni è un metodo di apprendimento dell'insieme destinato alle attività di classificazione. I metodi Ensemble sono basati sul principio generale che invece di basarsi su un singolo modello, è possibile ottenere risultati migliori e un modello più generalizzato creando più modelli correlati e combinando tali modelli in qualche modo. In genere, i modelli di insieme offrono una migliore copertura e accuratezza rispetto a singoli alberi delle decisioni. 

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

## <a name="how-to-configure"></a>Come configurare
  
1.  Aggiungere il modulo **Two-Class Decision Forest** all'esperimento in Azure Machine Learning e aprire il riquadro delle **Proprietà** del modulo. 

    Il modulo è reperibile in **Machine Learning**. Espandere **Initialize**e quindi **Classification**.  
  
2.  Per il **Metodo**di ricampionamento, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra l'insaccamento o la **replica**.  
  
    -   Insaccamento: L'insaccamento viene chiamato anche *aggregazione bootstrap*. In questo metodo ogni albero viene ampliato in un nuovo esempio, creato eseguendo il campionamento casuale del set di dati originale con sostituzione fino a quando non si dispone di un set di dati di dimensioni originali.  
  
         Gli output dei modelli vengono combinati *votando*, ovvero una forma di aggregazione. Ogni albero in una foresta delle decisioni di classificazione restituisce un istogramma di frequenza non normalizzato di etichette. L'aggregazione consiste nel sommare questi istogrammi e normalizzare per ottenere le "probabilità" per ciascuna etichetta. In questo modo, gli alberi con attendibilità di stima elevata avranno un peso maggiore nella decisione finale dell'insieme.  
  
         Per ulteriori informazioni, vedere la voce Wikipedia per l'aggregazione bootstrap.  
  
    -   **Replica**: Nella replica ogni albero viene sottoposto a training esattamente sugli stessi dati di input. La determinazione del predicato Split utilizzato per ogni nodo della struttura ad albero rimane casuale e gli alberi saranno diversi.   
  
3.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: Se si conosce il modo in cui si desidera configurare il modello, è possibile specificare come argomenti un set specifico di valori.
  
4.  Per **numero di alberi delle decisioni**, digitare il numero massimo di alberi delle decisioni che è possibile creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma aumenta il tempo di training.  
  
    > [!NOTE]
    >  Questo valore controlla anche il numero di alberi visualizzati durante la visualizzazione del modello sottoposto a training. Se si desidera visualizzare o stampare un singolo albero, è possibile impostare il valore su 1. Tuttavia, è possibile produrre solo un albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.
  
5.  Per la **profondità massima degli alberi delle decisioni**, digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero può aumentare la precisione, a rischio di overfitting e di aumento dei tempi di training.
  
6.  Per **numero di**divisioni casuali per nodo, digitare il numero di divisioni da usare durante la compilazione di ogni nodo dell'albero. Una *suddivisione* indica che le funzionalità di ogni livello dell'albero (nodo) sono divise in modo casuale.
  
7.  Per il **numero minimo di campioni per nodo foglia**, indicare il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.
  
     Aumentando questo valore, si aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito 1, anche un singolo caso può causare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque case che soddisfano le stesse condizioni.  
  
8.  Selezionare l'opzione **Consenti valori sconosciuti per le funzionalità categoriche** per creare un gruppo per i valori sconosciuti nei set di training o di convalida. Il modello può essere meno preciso per i valori noti, ma può fornire stime migliori per i valori nuovi (sconosciuti). 

     Se si deseleziona questa opzione, il modello può accettare solo i valori contenuti nei dati di training.
  
9. Alleghi un set di dati con etichetta e uno dei [moduli di training](module-reference.md):  
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .  
  
    
## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare l'albero che è stato creato a ogni iterazione, fare clic con il pulsante destro del mouse sull'output del modulo [Train Model](./train-model.md) e selezionare Visualize ( **Visualizza**).
  
    Fare clic su ogni albero per eseguire il drill-down nelle divisioni e visualizzare le regole per ogni nodo.

+ Per salvare uno snapshot del modello, fare clic con il pulsante destro del mouse sull'output del modello sottoposto a **Training** e selezionare **Salva modello**. Il modello salvato non viene aggiornato nelle esecuzioni successive dell'esperimento.

+ Per usare il modello per l'assegnazione dei punteggi, aggiungere il modulo **Score Model** a un esperimento.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 