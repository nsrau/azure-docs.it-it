---
title: Come gestire le impostazioni - Custom Translator
titleSuffix: Azure Cognitive Services
description: Come gestire le impostazioni, creare un'area di lavoro, condividere l'area di lavoro e gestire la chiave di sottoscrizione in Custom Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 6e8406cdd265063622bc5dcebbb9f22549b3633a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60513920"
---
# <a name="how-to-manage-settings"></a>Come gestire le impostazioni

All'interno della pagina delle impostazioni di Custom Translator è possibile creare una nuova area di lavoro, condividere l'area di lavoro e aggiungere o modificare la chiave di sottoscrizione di Microsoft Translation.

Per accedere alla pagina delle impostazioni:

1. Accedere al portale di [Custom Translator](https://portal.customtranslator.azure.ai/).
2. Nel portale di Custom Translator fare clic sull'icona a forma di ingranaggio nella barra laterale.

    ![Collegamento alle impostazioni](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Associazione della sottoscrizione di Microsoft Translator

È necessario disporre di una chiave di sottoscrizione per l'API Traduzione testuale di Microsoft Translator associata all'area di lavoro per il training o la distribuzione dei modelli.

Se non è disponibile una sottoscrizione, seguire questa procedura:

1. Sottoscrivere l'API Traduzione testuale di Microsoft Translator. Questo articolo illustra come creare una sottoscrizione per l'API Traduzione testuale di Microsoft Translator.
2. Prendere nota della chiave per la sottoscrizione di Microsoft Translator. Sono accettabili sia Key1 che Key2.
3. Tornare al portale di Custom Translator.

### <a name="add-existing-key"></a>Aggiungere la chiave esistente

1.  Passare alla pagina "Settings" (Impostazioni) per l'area di lavoro.
2.  Fare clic su Add Key (Aggiungi chiave)

    ![Come aggiungere la chiave di sottoscrizione](media/how-to/how-to-add-subscription-key.png)

3. Nella finestra di dialogo immettere la chiave per la sottoscrizione di Microsoft Translator e quindi fare clic sul pulsante "Add" (Aggiungi).

    ![Come aggiungere la chiave di sottoscrizione](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Dopo aver aggiunto una chiave, è possibile modificare o eliminare la chiave in qualsiasi momento.

    ![Chiave di sottoscrizione dopo l'aggiunta](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Gestire l'area di lavoro

Un'area di lavoro è uno spazio per la composizione e la compilazione del sistema di traduzione personalizzato. Un'area di lavoro può contenere più progetti, modelli e documenti.

Se parti diverse del lavoro devono essere condivise con utenti differenti, la creazione di più aree di lavoro può risultare utile.

## <a name="create-a-new-workspace"></a>Creazione di una nuova area di lavoro

1.  Passare alla pagina "Settings" (Impostazioni) dell'area di lavoro.
2.  Fare clic sul pulsante "New workspace" (Nuova area di lavoro) nella sezione "Create New Workspace" (Crea nuova area di lavoro).

    ![Creare una nuova area di lavoro](media/how-to/create-new-workspace.png)

4.  Nella finestra di dialogo immettere il nome della nuova area di lavoro.
5.  Fare clic su "Create" (Crea).

    ![Finestra di dialogo per creare una nuova area di lavoro](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Condividere l'area di lavoro

In Custom Translator è possibile condividere l'area di lavoro con altri utenti, se è necessario condividere parti diverse del lavoro con utenti differenti.

1.  Passare alla pagina "Settings" (Impostazioni) dell'area di lavoro.
2.  Fare clic sul pulsante "Share" (Condividi) nella sezione "Sharing Settings" (Impostazioni di condivisione).

    ![Condividere l'area di lavoro](media/how-to/share-workspace.png)

3.  Nella finestra di dialogo immettere un elenco delimitato da virgole di indirizzi di posta elettronica con cui si vuole condividere l'area di lavoro. Per la condivisione, assicurarsi di specificare l'indirizzo di posta elettronica usato dall'utente per accedere a Custom Translator. Selezionare quindi il livello appropriato di autorizzazioni di condivisione.

4.  Se l'area di lavoro ha ancora il nome predefinito "My workspace", è necessario modificarlo prima di condividere l'area di lavoro.
5.  Fare clic su "Save" (Salva).

## <a name="sharing-permissions"></a>Autorizzazioni di condivisione

1.  **Reader (Lettore):** un lettore nell'area di lavoro potrà visualizzare tutte le informazioni nell'area di lavoro.

2.  **Editor:** un editor nell'area di lavoro potrà aggiungere documenti, eseguire il training dei modelli ed eliminare documenti e progetti. Gli utenti con queste autorizzazioni possono aggiungere una chiave di sottoscrizione, ma non possono modificare gli utenti con cui è condivisa l'area di lavoro, eliminare l'area di lavoro o modificare il nome dell'area di lavoro.

3.  **Owner (Proprietario):** un proprietario ha autorizzazioni complete per l'area di lavoro.

## <a name="change-sharing-permission"></a>Modificare l'autorizzazione di condivisione

Quando un'area di lavoro è condivisa, la sezione "Sharing Settings" (Impostazioni di condivisione) mostra tutti gli indirizzi di posta elettronica con cui è condivisa l'area di lavoro. È possibile modificare le autorizzazioni di condivisione esistenti per ogni indirizzo di posta elettronica, se si ha accesso come proprietario all'area di lavoro.

1.  Nella sezione "Sharing Settings" (Impostazioni di condivisione) viene indicato il livello di autorizzazione corrente per ogni indirizzo di posta elettronica in un menu a discesa.

2.  Fare clic sul menu a discesa e selezionare il nuovo livello di autorizzazione che si vuole assegnare a tale indirizzo di posta elettronica.

    ![Impostazioni delle autorizzazioni di condivisione](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- Scoprire [come eseguire la migrazione dell'area di lavoro e del progetto](how-to-migrate.md) da [Hub di Microsoft Translator](https://hub.microsofttranslator.com)
