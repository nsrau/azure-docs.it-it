---
title: Invio in batch di output in griglia di eventi di Azure IoT Edge | Microsoft Docs
description: Invio in batch di output in griglia di eventi IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7d6b83354baf3db5ddb65f94fee1c3dce2dcca94
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992457"
---
# <a name="output-batching"></a>Batch di output

Griglia di eventi supporta la distribuzione di più di un evento in una singola richiesta di recapito. Questa funzionalità consente di aumentare la velocità effettiva di recapito complessiva senza pagare l'overhead per richiesta HTTP. L'invio in batch è disattivato per impostazione predefinita e può essere attivato per sottoscrizione.

> [!WARNING]
> La durata massima consentita per l'elaborazione di ogni richiesta di recapito non viene modificata, anche se il codice del Sottoscrittore potrebbe eseguire più operazioni per ogni richiesta in batch. Il timeout di recapito viene impostato su 60 secondi.

## <a name="batching-policy"></a>Criteri di invio in batch

Il comportamento di invio in batch di griglia di eventi può essere personalizzato per ogni Sottoscrittore, modificando le due impostazioni seguenti:

* Numero massimo di eventi per batch

  Questa impostazione consente di impostare un limite massimo per il numero di eventi che possono essere aggiunti a una richiesta di recapito in batch.

* Dimensioni batch preferite in kilobyte

  Questa manopola viene utilizzata per controllare ulteriormente il numero massimo di kilobyte che possono essere inviati per ogni richiesta di recapito

## <a name="batching-behavior"></a>Comportamento di invio in batch

* All o None

  Griglia di eventi opera con la semantica All-or-none. Non supporta il completamento parziale del recapito di un batch. I Sottoscrittori devono prestare attenzione a richiedere solo il numero di eventi per batch, perché possono essere ragionevolmente gestiti in 60 secondi.

* Batch ottimistica

  Le impostazioni dei criteri di invio in batch non sono limiti rigidi per il comportamento di invio in batch e vengono rispettate in base al massimo sforzo. A bassa frequenza degli eventi, spesso si osserverà che le dimensioni del batch sono inferiori al numero massimo di eventi richiesti per batch.

* Il valore predefinito è OFF

  Per impostazione predefinita, griglia di eventi aggiunge solo un evento a ogni richiesta di recapito. Per attivare la suddivisione in batch, è possibile impostare una delle impostazioni indicate in precedenza nell'articolo nel file JSON della sottoscrizione di eventi.

* Valori predefiniti

  Quando si crea una sottoscrizione di eventi, non è necessario specificare entrambe le impostazioni (numero massimo di eventi per batch e dimensioni approssimative del batch in kilo byte). Se è impostata una sola impostazione, griglia di eventi utilizza i valori predefiniti (configurabili). Vedere le sezioni seguenti per i valori predefiniti e come eseguirne l'override.

## <a name="turn-on-output-batching"></a>Attiva batch di output

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Configurazione dei valori massimi consentiti

Le seguenti impostazioni dell'ora di distribuzione controllano il valore massimo consentito durante la creazione di una sottoscrizione di eventi.

| Nome proprietà | Descrizione |
| ------------- | ----------- | 
| `api:deliveryPolicyLimits:maxpreferredBatchSizeInKilobytes` | Valore massimo consentito per la manopola `PreferredBatchSizeInKilobytes`. `1033`predefinita.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Valore massimo consentito per la manopola `MaxEventsPerBatch`. `50`predefinita.

## <a name="configuring-runtime-default-values"></a>Configurazione dei valori predefiniti di runtime

Le impostazioni dell'ora di distribuzione seguenti controllano il valore predefinito di runtime di ogni manopola quando non è specificato nella sottoscrizione dell'evento. Per ripetere l'iterazione, è necessario impostare almeno una manopola sulla sottoscrizione dell'evento per attivare il comportamento di invio in batch.

| Nome proprietà | Descrizione |
| ------------- | ----------- |
| `broker:defaultMaxBatchSizeInBytes` | Dimensioni massime della richiesta di recapito quando si specifica solo `MaxEventsPerBatch`. `1_058_576`predefinita.
| `broker:defaultMaxEventsPerBatch` | Numero massimo di eventi da aggiungere a un batch quando viene specificato solo `MaxBatchSizeInBytes`. `10`predefinita.
