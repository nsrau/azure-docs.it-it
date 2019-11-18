---
title: Automazione del flusso di lavoro (anteprima) nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come creare e automatizzare i flussi di lavoro nel centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 1abfd8af7e0ef18d4e7bcf05b4726c5d0bcb0a84
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151701"
---
# <a name="workflow-automation-preview"></a>Automazione del flusso di lavoro (anteprima)

Ogni programma di sicurezza include più flussi di lavoro per la risposta agli eventi imprevisti. Questi processi possono includere la notifica a stakeholder pertinenti, l'avvio di un processo di gestione delle modifiche e l'applicazione di procedure di correzione specifiche. Gli esperti di sicurezza raccomandano di automatizzare tutti i passaggi di queste procedure come possibile. L'automazione riduce i costi. Consente inoltre di migliorare la sicurezza garantendo che i passaggi del processo vengano eseguiti rapidamente, in modo coerente e in base ai requisiti predefiniti.

Questo articolo descrive la funzionalità di automazione del flusso di lavoro (anteprima) del Centro sicurezza di Azure. Questa funzionalità di anteprima può attivare app per la logica in avvisi di sicurezza e consigli. Ad esempio, potrebbe essere necessario che il Centro sicurezza invii un messaggio di posta elettronica a un utente specifico quando si verifica un avviso. Si apprenderà anche come creare app per la logica usando app per la [logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Se in precedenza è stata usata la visualizzazione PlayBook (anteprima) nella barra laterale, le stesse funzionalità sono disponibili insieme alla funzionalità espansa nella nuova pagina di automazione del flusso di lavoro (anteprima).


## <a name="requirements"></a>Requisiti

* Per usare i flussi di lavoro di app per la logica di Azure, è necessario disporre dei seguenti ruoli/autorizzazioni per le app per la logica:

    * Le autorizzazioni dell'operatore per l'app per la [logica](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) sono obbligatorie o l'accesso in lettura/attivazione dell'app per la logica . questo ruolo non può creare o modificare app per la logica.

    * Per la creazione e la modifica delle app per la logica sono necessarie le autorizzazioni di [collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)

* Se si vogliono usare i connettori delle app per la logica, potrebbero essere necessarie credenziali aggiuntive per accedere ai rispettivi servizi (ad esempio, le istanze di Outlook/teams/Slack)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Creare un'app per la logica e definire quando deve essere eseguita automaticamente 

1. Dall'intestazione laterale del Centro sicurezza, selezionare **automazione del flusso di lavoro (anteprima)** .

    [![elenco di automazione del flusso di lavoro](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Da questa pagina è possibile creare nuove regole di automazione, nonché abilitare, disabilitare o eliminare quelle esistenti.  
1. Per definire un nuovo flusso di lavoro, fare clic su **Aggiungi automazione del flusso di lavoro**. 

    Viene visualizzato un riquadro con le opzioni per la nuova automazione. Qui è possibile immettere:
    1. Un nome e una descrizione per l'automazione.
    1. Trigger che avvierà questo flusso di lavoro automatico. Ad esempio, potrebbe essere necessario che l'app per la logica venga eseguita quando viene generato un avviso di sicurezza che contiene "SQL".
    1. App per la logica che verrà eseguita quando vengono soddisfatte le condizioni del trigger. 

        [![elenco di automazione del flusso di lavoro](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Nella sezione azioni fare clic su **Crea nuovo** per avviare il processo di creazione dell'app per la logica.

    Verranno eseguite le app per la logica di Azure.

    [![la creazione di una nuova app per la logica](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Immettere un nome, un gruppo di risorse e un percorso e fare clic su **Crea**.

1. Nella nuova app per la logica è possibile scegliere tra i modelli predefiniti predefiniti della categoria sicurezza. In alternativa, è possibile definire un flusso personalizzato di eventi che si verificano quando viene attivato questo processo.

    Nella finestra di progettazione dell'app per la logica sono supportati i trigger seguenti dei connettori del Centro sicurezza:

    * **Quando viene creata o attivata una raccomandazione del Centro sicurezza di Azure (anteprima)**
    * **Quando viene creato o attivato un avviso del Centro sicurezza di Azure (anteprima)**
    
    > [!NOTE]
    > Se si usa il trigger legacy "quando viene attivata una risposta a un avviso del Centro sicurezza di Azure", le app per la logica non verranno avviate dalla funzionalità di automazione del flusso di lavoro. Usare invece uno dei trigger indicati in precedenza. 

    [![app per la logica di esempio](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Dopo aver definito l'app per la logica, tornare al riquadro definizione di automazione del flusso di lavoro ("Aggiungi automazione flusso di lavoro"). Fare clic su **Aggiorna** per assicurarsi che la nuova app per la logica sia disponibile per la selezione.

    ![Aggiorna](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selezionare l'app per la logica e salvare l'automazione. Si noti che l'elenco a discesa app per la logica Mostra solo le app per la logica con connettori del Centro sicurezza indicati in precedenza.


## <a name="manually-trigger-a-logic-app"></a>Attivare manualmente un'app per la logica

È anche possibile eseguire manualmente le app per la logica quando si visualizza una raccomandazione di sicurezza.

Per eseguire manualmente un'app per la logica, aprire una raccomandazione e fare clic su trigger app per la logica (anteprima):

[![attivare manualmente un'app per la logica](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare app per la logica, eseguirle manualmente nel centro sicurezza e automatizzarne l'esecuzione. 

Per altri materiali correlati, vedere gli articoli seguenti: 

- [Raccomandazioni per la sicurezza nel centro sicurezza di Azure](security-center-recommendations.md)
- [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
- [Informazioni sulle app per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Connettori di App per la logica](https://docs.microsoft.com/connectors/)
