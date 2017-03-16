---
title: Log di diagnostica del bus di servizio di Azure | Microsoft Docs
description: Informazioni su come analizzare i log di diagnostica del bus di servizio in Microsoft Azure.
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Log di diagnostica del bus di servizio

## <a name="introduction"></a>Introduzione
Il bus di servizio espone due tipi di log: 
* [Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), che sono sempre abilitati e offrono informazioni approfondite sulle operazioni eseguite sui processi.
* [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), che sono configurabili dall'utente e offrono informazioni più approfondite su ciò che accade durante il processo, a partire da quando viene creato e aggiornato, mentre viene eseguito, fino a quando viene eliminato.

## <a name="how-to-enable-diagnostic-logs"></a>Come abilitare i log di diagnostica
I log di diagnostica sono **disattivati** per impostazione predefinita. Per abilitarli, seguire questa procedura:

Accedere al Portale di Azure, passare al pannello del processo di streaming e usare il pannello "Log di diagnostica" in "Monitoraggio".

![navigazione al pannello dei log di diagnostica](./media/service-bus-diagnostic-logs/image1.png)  

Fare quindi clic sul collegamento "Attiva diagnostica"

![attivazione dei log di diagnostica](./media/service-bus-diagnostic-logs/image2.png)

Nella diagnostica aperta modificare lo stato su "On" (Attivo).

![modifica dello stato dei log di diagnostica](./media/service-bus-diagnostic-logs/image3.png)

Configurare la destinazione di archiviazione desiderata (account di archiviazione, hub eventi, Log Analytics) e selezionare le categorie di log che si desidera raccogliere (esecuzione, creazione). Salvare quindi la nuova configurazione di diagnostica.

Dopo essere stata salvata, la configurazione richiederà circa 10 minuti per risultare attiva, quindi i log inizieranno a essere visualizzati nella destinazione di archiviazione configurata, visualizzabile nel pannello "Log di diagnostica":

Altre informazioni sulla configurazione sono disponibili nella pagina relativa ai [log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica

Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti campi stringa nel formato illustrato di seguito.

### <a name="operation-logs"></a>Log delle operazioni

OperationalLogs acquisisce ciò che avviene durante il funzionamento del bus di servizio, in particolare il tipo di operazione, come la creazione di code, le risorse usate e lo stato dell'operazione.

Nome | Descrizione
------- | -------
ActivityId | ID interno per scopi di rilevamento
EventName | Nome operazione             
resourceId | ID risorsa ARM
SubscriptionId | ID sottoscrizione
EventTimeString | Durata dell'operazione
EventProperties | Proprietà dell'operazione
Stato | Stato dell'operazione
Chiamante | Chiamante dell'operazione (portale o client di gestione)
category | OperationalLogs

#### <a name="example-operation-log"></a>Log di operazioni di esempio

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione al bus di servizio](service-bus-messaging-overview.md)
* [Introduzione al bus di servizio](service-bus-create-namespace-portal.md)

