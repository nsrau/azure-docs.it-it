---
title: 'Punteggio SVD Recommender: Riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Score SVD Recommender in Azure Machine Learning per assegnare un punteggio alle stime dei consigli per un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/10/2020
ms.openlocfilehash: 82c3454ad4c8db3a9b19084f5b6ece988cc86b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455979"
---
# <a name="score-svd-recommender"></a>Score SVD Recommender

Questo articolo descrive come usare il modulo Score SVD Recommender nella finestra di progettazione di Azure Machine Learning (anteprima). Usare questo modulo per creare stime usando un modello di raccomandazione sottoposto a training basato sull'algoritmo SVD (Single Value Decomposition).

Il consigliabile SVD può generare due diversi tipi di stime:

- [Stimare le valutazioni per un determinato utente ed elemento](#prediction-of-ratings)
- [Raccomanda elementi a un utente](#recommendations-for-users)

Quando si crea il secondo tipo di stime, è possibile operare in una di queste modalità:When you're creating the second type of predictions, you can operate in one of these modes:

- **La modalità di produzione** considera tutti gli utenti o gli articoli. Viene in genere utilizzato in un servizio Web.It's typically used in a web service.

  È possibile creare punteggi per i nuovi utenti, non solo per gli utenti visualizzati durante la formazione. Per ulteriori informazioni, vedere le [note tecniche](#technical-notes). 

- **La modalità** di valutazione opera su un set ridotto di utenti o elementi che possono essere valutati. Viene in genere utilizzato durante le operazioni della pipeline.

Per ulteriori informazioni sull'algoritmo del consigliabile SVD, vedere il documento di ricerca Tecniche di fattorizzazione della matrice per i [sistemi di raccomandazione](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Come configurare Score SVD Recommender

Questo modulo supporta due tipi di stime, ognuna con requisiti diversi. 

###  <a name="prediction-of-ratings"></a>Previsione delle valutazioni

Quando si stimano le classificazioni, il modello calcola il modo in cui un utente reagirà a un determinato elemento, dati i dati di training. I dati di input per il punteggio devono fornire una frequenza sia a un utente che all'elemento.

1. Aggiungere un modello di raccomandazione sottoposto a training alla pipeline e connetterlo a **SVD recommender sottoposto**a training. È necessario creare il modello utilizzando il modulo [Train SVD Recommender.](train-SVD-recommender.md)

2. Per **il tipo di stima Del consigliabile**, selezionare **Previsione valutazione**. Non sono necessari altri parametri.

3. Aggiungere i dati per i quali si desidera eseguire le stime e connetterli al set di dati per assegnare un **punteggio**a .

   Affinché il modello preveda le classificazioni, il set di dati di input deve contenere coppie utente-elemento.

   Il set di dati può contenere una terza colonna facoltativa di classificazioni per la coppia utente-elemento nella prima e nella seconda colonna. Ma la terza colonna verrà ignorata durante la stima.

4. Inviare la pipeline.

### <a name="results-for-rating-predictions"></a>Risultati per le previsioni di valutazione 

Il set di dati di output contiene tre colonne: utenti, elementi e la classificazione stimata per ogni utente e elemento di input.

###  <a name="recommendations-for-users"></a>Consigli per gli utenti 

Per consigliare gli elementi per gli utenti, è necessario fornire un elenco di utenti e elementi come input. Da questi dati, il modello utilizza le proprie conoscenze sugli elementi esistenti e gli utenti per generare un elenco di elementi con probabile attrattiva per ogni utente. È possibile personalizzare il numero di suggerimenti restituiti. È inoltre possibile impostare una soglia per il numero di raccomandazioni precedenti necessarie per generare una raccomandazione.

1. Aggiungere un modello di raccomandazione sottoposto a training alla pipeline e connetterlo a **SVD recommender sottoposto**a training.  È necessario creare il modello utilizzando il modulo [Train SVD Recommender.](train-svd-recommender.md)

2. Per consigliare gli elementi per un elenco di utenti, impostare Tipo di **stima Recommender** su **Raccomandazione elemento**.

3. Per **Selezione articolo consigliata**, indicare se si sta utilizzando il modulo di assegnazione dei punteggi nell'ambiente di produzione o per la valutazione del modello. Scegliere uno dei seguenti valori:

    - **Da tutti gli elementi:** selezionare questa opzione se si sta configurando una pipeline da utilizzare in un servizio Web o nell'ambiente di produzione.  Questa opzione abilita la *modalità di produzione.* Il modulo fornisce raccomandazioni da tutti gli elementi visti durante l'allenamento.

    - **Da elementi classificati (per la valutazione**del modello): selezionare questa opzione se si sta sviluppando o testando un modello. Questa opzione abilita la *modalità di valutazione*. Il modulo fornisce suggerimenti solo da gli elementi nel set di dati di input che sono stati valutati.
    
    - **Da elementi senza classificazione (per suggerire nuovi elementi agli utenti):** selezionare questa opzione se si desidera che il modulo esegua suggerimenti solo da tali elementi nel set di dati di training che non sono stati valutati. 

4. Aggiungere il set di dati per il quale si desidera eseguire le stime e connetterlo al set di dati per assegnare un **punteggio.**

    - **Per Da tutti gli elementi**, il set di dati di input deve essere costituito da una colonna. Contiene gli identificatori degli utenti per i quali formulare raccomandazioni.

      Il set di dati può includere due colonne aggiuntive di identificatori e classificazioni di elementi, ma queste due colonne vengono ignorate. 

    - Per **From Rated Items (per la valutazione del modello),** il set di dati di input deve essere costituito da coppie user-item. La prima colonna deve contenere l'identificatore utente. La seconda colonna deve contenere gli identificatori di elemento corrispondenti.

      Il set di dati può includere una terza colonna di classificazioni degli elementi utente, ma questa colonna viene ignorata.

    - Per **Da elementi non classificati (per suggerire nuovi elementi agli utenti),** il set di dati di input deve essere costituito da coppie utente-elemento. La prima colonna deve contenere l'identificatore utente. La seconda colonna deve contenere gli identificatori di elemento corrispondenti.

     Il set di dati può includere una terza colonna di classificazioni degli elementi utente, ma questa colonna viene ignorata.

5. **Numero massimo di elementi da consigliare a un utente:** immettere il numero di elementi da restituire per ogni utente. Per impostazione predefinita, il modulo consiglia cinque elementi.

6. **Dimensione minima del pool di suggerimenti per utente:** immettere un valore che indichi il numero di raccomandazioni precedenti necessarie. Per impostazione predefinita, questo parametro è impostato su 2, ovvero almeno altri due utenti hanno consigliato l'elemento.

   Utilizzare questa opzione solo se si sta segnando un punteggio in modalità di valutazione. L'opzione non è disponibile se si seleziona **Da tutti gli elementi** o Da elementi non **classificati (per suggerire nuovi elementi agli utenti).**

7.  Per **Da elementi non classificati (per suggerire nuovi elementi agli utenti)**, utilizzare la terza porta di input, denominata Dati **training**, per rimuovere gli elementi già valutati dai risultati della stima.

    Per applicare questo filtro, collegare il set di dati di training originale alla porta di input.

8. Inviare la pipeline.

### <a name="results-of-item-recommendation"></a>Risultati della raccomandazione dell'articolo

Il set di dati con punteggio restituito da Score SVD Recommender elenca gli elementi consigliati per ogni utente:

- La prima colonna contiene gli identificatori utente.
- Vengono generate numerose colonne aggiuntive, a seconda del valore impostato per **Numero massimo di elementi da consigliare a un utente.** Ogni colonna contiene un elemento consigliato (in base all'identificatore). Le raccomandazioni sono ordinate in base all'affinità utente-elemento. L'elemento con l'affinità più alta viene inserito nella colonna **Item 1**.

> [!WARNING]
> Non è possibile valutare questo set di dati con punteggio usando il modulo [Valuta Consiglier.You](evaluate-recommender.md) can't evaluated this scored dataset by using the Evaluate Recommender module.


##  <a name="technical-notes"></a>Note tecniche

Se si dispone di una pipeline con il consigliabile SVD e si sposta il modello nell'ambiente di produzione, tenere presente che esistono differenze fondamentali tra l'utilizzo del recommender in modalità di valutazione e l'utilizzo in modalità di produzione.

La valutazione, per definizione, richiede stime che possono essere verificate rispetto a *dati verificati* in un set di test. Quando si valuta il consigliabile, è necessario prevedere solo gli elementi che sono stati valutati nel set di test. In questo modo si limitano i valori possibili previsti.

Quando si rende operativo il modello, in genere si modifica la modalità di stima per fornire suggerimenti in base a tutti gli elementi possibili, al fine di ottenere le stime migliori. Per molte di queste previsioni, non c'è una verità di base corrispondente. Pertanto, l'accuratezza della raccomandazione non può essere verificata nello stesso modo come durante le operazioni della pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
