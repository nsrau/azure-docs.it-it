---
title: 'Griglia di eventi di Azure: risoluzione dei problemi di convalida della sottoscrizione'
description: Questo articolo illustra come risolvere i problemi di convalida delle sottoscrizioni.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 857760182675d5673a3b09495c2faaf7372a4164
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592941"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Risolvere i problemi di convalida delle sottoscrizioni di Griglia di eventi di Azure
Durante la creazione della sottoscrizione di eventi, se viene visualizzato un messaggio di errore come `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , significa che si è verificato un errore nell'handshake di convalida. Per risolvere questo errore, verificare gli aspetti seguenti:

- Eseguire una richiesta HTTP POST all'URL del webhook con un corpo della richiesta [SubscriptionValidationEvent di esempio](webhook-event-delivery.md#validation-details) con il posting o curl o uno strumento simile.
- Se il webhook sta implementando un meccanismo di handshake di convalida sincrona, verificare che ValidationCode venga restituito come parte della risposta.
- Se il webhook sta implementando un meccanismo di handshake di convalida asincrono, verificare che sia stato restituito HTTP POST 200 OK.
- Se il webhook restituisce `403 (Forbidden)` la risposta, controllare se il webhook è dietro un Gateway applicazione Azure o un Web Application Firewall. In caso contrario, è necessario disabilitare le regole del firewall ed eseguire di nuovo HTTP POST:
    - 920300 (richiesta mancante di un'intestazione Accept)
    - 942430 (argomenti limitati al rilevamento di anomalie dei caratteri SQL (args): numero di caratteri speciali superato (12))
    - 920230 (codifica più URL rilevata)
    - 942130 (attacco SQL injection: rilevato tautologia SQL).
    - 931130 (possibile attacco RFI (Remote File Inclusion) = riferimento all'esterno del dominio/collegamento)

> [!IMPORTANT]
> Per informazioni dettagliate sulla convalida degli endpoint per webhook, vedere [Recapito di eventi webhook](webhook-event-delivery.md).

Le sezioni seguenti illustrano come convalidare le sottoscrizioni di un evento usando post e curl.  

## <a name="validate-event-grid-event-subscription-using-postman"></a>Convalidare la sottoscrizione di eventi di Griglia di eventi con Postman
Di seguito è riportato un esempio d'uso di Postman per la convalida di una sottoscrizione webhook di un evento di Griglia di eventi: 

![Convalida della sottoscrizione di eventi di Griglia di eventi con Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Viene riportato un file .json **SubscriptionValidationEvent** di evento:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Di seguito è riportato l'esempio di risposta riuscita:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Per altre informazioni sulla convalida degli eventi di Griglia di eventi per webhook, vedere [Convalida dell'endpoint con eventi di Griglia di eventi](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).


## <a name="validate-event-grid-event-subscription-using-curl"></a>Convalidare la sottoscrizione di eventi di Griglia di eventi con Curl 
Di seguito è riportato un comando Curl di esempio per la convalida di una sottoscrizione webhook di un evento di Griglia di eventi: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="validate-cloud-event-subscription-using-postman"></a>Convalidare la sottoscrizione di eventi cloud con Postman
Di seguito è riportato un esempio d'uso di Postman per la convalida di una sottoscrizione webhook di un evento cloud: 

![Convalida della sottoscrizione di eventi cloud con Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Usare il metodo **HTTP OPTIONS** per la convalida con eventi cloud. Per altre informazioni sulla convalida di eventi cloud per webhook, vedere [Convalida dell'endpoint con eventi cloud](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="troubleshoot-event-subscription-validation"></a>Risolvere i problemi di convalida della sottoscrizione di eventi

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, pubblicare il problema nel [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) oppure aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
