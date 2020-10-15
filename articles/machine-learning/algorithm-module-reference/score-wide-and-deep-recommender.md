---
title: Usare il modulo Score Wide & il modulo di raccomandazione Deep
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Score Wide & provisioning avanzato in Azure Machine Learning per assegnare punteggi alle stime delle raccomandazioni per un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905159"
---
# <a name="score-wide-and-deep-recommender"></a>Score Wide and Deep Recommender

Questo articolo descrive come usare il modulo **Score Wide e recommendal Recommender** in Azure machine learning designer, per creare stime basate su un modello di raccomandazione con training, in base all'ampia & Deep Learning di Google.

La raccomandazione Wide e Deep può generare due tipi diversi di stime:

- [Stimare le valutazioni per un determinato utente ed elemento](#predict-ratings)

- [Raccomandare elementi a un determinato utente](#recommend-items)


Quando si crea il secondo tipo di stime, è possibile operare in modalità di *produzione* o di *valutazione*.

- La **modalità di produzione** prende in considerazione tutti gli utenti o gli elementi e viene in genere utilizzata in un servizio Web. È possibile creare punteggi per i nuovi utenti, non solo per gli utenti visualizzati durante il training. 

- La **modalità di valutazione** funziona su un set ridotto di utenti o elementi che possono essere valutati e viene in genere usato durante la sperimentazione.

Altre informazioni sul Consiglio più ampio e profondo e la relativa teoria sottostante sono disponibili nel documento di ricerca pertinente:  [wide & Deep learning per i sistemi di raccomandazione](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Come configurare l'assegnazione di punteggi e il Consiglio più profondo

Questo modulo supporta diversi tipi di consigli, ognuno con requisiti diversi. Fare clic sul collegamento per il tipo di dati e il tipo di raccomandazione che si desidera creare.

+ [Classificazioni stimate](#predict-ratings)
+ [Consigliare elementi](#recommend-items)

### <a name="predict-ratings"></a>Classificazioni stimate

Quando si stimano le classificazioni, il modello calcola il modo in cui un determinato utente reagirà a un particolare elemento, in base ai dati di training. Pertanto, i dati di input per il punteggio devono fornire un utente e l'elemento da valutare.

1. È possibile aggiungere un modello di raccomandazione approfondita & Deep per l'esperimento e connetterlo a un **modello di raccomandazione approfondita e a livello di training**.  È necessario creare il modello utilizzando [Train Wide e Deep Recommender](train-wide-and-deep-recommender.md).

2. **Tipo di stima del raccomandazione**: selezionare la **stima della classificazione**. Non sono richiesti altri parametri.

3. Aggiungere i dati per i quali si desidera eseguire stime e connetterli al **set di dati per**assegnare un punteggio.

    Per stimare le classificazioni, il set di dati di input deve contenere coppie utente-elemento.

    Il set di dati può contenere una terza colonna facoltativa di classificazioni per la coppia utente-elemento nella prima e nella seconda colonna, ma la terza colonna verrà ignorata durante la stima.

4.  (Facoltativo) Se si dispone di un set di dati di funzionalità utente, connetterlo alle **funzionalità dell'utente**.

    Il set di dati delle funzionalità utente deve contenere l'identificatore utente nella prima colonna. Le colonne rimanenti devono contenere valori che caratterizzano gli utenti, ad esempio il sesso, le preferenze, la posizione e così via.
  
    Le funzionalità degli utenti che hanno valutato gli elementi nel set di dati di training vengono ignorate dall'assegnazione dei **punteggi e dal Consiglio**avanzato, perché sono già state apprese durante il training. Pertanto, filtrare il set di dati in anticipo per includere solo *gli utenti con avvio a freddo*o gli utenti che non hanno valutato alcun elemento.

    > [!WARNING]
    > Se è stato eseguito il training del modello senza usare le funzionalità utente, non è possibile introdurre le funzionalità utente durante il punteggio.

5. Se si dispone di un set di dati di funzionalità degli elementi, è possibile connetterlo alle **funzionalità degli elementi**.

    Il set di dati features dell'elemento deve contenere un identificatore di elemento nella prima colonna. Le colonne rimanenti devono contenere valori che caratterizzano gli elementi.

    Le funzionalità degli elementi classificati nel set di dati di training vengono ignorate dall'assegnazione dei **punteggi e dal Consiglio** avanzato, perché sono già state apprese durante il training. Limitare quindi il set di dati di assegnazione dei punteggi a *elementi di avvio a freddo*o elementi che non sono stati classificati da alcun utente.

    > [!WARNING]
    > Se è stato eseguito il training del modello senza usare le funzionalità degli elementi, non è possibile introdurre le funzionalità degli elementi durante il punteggio.

7. Eseguire l'esperimento.

### <a name="results-for-rating-predictions"></a>Risultati per le stime di valutazione 

Il set di dati di output contiene tre colonne, che contengono l'utente, l'elemento e la classificazione stimata per ogni utente e elemento di input.

Durante l'assegnazione dei punteggi vengono inoltre applicate le modifiche seguenti:

-  Per una colonna della funzionalità di un utente o di un elemento numerico, i valori mancanti vengono sostituiti automaticamente con la **media** dei valori del set di training non mancanti. Per la funzionalità categorica, i valori mancanti vengono sostituiti con lo stesso valore categorico diverso da tutti i possibili valori di questa funzionalità.
-  Non viene applicata alcuna conversione ai valori della funzionalità per mantenere i densità.

### <a name="recommend-items"></a>Consigliare elementi

Per consigliare gli elementi per gli utenti, è necessario fornire un elenco di utenti e elementi come input. Da questi dati, il modello utilizza le informazioni relative agli elementi e agli utenti esistenti per generare un elenco di elementi con un probabile appeal per ogni utente. È possibile personalizzare il numero di raccomandazioni restituite e impostare una soglia per il numero di raccomandazioni precedenti necessarie per generare un'indicazione.

1. Aggiungere un modello di raccomandazione esteso e approfondito all'esperimento e connetterlo a un **modello di raccomandazione approfondita e di training**.  È necessario creare il modello utilizzando [Train Wide e Deep Recommender](train-wide-and-deep-recommender.md).

2. Per consigliare gli elementi per un determinato elenco di utenti, impostare Recommender **PREDICTION Kind** su **Item Recommendation**.

3. **Selezione elemento consigliata**: indicare se si sta usando il modulo di assegnazione dei punteggi in produzione o per la valutazione del modello, scegliendo uno dei valori seguenti:

    - **Da elementi classificati (per la valutazione del modello)**: selezionare questa opzione se si sta sviluppando o testando un modello. Questa opzione Abilita la **modalità di valutazione**e il modulo apporta consigli solo da tali elementi nel set di dati di input che sono stati classificati.
    - **Da tutti gli elementi**: selezionare questa opzione se si sta configurando un esperimento da usare in un servizio Web o in un ambiente di produzione.  Questa opzione Abilita la **modalità di produzione**e il modulo apporta consigli da tutti gli elementi visualizzati durante il training.
    - **Da elementi non classificati (per suggerire nuovi elementi agli utenti)**: selezionare questa opzione se si desidera che il modulo faccia raccomandazioni solo da tali elementi del set di dati di training che non sono stati classificati. 
4. Aggiungere il set di dati per il quale si desidera eseguire stime e connetterlo al **set di dati per**assegnare un punteggio.

    - Se si sceglie l'opzione, **da tutti gli elementi**, il set di dati di input deve essere costituito da una sola colonna, contenente gli identificatori degli utenti per i quali si desidera eseguire le raccomandazioni.

        Il set di dati può includere due colonne aggiuntive di identificatori e classificazioni degli elementi, ma queste due colonne vengono ignorate. 

    - Se si sceglie l'opzione **da elementi classificati (per la valutazione del modello)**, il set di dati di input deve essere costituito da **coppie utente-elemento**. La prima colonna deve contenere l'identificatore **utente** . La seconda colonna deve contenere gli identificatori di **elemento** corrispondenti.

        Il set di dati può includere una terza colonna di classificazioni di elementi utente, ma questa colonna viene ignorata.
        
    - Per **da elementi senza classificazione (per suggerire nuovi elementi agli utenti)**, il set di dati di input deve essere costituito da coppie utente-elemento. La prima colonna deve contenere l'identificatore utente. La seconda colonna deve contenere gli identificatori di elemento corrispondenti.

        Il set di dati può includere una terza colonna di classificazioni di elementi utente, ma questa colonna viene ignorata.

5. (Facoltativo) Se si dispone di un set di dati di **funzionalità utente**, connetterlo alle **funzionalità dell'utente**.

    La prima colonna nel set di dati delle funzionalità utente deve contenere l'identificatore utente. Le colonne rimanenti devono contenere valori che caratterizzano l'utente, ad esempio il sesso, le preferenze, la posizione e così via.

    Le funzionalità degli utenti che hanno valutato gli elementi vengono ignorate dall'assegnazione dei **punteggi e dal Consiglio**avanzato, perché queste funzionalità sono già state apprese durante il training. È pertanto possibile filtrare il set di dati in anticipo per includere solo *gli utenti con avvio a freddo*o gli utenti che non hanno valutato alcun elemento.

    > [!WARNING]
    >  Se è stato eseguito il training del modello senza utilizzare le funzionalità utente, non è possibile utilizzare applica funzionalità durante l'assegnazione dei punteggi.

6. Opzionale Se si dispone di un set di dati di **funzionalità degli elementi**, è possibile connetterlo alle **funzionalità degli elementi**.

    La prima colonna nel set di dati features dell'elemento deve contenere l'identificatore dell'elemento. Le colonne rimanenti devono contenere valori che caratterizzano gli elementi.

    Le funzionalità degli elementi classificati vengono ignorate dall'assegnazione dei **punteggi e dal Consiglio**avanzato, perché queste funzionalità sono già state apprese durante il training. Pertanto, è possibile limitare il set di dati di assegnazione dei punteggi a *elementi di avvio a freddo*o elementi che non sono stati classificati da alcun utente.

    > [!WARNING]
    >  Se è stato eseguito il training del modello senza usare le funzionalità degli elementi, non usare le funzionalità degli elementi per l'assegnazione dei punteggi.  

7. **Numero massimo di elementi da consigliare a un utente**: digitare il numero di elementi da restituire per ogni utente. Per impostazione predefinita, sono consigliati 5 elementi.

8. **Dimensioni minime del pool di raccomandazioni per utente**: digitare un valore che indichi il numero di raccomandazioni precedenti richieste.  Per impostazione predefinita, questo parametro è impostato su 2, ovvero è necessario che l'elemento sia stato consigliato da almeno altri due utenti.

    Questa opzione deve essere utilizzata solo se si assegna un punteggio in modalità di valutazione. L'opzione non è disponibile se si seleziona **da tutti gli elementi** o **da elementi non classificati (per suggerire nuovi elementi agli utenti)**.

9. Per **da elementi senza classificazione (per suggerire nuovi elementi agli utenti)**, usare la terza porta di input, denominata **dati di training**, per rimuovere gli elementi che sono già stati classificati dai risultati della stima.

    Per applicare questo filtro, connettere il set di dati di training originale alla porta di input.

10. Eseguire l'esperimento.
### <a name="results-of-item-recommendation"></a>Risultati della raccomandazione per gli elementi

Il set di dati con punteggio restituito da **Score Wide and Deep Recommender** elenca gli elementi consigliati per ogni utente.

- La prima colonna contiene gli identificatori utente.
- Vengono generate varie colonne aggiuntive, a seconda del valore impostato per il **numero massimo di elementi da consigliare a un utente**. Ogni colonna contiene un elemento consigliato (per identificatore). Le indicazioni sono ordinate in base all'affinità utente-elemento, con l'elemento con l'affinità più elevata Put nella colonna, **elemento 1**.

##  <a name="technical-notes"></a>Note tecniche

In questa sezione vengono fornite le risposte ad alcune domande comuni sull'utilizzo dell'ampia & raccomandazione approfondita per la creazione di stime.  

###  <a name="cold-start-users-and-recommendations"></a>Utenti e raccomandazioni per avvio a freddo

In genere, per creare le raccomandazioni, il modulo **Punteggio Wide e Deep Recommender** richiede gli stessi input utilizzati per il training del modello, incluso un ID utente. Ciò è dovuto al fatto che l'algoritmo deve sapere se ha appreso qualcosa su questo utente durante il training. 

Tuttavia, per i nuovi utenti, è possibile che non si disponga di un ID utente, solo alcune funzionalità utente, ad esempio età, sesso e così via.

È comunque possibile creare raccomandazioni per gli utenti che sono nuovi al sistema gestendoli come *utenti "avvio a freddo"*. Per tali utenti, l'algoritmo di raccomandazione non usa la cronologia passata o le classificazioni precedenti ma solo le funzioni utente.

Ai fini della stima, un utente con avvio a freddo viene definito come un utente con un ID che non è stato usato per il training. Per assicurarsi che gli ID non corrispondano a quelli utilizzati per il training, è possibile creare nuovi identificatori. Ad esempio, è possibile generare ID casuali all'interno di un intervallo specificato oppure allocare in anticipo una serie di ID per gli utenti con avvio a freddo.

Se tuttavia non si hanno dati di filtro collaborativo, ad esempio un vettore di funzioni utente, è preferibile usare uno strumento di apprendimento di classificazione o regressione.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Uso della produzione da parte di un consigliatore ampio e profondo

Se è stata sperimentata la raccomandazione ampia e profonda e quindi si sposta il modello nell'ambiente di produzione, tenere presenti queste differenze principali quando si usa il Consiglio in modalità di valutazione e in modalità di produzione:

- La valutazione, per definizione, richiede stime che possono essere verificate rispetto a *dati verificati* in un set di test. Pertanto, quando si valuta la raccomandazione, è necessario stimare solo gli elementi che sono stati classificati nel set di test limitando necessariamente i possibili valori stimati.

    Tuttavia, quando si rende operativo il modello, in genere si modifica la modalità di stima per creare raccomandazioni in base a tutti gli elementi possibili in modo da ottenere le stime migliori. Per molte di queste stime, non esiste una verità di base corrispondente, pertanto non è possibile verificare l'accuratezza della raccomandazione come durante la sperimentazione.

- Se non si fornisce un ID utente nell'ambiente di produzione e si specifica solo un vettore di funzione, si potrebbe ottenere come risposta un elenco di tutte le raccomandazioni per tutti gli utenti possibili. Assicurarsi di fornire un ID utente.

    Per limitare il numero di raccomandazioni restituite, è possibile anche specificare il numero massimo di elementi restituiti per ogni utente. 



## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) in Azure Machine Learning. 
