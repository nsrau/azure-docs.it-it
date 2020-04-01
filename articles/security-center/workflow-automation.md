---
title: Automazione del flusso di lavoro nel Centro sicurezza di Azure Documenti Microsoft
description: Informazioni su come creare e automatizzare flussi di lavoro nel Centro sicurezza di AzureLearn how to create and automate workflows in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397905"
---
# <a name="workflow-automation"></a>Automazione del flusso di lavoro

Ogni programma di sicurezza include più flussi di lavoro per la risposta agli incidenti. Questi processi possono includere la notifica alle parti interessate, l'avvio di un processo di gestione delle modifiche e l'applicazione di passaggi di correzione specifici. Gli esperti di sicurezza consigliano di automatizzare il maggior numero possibile di passaggi di tali procedure. L'automazione riduce l'overhead. Può anche migliorare la sicurezza assicurando che i passaggi del processo vengano eseguiti in modo rapido, coerente e in base ai requisiti predefiniti.

Questo articolo descrive la funzionalità di automazione del flusso di lavoro del Centro sicurezza di Azure.This article describes the workflow automation feature of Azure Security Center. Questa funzionalità può attivare App per la logica in avvisi e consigli di sicurezza. Ad esempio, è possibile che si desideri che il Centro sicurezza invii un messaggio di posta elettronica a un utente specifico quando si verifica un avviso. Si apprenderà anche come creare app per la logica usando app per la logica di [Azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

> [!NOTE]
> Se in precedenza hai utilizzato la vista Playbook (anteprima) nella barra laterale, troverai le stesse funzionalità insieme alla funzionalità espansa nella nuova pagina di automazione del flusso di lavoro.


## <a name="requirements"></a>Requisiti

* Per usare i flussi di lavoro delle app per la logica di Azure, è necessario disporre dei ruoli/autorizzazioni delle app per la logica seguenti:To work with Azure Logic Apps workflows, you must have the following Logic Apps roles/permissions:

    * Le autorizzazioni [dell'operatore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) di app per la logica sono necessarie o l'accesso di lettura/trigger app per la logica (questo ruolo non può creare o modificare app per la logica, *ma solo eseguire* quelle esistenti)Logic App Operator permissions are required or Logic App read/trigger access (this role can't create or edit logic apps; only run existing ones)

    * Le autorizzazioni [collaboratori app per](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) la logica sono necessarie per la creazione e la modifica dell'app per la logica

* Se si desidera utilizzare i connettori App per la logica, potrebbero essere necessarie credenziali aggiuntive per accedere ai rispettivi servizi (ad esempio, le istanze di Outlook/Teams/Slack)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Creare un'app per la logica e definire quando deve essere eseguita automaticamenteCreate a Logic App and define when it should automatically run 

1. Dalla barra laterale del Centro sicurezza selezionare **Automazione flusso di lavoro.**

    [![Elenco delle automazioni del flusso di lavoro](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Da questa pagina è possibile creare nuove regole di automazione, nonché abilitare, disabilitare o eliminare quelle esistenti.  
1. Per definire un nuovo flusso di lavoro, fare clic su **Aggiungi automazione flusso di lavoro**. 

    Viene visualizzato un riquadro con le opzioni per la nuova automazione. Qui è possibile inserire:
    1. Un nome e una descrizione per l'automazione.
    1. I trigger che avvieranno questo flusso di lavoro automatico. Ad esempio, è possibile che si desideri che l'app per la logica venga eseguita quando viene generato un avviso di sicurezza contenente "SQL".
    1. App per la logica che verrà eseguita quando vengono soddisfatte le condizioni del trigger. 

        [![Elenco delle automazioni del flusso di lavoro](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Nella sezione Azioni fare clic su **Crea una nuova** per avviare il processo di creazione di App per la logica.

    Verrà visualizzata l'app per la logica di Azure.You'll be taken to Azure Logic Apps.

    [![Creazione di una nuova app per la logicaCreating a new Logic App](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Immettere un nome, un gruppo di risorse e un percorso, quindi fare clic su **Crea**.

1. Nella nuova app per la logica, è possibile scegliere tra i modelli predefiniti incorporati della categoria di sicurezza. In alternativa, è possibile definire un flusso personalizzato di eventi che si verifichi quando viene attivato questo processo.

    Nella finestra di progettazione App per la logica sono supportati i trigger seguenti dai connettori del Centro sicurezza:In the Logic App designer the following triggers from the Security Center connectors are supported:

    * **Quando viene creata o attivata una raccomandazione del Centro sicurezza di AzureWhen an Azure Security Center Recommendation is created or triggered**
    * **Quando viene creato o attivato un avviso del Centro sicurezza di AzureWhen an Azure Security Center Alert is created or triggered** 
    
    > [!TIP]
    > È possibile personalizzare il trigger in modo che sia correlato solo agli avvisi con i livelli di gravità di interesse.
    
    > [!NOTE]
    > Se si usa il trigger legacy "Quando viene attivata una risposta a un avviso del Centro sicurezza di Azure", le app per la logica non verranno avviate dalla funzionalità automazione flusso di lavoro. Utilizzare invece uno dei trigger menzionati in precedenza. 

    [![App per la logica di esempioSample Logic App](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Dopo aver definito l'app per la logica, tornare al riquadro di definizione dell'automazione del flusso di lavoro ("Aggiungere l'automazione del flusso di lavoro"). Fare clic su **Aggiorna** per verificare che la nuova app per la logica sia disponibile per la selezione.

    ![Aggiorna](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selezionare l'app per la logica e salvare l'automazione. Si noti che l'elenco a discesa App per la logica mostra solo app per la logica con connettori del Centro sicurezza di supporto menzionati in precedenza.


## <a name="manually-trigger-a-logic-app"></a>Attivare manualmente un'app per la logicaManually trigger a Logic App

È inoltre possibile eseguire applicazioni logici manualmente quando si visualizza un avviso di sicurezza o qualsiasi raccomandazione che offre [correzione rapida.](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)

Per eseguire manualmente un'app per la logica, aprire un avviso o una raccomandazione che supporta la correzione rapida e fare clic su **Trigger Logic App**:

[![Attivare manualmente un'app per la logicaManually trigger a Logic App](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schemi dei tipi di datiData types schemas

Per visualizzare gli schemi di eventi non elaborati degli eventi di avviso o di avviso di sicurezza passati all'istanza dell'app per la logica, visitare gli schemi dei tipi di dati di [automazione](https://aka.ms/ASCAutomationSchemas)del flusso di lavoro . Ciò può essere utile nei casi in cui non si utilizzano connettori App per la logica del Centro sicurezza incorporati menzionati in precedenza, ma invece si utilizza il connettore HTTP generico di App per la logica: è possibile usare lo schema JSON dell'evento per analizzarlo manualmente come meglio credi.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, si è appreso sulla creazione di applicazioni per la logica, automazione dell'esecuzione nel Centro sicurezza e l'esecuzione manuale. 

Per altri materiali correlati, vedere: 

- [Il modulo Microsoft Learn su come utilizzare l'automazione del flusso di lavoro per automatizzare una risposta di sicurezza](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md)
- [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
- [Informazioni su App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Connettori di App per la logica](https://docs.microsoft.com/connectors/)
- [Schemi dei tipi di dati di automazione del flusso di lavoroWorkflow automation data types schemas](https://aka.ms/ASCAutomationSchemas)
