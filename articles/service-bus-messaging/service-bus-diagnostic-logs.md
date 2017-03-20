---
title: Log di diagnostica del bus di servizio di Azure | Microsoft Docs
description: Informazioni su come configurare i log di diagnostica per il bus di servizio in Azure.
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
ms.sourcegitcommit: abcb0eee979853948cf6d981ff8f3a457eeeeef0
ms.openlocfilehash: 65fe81dc90f2dc7a251860adfdd8374912cb8d73
ms.lasthandoff: 03/01/2017


---
# <a name="service-bus-diagnostic-logs"></a>Log di diagnostica del bus di servizio

È possibile visualizzare due tipi di log per il bus di servizio di Azure:
* **[Log attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Questi log contengono informazioni sulle operazioni eseguite in un processo. I log sono sempre attivati.
* **[Log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. È possibile configurare i log di diagnostica per informazioni più complete su tutto ciò che accade in un processo. I log di diagnostica coprono le attività che si verificano dal momento della creazione del processo fino alla sua eliminazione, inclusi gli aggiornamenti e le attività che si verificano durante l'esecuzione del processo.

## <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica
I log di diagnostica sono **disattivati** per impostazione predefinita. Per attivarli:

1.    Nel Portale di Azure passare al pannello Processo di streaming.

2.    In **Monitoraggio** andare al pannello **Log di diagnostica**.

    ![navigazione al pannello dei log di diagnostica](./media/service-bus-diagnostic-logs/image1.png)  

3.    Selezionare **Attiva diagnostica**.

    ![attivazione dei log di diagnostica](./media/service-bus-diagnostic-logs/image2.png)

4.    Per **Stato** selezionare **Attivo**.

    ![modifica dello stato dei log di diagnostica](./media/service-bus-diagnostic-logs/image3.png)

5.    Impostare la destinazione di archiviazione desiderata, ad esempio un account di archiviazione, un hub eventi o Azure Log Analytics.

6.    Selezionare le categorie di log che si desidera raccogliere, ad esempio **Esecuzione** o **Creazione e modifica**.

7.    Salvare le nuove impostazioni di diagnostica.

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, all'interno del pannello **Log di diagnostica**.

Per altre informazioni sulla configurazione della diagnostica, consultare la [panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica

Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce presenta campi stringa nel formato descritto negli esempi seguenti.

## <a name="operation-logs-example"></a>Esempio di log operazioni

I log nella categoria **OperationalLogs** acquisiscono i dati relativi all'operazione del bus di servizio. In particolare, questi log acquisiscono il tipo di operazione, tra cui la creazione delle code, le risorse usate e lo stato dell'operazione.

Le stringhe JSON dei log operazioni includono gli elementi elencati nella tabella seguente:

Nome | Descrizione
------- | -------
ActivityId | ID interno, usato a scopo di rilevamento
EventName | Nome operazione             
resourceId | ID della risorsa Azure Resource Manager
SubscriptionId | ID sottoscrizione
EventTimeString | Durata dell'operazione
EventProperties | Proprietà dell'operazione
Status | Stato dell'operazione
Chiamante | Chiamante dell'operazione (Portale di Azure o client di gestione)
category | OperationalLogs

Di seguito è riportato un esempio di stringa JSON di log operazioni:

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

