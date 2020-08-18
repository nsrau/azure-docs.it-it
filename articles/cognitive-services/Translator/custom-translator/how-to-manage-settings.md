---
title: Come gestire le impostazioni - Custom Translator
titleSuffix: Azure Cognitive Services
description: Come gestire le impostazioni, creare un'area di lavoro, condividere l'area di lavoro e gestire la chiave di sottoscrizione in Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 488ca52abdd5f8d6cfd1f92a5f91861a17a77d19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510519"
---
# <a name="how-to-manage-settings"></a>Come gestire le impostazioni

Nella pagina Impostazioni convertitore personalizzato è possibile condividere l'area di lavoro, modificare la chiave della sottoscrizione di conversione ed eliminare l'area di lavoro.

Per accedere alla pagina delle impostazioni:

1. Accedere al portale di [Custom Translator](https://portal.customtranslator.azure.ai/).
2. Nel portale di Custom Translator fare clic sull'icona a forma di ingranaggio nella barra laterale.

    ![Collegamento alle impostazioni](media/how-to/how-to-settings.png)

## <a name="associating-translator-subscription"></a>Associazione della sottoscrizione di Translator

Per eseguire il training o la distribuzione di modelli, è necessario disporre di una chiave di sottoscrizione di traduzione associata all'area di lavoro.

Se non è disponibile una sottoscrizione, seguire questa procedura:

1. Sottoscrizione per la creazione di una risorsa di conversione. Seguire le [indicazioni per iscriversi a Translator](https://docs.microsoft.com/azure/cognitive-services/translator/translator-how-to-signup) per sottoscrivere e acquisire una chiave di conversione.
2. Prendere nota della chiave per la sottoscrizione di Translator. Sono accettabili sia Key1 che Key2.
3. Tornare al portale di Custom Translator.

## <a name="create-a-new-workspace"></a>Creazione di una nuova area di lavoro

1. Fare clic sul pulsante "+ Crea area di lavoro" nella barra laterale "convertitore personalizzato".

    ![Creare una nuova area di lavoro](media/how-to/create-new-workspace.png)

2. Nella finestra di dialogo immettere il nome della nuova area di lavoro.
3. Fare clic su "Avanti".
4. Scegliere il tipo di sottoscrizione.
5. Selezionare area di sottoscrizione. Quando è stata creata la chiave della risorsa di conversione, l'area deve corrispondere all'area selezionata.
6. Immettere la chiave per la sottoscrizione di conversione, quindi fare clic sul pulsante "Salva".

    ![Finestra di dialogo per creare una nuova area di lavoro](media/how-to/create-new-workspace-dialog.png)


### <a name="modify-existing-key"></a>Modifica chiave esistente

1. Passare alla pagina "Settings" (Impostazioni) per l'area di lavoro.
2. Fare clic su Cambia chiave

    ![Come aggiungere la chiave di sottoscrizione](media/how-to/how-to-add-subscription-key.png)

3. Nella finestra di dialogo immettere la chiave per la sottoscrizione di conversione, quindi fare clic sul pulsante "Salva".

    ![Come aggiungere la chiave di sottoscrizione](media/how-to/how-to-add-subscription-key-dialog.png)

## <a name="manage-your-workspace"></a>Gestire l'area di lavoro

Un'area di lavoro è uno spazio per la composizione e la compilazione del sistema di traduzione personalizzato. Un'area di lavoro può contenere più progetti, modelli e documenti.

Se parti diverse del lavoro devono essere condivise con utenti differenti, la creazione di più aree di lavoro può risultare utile.

## <a name="share-your-workspace"></a>Condividere l'area di lavoro

In Custom Translator è possibile condividere l'area di lavoro con altri utenti, se è necessario condividere parti diverse del lavoro con utenti differenti.

1. Passare alla pagina "Settings" (Impostazioni) dell'area di lavoro.
2. Fare clic sul pulsante "Aggiungi persone" nella sezione "impostazioni di condivisione".

    ![Condividere l'area di lavoro](media/how-to/share-workspace.png)

3. Nella finestra di dialogo immettere un elenco delimitato da virgole di indirizzi di posta elettronica con cui si vuole condividere l'area di lavoro. Per la condivisione, assicurarsi di specificare l'indirizzo di posta elettronica usato dall'utente per accedere a Custom Translator. Selezionare quindi il livello di autorizzazione di condivisione appropriato e fare clic sul pulsante "Save" (Salva).

    ![Finestra di dialogo Condividi area di lavoro](media/how-to/share-workspace-dialog.png)

4. Se l'area di lavoro ha ancora il nome predefinito "My workspace", è necessario modificarlo prima di condividere l'area di lavoro.
5. Fare clic su "Save" (Salva).

## <a name="sharing-permissions"></a>Autorizzazioni di condivisione

1. **Reader (Lettore):** un lettore nell'area di lavoro potrà visualizzare tutte le informazioni nell'area di lavoro.

2. **Editor:** un editor nell'area di lavoro potrà aggiungere documenti, eseguire il training dei modelli ed eliminare documenti e progetti. Gli utenti con queste autorizzazioni possono aggiungere una chiave di sottoscrizione, ma non possono modificare gli utenti con cui è condivisa l'area di lavoro, eliminare l'area di lavoro o modificare il nome dell'area di lavoro.

3. **Owner (Proprietario):** un proprietario ha autorizzazioni complete per l'area di lavoro.

## <a name="change-sharing-permission"></a>Modificare l'autorizzazione di condivisione

Quando un'area di lavoro è condivisa, nella sezione "impostazioni di condivisione" vengono visualizzati tutti gli indirizzi di posta elettronica con cui questa area di lavoro è condivisa. È possibile modificare le autorizzazioni di condivisione esistenti per ogni indirizzo di posta elettronica, se si ha accesso come proprietario all'area di lavoro.

1. Nella sezione "impostazioni di condivisione" per ogni messaggio di posta elettronica un menu a discesa Visualizza il livello di autorizzazione corrente.

2. Fare clic sul menu a discesa e selezionare il nuovo livello di autorizzazione che si vuole assegnare a tale indirizzo di posta elettronica.

    ![Impostazioni delle autorizzazioni di condivisione](media/how-to/sharing-permission-settings.png)

## <a name="pin-your-workspace"></a>Aggiungere l'area di lavoro

Per impostazione predefinita, la prima area di lavoro creata è bloccata. Ogni volta che si accede, l'area di lavoro bloccata viene visualizzata sul carico del sito. Se è stato creato un numero elevato di aree di lavoro e si vuole crearne uno predefinito al momento dell'accesso, è necessario aggiungerlo.

1. Nella barra laterale fare clic sul nome dell'area di lavoro che si desidera aggiungere.
2. Passare alla pagina "Settings" (impostazioni) per l'area di lavoro.
3. Fare clic sull'icona Aggiungi.

    ![Aggiungi area di lavoro](media/how-to/how-to-pin-workspace.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come creare l'area di lavoro e i progetti](workspace-and-project.md)
