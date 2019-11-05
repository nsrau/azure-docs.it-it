---
title: Crea una nuova app-LUIS
titleSuffix: Azure Cognitive Services
description: Creare e gestire le applicazioni nella pagina Web di Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500300"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Creare una nuova app LUIS nel portale LUIS
Un'app LUIS può essere creata in due modi. È possibile creare un'app LUIS nel portale [LUIS](https://www.luis.ai) o tramite le [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) di creazione di LUIS.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>Usare il portale LUIS

È possibile creare una nuova app nel portale di LUIS in diversi modi:

* Iniziare con un'applicazione vuota e creare finalità, espressioni ed entità.
* Iniziare con un'applicazione vuota e aggiungere un [dominio predefinito](luis-how-to-use-prebuilt-domains.md).
* Importare un'applicazione LUIS da un file JSON che contiene già finalità, espressioni ed entità.

## <a name="using-the-authoring-apis"></a>Usare le API di creazione
È possibile creare una nuova app con le API di creazione in due modi:

* [Inizia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) con un'applicazione vuota e crea finalità, espressioni ed entità.
* [Inizia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) con un dominio predefinito.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Creare la nuova appa in LUIS

1. Nella pagina **app personali** selezionare **+ Crea**.

    ![Elenco di app di Language Understanding](./media/luis-create-new-app/apps-list.png)


2. Nella finestra di dialogo, assegna all'applicazione il nome di "TravelAgent".

    ![Creare una nuova finestra di dialogo dell'applicazione](./media/luis-create-new-app/create-app.png)

3. Scegli le impostazioni cultura dell'applicazione (per l'applicazione TravelAgent, scegli la lingua inglese), quindi seleziona **Fine**. 

    > [!NOTE]
    > Le impostazioni cultura non possono essere modificate dopo la creazione dell'applicazione. 

## <a name="import-an-app-from-file"></a>Importa un'app da un file

1. Nella pagina **App personali**, seleziona **Importa una nuova applicazione**.
1. Nella finestra di dialogo popup selezionare un file JSON app valido e quindi fare clic su **fine**.

### <a name="import-errors"></a>Errori di importazione

Possibili errori: 

* Esiste già un'app con lo stesso nome. Per risolvere il problema, reimportare l'app e impostare il **nome facoltativo** su un nuovo nome. 

## <a name="export-app-for-backup"></a>Esporta app per il backup

1. Nella pagina **app personali** selezionare **Esporta**.
1. Selezionare **Esporta come JSON**. Il browser Scarica la versione attiva dell'app.
1. Aggiungere questo file al sistema di backup per archiviare il modello.

## <a name="export-app-for-containers"></a>Esporta app per contenitori

1. Nella pagina **app personali** selezionare **Esporta**.
1. Selezionare **Esporta come contenitore** e quindi selezionare lo slot pubblicato (produzione o fase) che si vuole esportare.
1. Usare questo file con il [contenitore Luis](luis-container-howto.md). 

    Se si è interessati all'esportazione di un modello sottoposto a training ma non ancora pubblicato da usare con il contenitore LUIS, passare alla pagina **versioni** ed esportare da questa posizione. 

## <a name="delete-app"></a>Eliminare l'applicazione

1. Nella pagina **App personali** seleziona i tre punti di sospensione (...) alla fine della riga dell'applicazione.
1. Seleziona **Elimina** dal menu.
1. Nella finestra di conferma fai clic su **Ok**.

## <a name="next-steps"></a>Passaggi successivi

La prima attività dell'applicazione consiste nell'[aggiungere finalità](luis-how-to-add-intents.md).
