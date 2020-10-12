---
title: Recapito eventi webhook
description: Questo articolo descrive il recapito eventi webhook e la convalida degli endpoint quando si usano webhook.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e9a52d0cb3e4e880d91e1b748d97ef3041298930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87461239"
---
# <a name="webhook-event-delivery"></a>Recapito eventi webhook
I webhook sono uno dei modi per ricevere gli eventi da Griglia di eventi di Azure. Quando un nuovo evento è pronto, il servizio Griglia di eventi esegue il POST di una richiesta HTTP nell'endpoint configurato con l'evento nel corpo della richiesta.

Analogamente a molti altri servizi che supportano i webhook, Griglia di eventi richiede la dimostrazione della proprietà dell'endpoint del webhook prima dell'inizio del recapito di eventi a tale endpoint. Questo requisito impedisce a un utente malintenzionato di sovraccaricare l'endpoint con eventi. Quando si usa uno dei tre servizi di Azure elencati di seguito, l'infrastruttura di Azure gestisce automaticamente questa convalida:

- App per la logica di Azure con il [connettore Griglia di eventi](/connectors/azureeventgrid/)
- Automazione di Azure tramite [webhook](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)
- Funzioni di Azure con il [trigger Griglia di eventi](../azure-functions/functions-bindings-event-grid.md)

## <a name="endpoint-validation-with-event-grid-events"></a>Convalida degli endpoint con eventi di Griglia di eventi
Se si usa un altro tipo di endpoint, ad esempio un trigger HTTP basato su una funzione di Azure, il codice dell'endpoint deve partecipare a un handshake di convalida con Griglia di eventi. Griglia di eventi supporta due modalità di convalida della sottoscrizione.

1. **Handshake sincrono**: In fase di creazione della sottoscrizione di eventi, Griglia di eventi invia un evento di convalida della sottoscrizione all'endpoint. Lo schema di questo evento è simile a qualsiasi altro evento di Griglia di eventi. La parte di dati dell'evento include una proprietà `validationCode`. L'applicazione verifica che la richiesta di convalida sia per una sottoscrizione di eventi prevista e restituisce il codice di convalida nella risposta in modo sincrono. Questo meccanismo di handshake è supportato in tutte le versioni di Griglia di eventi.

2. **Handshake asincrono**: In alcuni casi, non è possibile restituire il codice di convalida nella risposta in modo sincrono. Se ad esempio si usa un servizio di terze parti,[`Zapier`](https://zapier.com) o [IFTTT](https://ifttt.com/), non è possibile rispondere con il codice di convalida a livello di codice.

   A partire dalla versione 2018-05-01-preview, Griglia di eventi supporta un handshake di convalida manuale. Se si sta creando una sottoscrizione di eventi con un SDK o uno strumento che usa l'API 2018-05-01-preview o versione successiva, Griglia di eventi invia una proprietà `validationUrl` nella parte di dati dell'evento di convalida della sottoscrizione. Per completare l'handshake, trovare l'URL nei dati dell'evento ed eseguire una richiesta GET per tale URL. È possibile usare un client REST o un Web browser.

   L'URL specificato è valido per **5 minuti**. Durante questo periodo, lo stato di provisioning della sottoscrizione di eventi è `AwaitingManualAction`. Se non si completa la convalida manuale entro 5 minuti, lo stato di provisioning viene impostato su `Failed`. È possibile creare la sottoscrizione all'evento nuovamente prima di avviare la convalida manuale.

   Questo meccanismo di autenticazione richiede anche che l'endpoint del webhook restituisca il codice di stato HTTP 200 per sapere che la richiesta POST per l'evento di convalida è stata accettata prima di passare alla modalità di convalida manuale. In altre parole, se l'endpoint restituisce 200 ma non restituisce una risposta di convalida in modo sincrono, viene eseguita la transizione della modalità di convalida a manuale. Se entro 5 minuti viene riscontrata una richiesta GET per l'URL di convalida, l'handshake di convalida viene considerato riuscito.

> [!NOTE]
> L'uso di certificati autofirmati per la convalida non è supportato. Usare un certificato firmato da un'autorità di certificazione commerciale (CA, Certificate Authority).

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

È necessario restituire un codice di stato risposta HTTP 200 OK. HTTP 202 Accettata non una risposta di convalida di sottoscrizione di Griglia di eventi riconosciuta come valida. La richiesta HTTP deve essere completata entro 30 secondi. Se l'operazione non termina entro 30 secondi, verrà annullata e potrà essere ritentata dopo 5 secondi. Se tutti i tentativi hanno esito negativo, l'operazione verrà considerata errore di handshake di convalida.

In alternativa, è possibile convalidare manualmente la sottoscrizione inviando una richiesta GET all'URL di convalida. La sottoscrizione dell'evento rimane nello stato in sospeso fino a quando non viene convalidata. L'URL di convalida usa la porta 553. Se le regole del firewall bloccano la porta 553, per la riuscita dell'handshake manuale può essere necessario aggiornarle.

Per un esempio di gestione dell'handshake di convalida della sottoscrizione, vedere un [ esempio C#](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Convalida degli endpoint con CloudEvents v1.0
Se si ha già familiarità con Griglia di eventi, è possibile che si conosca l'handshake di convalida degli endpoint per evitare casi di uso improprio. CloudEvents v1.0 implementa la propria [semantica di protezione dall'uso improprio](webhook-event-delivery.md) tramite il metodo HTTP OPTIONS. Per altre informazioni, leggere [qui](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Quando si usa lo schema CloudEvents per l'output, Griglia di eventi usa la protezione dall'uso improprio di CloudEvents v1.0 al posto del meccanismo di eventi di convalida di Griglia di eventi.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente per informazioni su come risolvere i problemi di convalida delle sottoscrizioni di eventi: 

[Risolvere i problemi di convalida delle sottoscrizioni di eventi](troubleshoot-subscription-validation.md)
