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
ms.openlocfilehash: 25de69873857512a70d6417973d4a85883ac6455
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517917"
---
# <a name="score-svd-recommender"></a>Punteggio SVD Recommender

Questo articolo descrive come usare il modulo **Score SVD Recommender** in Azure Machine Learning Designer (Preview). Usare questo modulo per creare stime usando un modello di raccomandazione con training basato sull'algoritmo SVD (Single value Decomposition).

SVD Recommender può generare due tipi diversi di stime:

- [Stimare le valutazioni per un determinato utente ed elemento](#predict-ratings)

- [Raccomandare elementi a un determinato utente](#recommend)

Quando si crea il secondo tipo di stime, è possibile operare in modalità di *produzione* o di *valutazione*.

- La **modalità di produzione** prende in considerazione tutti gli utenti o gli elementi e viene in genere utilizzata in un servizio Web.

    È possibile creare punteggi per i nuovi utenti, non solo per gli utenti visualizzati durante il training. Per altre informazioni, vedere [questa sezione](#technical-notes). 

- La **modalità di valutazione** funziona su un set ridotto di utenti o elementi che possono essere valutati e viene in genere usato durante la pipeline.

Per ulteriori informazioni sull'algoritmo SVD Recommender, vedere il documento di ricerca relativo alle [tecniche di fattorizzazione delle matrici per i sistemi di raccomandazione](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

                                                                                                                                    


## <a name="how-to-configure-score-svd-recommender"></a>Come configurare Score SVD Recommender

Questo modulo supporta diversi tipi di consigli, ognuno con requisiti diversi. Fare clic sul collegamento per il tipo di dati e il tipo di raccomandazione che si desidera creare.

+ [Classificazioni stimate](#predict-ratings)
+ [Consigliare elementi](#recommend)

###  <a name="predict-ratings"></a>Classificazioni stimate

Quando si stimano le classificazioni, il modello calcola il modo in cui un determinato utente reagirà a un particolare elemento, in base ai dati di training. Pertanto, i dati di input per il punteggio devono fornire un utente e l'elemento da valutare.

1. Aggiungere un modello di raccomandazione con training alla pipeline e connetterlo a **SVD Recommender**sottoposto a training.  È necessario creare il modello utilizzando [Train SVD Recommender](train-SVD-recommender.md).

2. **Tipo di stima del raccomandazione**: selezionare la **stima della classificazione**. Non sono necessari altri parametri.

3. Aggiungere i dati per i quali si desidera eseguire stime e connetterli al **set di dati per**assegnare un punteggio.

    Per stimare le classificazioni, il set di dati di input deve contenere coppie utente-elemento.

    Il set di dati può contenere una terza colonna facoltativa di classificazioni per la coppia utente-elemento nella prima e nella seconda colonna, ma la terza colonna verrà ignorata durante la stima.

4. Eseguire la pipeline.

### <a name="results-for-rating-predictions"></a>Risultati per le stime di valutazione 

Il set di dati di output contiene tre colonne, che contengono l'utente, l'elemento e la classificazione stimata per ogni utente e elemento di input.

###  <a name="recommend"></a>Consigliato 

Per consigliare gli elementi per gli utenti, è necessario fornire un elenco di utenti e elementi come input. Da questi dati, il modello utilizza le informazioni relative agli elementi e agli utenti esistenti per generare un elenco di elementi con un probabile appeal per ogni utente. È possibile personalizzare il numero di raccomandazioni restituite e impostare una soglia per il numero di raccomandazioni precedenti necessarie per generare un'indicazione.

1. Aggiungere un modello di raccomandazione con training alla pipeline e connetterlo a **SVD Recommender**sottoposto a training.  È necessario creare il modello utilizzando [Train SVD Recommender](train-svd-recommender.md).

2. Per consigliare gli elementi per un determinato elenco di utenti, impostare Recommender **PREDICTION Kind** su **Item Recommendation**.

3. **Selezione elemento consigliata**: indicare se si sta usando il modulo di assegnazione dei punteggi in produzione o per la valutazione del modello, scegliendo uno dei valori seguenti:

    - **Da tutti gli elementi**: selezionare questa opzione se si configura una pipeline da usare in un servizio Web o in un ambiente di produzione.  Questa opzione Abilita la **modalità di produzione**e il modulo apporta consigli da tutti gli elementi visualizzati durante il training.

    - **Da elementi classificati (per la valutazione del modello)** : selezionare questa opzione se si sta sviluppando o testando un modello. Questa opzione Abilita la **modalità di valutazione**e il modulo apporta consigli solo da tali elementi nel set di dati di input che sono stati classificati.
    
    - **Da elementi non classificati (per suggerire nuovi elementi agli utenti)** : selezionare questa opzione e il modulo apporta consigli solo dagli elementi del set di dati di training che non sono stati classificati. 

4. Aggiungere il set di dati per il quale si desidera eseguire stime e connetterlo al **set di dati per**assegnare un punteggio.

    - Per **da tutti gli elementi**, il set di dati di input deve essere costituito da una sola colonna, contenente gli identificatori degli utenti per i quali si desidera creare raccomandazioni.

        Il set di dati può includere due colonne aggiuntive di identificatori e classificazioni di elementi, ma queste due colonne vengono ignorate. 

    - Per gli **elementi classificati (per la valutazione del modello)** , il set di dati di input deve essere costituito da **coppie utente-elemento**. La prima colonna deve contenere l'identificatore **utente** . La seconda colonna deve contenere gli identificatori di **elemento** corrispondenti.

        Il set di dati può includere una terza colonna di classificazioni di elementi utente, ma questa colonna viene ignorata.

    - Per **da elementi senza classificazione (per suggerire nuovi elementi agli utenti)** , il set di dati di input deve essere costituito da **coppie utente-elemento**. La prima colonna deve contenere l'identificatore **utente** . La seconda colonna deve contenere gli identificatori di **elemento** corrispondenti.

        Il set di dati può includere una terza colonna di classificazioni di elementi utente, ma questa colonna viene ignorata.

5. **Numero massimo di elementi da consigliare a un utente**: digitare il numero di elementi da restituire per ogni utente. Per impostazione predefinita, sono consigliati cinque elementi.

6. **Dimensioni minime del pool di raccomandazioni per utente**: digitare un valore che indichi il numero di raccomandazioni precedenti richieste.  Per impostazione predefinita, questo parametro è impostato su 2, ovvero è necessario che l'elemento sia stato consigliato da almeno altri due utenti.

    Questa opzione deve essere utilizzata solo se si assegna un punteggio in modalità di valutazione. L'opzione non è disponibile se si seleziona **da tutti gli elementi** o **da elementi non classificati (per suggerire nuovi elementi agli utenti)** .

7.  Per **da elementi senza classificazione (per suggerire nuovi elementi agli utenti)** , usare la terza porta di input, denominata **dati di training**, per rimuovere gli elementi che sono già stati classificati dai risultati della stima.

    Per applicare questo filtro, connettere il set di dati di training originale alla porta di input.

8. Eseguire la pipeline.

### <a name="results-of-item-recommendation"></a>Risultati della raccomandazione per gli elementi

Il set di dati con punteggio restituito da **Score SVD Recommender** elenca gli elementi consigliati per ogni utente.

- La prima colonna contiene gli identificatori utente.
- Vengono generate varie colonne aggiuntive, a seconda del valore impostato per il **numero massimo di elementi da consigliare a un utente**. Ogni colonna contiene un elemento consigliato (per identificatore). Le indicazioni sono ordinate in base all'affinità utente-elemento, con l'elemento con l'affinità più elevata Put nella colonna, **elemento 1**.

> [!WARNING]
> Questo set di dati con punteggio non può essere valutato con il modulo [Evaluate Recommender](evaluate-recommender.md) .


##  <a name="technical-notes"></a>Note tecniche

Questa sezione contiene le risposte ad alcune domande comuni sull'uso del raccomandazione per la creazione di stime.  

###  <a name="production-use-of-the-svd-recommender"></a>Uso della produzione di SVD Recommender

Se si dispone di una pipeline con il SVD Recommender e si sposta il modello nell'ambiente di produzione, tenere presenti queste differenze principali quando si usa il Consiglio in modalità di valutazione e in modalità di produzione:

- La valutazione, per definizione, richiede stime che possono essere verificate rispetto alla *verità di base* in un set di test. Pertanto, quando si valuta il sistema di raccomandazione, è necessario stimare solo gli elementi che sono stati classificati nel set di test. Questa operazione limita necessariamente i valori possibili stimati.

    Tuttavia, quando si rendere operativo il modello, in genere si modifica la modalità di stima per creare consigli in base a tutti i possibili elementi, in modo da ottenere le stime migliori. Per molte di queste stime, non esiste una verità di base corrispondente, pertanto non è possibile verificare l'accuratezza della raccomandazione nello stesso modo che durante la pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
