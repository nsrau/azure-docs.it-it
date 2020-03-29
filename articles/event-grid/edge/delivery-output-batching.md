---
title: Batch di output in Azure Event Grid IoT Edge Documenti Microsoft
description: Output batching in Event Grid on IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a6f033af34088081090251f2e5e7cd4a07ce43cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841748"
---
# <a name="output-batching"></a>Suddivisione in batch per l'output

Griglia di eventi supporta più di un evento in una singola richiesta di recapito. Questa funzionalità consente di aumentare la velocità effettiva di recapito complessiva senza pagare i costi generali HTTP per richiesta. L'invio in batch è disattivato per impostazione predefinita e può essere attivato per ogni sottoscrizione.

> [!WARNING]
> La durata massima consentita per elaborare ogni richiesta di recapito non cambia, anche se il codice del sottoscrittore deve potenzialmente eseguire più lavoro per ogni richiesta in batch. Il timeout di recapito viene impostato su 60 secondi.

## <a name="batching-policy"></a>Criteri di invio in batch

Il comportamento di invio in batch della griglia di eventi può essere personalizzato per ogni sottoscrittore, modificando le due impostazioni seguenti:

* Numero massimo di eventi per batch

  Questa impostazione consente di impostare un limite massimo per il numero di eventi che possono essere aggiunti a una richiesta di recapito in batch.

* Dimensioni batch preferite in Kilobyte

  Questa manopola viene utilizzata per controllare ulteriormente il numero massimo di kilobyte che possono essere inviati per ogni richiesta di recapito

## <a name="batching-behavior"></a>Comportamento di invio in batch

* Tutti o nessuno

  Griglia di eventi opera con la semantica all-or-none. Non supporta l'esito positivo parziale di un recapito in batch. I sottoscrittori devono fare attenzione a richiedere solo il numero di eventi per batch che possono ragionevolmente gestire in 60 secondi.

* Batch ottimistico

  Le impostazioni dei criteri di invio in batch non sono limiti rigorosi sul comportamento di invio in batch e vengono rispettate in base al massimo sforzo. Con una frequenza di eventi bassa, si osserva spesso che la dimensione del batch è inferiore al numero massimo di eventi richiesto per batch.

* L'impostazione predefinita è OFF

  Per impostazione predefinita, Griglia di eventi aggiunge un solo evento a ogni richiesta di recapito. Il modo per attivare l'invio in batch consiste nell'impostare una delle impostazioni menzionate in precedenza nell'articolo nella sottoscrizione di eventi JSON.

* Valori predefiniti

  Non è necessario specificare entrambe le impostazioni (Numero massimo di eventi per batch e Dimensione batch approssimativa in kilo byte) durante la creazione di una sottoscrizione di eventi. Se è impostata una sola impostazione, Griglia di eventi utilizza valori predefiniti (configurabili). Vedere le sezioni seguenti per i valori predefiniti e come eseguirne l'override.

## <a name="turn-on-output-batching"></a>Attivare l'invio in batch di output

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

Le impostazioni dell'ora di distribuzione seguenti controllano il valore massimo consentito durante la creazione di una sottoscrizione di eventi.

| Nome proprietà | Descrizione |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Valore massimo consentito per la `PreferredBatchSizeInKilobytes` manopola. Predefinito `1033`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valore massimo consentito per la `MaxEventsPerBatch` manopola. Predefinito `50`.

## <a name="configuring-runtime-default-values"></a>Configurazione dei valori predefiniti di runtime

Le impostazioni dell'ora di distribuzione seguenti controllano il valore predefinito di runtime di ogni manopola quando non è specificata nella sottoscrizione di eventi. Per ribadire, è necessario impostare almeno una manopola nella sottoscrizione di eventi per attivare il comportamento di invio in batch.

| Nome proprietà | Descrizione |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Dimensione massima della `MaxEventsPerBatch` richiesta di recapito quando viene specificata solo. Predefinito `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Numero massimo di eventi da aggiungere `MaxBatchSizeInBytes` a un batch quando viene specificato solo. Predefinito `10`.
