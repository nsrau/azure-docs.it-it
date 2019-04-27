---
title: Entità predefinite
titleSuffix: Azure Cognitive Services
description: LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. Il supporto dell'entità predefinita varia a seconda delle impostazioni cultura dell'app LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cfc4ff58cfeb65f80f9ac5ce2dd532defde5ef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596130"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entità predefinite per riconoscere tipi di dati comuni

LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. 

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita

1. Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Entità** nel riquadro sinistro. 

1. Nella pagina **Entità** fare clic su **Aggiungi entità predefinite**.

1. Nella finestra di dialogo **Add prebuilt entities** (Aggiungi entità predefinite) selezionare l'entità predefinita datetimeV2. 

    ![Finestra di dialogo Add prebuilt entities (Aggiungi entità predefinite)](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selezionare **Operazione completata**.

## <a name="publish-the-app"></a>Pubblicare l'app

Il modo più semplice per visualizzare il valore di un'entità predefinita consiste nell'eseguire una query dall'endpoint pubblicato. 

1. Nella barra degli strumenti superiore selezionare l'azione **Publish** (Pubblica). Eseguire la pubblicazione in **Production** (Produzione). 

1. Quando viene visualizzata la notifica verde di operazione riuscita, selezionare il collegamento **Refer to the list of endpoints** (Fare riferimento all'elenco degli endpoint) per visualizzare gli endpoint.

1. Selezionare un endpoint. Viene aperta una nuova scheda del browser che contiene tale endpoint. Mantenere aperta la scheda del browser e passare alla sezione **Test**.

## <a name="test"></a>Test
Dopo aver aggiunto l'entità, non è necessario eseguire il training dell'app. 

Per testare la nuova finalità nell'endpoint, aggiungere un valore per il parametro **q**. Usare la tabella seguente per le espressioni suggerite per **q**:

|Espressione di test|Valore dell'entità|
|--|:--|
|Prenota un volo per domani|19/10/2018|
|Annulla l'appuntamento del 3 marzo|LUIS ha restituito i risultati relativi alla data del 3 marzo più recente nel passato (03/03/2018) e alla data del 3 marzo nel futuro (03/03/2019) perché nell'espressione non è stato specificato alcun anno.|
|Pianifica una riunione alle 10:00|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Contrassegno di entità contenenti un token di entità predefinita
 Se è presente testo, ad esempio `HH-1234`, che si vuole contrassegnare come entità personalizzata _e_ al modello è stata aggiunta l'[entità numero predefinita](luis-reference-prebuilt-number.md), non sarà possibile contrassegnare l'entità personalizzata nel portale di LUIS. È possibile contrassegnarla con l'API. 

 Per contrassegnare questo tipo di token, di cui una parte è già contrassegnata con un'entità predefinita, rimuovere l'entità predefinita dall'app LUIS. Non è necessario eseguire il training dell'app. Contrassegnare quindi il token con l'entità personalizzata. Aggiungere quindi l'entità di nuovo all'app LUIS.

 Per un altro esempio, considerare l'espressione come elenco delle preferenze di classe: `I want first year spanish, second year calculus, and fourth year english lit.` Se nell'app LUIS è stata aggiunta l'entità predefinita Ordinal, gli elementi `first`, `second` e `fourth` saranno già contrassegnati come ordinali. Se si vuole acquisire l'ordinale e la classe, è possibile creare un'entità composita ed eseguirne il wrapping nell'entità Ordinal predefinita e nell'entità personalizzata per il nome di classe.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle entità predefinite](./luis-reference-prebuilt-entities.md)
