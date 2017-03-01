---
title: Usare le istruzioni switch nelle app per la logica di Azure | Microsoft Docs
description: Le istruzioni switch consentono di eseguire facilmente azioni diverse in base al valore di un&quot;espressione nell&quot;App per la logica
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>Usare le istruzioni switch nell'App per la logica
Durante la creazione di un flusso di lavoro, è spesso necessario eseguire azioni diverse in base al valore di un oggetto o di un'espressione. Ad esempio, si potrebbe fare in modo che App per la logica assuma un comportamento diverso in base al codice di stato di una richiesta HTTP o all'opzione selezionata di un'email di approvazione.

Questi scenari si possono ottenere con un'istruzione switch: App per la logica valuta un'espressione o un token e sceglie il caso con lo stesso valore in cui eseguire azioni. All'istruzione switch deve corrispondere un solo caso.

 > [!TIP]
 > Come tutti i linguaggi di programmazione, le istruzioni switch supportano solo gli operatori di uguaglianza. Se sono necessari altri operatori relazionali (ad esempio, maggiore di), usare un'istruzione di condizione.
 >
 > Per garantire il comportamento di esecuzione deterministico, i casi devono contenere un valore univoco e statico anziché un token dinamico o un'espressione.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure attiva.
    - Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) o provare l'[App per la logica gratuita](https://tryappservice.azure.com/).
- [Conoscenze di base di App per la logica](logic-apps-what-are-logic-apps.md).

## <a name="working-with-switch-statement-in-designer"></a>Uso delle istruzioni switch nella finestra di progettazione
Per dimostrare l'uso delle istruzioni switch, verrà creata un'App per la logica che consente di monitorare i file caricati in Dropbox. L'App per la logica invierà un'email di approvazione per determinare la necessità di trasferimento a SharePoint. Le istruzioni switch verranno usate per eseguire azioni diverse a seconda dell'approvatore di valore selezionato.

1. Iniziare a creare un'App per la logica e selezionare il trigger di **Dropbox - When a file is created** (Dropbox - Quando viene creato un file).

 ![Usare il trigger Dropbox - When a file is created (Dropbox - Quando viene creato un file)](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Completare il trigger con un'azione di **Outlook.com - Inviare un'e-mail di approvazione**.

 > [!TIP]
 > Le app per la logica supportano anche uno scenario di posta elettronica di approvazione da un account di Outlook Office 365.

 - Se non si dispone già di una connessione, verrà chiesto di crearne una.
 - Compilare i campi obbligatori, Microsoft invierà un'email a approvers@contoso.com.
 - In *Opzioni utente*, inserire `Approve, Reject`.

 ![Configurare la connessione](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Aggiungere un'istruzione switc.
 - Selezionare **+ Nuovo passaggio**, **... Altro**, **Add a switch statement** (Aggiungi un'istruzione switch).
 - Per selezionare l'operazione da eseguire in base all'output `SelectedOptions` dell'azione*Send approval email* (Invia email di approvazione) cercarla nel selettore **Aggiungi contenuto dinamico**.
 - Usare *Caso 1* da gestire quando l'utente seleziona `Approve`.
    - In caso di approvazione, copiare il file originale in SharePoint Online con l'azione di **SharePoint Online - Crea file**.
    - Aggiungere un'altra azione nel caso per informare gli utenti che un nuovo file è disponibile in SharePoint.
 - Aggiungere un altro caso da gestire quando l'utente seleziona `Reject`.
    - In caso di rifiuto, inviare un'email di notifica per informare gli altri approvatori che il file è stato rifiutato e non è richiesta alcuna azione successiva.
 - È noto che `SelectedOptions` è dotata solo di due opzioni, il caso *predefinito* caso può essere lasciato vuoto.

 ![Istruzioni switch](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > Le istruzioni switch richiedono almeno un caso oltre al caso predefinito.

4. In seguito all'istruzione switch, eliminare il file originale caricato in Dropbox con l'azione di **Dropbox - Elimina file**.

5. Salvare l'app per la logica e testarla caricando un file su Dropbox. L'utente riceve un'email di approvazione subito dopo, seleziona un'opzione e osserva il comportamento.
 > [!TIP]
 > Scoprire come [monitorare le app per la logica](logic-apps-monitor-your-logic-apps.md).

## <a name="understanding-code-behind"></a>Informazioni sul codice sottostante
Ora la creazione di App per la logica mediante le istruzioni switch è stata eseguita correttamente. Di seguito viene illustrato il code sottostante.

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

`"Switch"` è il nome dell'istruzione switch che può essere rinominata per migliorarne la leggibilità. `"type": "Switch"` indica che l'azione è un'istruzione switch. `"expression"`, in questo caso, un'opzione selezionata dell'utente, viene valutata per ogni caso dichiarato in seguito nella definizione. `"cases"` può contenere diversi casi e se nessun caso corrisponde all'espressione switch, vengono eseguite le azioni in `"default"`.

In `"cases"` ci possono essere diversi casi. Per ogni caso, `"Case 1"` è il nome del caso che può essere rinominato per migliorarne la leggibilità. `"case"` specifica l'etichetta del caso, con cui viene confronta l'espressione switch e che deve essere un valore costante e univoco.  

## <a name="next-steps"></a>Passaggi successivi
- Provare altre [funzionalità dell'App per la logica](logic-apps-use-logic-app-features.md).
- Informazioni sulla [gestione degli errori e delle eccezioni](logic-apps-exception-handling.md).
- Esplorare altre [funzionalità del linguaggio del flusso di lavoro](logic-apps-author-definitions.md).
- Lasciare un commento con le domande, i commenti o un [suggerimento su come migliorare l'App per la logica](https://feedback.azure.com/forums/287593-logic-apps).


<!--HONumber=Feb17_HO2-->


