---
title: Recapito eventi webhook
description: Questo articolo descrive il recapito degli eventi del webhook e la convalida degli endpoint quando si usano i webhook.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 7ae8a21d4ea9216bea13d47ad5ae41f3bc1c2089
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630174"
---
# <a name="webhook-event-delivery"></a>Recapito eventi webhook
I webhook sono uno dei modi per ricevere gli eventi da Griglia di eventi di Azure. Quando un nuovo evento è pronto, il servizio Griglia di eventi esegue il POST di una richiesta HTTP nell'endpoint configurato con l'evento nel corpo della richiesta.

Analogamente a molti altri servizi che supportano i webhook, Griglia di eventi richiede la dimostrazione della proprietà dell'endpoint del webhook prima dell'inizio del recapito di eventi a tale endpoint. Questo requisito impedisce a un utente malintenzionato di sovraccaricare l'endpoint con eventi. Quando si usa uno dei tre servizi di Azure elencati di seguito, l'infrastruttura di Azure gestisce automaticamente questa convalida:

- App per la logica di Azure con il [connettore Griglia di eventi](https://docs.microsoft.com/connectors/azureeventgrid/)
- Automazione di Azure tramite [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Funzioni di Azure con il [trigger Griglia di eventi](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Convalida degli endpoint con eventi di griglia di eventi
Se si usa un altro tipo di endpoint, ad esempio un trigger HTTP basato su una funzione di Azure, il codice dell'endpoint deve partecipare a un handshake di convalida con Griglia di eventi. Griglia di eventi supporta due modalità di convalida della sottoscrizione.

1. **Handshake sincrono**: al momento della creazione della sottoscrizione di eventi, griglia di eventi Invia un evento di convalida della sottoscrizione all'endpoint. Lo schema di questo evento è simile a qualsiasi altro evento di Griglia di eventi. La parte di dati dell'evento include una proprietà `validationCode`. L'applicazione verifica che la richiesta di convalida sia per una sottoscrizione di eventi prevista e restituisce il codice di convalida nella risposta in modo sincrono. Questo meccanismo di handshake è supportato in tutte le versioni di Griglia di eventi.

2. **Handshake asincrono**: in alcuni casi non è possibile restituire il ValidationCode in risposta in modo sincrono. Se ad esempio si usa un servizio di terze parti, ad [`Zapier`](https://zapier.com) esempio o [IFTTT](https://ifttt.com/), non è possibile rispondere a livello di codice con il codice di convalida.

   A partire dalla versione 2018-05-01-preview, Griglia di eventi supporta un handshake di convalida manuale. Se si sta creando una sottoscrizione di eventi con un SDK o uno strumento che usa l'API 2018-05-01-preview o versione successiva, Griglia di eventi invia una proprietà `validationUrl` nella parte di dati dell'evento di convalida della sottoscrizione. Per completare l'handshake, trovare l'URL nei dati degli eventi ed eseguire una richiesta GET. È possibile usare un client REST o un Web browser.

   L'URL specificato è valido per **5 minuti**. Durante questo periodo, lo stato di provisioning della sottoscrizione di eventi è `AwaitingManualAction`. Se la convalida manuale non viene completata entro 5 minuti, lo stato di provisioning è impostato `Failed`su. È possibile creare la sottoscrizione all'evento nuovamente prima di avviare la convalida manuale.

   Questo meccanismo di autenticazione richiede anche che l'endpoint del webhook restituisca un codice di stato HTTP 200 in modo che sappia che il POST per l'evento di convalida è stato accettato prima di poter essere inserito nella modalità di convalida manuale. In altre parole, se l'endpoint restituisce 200 ma non restituisce una risposta di convalida in modo sincrono, la modalità viene passata alla modalità di convalida manuale. Se è presente un'operazione GET sull'URL di convalida entro 5 minuti, l'handshake di convalida viene considerato riuscito.

> [!NOTE]
> L'uso di certificati autofirmati per la convalida non è supportato. Usare invece un certificato firmato da un'autorità di certificazione (CA).

### <a name="validation-details"></a>Dettagli di convalida

- In fase di creazione/aggiornamento della sottoscrizione di eventi, Griglia di eventi inserisce un evento di convalida della sottoscrizione nell'endpoint di destinazione.
- L'evento contiene un valore di intestazione "aeg-event-type: SubscriptionValidation".
- Il corpo dell'evento ha lo stesso schema degli altri eventi di Griglia di eventi.
- La proprietà eventType dell'evento è `Microsoft.EventGrid.SubscriptionValidationEvent`.
- La proprietà Data dell'evento include una proprietà `validationCode` con una stringa generata in modo casuale. ad esempio "validationCode: acb13…".
- I dati dell'evento includono anche una proprietà `validationUrl` con un URL che è possibile usare per convalidare manualmente la sottoscrizione.
- La matrice contiene solo l'evento di convalida. Gli altri eventi vengono inviati in una richiesta separata dopo che è stato rimandato il codice di convalida.
- Gli SDK DataPlane di Griglia di eventi includono classi corrispondenti ai dati degli eventi di convalida della sottoscrizione e alla risposta di convalida della sottoscrizione.

Un esempio di SubscriptionValidationEvent è mostrato di seguito:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
      "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Per dimostrare la proprietà dell'endpoint, rimandare il codice di convalida nella proprietà validationResponse, come mostrato nell'esempio seguente:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

È necessario restituire un codice di stato risposta HTTP 200 OK. HTTP 202 Accettata non una risposta di convalida di sottoscrizione di Griglia di eventi riconosciuta come valida. La richiesta HTTP deve essere completata entro 30 secondi. Se l'operazione non termina entro 30 secondi, l'operazione verrà annullata e potrà essere ritentata dopo 5 secondi. Se tutti i tentativi hanno esito negativo, verranno trattati come errori di handshake di convalida.

In alternativa, è possibile convalidare manualmente la sottoscrizione inviando una richiesta GET all'URL di convalida. La sottoscrizione dell'evento rimane nello stato in sospeso fino a quando non viene convalidata. L'URL di convalida usa la porta 553. Se le regole del firewall bloccano la porta 553, potrebbe essere necessario aggiornare le regole per un handshake manuale riuscito.

Per un esempio di gestione dell'handshake di convalida della sottoscrizione, vedere un [ esempio C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Convalida degli endpoint con CloudEvents v 1.0
Se si ha già familiarità con griglia di eventi, è possibile che l'handshake di convalida degli endpoint della griglia di eventi non venga usato per impedire abusi. CloudEvents v 1.0 implementa la propria [semantica di protezione da abusi](webhook-event-delivery.md) usando il metodo delle opzioni http. Per altre informazioni, vedere [qui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Quando si usa lo schema CloudEvents per l'output, griglia di eventi USA con la protezione dagli abusi di CloudEvents v 1.0 al posto del meccanismo di convalida degli eventi di griglia di eventi.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per informazioni su come risolvere i problemi di convalida della sottoscrizione di eventi: 

[Risolvere i problemi di convalida della sottoscrizione di eventi](troubleshoot-subscription-validation.md)
