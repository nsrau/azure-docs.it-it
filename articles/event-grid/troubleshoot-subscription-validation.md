---
title: 'Griglia di eventi di Azure: risoluzione dei problemi di convalida della sottoscrizione'
description: Questo articolo illustra come risolvere i problemi di convalida delle sottoscrizioni.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48844859013507ab684ef8879b7b85dd6b6fe8cd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118988"
---
# <a name="troubleshoot-azure-event-grid-subscription-validations"></a>Risolvere i problemi di convalida delle sottoscrizioni di Griglia di eventi di Azure
Questo articolo offre informazioni sulla risoluzione dei problemi relativi alla convalida delle sottoscrizioni di eventi. 

> [!IMPORTANT]
> Per informazioni dettagliate sulla convalida degli endpoint per webhook, vedere [Recapito di eventi webhook](webhook-event-delivery.md).

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

## <a name="error-code-403"></a>Codice errore: 403
Se il webhook restituisce 403 (accesso negato) nella risposta, controllare se il webhook si trova dietro a un gateway applicazione di Azure o ad un web application firewall. In tal caso, sarà necessario disabilitare le regole del firewall seguenti e ripetere l'operazione HTTP POST:

  - 920300 (Nella richiesta manca un'intestazione Accept, è possibile risolvere il problema)
  - 942430 (Rilevamento anomalie caratteri SQL con restrizioni (args): n. di caratteri speciali in eccesso (12))
  - 920230 (Rilevata codifica multipla URL)
  - 942130 (Attacco SQL injection: rilevata tautologia SQL.)
  - 931130 (Possibile attacco di tipo Remote File Inclusion (RFI): collegamento/riferimento fuori dominio)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, pubblicare il problema nel [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) oppure aprire un [ticket di supporto](https://azure.microsoft.com/support/options/). 
