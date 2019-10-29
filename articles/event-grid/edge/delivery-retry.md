---
title: 'Recapito e nuovo tentativo: griglia di eventi di Azure IoT Edge | Microsoft Docs'
description: Recapito e riprova in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0a678023b1097c4bdec70d866632da6ae4ad57bb
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992392"
---
# <a name="delivery-and-retry"></a>Recapito e nuovo tentativo

Griglia di eventi fornisce il recapito durevole. Tenta di recapitare ogni messaggio almeno una volta per ogni sottoscrizione corrispondente. Se l'endpoint di un Sottoscrittore non riconosce la ricezione di un evento o se si verifica un errore, la griglia di eventi esegue un nuovo tentativo di recapito in base a una **pianificazione** e un **criterio di ripetizione**corretti.  Attualmente il modulo di griglia di eventi recapita un evento alla volta nel Sottoscrittore. Il payload è tuttavia una matrice con un singolo evento.

> [!IMPORTANT]
>Non è disponibile alcun supporto di persistenza per i dati dell'evento. Ciò significa che la ridistribuzione o il riavvio del modulo di griglia di eventi provocherà la perdita di eventuali eventi non ancora recapitati.

## <a name="retry-schedule"></a>Pianificazione tentativi

Griglia di eventi attende fino a 60 secondi per una risposta dopo il recapito di un messaggio. Se l'endpoint del Sottoscrittore non ACK la risposta, il messaggio verrà accodato in una delle code di back-off per i tentativi successivi.

Sono presenti due code di backup preconfigurate che determinano la pianificazione in base alla quale verrà effettuato un nuovo tentativo. Sono:

| Pianificazione | Descrizione |
| ---------| ------------ |
| 1 minuto | I messaggi che terminano qui vengono tentati ogni minuto.
| 10 minuti | I messaggi che terminano qui vengono tentati ogni 10 minuti.

### <a name="how-it-works"></a>Funzionamento

1. Il messaggio arriva al modulo di griglia di eventi. Si è tentato di recapitare immediatamente il prodotto.
1. Se il recapito non riesce, il messaggio viene accodato in una coda di 1 minuto e viene eseguito un nuovo tentativo dopo un minuto.
1. Se il recapito continua a non riuscire, il messaggio viene accodato in una coda di 10 minuti e viene eseguito un nuovo tentativo ogni 10 minuti.
1. I recapiti vengono tentati fino al raggiungimento dei limiti dei criteri.

## <a name="retry-policy-limits"></a>Limiti dei criteri di ripetizione

Sono disponibili due configurazioni che determinano i criteri di ripetizione dei tentativi. Sono:

* Numero massimo di tentativi
* Time-to-Live (TTL) evento

Un evento verrà eliminato se viene raggiunto uno dei limiti del criterio di ripetizione dei tentativi. La pianificazione dei tentativi è stata descritta nella sezione relativa alla pianificazione dei tentativi. La configurazione di questi limiti può essere eseguita per tutti i sottoscrittori o per ogni sottoscrizione. La sezione seguente descrive tutti i dettagli.

## <a name="configuring-defaults-for-all-subscribers"></a>Configurazione delle impostazioni predefinite per tutti i sottoscrittori

Sono disponibili due proprietà: `brokers:defaultMaxDeliveryAttempts` e `broker:defaultEventTimeToLiveInSeconds` che possono essere configurate come parte della distribuzione di griglia di eventi, che controlla le impostazioni predefinite dei criteri di ripetizione dei tentativi per tutti i sottoscrittori.

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Numero massimo di tentativi di recapitare un evento. Valore predefinito: 30.
| `broker:defaultEventTimeToLiveInSeconds` | Durata (TTL) dell'evento in secondi dopo il quale viene eliminato un evento se non viene recapitato. Valore predefinito: **7200** secondi

## <a name="configuring-defaults-per-subscriber"></a>Configurazione delle impostazioni predefinite per ogni Sottoscrittore

È anche possibile specificare i limiti dei criteri di ripetizione dei tentativi in base alla sottoscrizione.
Per informazioni su come configurare le impostazioni predefinite per ogni Sottoscrittore, vedere la [documentazione dell'API](api.md) . Le impostazioni predefinite a livello di sottoscrizione sostituiscono le configurazioni a livello di modulo.

## <a name="examples"></a>Esempi

Nell'esempio seguente vengono impostati i criteri di ripetizione dei tentativi nel modulo di griglia di eventi con maxNumberOfAttempts = 3 e l'evento TTL di 30 minuti

```json
{
  "Env": [
    "broker:defaultMaxDeliveryAttempts=3",
    "broker:defaultEventTimeToLiveInSeconds=1800"
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

Nell'esempio seguente viene impostata una sottoscrizione di Webhook con maxNumberOfAttempts = 3 e un evento TTL di 30 minuti

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
