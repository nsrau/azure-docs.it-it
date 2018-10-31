---
title: Entità predefinite per Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. Il supporto dell'entità predefinita varia a seconda delle impostazioni cultura dell'app LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647596"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entità predefinite per riconoscere tipi di dati comuni

LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. 

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita

1. Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Entità** nel riquadro sinistro. 

1. Nella pagina **Entità** fare clic su **Manage prebuilt entities** (Gestisci entità predefinite).

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


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle entità predefinite](./luis-reference-prebuilt-entities.md)
