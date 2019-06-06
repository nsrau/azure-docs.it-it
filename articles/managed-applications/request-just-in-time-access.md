---
title: Abilitare e richiedere l'accesso just-in-time per applicazioni gestite di Azure
description: Viene descritto come server di pubblicazione di applicazioni gestite di Azure richiedono l'accesso just-in-time a un'applicazione gestita.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481775"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Abilitare e richiedere l'accesso just-in-time per applicazioni gestite di Azure

I consumer dell'applicazione gestita possono essere riluttanti a concedere l'accesso permanente al gruppo di risorse gestite. Come server di pubblicazione di un'applicazione di gestione, si potrebbe preferire che i consumer sappiano esattamente quando sarà necessario accedere alle risorse gestite. Per fornire ai fruitori un maggiore controllo sulla concessione dell'accesso alle risorse gestite, le applicazioni gestite di Azure offre una funzionalità denominata just-in-time (JIT) accesso, che è attualmente in anteprima.

Accesso JIT consente di richiedere l'accesso con privilegi elevato per le risorse dell'applicazione gestita per la risoluzione dei problemi o manutenzione. Si hanno sempre accesso in lettura alle risorse, ma per un periodo di tempo specifico è possibile avere un accesso più ampio.

Il flusso di lavoro per la concessione dell'accesso è:

1. Aggiungere un'applicazione gestita nel Marketplace e specificare che l'accesso JIT è disponibile.

1. Durante la distribuzione, il consumer consente l'accesso JIT per quell'istanza dell'applicazione gestita.

1. Dopo la distribuzione, il consumer può modificare le impostazioni per l'accesso JIT.

1. Si invia una richiesta per l'accesso quando è necessario risolvere i problemi o aggiornare le risorse gestite.

1. L'utente approva la richiesta.

Questo articolo è incentrato sulle azioni server di pubblicazione eseguire per abilitare l'accesso JIT e inviare le richieste. Per altre informazioni sull'approvazione richieste di accesso JIT, vedere [approvare l'accesso just-in-time in applicazioni gestite di Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Aggiungere il passaggio di accesso JIT all'interfaccia utente

Il file createuidefinition. JSON è esattamente come il file di interfaccia utente creato per l'accesso permanente, ma è necessario includere un passaggio che consente ai consumer di abilitare l'accesso JIT. Per altre informazioni sulla pubblicazione dell'applicazione gestita prima offerta in Azure Marketplace, vedere [applicazioni gestite di Azure nel Marketplace](publish-marketplace-app.md).

Per supportare la funzionalità JIT per l'offerta, aggiungere il contenuto seguente al file createuidefinition. JSON:

Nella sezione "passaggi":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
In "outputs":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Accesso JIT è disponibile in anteprima. Lo schema per la configurazione di JIT potrebbe cambiare nelle future iterazioni.

## <a name="enable-jit-access"></a>Abilitare l'accesso JIT

Quando si definisce l'offerta nel marketplace, assicurarsi di che abilitare l'accesso JIT.

1. Accedi per il [portale di pubblicazione per Cloud Partner](https://cloudpartner.azure.com).

1. Fornire i valori per pubblicare l'applicazione gestita nel marketplace. Selezionare **Yes** per **abilitare l'accesso JIT?**

   ![Abilitare l'accesso JIT alla VM](./media/request-just-in-time-access/marketplace-enable.png)

Sia stato aggiunto un passaggio di configurazione JIT per l'interfaccia utente e avrà abilitato l'accesso JIT nell'offerta di marketplace. Quando i consumer distribuire l'applicazione gestita, è possibile [abilitare l'accesso JIT per tale istanza](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Richiedere l'accesso

Quando si desidera accedere alle risorse gestite del consumer, si invia una richiesta per un ruolo specifico, ora e durata. Il consumer deve quindi approvare la richiesta.

Per inviare una richiesta di accesso JIT:

1. Selezionare **accesso JIT** per l'applicazione gestita è necessario accedere.

1. Selezionare **ruoli idonei**e selezionare **Activate** nella colonna dell'azione per il ruolo desiderato.

   ![Attivare la richiesta di accesso](./media/request-just-in-time-access/send-request.png)

1. Nel **attivare ruoli** formano, selezionare un'ora di inizio e la durata per il ruolo sia attivo. Selezionare **Activate** per inviare la richiesta.

   ![Attivare l'accesso](./media/request-just-in-time-access/activate-access.png) 

1. Visualizzare le notifiche per verificare che la nuova richiesta JIT verrà inviata al consumer.

   ![Notifica](./media/request-just-in-time-access/in-progress.png)

   A questo punto, è necessario attendere che il consumer possa [approvi la richiesta](approve-just-in-time-access.md#approve-requests).

1. Per visualizzare lo stato di tutte le richieste JIT per un'applicazione gestita, selezionare **accesso JIT** e **cronologia richiesta**.

   ![Visualizzare lo stato](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemi noti

L'ID dell'entità di account che richiede l'accesso JIT deve essere incluso in modo esplicito nella definizione di applicazione gestita. L'account non può essere solo incluso tramite un gruppo specificato nel pacchetto. Questa limitazione verrà risolto in una versione futura.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'approvazione delle richieste di accesso JIT, vedere [approvare l'accesso just-in-time in applicazioni gestite di Azure](approve-just-in-time-access.md).
