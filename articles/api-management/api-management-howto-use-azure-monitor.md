---
title: Monitorare Gestione API con Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come monitorare il servizio Gestione API di Azure usando Monitoraggio di Azure.
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0f64947755c79739bb6f15325929bd074cfd7210
ms.lasthandoff: 03/31/2017

---
# <a name="monitor-api-management-with-azure-monitor"></a>Monitorare Gestione API con Monitoraggio di Azure
Monitoraggio di Azure è un servizio di Azure che offre un'unica origine per il monitoraggio di tutte le risorse di Azure. Con Monitoraggio di Azure è possibile visualizzare, eseguire query, indirizzare, archiviare ed effettuare operazioni sulle metriche e sui log provenienti dalle risorse di Azure, tra cui Gestione API. 

Il video seguente illustra come monitorare Gestione API usando Monitoraggio di Azure. Per altre informazioni su Monitoraggio di Azure, vedere l'[introduzione a Monitoraggio di Azure]. 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>Metrica
Gestione API attualmente genera cinque metriche e si prevede di aggiungerne altre in futuro. Le metriche vengono generate ogni minuto in modo da ottenere una visibilità quasi in tempo reale dello stato e dell'integrità delle API. Di seguito è riportato un riepilogo delle metriche:
* Totale richieste gateway: numero di richieste di API nel periodo. 
* Richieste gateway riuscite: numero di richieste di API che hanno ricevuto codici di risposta HTTP corretta, tra cui 304, 307 e qualsiasi valore inferiore a 301, ad esempio, 200. 
* Richieste gateway non riuscite: numero di richieste di API che hanno ricevuto codici di risposta HTTP errata, tra cui 400 e qualsiasi valore superiore a 500.
* Richieste gateway non autorizzate: numero di richieste di API che hanno ricevuto codici di risposta HTTP tra cui 401, 403 e 429. 
* Altre richieste gateway: numero di richieste di API che hanno ricevuto codici di risposta HTTP che non appartengono a una delle categorie precedenti, ad esempio, 418.

È possibile accedere alle metriche del servizio Gestione API o alle metriche di tutte le risorse di Azure in Monitoraggio di Azure. Per visualizzare le metriche del servizio Gestione API:
1. Aprire il Portale di Azure.
2. Accedere al servizio Gestione API.
3. Fare clic su **Metrica**.

![Pannello delle metriche][metrics-blade]

Per altre informazioni sull'uso delle metriche, vedere [Panoramica delle metriche].

## <a name="activity-logs"></a>Log attività
I log attività offrono informazioni dettagliate sulle operazioni eseguite nei servizi Gestione API. In precedenza erano noti come "log di controllo" o "log operativi". L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sui servizi Gestione API. 

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET) né le operazioni eseguite nel portale di pubblicazione classico o usando le API di gestione originali.

È possibile accedere ai log attività del servizio Gestione API o ai log di tutte le risorse di Azure in Monitoraggio di Azure. Per visualizzare i log attività del servizio Gestione API:
1. Aprire il Portale di Azure.
2. Accedere al servizio Gestione API.
3. Fare clic su **Log attività**.

![Pannello dei log attività][activity-logs-blade]

Per altre informazioni sull'uso dei log attività, vedere [Panoramica del log attività di Azure].

## <a name="alerts"></a>Avvisi
È possibile configurare un avviso basato sulle metriche e sui log attività. Monitoraggio di Azure consente di configurare un avviso in modo che, se attivato, esegua queste operazioni:

* Inviare una notifica via posta elettronica
* Chiamare un webhook
* Richiamare un'app per la logica di Azure

È possibile configurare regole per gli avvisi nel servizio Gestione API o in Monitoraggio di Azure. Per configurarle in Gestione API: 
1. Aprire il Portale di Azure.
2. Accedere al servizio Gestione API.
3. Fare clic su **Regole di avviso**.

![Pannello Regole di avviso][alert-rules-blade]

Per altre informazioni sull'uso degli avvisi, vedere [Panoramica degli avvisi].

## <a name="diagnostic-logs"></a>Log di diagnostica
I log di diagnostica offrono informazioni dettagliate sulle operazioni e gli errori importanti per il controllo e per la risoluzione dei problemi. I log di diagnostica differiscono dai log attività. I log attività offrono informazioni approfondite sulle operazioni eseguite nelle risorse di Azure. I log di diagnostica forniscono informazioni approfondite sulle operazioni che la risorsa esegue automaticamente.

Attualmente Gestione API offre ogni ora log di diagnostica in batch sulla singola richiesta API con ogni voce con la struttura seguente:

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

È possibile accedere ai log di diagnostica del servizio Gestione API o ai log di tutte le risorse di Azure in Monitoraggio di Azure. Per visualizzare i log di diagnostica del servizio Gestione API:
1. Aprire il Portale di Azure.
2. Accedere al servizio Gestione API.
3. Fare clic su **Log di diagnostica**.

![Pannello Log di diagnostica][diagnostic-logs-blade]

Per altre informazioni sull'uso dei log di diagnostica, vedere [Raccogliere e usare i dati di diagnostica dalle risorse di Azure].

## <a name="next-step"></a>Passaggio successivo

* [introduzione a Monitoraggio di Azure]
* [Panoramica delle metriche]
* [Panoramica del log attività di Azure]
* [Raccogliere e usare i dati di diagnostica dalle risorse di Azure]
* [Panoramica degli avvisi]

[introduzione a Monitoraggio di Azure]: ../monitoring-and-diagnostics/monitoring-get-started.md
[Panoramica delle metriche]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[Panoramica del log attività di Azure]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[Raccogliere e usare i dati di diagnostica dalle risorse di Azure]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[Panoramica degli avvisi]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png

