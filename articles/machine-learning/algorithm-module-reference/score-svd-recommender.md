---
title: 'Score SVD Recommender: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Score SVD Recommender nel servizio Azure Machine Learning per assegnare punteggi alle stime delle raccomandazioni per un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: be590ba04de5c8ee671e7e865d04b4ea6c638c00
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717226"
---
# <a name="score-svd-recommender"></a>Punteggio SVD Recommender

Questo articolo descrive come usare il modulo Score SVD Recommender in Azure Machine Learning Designer (Preview). Utilizzare questo modulo per creare stime utilizzando un modello di raccomandazione con training basato sull'algoritmo SVD (Single value Decomposition).

SVD Recommender può generare due tipi diversi di stime:

- [Stimare le valutazioni per un determinato utente ed elemento](#prediction-of-ratings)
- [Consigliare elementi a un utente](#recommendations-for-users)

Quando si crea il secondo tipo di stime, è possibile usare una di queste modalità:

- La **modalità di produzione** prende in considerazione tutti gli utenti o gli elementi. Viene in genere usato in un servizio Web.

  È possibile creare punteggi per i nuovi utenti, non solo per gli utenti visualizzati durante il training. Per ulteriori informazioni, vedere le [Note tecniche](#technical-notes). 

- La **modalità di valutazione** funziona su un set ridotto di utenti o elementi che possono essere valutati. Viene in genere usato durante le operazioni della pipeline.

Per ulteriori informazioni sull'algoritmo SVD Recommender, vedere la pagina relativa alle [tecniche di factoring della matrice di ricerca per i sistemi di raccomandazione](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Come configurare Score SVD Recommender

Questo modulo supporta due tipi di stime, ognuna con requisiti diversi. 

###  <a name="prediction-of-ratings"></a>Stima delle classificazioni

Quando si stimano le classificazioni, il modello calcola il modo in cui un utente reagirà a un particolare elemento, in base ai dati di training. I dati di input per il punteggio devono fornire un utente e l'elemento da valutare.

1. Aggiungere un modello di raccomandazione con training alla pipeline e connetterlo a **SVD Recommender**sottoposto a training. È necessario creare il modello usando il modulo [Train SVD Recommender](train-SVD-recommender.md) .

2. Per il **tipo di stima della raccomandazione**selezionare **stima classificazione**. Non sono necessari altri parametri.

3. Aggiungere i dati per i quali si desidera eseguire stime e connetterli al **set di dati per**assegnare un punteggio.

   Per il modello per stimare le classificazioni, il set di dati di input deve contenere coppie di elementi utente.

   Il set di dati può contenere una terza colonna facoltativa di classificazioni per la coppia utente-elemento nella prima e nella seconda colonna. Ma la terza colonna verrà ignorata durante la stima.

4. Eseguire la pipeline.

### <a name="results-for-rating-predictions"></a>Risultati per le stime di valutazione 

Il set di dati di output contiene tre colonne: utenti, elementi e la classificazione stimata per ogni utente e elemento di input.

###  <a name="recommendations-for-users"></a>Suggerimenti per gli utenti 

Per consigliare gli elementi per gli utenti, è necessario fornire un elenco di utenti e elementi come input. Da questi dati, il modello utilizza le informazioni relative agli elementi e agli utenti esistenti per generare un elenco di elementi con un probabile appeal per ogni utente. È possibile personalizzare il numero di raccomandazioni restituite. È possibile impostare una soglia per il numero di raccomandazioni precedenti necessarie per generare un suggerimento.

1. Aggiungere un modello di raccomandazione con training alla pipeline e connetterlo a **SVD Recommender**sottoposto a training.  È necessario creare il modello usando il modulo [Train SVD Recommender](train-svd-recommender.md) .

2. Per consigliare gli elementi per un elenco di utenti, impostare Recommender **PREDICTION Kind** su **Item Recommendation**.

3. Per la **selezione di elementi consigliati**, indicare se si sta usando il modulo di assegnazione dei punteggi in produzione o per la valutazione del modello. Scegliere uno dei valori seguenti:

    - **Da tutti gli elementi**: selezionare questa opzione se si sta configurando una pipeline da usare in un servizio Web o in produzione.  Questa opzione Abilita la *modalità di produzione*. Il modulo apporta consigli da tutti gli elementi visualizzati durante il training.

    - **Da elementi classificati (per la valutazione del modello)** : selezionare questa opzione se si sta sviluppando o testando un modello. Questa opzione Abilita la *modalità di valutazione*. Il modulo apporta consigli solo dagli elementi del set di dati di input che sono stati classificati.
    
    - **Da elementi non classificati (per suggerire nuovi elementi agli utenti)** : selezionare questa opzione se si desidera che il modulo faccia raccomandazioni solo da tali elementi del set di dati di training che non sono stati classificati. 

4. Aggiungere il set di dati per il quale si desidera eseguire stime e connetterlo al **set di dati per**assegnare un punteggio.

    - Per **da tutti gli elementi**, il set di dati di input deve essere costituito da una sola colonna. Contiene gli identificatori degli utenti per i quali effettuare consigli.

      Il set di dati può includere due colonne aggiuntive di identificatori e classificazioni degli elementi, ma queste due colonne vengono ignorate. 

    - Per gli **elementi classificati (per la valutazione del modello)** , il set di dati di input deve essere costituito da coppie utente-elemento. La prima colonna deve contenere l'identificatore utente. La seconda colonna deve contenere gli identificatori di elemento corrispondenti.

      Il set di dati può includere una terza colonna di classificazioni di elementi utente, ma questa colonna viene ignorata.

    - Per **da elementi senza classificazione (per suggerire nuovi elementi agli utenti)** , il set di dati di input deve essere costituito da coppie utente-elemento. La prima colonna deve contenere l'identificatore utente. La seconda colonna deve contenere gli identificatori di elemento corrispondenti.

     Il set di dati può includere una terza colonna di classificazioni di elementi utente, ma questa colonna viene ignorata.

5. **Numero massimo di elementi da consigliare a un utente**: immettere il numero di elementi da restituire per ogni utente. Per impostazione predefinita, il modulo consiglia cinque elementi.

6. **Dimensioni minime del pool di raccomandazioni per utente**: immettere un valore che indichi il numero di raccomandazioni precedenti richieste. Per impostazione predefinita, questo parametro è impostato su **2**, ovvero almeno altri due utenti hanno raccomandato l'elemento.

   Usare questa opzione solo se si esegue il punteggio in modalità di valutazione. L'opzione non è disponibile se si seleziona **da tutti gli elementi** o **da elementi non classificati (per suggerire nuovi elementi agli utenti)** .

7.  Per **da elementi senza classificazione (per suggerire nuovi elementi agli utenti)** , usare la terza porta di input, denominata **dati di training**, per rimuovere gli elementi che sono già stati classificati dai risultati della stima.

    Per applicare questo filtro, connettere il set di dati di training originale alla porta di input.

8. Eseguire la pipeline.

### <a name="results-of-item-recommendation"></a>Risultati della raccomandazione per gli elementi

Il set di dati con punteggio restituito da Score SVD Recommender elenca gli elementi consigliati per ogni utente:

- La prima colonna contiene gli identificatori utente.
- Vengono generate varie colonne aggiuntive, a seconda del valore impostato per il **numero massimo di elementi da consigliare a un utente**. Ogni colonna contiene un elemento consigliato (per identificatore). Le indicazioni sono ordinate in base all'affinità utente-elemento. L'elemento con affinità più elevata viene inserito nella colonna **1**.

> [!WARNING]
> Non è possibile valutare questo set di dati con punteggio usando il modulo [Evaluate Recommender](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Note tecniche

Se si dispone di una pipeline con il SVD Recommender e si sposta il modello nell'ambiente di produzione, tenere presente che esistono differenze principali tra l'uso del raccomandazione in modalità di valutazione e l'uso in modalità di produzione.

La valutazione, per definizione, richiede stime che possono essere verificate rispetto alla *verità di base* in un set di test. Quando si valuta il sistema di raccomandazione, è necessario stimare solo gli elementi che sono stati classificati nel set di test. In questo modo si limitano i valori possibili stimati.

Quando si rendere operativo il modello, in genere si modifica la modalità di stima per creare consigli in base a tutti i possibili elementi, in modo da ottenere le stime migliori. Per molte di queste stime, non esiste una verità di base corrispondente. Pertanto, l'accuratezza della raccomandazione non può essere verificata nello stesso modo in cui avviene durante le operazioni della pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per il servizio Azure Machine Learning. 
