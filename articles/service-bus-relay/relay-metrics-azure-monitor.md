---
title: Metriche del sevizio di inoltro di Azure in Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Usare Monitoraggio di Azure per monitorare il servizio di inoltro di Azure
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: dd5bc0dd0088856954e06d880f2c03f0a74ed9d7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Metriche del sevizio di inoltro di Azure in Monitoraggio di Azure (anteprima)

Le metriche del servizio di inoltro di Azure indicano lo stato delle risorse nella sottoscrizione di Azure. Grazie a un set completo di dati delle metriche è possibile valutare l'integrità generale delle risorse di inoltro, non solo a livello di spazio dei nomi, ma anche a livello di entità. Queste statistiche possono rivelarsi importanti poiché consentono di monitorare lo stato del servizio di inoltro di Azure. Le metriche consentono anche di risolvere i problemi senza dover contattare il supporto di Azure.

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e l'esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub.

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedere alle metriche dal [portale di Azure](https://portal.azure.com) o usare le API di Monitoraggio di Azure (REST e .NET) e le soluzioni di analisi come Operations Management Suite e Hub eventi. Per altre informazioni, vedere le [metriche di Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere ai 30 giorni di dati più recenti. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati relativi alle metriche in un account di archiviazione di Azure. Questo approccio viene configurato nelle [Impostazioni di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) in Monitoraggio di Azure.

## <a name="access-metrics-in-the-portal"></a>Accedere alle metriche nel portale

È possibile monitorare le metriche nel tempo nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come visualizzare le richieste completate e le richieste in ingresso a livello di account:

![][1]

È anche possibile accedere alle metriche direttamente tramite lo spazio dei nomi. A tale scopo, selezionare lo spazio dei nomi e quindi fare clic su **Metriche (anteprima)**. 

Per le metriche che supportano le dimensioni, è necessario filtrare specificando il valore di dimensione da usare.

## <a name="billing"></a>Fatturazione

L'uso delle metriche in Monitoraggio di Azure è attualmente gratuito durante la fase di anteprima. Se tuttavia si usano soluzioni aggiuntive per inserire i dati delle metriche, è possibile che la fatturazione venga effettuata da tali soluzioni. Ad esempio, la fatturazione viene effettuata da Archiviazione di Azure se i dati relativi alle metriche vengono archiviati in un account di Archiviazione di Azure. La fatturazione viene effettuata da Operations Management Suite (OMS) anche se si esegue lo streaming dei dati relativi alle metriche verso OMS per analisi avanzate.

Le metriche seguenti offrono una panoramica dell'integrità del servizio. 

> [!NOTE]
> Numerose metriche risultano attualmente deprecate perché hanno assunto un nome diverso. Ciò potrebbe rendere necessario l'aggiornamento dei riferimenti. Le metriche contrassegnate con la parola "deprecata" non saranno supportate in futuro.

Tutti i valori delle metriche vengono inviati a Monitoraggio di Azure ogni minuto. La granularità temporale definisce l'intervallo di tempo per il quale vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche del servizio di inoltro di Azure è 1 minuto.

## <a name="connection-metrics"></a>Metriche di connessione

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| ListenerConnections-Success (anteprima) | Numero di connessioni riuscite del listener eseguite al servizio di inoltro di Azure in un periodo specificato. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerConnections-ClientError (anteprima)|Numero di errori del client nelle connessioni del listener in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerConnections-ServerError (anteprima)|Numero di errori del server nelle connessioni del listener in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-Success (anteprima)|Numero di connessioni riuscite del mittente eseguite in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-ClientError (anteprima)|Numero di errori del client nelle connessioni del mittente in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-ServerError (anteprima)|Numero di errori del server nelle connessioni del mittente in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerConnections-TotalRequests (anteprima)|Numero totale di connessioni del listener in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderConnections-TotalRequests (anteprima)|Richieste di connessione eseguite dai mittenti in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ActiveConnections (anteprima)|Numero di connessioni attive in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ActiveListeners (anteprima)|Numero di listener attivi in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|ListenerDisconnects (anteprima)|Numero di listener disconnessi in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|SenderDisconnects (anteprima)|Numero di mittenti disconnessi in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="memory-usage-metrics"></a>Metriche di utilizzo della memoria

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
|BytesTransferred (anteprima)|Numero di byte trasferiti in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Il servizio di inoltro di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure. L'aggiunta di dimensioni alle metriche è facoltativa. Se non si aggiungono le dimensioni, le metriche vengono specificate a livello di spazio dei nomi. 

|Nome della dimensione|DESCRIZIONE|
| ------------------- | ----------------- |
|EntityName| Il servizio di inoltro di Azure supporta le entità di messaggistica nello spazio dei nomi.|

## <a name="next-steps"></a>Passaggi successivi

Vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




