---
title: 'Esercitazione: Monitoraggio di Key Vault con Griglia di eventi di Azure'
description: 'Esercitazione: Usare Griglia di eventi di Azure per sottoscrivere eventi di Key Vault.'
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5771af365b763d2152eea4ef4f662e08769b378c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133356"
---
# <a name="tutorial-monitoring-key-vault-with-azure-event-grid-preview"></a>Esercitazione: Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)

L'integrazione di Key Vault con Griglia di eventi è attualmente in fase di anteprima. Consente agli utenti di ricevere una notifica quando lo stato di un segreto archiviato nell'insieme di credenziali delle chiavi cambia. Una modifica dello stato è relativa a un segreto che sta per scadere (entro 30 giorni dalla scadenza), un segreto scaduto o un segreto di cui è disponibile una nuova versione. Sono supportate le notifiche per tutti e tre i tipi di segreto (chiave, certificato e segreto).

Le applicazioni possono reagire a questi eventi usando moderne architetture senza server, senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti. Il push degli eventi viene eseguito attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai gestori degli eventi, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al webhook in uso, con pagamento in base all'utilizzo. Per informazioni sui prezzi, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Eventi e schemi di Key Vault

Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Gli eventi di Key Vault contengono tutte le informazioni necessarie per rispondere alle modifiche dei dati. Un evento di Key Vault si riconosce dalla proprietà eventType che inizia con "Microsoft.KeyVault".

Per altre informazioni, vedere [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Gli eventi vengono attivati solo per le versioni dei segreti (tutti e tre i tipi) create dopo l'impostazione della sottoscrizione.
>
> Per i segreti esistenti è necessario generare nuove versioni.

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di Key Vault è consigliabile seguire alcune procedure:

* È possibile configurare più sottoscrizioni per instradare gli eventi allo stesso gestore dell'evento. È importante non dare per scontato che gli eventi provengano da una determinata origine, ma controllare l'argomento del messaggio per assicurarsi che provenga dall'insieme di credenziali delle chiavi previsto.
* Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
* Ignorare i campi che non si conoscono.  Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
* Usare le corrispondenze di prefisso e suffisso "subject" per limitare gli eventi a un determinato evento.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Key Vault](key-vault-overview.md)
- [Panoramica di Griglia di eventi di Azure](../event-grid/overview.md)
- Procedura: [Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure (anteprima)](event-grid-tutorial.md).
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](event-grid-logicapps.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../event-grid/event-schema-key-vault.md)
- [Panoramica di Automazione di Azure](../automation/index.yml)
