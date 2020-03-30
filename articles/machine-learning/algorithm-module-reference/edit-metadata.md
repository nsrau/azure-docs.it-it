---
title: 'Modifica metadati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Modifica metadati in Azure Machine Learning per modificare i metadati associati alle colonne in un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064252"
---
# <a name="edit-metadata-module"></a>Modifica modulo metadati

Questo articolo descrive un modulo incluso nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare il modulo Modifica metadati per modificare i metadati associati alle colonne in un set di dati. Il valore e il tipo di dati del set di dati verranno modificati dopo l'utilizzo del modulo Modifica metadati.

Le modifiche tipiche ai metadati possono includere le operazioni seguenti:
  
+ Considerare le colonne booleane o numeriche come valori di categoria.
  
+ Indicazione quale colonna contiene l'etichetta della **classe** o contiene i valori che si desidera classificare o stimare.
  
+ Contrassegno delle colonne come entità geografiche.
  
+ Modifica dei valori di data/ora in valori numerici o viceversa.
  
+ Ridenominazione delle colonne.
  
 Utilizzare Modifica metadati ogni volta che è necessario modificare la definizione di una colonna, in genere per soddisfare i requisiti per un modulo a valle. Ad esempio, alcuni moduli funzionano solo con tipi di `IsFeature` dati `IsCategorical`specifici o richiedono flag sulle colonne, ad esempio .  
  
 Dopo aver eseguito l'operazione richiesta, è possibile ripristinare lo stato originale dei metadati.
  
## <a name="configure-edit-metadata"></a>Configurare Modifica metadati
  
1. Nella finestra di progettazione di Azure Machine Learning aggiungere il modulo Modifica metadati alla pipeline e connettere il set di dati da aggiornare. È possibile trovare il modulo nella categoria **Trasformazione dati.**
  
1. Fare clic su **Modifica colonna** nel riquadro di destra del modulo e scegliere la colonna o il set di colonne da utilizzare. È possibile scegliere le colonne singolarmente in base al nome o all'indice oppure scegliere un gruppo di colonne in base al tipo.  
  
1. Selezionare l'opzione **Tipo** di dati se è necessario assegnare un tipo di dati diverso alle colonne selezionate. Potrebbe essere necessario modificare il tipo di dati per determinate operazioni. Ad esempio, se il set di dati di origine contiene numeri gestiti come testo, è necessario modificarli in un tipo di dati numerico prima di utilizzare le operazioni matematiche.

    + I tipi di dati supportati sono **String**, **Integer**, **Double**, **Boolean**e **DateTime**.

    + Se si selezionano più colonne, è necessario applicare le modifiche ai metadati a *tutte le* colonne selezionate. Si supponga, ad esempio, di scegliere due o tre colonne numeriche. È possibile modificarli tutti in un tipo di dati stringa e rinominarli in un'unica operazione. Tuttavia, non è possibile modificare una colonna in un tipo di dati stringa e un'altra colonna da un float a un numero intero.
  
    + Se non si specifica un nuovo tipo di dati, i metadati della colonna vengono modificati.

    + Il tipo di colonna e i valori verranno modificati dopo l'esecuzione dell'operazione Modifica metadati. È possibile ripristinare il tipo di dati originale in qualsiasi momento utilizzando Modifica metadati per reimpostare il tipo di dati della colonna.  

    > [!NOTE]
    > Se si modifica qualsiasi tipo di numero per il tipo **DateTime,** lasciare vuoto il campo **Formato DateTime.** Attualmente non è possibile specificare il formato dei dati di destinazione.  

1. Selezionare l'opzione **Categorical** per specificare che i valori nelle colonne selezionate devono essere considerati come categorie.

    Ad esempio, potresti avere una colonna che contiene i numeri 0, 1 e 2, ma sai che i numeri in realtà significano "Fumatore", "Non fumatore" e "Sconosciuto". In tal caso, contrassegnando la colonna come categoria si garantisce che i valori vengano utilizzati solo per raggruppare i dati e non nei calcoli numerici.
  
1. Usare l'opzione **Campi** se si vuole modificare il modo in cui Azure Machine Learning usa i dati in un modello.

    + **Funzionalità**: Utilizzare questa opzione per contrassegnare una colonna come funzionalità nei moduli che operano solo su colonne di entità geografiche. Per impostazione predefinita, tutte le colonne vengono inizialmente considerate come funzioni.  
  
    + **Etichetta**: Utilizzare questa opzione per contrassegnare l'etichetta, nota anche come attributo stimabile o variabile di destinazione. Molti moduli richiedono che nel set di dati sia presente esattamente una colonna di etichette.

        In molti casi, Azure Machine Learning può dedurre che una colonna contiene un'etichetta di classe. Impostando questi metadati, è possibile assicurarsi che la colonna venga identificata correttamente. L'impostazione di questa opzione non modifica i valori dei dati. Cambia solo il modo in cui alcuni algoritmi di apprendimento automatico gestiscono i dati.
  
    > [!TIP]
    > Hai dati che non rientrano in queste categorie? Ad esempio, il set di dati potrebbe contenere valori, ad esempio identificatori univoci che non sono utili come variabili. A volte tali ID possono causare problemi quando vengono utilizzati in un modello.
    >
    > Fortunatamente, Azure Machine Learning mantiene tutti i dati, in modo che non sia necessario eliminare tali colonne dal set di dati. Quando è necessario eseguire operazioni su un set speciale di colonne, è sufficiente rimuovere temporaneamente tutte le altre colonne utilizzando il modulo [Seleziona colonne nel set](select-columns-in-dataset.md) di dati. Successivamente è possibile unire nuovamente le colonne nel set di dati utilizzando il modulo [Aggiungi colonne.](add-columns.md)  
  
1. Utilizzare le opzioni seguenti per cancellare le selezioni precedenti e ripristinare i metadati sui valori predefiniti.  
  
    + **Cancella funzione**: Utilizzare questa opzione per rimuovere il flag di funzionalità.  
  
         Tutte le colonne vengono inizialmente considerate come feature. Per i moduli che eseguono operazioni matematiche, potrebbe essere necessario utilizzare questa opzione per impedire che le colonne numeriche vengano considerate come variabili.
  
    + **Cancella etichetta**: Utilizzare questa opzione per rimuovere i metadati **dell'etichetta** dalla colonna specificata.  
  
    + **Cancella punteggio**: utilizzare questa opzione per rimuovere i metadati del **punteggio** dalla colonna specificata.  
  
         Al momento non è possibile contrassegnare in modo esplicito una colonna come punteggio in Azure Machine Learning.You can currently't explicitly mark a column as a score in Azure Machine Learning. Tuttavia, alcune operazioni comportano un flag di punteggio internamente un punteggio. Inoltre, un modulo R personalizzato potrebbe restituire i valori di punteggio.

1. Per **Nuovi nomi di colonna,** immettere il nuovo nome della colonna o delle colonne selezionate.  
  
    + I nomi di colonna possono utilizzare solo caratteri supportati dalla codifica UTF-8. Non sono consentiti stringhe vuote, valori Null o nomi costituiti interamente da spazi.  
  
    + Per rinominare più colonne, immettere i nomi come elenco separato da virgole in ordine di indici di colonna.  
  
    + Tutte le colonne selezionate devono essere rinominate. Non è possibile omettere o ignorare le colonne.  
  
1. Inviare la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning.
