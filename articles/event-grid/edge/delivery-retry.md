---
title: Recapito e ritentare - Azure Event Grid IoT Edge Documenti Microsoft
description: Recapito e riprovare in Griglia di eventi su IoT Edge.Delivery and retry in Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841713"
---
# <a name="delivery-and-retry"></a>Recapito e nuovo tentativo

Griglia di eventi fornisce il recapito durevole. Tenta di recapitare ogni messaggio almeno una volta per ogni sottoscrizione corrispondente immediatamente. Se l'endpoint di un sottoscrittore non riconosce la ricezione di un evento o se si verifica un errore, Griglia eventi ritenta il recapito in base a una pianificazione di **tentativi** fissa e a criteri **di ripetizione dei tentativi.**  Per impostazione predefinita, il modulo Griglia di eventi recapita un evento alla volta al sottoscrittore. Il payload è tuttavia un array con un singolo evento. È possibile fare in modo che il modulo fornisca più di un evento alla volta abilitando la funzionalità di invio in batch di output. Per informazioni dettagliate su questa funzionalità, vedere [Invio in batch di output](delivery-output-batching.md).  

> [!IMPORTANT]
>Non è disponibile alcun supporto di persistenza per i dati degli eventi. Ciò significa che la ridistribuzione o il riavvio del modulo Griglia di eventi causerà la perdita di tutti gli eventi non ancora recapitati.

## <a name="retry-schedule"></a>Pianificazione tentativi

Griglia di eventi attende fino a 60 secondi per una risposta dopo il recapito di un messaggio. Se l'endpoint del sottoscrittore non accapsula la risposta, il messaggio verrà accodato in una delle code di ritorno per i tentativi successivi.

Esistono due code di backup preconfigurate che determinano la pianificazione in base alla quale verrà eseguito un nuovo tentativo. ovvero:

| Pianificazione | Descrizione |
| ---------| ------------ |
| 1 minuto | I messaggi che finiscono qui vengono tentati ogni minuto.
| 10 minuti | I messaggi che finiscono qui vengono tentati ogni 10 minuti.

### <a name="how-it-works"></a>Funzionamento

1. Il messaggio arriva nel modulo Griglia di eventi. Tentativo di consegnarlo immediatamente.
1. Se il recapito non riesce, il messaggio viene accodato nella coda di 1 minuto e ritentato dopo un minuto.
1. Se il recapito continua a non riuscire, il messaggio viene accodato nella coda di 10 minuti e ritentato ogni 10 minuti.
1. Le consegne vengono tentate finché non vengono raggiunti i limiti dei criteri di esito positivo o di ripetizione dei tentativi.

## <a name="retry-policy-limits"></a>Limiti dei criteri di ripetizione dei tentativiRetry policy limits

Esistono due configurazioni che determinano i criteri di ripetizione dei tentativi. ovvero:

* Numero massimo di tentativi
* Tempo di live (TTL) per gli eventi

Un evento verrà eliminato se viene raggiunto uno dei limiti dei criteri di ripetizione dei tentativi. La pianificazione dei tentativi stessa è stata descritta nella sezione Pianificazione tentativi. La configurazione di questi limiti può essere eseguita per tutti i sottoscrittori o per sottoscrizione. La sezione seguente descrive ciascuno di essi è un ulteriore dettaglio.

## <a name="configuring-defaults-for-all-subscribers"></a>Configurazione delle impostazioni predefinite per tutti i sottoscrittori

Esistono due `brokers__defaultMaxDeliveryAttempts` proprietà: `broker__defaultEventTimeToLiveInSeconds` e che possono essere configurate come parte della distribuzione di Griglia di eventi, che controlla le impostazioni predefinite dei criteri di ripetizione dei tentativi per tutti i sottoscrittori.

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Numero massimo di tentativi di recapito di un evento. Valore predefinito: 30.
| `broker__defaultEventTimeToLiveInSeconds` | TTL dell'evento in secondi dopo il quale un evento verrà eliminato se non viene recapitato. Valore predefinito: **7200** secondi

## <a name="configuring-defaults-per-subscriber"></a>Configurazione delle impostazioni predefinite per sottoscrittore

È inoltre possibile specificare limiti dei criteri di ripetizione dei tentativi per ogni sottoscrizione.
Vedere la [documentazione dell'API](api.md) per informazioni su come configurare le impostazioni predefinite per ogni sottoscrittore. Le impostazioni predefinite a livello di sottoscrizione sostituiscono le configurazioni a livello di modulo.

## <a name="examples"></a>Esempi

L'esempio seguente imposta i criteri di ripetizione dei tentativi nel modulo Griglia di eventi con maxNumberOfAttempts - 3 e TTL evento di 30 minuti

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

Nell'esempio seguente viene impostata una sottoscrizione di hook Web con maxNumberOfAttempts - 3 e TTL evento di 30 minuti

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
