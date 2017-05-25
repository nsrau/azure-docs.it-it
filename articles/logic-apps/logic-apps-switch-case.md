---
title: Istruzione switch per diverse azioni in app per la logica di Azure | Microsoft Docs
description: Scegliere diverse azioni da eseguire in app per la logica in base ai valori di espressione tramite un&quot;istruzione switch
services: logic-apps
keywords: Istruzione switch
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 196f6d03567cbad8e061d45be546bc86015ede2e
ms.contentlocale: it-it
ms.lasthandoff: 04/04/2017

---

# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Eseguire diverse azioni in app per la logica con un'istruzione switch

Durante la creazione di un flusso di lavoro, è spesso necessario eseguire diverse azioni in base al valore di un oggetto o di un'espressione. Ad esempio, si vuole che l'app per la logica si comporti diversamente in base al codice di stato di una richiesta HTTP o all'opzione selezionata in un messaggio di posta elettronica.

È possibile usare un'istruzione switch per implementare questi scenari. Le app per la logica possono valutare un'espressione o un token e scegliere il case con lo stesso valore per eseguire le azioni specificate. All'istruzione switch deve corrispondere un solo caso.

> [!TIP]
> Come tutti i linguaggi di programmazione, le istruzioni switch supportano solo gli operatori di uguaglianza. Se sono necessari altri operatori relazionali, ad esempio "maggiore di", usare un'istruzione di condizione.
> Per garantire il comportamento di esecuzione deterministico, i casi devono contenere un valore univoco e statico anziché un token dinamico o un'espressione.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) o provare l'[App per la logica gratuita](https://tryappservice.azure.com/).
- [Conoscenze di base di app per la logica](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Aggiungere un'istruzione switch al flusso di lavoro

Per illustrare l'uso delle istruzioni switch, questo esempio crea un'app per la logica che monitora i file caricati in Dropbox. Quando i nuovi file vengono caricati, l'app per la logica invia un messaggio di posta elettronica a un revisore che sceglie se trasferire o meno i file a SharePoint. L'app usa un'istruzione switch che esegue diverse azioni in base al valore selezionato dal revisore.

1. Creare un'app per la logica e selezionare questo trigger: **Dropbox - When a file is created** (Dropbox - Quando viene creato un file).

   ![Usare il trigger Dropbox - When a file is created (Dropbox - Quando viene creato un file)](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Sotto il trigger, aggiungere questa azione: **Outlook.com - Invia messaggio di posta elettronica di approvazione**

   > [!TIP]
   > Le app per la logica supportano anche gli scenari di posta elettronica di approvazione da un account di Outlook Office 365.

   - Se non si ha una connessione esistente, viene richiesto di crearne una.
   - Compilare i campi obbligatori. Ad esempio, sotto **A**, specificare l'indirizzo di posta elettronica per inviare il messaggio di posta elettronica al revisore.
   - In **Opzioni utente**, inserire `Approve, Reject`.

   ![Configurare la connessione](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Aggiungere un'istruzione switc.

   - Selezionare **+ Nuovo passaggio** > **... Altro** > **Aggiungi istruzione switch case**. 
   - Si vuole ora selezionare l'azione da eseguire in base all'output di `SelectedOptions` dall'azione *Invia messaggio di posta elettronica di approvazione*. 
   È possibile trovare questo campo nel selettore **Aggiungi contenuto dinamico**.
   - Usare *Case1* per eseguire la gestione quando l'utente seleziona `Approve`.
     - Se approvato, copiare il file originale in SharePoint Online con l'azione [**SharePoint Online - Crea file**](../connectors/connectors-create-api-sharepointonline.md).
     - Aggiungere un'altra azione nel caso per informare gli utenti che un nuovo file è disponibile in SharePoint.
   - Aggiungere un altro case da gestire quando l'utente seleziona `Reject`.
     - In caso di rifiuto, inviare un'email di notifica per informare gli altri approvatori che il file è stato rifiutato e non è richiesta alcuna azione successiva.
   - Dato che `SelectedOptions` offre solo due opzioni, è possibile lasciare il case vuoto **predefinito**.

   ![Istruzioni switch](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Le istruzioni switch richiedono almeno un case aggiuntivo oltre al case predefinito.

4. Dopo l'istruzione switch, eliminare il file originale caricato in Dropbox aggiungendo l'azione **Dropbox - Elimina file**

5. Salvare l'app per la logica. Testare l'app caricando un file in Dropbox. Si dovrebbe ricevere in breve tempo un messaggio di posta elettronica di approvazione. Selezionare un'opzione e osservare il comportamento.

   > [!TIP]
   > Vedere come [monitorare le app per la logica](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>Capire il codice dietro le istruzioni switch

Dopo aver creato correttamente un'app per la logica tramite un'istruzione switch, esaminare la definizione del codice dietro l'istruzione switch.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

* `"Switch"` è il nome dell'istruzione switch che può essere rinominata per migliorarne la leggibilità. 
* `"type": "Switch"` indica che l'azione è un'istruzione switch. 
* `"expression"` è l'opzione selezionata dal revisore in questo esempio e viene valutata in ogni case dichiarato successivamente nella definizione. 
* `"cases"` può contenere qualsiasi numero di case. Per ogni case, `"Case *"` è il nome predefinito del case, che è possibile rinominare per migliorare la leggibilità. 
`"case"` specifica l'etichetta di case, che viene usata dall'espressione switch per effettuare un confronto e deve contenere un valore costante e univoco. Se nessuno dei case corrisponde all'espressione switch, vengono eseguite le azioni incluse in `"default"`.

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti delle app per la logica di Azure, vedere il [forum sulle app per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento delle App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [aggiungere condizioni](logic-apps-use-logic-app-features.md)
- Informazioni sulla [gestione di errori ed eccezioni](logic-apps-exception-handling.md)
- Esplorare altre [funzionalità di linguaggio del flusso di lavoro](logic-apps-author-definitions.md)
