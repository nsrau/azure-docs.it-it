---
title: 'Griglia di eventi di Azure: risoluzione dei problemi di convalida della sottoscrizione'
description: Questo articolo illustra come risolvere i problemi di convalida delle sottoscrizioni.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630187"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Risolvere gli errori di griglia di eventi di Azure
Questa guida alla risoluzione dei problemi fornisce informazioni sulla risoluzione dei problemi relativi alle convalide delle sottoscrizioni di eventi. 


## <a name="troubleshoot-event-subscription-validation"></a>Risolvere i problemi di convalida della sottoscrizione di eventi

Durante la creazione della sottoscrizione di eventi, se viene visualizzato un messaggio di `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`errore come, significa che si è verificato un errore nell'handshake di convalida. Per risolvere questo errore, verificare gli aspetti seguenti:

- Eseguire una richiesta HTTP POST all'URL del webhook con un corpo della richiesta [SubscriptionValidationEvent di esempio](webhook-event-delivery.md#validation-details) con il posting o curl o uno strumento simile.
- Se il webhook sta implementando un meccanismo di handshake di convalida sincrona, verificare che ValidationCode venga restituito come parte della risposta.
- Se il webhook sta implementando un meccanismo di handshake di convalida asincrono, verificare che il POST HTTP stia restituendo 200 OK.
- Se il webhook restituisce 403 (accesso negato) nella risposta, controllare se il webhook è dietro un gateway applicazione Azure o un Web Application Firewall. In caso contrario, è necessario disabilitare le regole del firewall ed eseguire di nuovo HTTP POST:

  920300 (per la richiesta manca un'intestazione Accept, è possibile risolvere il problema)

  942430 (argomenti limitati al rilevamento di anomalie dei caratteri SQL (args): numero di caratteri speciali superato (12))

  920230 (codifica più URL rilevata)

  942130 (attacco SQL injection: rilevato tautologia SQL).

  931130 (possibile attacco RFI (Remote File Inclusion) = riferimento all'esterno del dominio/collegamento)

> [!IMPORTANT]
> Per informazioni dettagliate sulla convalida degli endpoint per i webhook, vedere [recapito di eventi webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Convalidare la sottoscrizione di eventi di griglia di eventi con il post
Di seguito è riportato un esempio di uso di post per la convalida di una sottoscrizione webhook di un evento di griglia di eventi: 

![Convalida della sottoscrizione di eventi di griglia di eventi con il postore](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Ecco un file JSON SubscriptionValidationEvent di esempio:

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

Per altre informazioni sulla convalida degli eventi di griglia di eventi per i webhook, vedere [convalida degli endpoint con eventi di griglia di eventi](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="validate-cloud-event-subscription-using-postman"></a>Convalidare la sottoscrizione di eventi cloud tramite il post
Ecco un esempio di uso di post per la convalida di una sottoscrizione webhook di un evento Cloud: 

![Convalida della sottoscrizione di eventi cloud con il postore](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Usare il metodo di **Opzioni http** per la convalida con eventi cloud. Per altre informazioni sulla convalida degli eventi cloud per i webhook, vedere [convalida degli endpoint con eventi Cloud](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="event-grid-event-subscription-validation-using-curl"></a>Convalida della sottoscrizione di eventi di griglia di eventi con curl 
Ecco il comando di esempio curl per la convalida di una sottoscrizione webhook di un evento di griglia di eventi: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Passaggi successivi
Se è necessaria ulteriore assistenza, pubblicare il problema nel [forum stack overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
