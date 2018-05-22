---
title: Schemi di Griglia di eventi di Azure per gli eventi di Servizi multimediali
description: Descrive le proprietà disponibili per gli eventi di Servizi multimediali con Griglia di eventi di Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Schemi di Griglia di eventi di Azure per gli eventi di Servizi multimediali

Questo articolo illustra gli schemi e le proprietà degli eventi di Servizi multimediali.

## <a name="media-services-job-state-change-event"></a>Evento di modifica dello stato del processo di Servizi multimediali

Questa sezione illustra le proprietà e gli schemi dell'evento di modifica dello stato del processo di Servizi multimediali.  

### <a name="available-event-types"></a>Tipi di evento disponibili

Un **processo** di Servizi multimediali genera i tipi di eventi seguenti:

| Tipo evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Generato quando uno stato del processo viene modificato. |

### <a name="example-event"></a>Evento di esempio

L'esempio seguente illustra lo schema di un evento dello stato del processo completato: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| topic | stringa | Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso della risorsa nella risorsa dell'account di Servizi multimediali. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. Ad esempio, "Microsoft.Media.JobStateChange". |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | oggetto | Dati dell'evento di Servizi multimediali. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| previousState | stringa | Stato del processo prima dell'evento. |
| state | stringa | Nuovo stato del processo notificato in questo evento. Ad esempio, "Queued: il processo è in attesa di risorse" o "Scheduled: il processo sta per iniziare".|

Dove lo stato del processo può essere uno dei valori: *Queued*, *Scheduled*, *Processing*, *Finished*, *Error*, *Canceled*, *Canceling*

## <a name="next-steps"></a>Passaggi successivi

[Register for job state change events](job-state-events-cli-how-to.md) (Eseguire la registrazione agli eventi di modifica dello stato del processo)
