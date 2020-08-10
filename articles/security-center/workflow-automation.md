---
title: Automazione del flusso di lavoro nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come creare e automatizzare i flussi di lavoro nel centro sicurezza di Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 19fcefdfd163bcbd9cd2adfcba6bf6711b9bee18
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88041673"
---
# <a name="workflow-automation"></a>Automazione del flusso di lavoro

Ogni programma di sicurezza include più flussi di lavoro per la risposta agli eventi imprevisti. Questi processi possono includere la notifica a stakeholder di rilievo, l'avvio di un processo di gestione delle modifiche e l'applicazione di procedure di correzione specifiche. Gli esperti di sicurezza raccomandano di automatizzare quante più procedure possibili. L'automazione riduce i costi. Consente inoltre di migliorare la sicurezza garantendo che i passaggi del processo vengano eseguiti rapidamente, in modo coerente e in base ai requisiti predefiniti.

Questo articolo descrive la funzionalità di automazione del flusso di lavoro del Centro sicurezza di Azure. Questa funzionalità può attivare app per la logica su avvisi di sicurezza e raccomandazioni. Ad esempio, potrebbe essere necessario che il Centro sicurezza invii un messaggio di posta elettronica a un utente specifico quando si verifica un avviso. Si apprenderà anche come creare app per la logica usando app per la [logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Se in precedenza è stata usata la visualizzazione PlayBook (anteprima) nella barra laterale, si troveranno le stesse funzionalità insieme alla funzionalità espansa nella pagina nuova automazione del flusso di lavoro.



## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|Livello gratuito|
|Ruoli e autorizzazioni necessari:|**Reader** nella sottoscrizione che contiene la configurazione di esportazione<br>**Ruolo** o **proprietario** amministratore della sicurezza nel gruppo di risorse<br>Deve disporre anche delle autorizzazioni di scrittura per la risorsa di destinazione<br><br>Per usare i flussi di lavoro di app per la logica di Azure, è necessario disporre anche dei seguenti ruoli/autorizzazioni per le app per la logica:<br> - Le autorizzazioni dell'operatore per l'app per la [logica](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) sono obbligatorie o l'accesso in lettura/attivazione dell'app per la logica *run* . questo ruolo non può creare o modificare app per la logica.<br> - Per la creazione e la modifica delle app per la logica sono necessarie le autorizzazioni di [collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor)<br>Se si vogliono usare i connettori delle app per la logica, potrebbero essere necessarie credenziali aggiuntive per accedere ai rispettivi servizi (ad esempio, le istanze di Outlook/teams/Slack)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Gov per la Cina, altri gov|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Creare un'app per la logica e definire quando deve essere eseguita automaticamente 

1. Dall'intestazione laterale del Centro sicurezza selezionare **automazione del flusso di lavoro**.

    [![Elenco di automazione del flusso di lavoro](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Da questa pagina è possibile creare nuove regole di automazione, nonché abilitare, disabilitare o eliminare quelle esistenti.  
1. Per definire un nuovo flusso di lavoro, fare clic su **Aggiungi automazione del flusso di lavoro**. 

    Viene visualizzato un riquadro con le opzioni per la nuova automazione. Qui è possibile immettere:
    1. Un nome e una descrizione per l'automazione.
    1. Trigger che avvierà questo flusso di lavoro automatico. Ad esempio, potrebbe essere necessario che l'app per la logica venga eseguita quando viene generato un avviso di sicurezza che contiene "SQL".
    1. App per la logica che verrà eseguita quando vengono soddisfatte le condizioni del trigger. 

        [![Elenco di automazione del flusso di lavoro](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Nella sezione azioni fare clic su **Crea nuovo** per avviare il processo di creazione dell'app per la logica.

    Verranno eseguite le app per la logica di Azure.

    [![Creazione di una nuova app per la logica](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Immettere un nome, un gruppo di risorse e un percorso e fare clic su **Crea**.

1. Nella nuova app per la logica è possibile scegliere tra i modelli predefiniti predefiniti della categoria sicurezza. In alternativa, è possibile definire un flusso personalizzato di eventi che si verificano quando viene attivato questo processo.

    Nella finestra di progettazione dell'app per la logica sono supportati i trigger seguenti dei connettori del Centro sicurezza:

    * **Quando viene creata o attivata una raccomandazione del Centro sicurezza di Azure**
    * **Quando viene creato o attivato un avviso del Centro sicurezza di Azure** 
    
    > [!TIP]
    > È possibile personalizzare il trigger in modo che si riferisca solo agli avvisi con i livelli di gravità che interessano.
    
    > [!NOTE]
    > Se si usa il trigger legacy "quando viene attivata una risposta a un avviso del Centro sicurezza di Azure", le app per la logica non verranno avviate dalla funzionalità di automazione del flusso di lavoro. Usare invece uno dei trigger indicati in precedenza. 

    [![App per la logica di esempio](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Dopo aver definito l'app per la logica, tornare al riquadro definizione di automazione del flusso di lavoro ("Aggiungi automazione flusso di lavoro"). Fare clic su **Aggiorna** per assicurarsi che la nuova app per la logica sia disponibile per la selezione.

    ![Aggiorna](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Selezionare l'app per la logica e salvare l'automazione. Si noti che l'elenco a discesa app per la logica Mostra solo le app per la logica con connettori del Centro sicurezza indicati in precedenza.


## <a name="manually-trigger-a-logic-app"></a>Attivare manualmente un'app per la logica

È anche possibile eseguire manualmente le app per la logica quando si visualizza un avviso di sicurezza o qualsiasi raccomandazione che offra una [correzione rapida](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation).

Per eseguire manualmente un'app per la logica, aprire un avviso o un suggerimento che supporti la correzione rapida e fare clic su **trigger app**per la logica:

[![Attivare manualmente un'app per la logica](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schemi dei tipi di dati

Per visualizzare gli schemi di eventi non elaborati degli avvisi di sicurezza o degli eventi di raccomandazione passati all'istanza dell'app per la logica, vedere gli [schemi dei tipi di dati di automazione del flusso di lavoro](https://aka.ms/ASCAutomationSchemas). Questo può essere utile nei casi in cui non si usano i connettori di app per la logica predefiniti del Centro sicurezza indicati in precedenza, ma si usa il connettore HTTP generico dell'app per la logica. è possibile usare lo schema JSON dell'evento per analizzarlo manualmente nel modo appropriato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare app per la logica, automatizzare l'esecuzione nel centro sicurezza ed eseguirle manualmente. 

Per altri materiali correlati, vedere: 

- [Il modulo Microsoft Learn su come usare l'automazione dei flussi di lavoro per automatizzare una risposta di sicurezza](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md)
- [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
- [Informazioni su App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Connettori per le app per la logica](https://docs.microsoft.com/connectors/)
- [Schemi dei tipi di dati di automazione del flusso di lavoro](https://aka.ms/ASCAutomationSchemas)
