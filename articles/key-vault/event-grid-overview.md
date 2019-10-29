---
title: Monitoraggio Key Vault con griglia di eventi di Azure
description: Usare griglia di eventi di Azure per sottoscrivere eventi Key Vault
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033498"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Monitoraggio Key Vault con griglia di eventi di Azure (anteprima)

Key Vault integrazione con griglia di eventi è attualmente in fase di anteprima. Consente agli utenti di ricevere una notifica quando lo stato di un segreto archiviato in Key Vault è stato modificato. Una modifica dello stato viene definita come un segreto che sta per scadere (entro 30 giorni dalla scadenza), un segreto scaduto o un segreto con una nuova versione disponibile. Sono supportate le notifiche per tutti e tre i tipi di segreto (chiave, certificato e segreto).

Le applicazioni possono rispondere a questi eventi usando architetture senza server moderne, senza la necessità di codice complesso o servizi di polling costosi e inefficienti. Gli eventi vengono inviati tramite [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai gestori di eventi, ad esempio funzioni di [Azure](https://azure.microsoft.com/services/functions/), app per la logica di [Azure](https://azure.microsoft.com/services/logic-apps/)o anche il proprio webhook, pagando solo per le risorse usate. Per informazioni sui prezzi, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Eventi e schemi di Key Vault

Griglia di eventi usa le [sottoscrizioni di eventi](../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Key Vault eventi contengono tutte le informazioni necessarie per rispondere alle modifiche apportate ai dati. È possibile identificare un evento Key Vault perché la proprietà eventType inizia con "Microsoft. Vault".

Per ulteriori informazioni, vedere lo [schema dell'evento Key Vault](../event-grid/event-schema-key-vault.md).

> [!NOTE]
> Gli eventi vengono attivati solo per le versioni segrete (tutti e tre i tipi) creati dopo l'impostazione della sottoscrizione.
>
> Per i segreti esistenti, è necessario generare nuove versioni.

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Le applicazioni che gestiscono gli eventi di Key Vault devono seguire alcune procedure consigliate:

* È possibile configurare più sottoscrizioni per instradare gli eventi allo stesso gestore dell'evento. È importante non presupporre che gli eventi provengano da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provenga dall'insieme di credenziali delle chiavi previsto.
* Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
* Ignorare i campi che non si conoscono.  Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
* Usare le corrispondenze di prefisso e suffisso "subject" per limitare gli eventi a un determinato evento.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Key Vault](key-vault-overview.md)
- [Panoramica di griglia di eventi di Azure](../event-grid/overview.md)
- [Procedura: indirizzare eventi di Key Vault ai Runbook di automazione (anteprima)](event-grid-tutorial.md).
- [Schema di eventi di griglia di eventi di Azure per Azure Key Vault (anteprima)](../event-grid/event-schema-key-vault.md)
- [Panoramica di Automazione di Azure](../automation/index.yml)
