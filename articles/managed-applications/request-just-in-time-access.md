---
title: Richiedere l'accesso just-in-Time-applicazioni gestite di Azure
description: Descrive in che modo gli editori delle applicazioni gestite di Azure richiedono l'accesso JIT a un'applicazione gestita.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: fdd1dcf5ab6b58a91534a465a8c92c10de0d2097
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528960"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Abilitare e richiedere l'accesso just-in-time per le applicazioni gestite di Azure

I consumer dell'applicazione gestita potrebbero essere riluttanti a concedere l'accesso permanente al gruppo di risorse gestite. Come server di pubblicazione di un'applicazione di gestione, è preferibile che gli utenti conoscano esattamente quando è necessario accedere alle risorse gestite. Per offrire agli utenti un maggiore controllo sulla concessione dell'accesso alle risorse gestite, le applicazioni gestite di Azure forniscono una funzionalità denominata accesso JIT (just-in-Time), attualmente in anteprima.

L'accesso JIT consente di richiedere l'accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. Si ha sempre accesso in sola lettura alle risorse, ma per un periodo di tempo specifico è possibile avere un accesso maggiore.

Il flusso di lavoro per la concessione dell'accesso è:

1. Si aggiunge un'applicazione gestita al Marketplace e si specifica che è disponibile l'accesso JIT.

1. Durante la distribuzione, il consumer Abilita l'accesso JIT per l'istanza dell'applicazione gestita.

1. Dopo la distribuzione, il consumer può modificare le impostazioni per l'accesso JIT.

1. Inviare una richiesta di accesso quando è necessario risolvere i problemi o aggiornare le risorse gestite.

1. Il consumer approva la richiesta.

Questo articolo è incentrato sulle azioni eseguite dai Publisher per abilitare l'accesso JIT e inviare le richieste. Per informazioni sull'approvazione delle richieste di accesso JIT, vedere [approva l'accesso just-in-time nelle applicazioni gestite di Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Aggiungere il passaggio di accesso JIT all'interfaccia utente

Il file CreateUiDefinition. JSON è esattamente come il file dell'interfaccia utente creato per l'accesso permanente, ad eccezione del fatto che è necessario includere un passaggio che consenta agli utenti di abilitare l'accesso JIT. Per altre informazioni sulla pubblicazione della prima offerta di applicazioni gestite in Azure Marketplace, vedere [applicazioni gestite di Azure nel Marketplace](publish-marketplace-app.md).

Per supportare la funzionalità JIT per l'offerta, aggiungere il contenuto seguente al file CreateUiDefinition. JSON:

In "Steps":

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
 
In "output":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> L'accesso JIT è in anteprima. Lo schema per la configurazione JIT potrebbe cambiare nelle iterazioni future.

## <a name="enable-jit-access"></a>Abilitare l'accesso JIT

Quando si definisce l'offerta nel Marketplace, assicurarsi di abilitare l'accesso JIT.

1. Accedere al portale di [pubblicazione Cloud Partner](https://cloudpartner.azure.com).

1. Fornire i valori per pubblicare l'applicazione gestita nel Marketplace. Selezionare **Sì** per **abilitare l'accesso JIT?**

   ![Abilitare l'accesso JIT alla VM](./media/request-just-in-time-access/marketplace-enable.png)

È stato aggiunto un passaggio di configurazione JIT all'interfaccia utente e l'accesso JIT è stato abilitato nell'offerta del Marketplace. Quando i consumer distribuiscono l'applicazione gestita, possono [attivare l'accesso JIT per la relativa istanza](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Richiedere l'accesso

Quando è necessario accedere alle risorse gestite del consumer, si invia una richiesta per un ruolo, un'ora e una durata specifici. Il consumer deve quindi approvare la richiesta.

Per inviare una richiesta di accesso JIT:

1. Selezionare **accesso JIT** per l'applicazione gestita a cui è necessario accedere.

1. Selezionare **ruoli idonei**e selezionare **attiva** nella colonna azione per il ruolo desiderato.

   ![Attivare la richiesta di accesso](./media/request-just-in-time-access/send-request.png)

1. Nel modulo **attiva ruolo** selezionare un'ora di inizio e una durata per il ruolo attivo. Selezionare **Activate (attiva** ) per inviare la richiesta.

   ![Attivare l'accesso](./media/request-just-in-time-access/activate-access.png) 

1. Visualizzare le notifiche per verificare che la nuova richiesta JIT venga inviata correttamente al consumer.

   ![Notifica](./media/request-just-in-time-access/in-progress.png)

   A questo punto, è necessario attendere che il consumer [approvi la richiesta](approve-just-in-time-access.md#approve-requests).

1. Per visualizzare lo stato di tutte le richieste JIT per un'applicazione gestita, selezionare **accesso JIT** e **cronologia delle richieste**.

   ![Visualizza lo stato](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemi noti

L'ID entità dell'account che richiede l'accesso JIT deve essere incluso in modo esplicito nella definizione dell'applicazione gestita. L'account non può essere incluso solo tramite un gruppo specificato nel pacchetto. Questa limitazione verrà risolta in una versione futura.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'approvazione delle richieste di accesso JIT, vedere [approva l'accesso just-in-time nelle applicazioni gestite di Azure](approve-just-in-time-access.md).
