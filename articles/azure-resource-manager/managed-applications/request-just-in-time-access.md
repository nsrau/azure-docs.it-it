---
title: Richiedere l'accesso just-in-time
description: Viene descritto come gli editori di applicazioni gestite di Azure richiedono l'accesso just-in-time a un'applicazione gestita.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651384"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Abilitare e richiedere l'accesso Just-In-Time per le applicazioni gestite di AzureEnable and request just-in-time access for Azure Managed Applications

I consumer dell'applicazione gestita possono essere riluttanti a concedere l'accesso permanente al gruppo di risorse gestite. In qualità di editore di un'applicazione di gestione, è preferibile che gli utenti sappiano esattamente quando è necessario accedere alle risorse gestite. Per concedere ai consumer un maggiore controllo sulla concessione dell'accesso alle risorse gestite, le applicazioni gestite di Azure forniscono una funzionalità denominata accesso JIT (Just-In-Time), attualmente in anteprima.

L'accesso JIT consente di richiedere un accesso con privilegi elevati alle risorse di un'applicazione gestita per la risoluzione dei problemi o la manutenzione. Si dispone sempre dell'accesso in sola lettura alle risorse, ma per un periodo di tempo specifico è possibile ottenere un accesso maggiore.

Il flusso di lavoro per concedere l'accesso è:The work flow for granting access is:

1. Aggiungere un'applicazione gestita al marketplace e specificare che l'accesso JIT è disponibile.

1. Durante la distribuzione, il consumer abilita l'accesso JIT per l'istanza dell'applicazione gestita.

1. Dopo la distribuzione, il consumer può modificare le impostazioni per l'accesso JIT.

1. Si invia una richiesta di accesso quando è necessario risolvere i problemi o aggiornare le risorse gestite.

1. Il consumatore approva la tua richiesta.

Questo articolo è incentrato sulle azioni eseguite dagli editori per abilitare l'accesso JIT e inviare richieste. Per informazioni sull'approvazione delle richieste di accesso JIT, vedere [Approvare l'accesso Just-In-Time in Applicazioni gestite](approve-just-in-time-access.md)di Azure.

## <a name="add-jit-access-step-to-ui"></a>Aggiungere il passaggio di accesso JIT all'interfaccia utenteAdd JIT access step to UI

Il file CreateUiDefinition.json è esattamente come il file dell'interfaccia utente creato per l'accesso permanente, ad eccezione del fatto che è necessario includere un passaggio che consenta ai consumer di abilitare l'accesso JIT. Per altre informazioni sulla pubblicazione della prima offerta di applicazioni gestite in Azure Marketplace, vedere [Applicazioni gestite di Azure nel Marketplace.](publish-marketplace-app.md)

Per supportare la funzionalità JIT per l'offerta, aggiungere il contenuto seguente al file CreateUiDefinition.json:To support JIT capability for your offer, add the following content to your CreateUiDefinition.json file:

In "passi":

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
 
In "uscite":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> L'accesso JIT è in anteprima. Lo schema per la configurazione JIT potrebbe cambiare nelle iterazioni future.

## <a name="enable-jit-access"></a>Abilitare l'accesso JIT

Quando definisci la tua offerta nel marketplace, assicurati di abilitare l'accesso JIT.

1. Accedere al portale di [pubblicazione Cloud Partner.](https://cloudpartner.azure.com)

1. Fornire i valori per pubblicare l'applicazione gestita nel marketplace. Selezionare **Sì** per **Abilita accesso JIT?**

   ![Abilitare l'accesso JIT alla VM](./media/request-just-in-time-access/marketplace-enable.png)

È stato aggiunto un passaggio di configurazione JIT all'interfaccia utente e l'accesso JIT è stato abilitato nell'offerta del marketplace. Quando i consumer distribuiscono l'applicazione gestita, possono [attivare l'accesso JIT per l'istanza](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Richiedere l'accesso

Quando è necessario accedere alle risorse gestite del consumer, si invia una richiesta per un ruolo, un tempo e una durata specifici. Il consumer deve quindi approvare la richiesta.

Per inviare una richiesta di accesso JIT:

1. Selezionare **Accesso JIT** per l'applicazione gestita a cui è necessario accedere.

1. Selezionare **Ruoli idonei**e selezionare **Attiva** nella colonna Azione per il ruolo desiderato.

   ![Attivare la richiesta di accesso](./media/request-just-in-time-access/send-request.png)

1. Nel modulo **Attiva ruolo** selezionare un'ora di inizio e una durata per il ruolo da attivare. Selezionare **Attiva** per inviare la richiesta.

   ![Attivare l'accesso](./media/request-just-in-time-access/activate-access.png) 

1. Visualizzare le notifiche per verificare che la nuova richiesta JIT sia stata inviata correttamente al consumer.

   ![Notification](./media/request-just-in-time-access/in-progress.png)

   A questo punto, è necessario attendere che il consumer [approvi la richiesta.](approve-just-in-time-access.md#approve-requests)

1. Per visualizzare lo stato di tutte le richieste JIT per un'applicazione gestita, selezionare **Accesso JIT** e **Cronologia richieste**.

   ![Visualizzare lo stato](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemi noti

L'ID entità dell'account che richiede l'accesso JIT deve essere incluso in modo esplicito nella definizione dell'applicazione gestita. L'account non può essere incluso solo tramite un gruppo specificato nel pacchetto. Questa limitazione verrà corretta in una versione futura.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'approvazione delle richieste di accesso JIT, vedere [Approvare l'accesso just-in-time nelle applicazioni gestite](approve-just-in-time-access.md)di Azure.
