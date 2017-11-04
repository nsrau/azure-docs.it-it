---
title: Metriche degli hub eventi di Azure in Monitoraggio di Azure (anteprima) | Microsoft Docs
description: Usare Monitoraggio di Azure per monitorare gli hub eventi
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: c3ee6a87592145801167b35ff281c04062e525da
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Metriche degli hub eventi di Azure in Monitoraggio di Azure (anteprima)

Le metriche degli hub eventi di Azure indicano lo stato delle risorse degli hub eventi nella sottoscrizione di Azure. Grazie a un set completo di dati delle metriche è possibile valutare l'integrità generale delle risorse degli hub eventi, non solo a livello di spazio dei nomi, ma anche a livello di entità. Queste statistiche possono rivelarsi importanti perché consentono di monitorare lo stato degli hub eventi. Le metriche consentono anche di risolvere i problemi senza dover contattare il supporto di Azure.

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e l'esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub.

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedere alle metriche dal [portale di Azure](https://portal.azure.com) o usare le API di Monitoraggio di Azure (REST e .NET) e le soluzioni di analisi come Operations Management Suite e Hub eventi. Per altre informazioni, vedere le [metriche di Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere ai 30 giorni di dati più recenti. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati relativi alle metriche in un account di archiviazione di Azure. Questo approccio viene configurato nelle [Impostazioni di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) in Monitoraggio di Azure.

## <a name="access-metrics-in-the-portal"></a>Accedere alle metriche nel portale

È possibile monitorare le metriche nel tempo nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come visualizzare le richieste completate e le richieste in ingresso a livello di account:

![][1]

È anche possibile accedere alle metriche direttamente tramite lo spazio dei nomi. A tale scopo, selezionare lo spazio dei nomi e quindi fare clic su **Metriche (anteprima)**. Per visualizzare le metriche filtrate in base all'ambito dell'hub eventi, selezionare l'hub eventi e quindi fare clic su **Metriche (anteprima)**.

Per le metriche che supportano le dimensioni, è necessario filtrare specificando il valore di dimensione da usare come illustrato nell'esempio seguente:

![][2]

## <a name="billing"></a>Fatturazione

L'uso delle metriche in Monitoraggio di Azure è attualmente gratuito durante la fase di anteprima. Se tuttavia si usano soluzioni aggiuntive per inserire i dati delle metriche, è possibile che la fatturazione venga effettuata da tali soluzioni. Ad esempio, la fatturazione viene effettuata da Archiviazione di Azure se i dati relativi alle metriche vengono archiviati in un account di Archiviazione di Azure. La fatturazione viene effettuata da Operations Management Suite (OMS) anche se si esegue lo streaming dei dati relativi alle metriche verso OMS per analisi avanzate.

Le metriche seguenti offrono una panoramica dell'integrità del servizio. 

> [!NOTE]
> Numerose metriche risultano attualmente deprecate perché hanno assunto un nome diverso. Ciò potrebbe rendere necessario l'aggiornamento dei riferimenti. Le metriche contrassegnate con la parola "deprecata" non saranno supportate in futuro.

Tutti i valori delle metriche vengono inviati a Monitoraggio di Azure ogni minuto. La granularità temporale definisce l'intervallo di tempo per il quale vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche di tutti gli hub eventi è 1 minuto.

## <a name="request-metrics"></a>Metriche per le richieste

Conta il numero di richieste di operazioni di dati e gestione.

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
| Richieste in ingresso (anteprima) | Numero di richieste inviate al servizio Hub eventi di Azure in un periodo specificato. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName |
| Richieste completate (anteprima)   | Numero di richieste completate inviate al servizio Hub eventi di Azure in un periodo specificato. <br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName |
| Errori server (anteprima) | Numero di richieste non elaborate a causa di un errore nel servizio Hub eventi di Azure in un periodo specificato. <br/><br/>Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName |
|Errori utente (anteprima)|Numero di richieste non elaborate a causa di errori utente in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Richieste limitate (anteprima)|Numero di richieste che sono state limitate perché è stato superato il limite di utilizzo di unità elaborate.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Errori di superamento quota (anteprima)|Il numero di richieste supera la quota disponibile. Per altre informazioni sulle quote degli hub eventi, vedere [questo articolo](event-hubs-quotas.md).<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="throughput-metrics"></a>Metriche di elaborazione

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|Richieste limitate (anteprima)|Numero di richieste che sono state limitate perché è stato superato il limite di utilizzo di unità elaborate.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="message-metrics"></a>Metriche per i messaggi

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|Messaggi in ingresso (anteprima)|Numero di eventi o messaggi inviati agli hub eventi in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Messaggi in uscita (anteprima)|Numero di eventi o messaggi recuperati dagli hub eventi in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Byte in ingresso (anteprima)|Numero di byte inviati al servizio Hub eventi di Azure in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Byte in uscita (anteprima)|Numero di byte recuperati dal servizio Hub eventi di Azure in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="connection-metrics"></a>Metriche di connessione

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|ActiveConnections (anteprima)|Numero di connessioni attive in uno spazio dei nomi e in un'entità.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Connessioni aperte (anteprima)|Numero di connessioni aperte.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Connessioni chiuse (anteprima)|Numero di connessioni chiuse.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="event-hubs-capture-metrics"></a>Metriche di acquisizione di hub eventi

È possibile monitorare le metriche di acquisizione degli hub eventi quando si abilita la funzionalità di acquisizione per gli hub di eventi. Le metriche seguenti descrivono ciò che è possibile monitorare con la funzionalità di acquisizione abilitata.

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|Backlog acquisiti (anteprima)|Numero di byte da acquisire nella destinazione selezionata.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Messaggi acquisiti (anteprima)|Numero di messaggi o eventi acquisiti nella destinazione selezionata in un periodo specificato.<br/><br/> Unità: conteggio <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|
|Byte acquisiti (anteprima)|Numero di byte acquisiti nella destinazione selezionata in un periodo specificato.<br/><br/> Unità: byte <br/> Tipo di aggregazione: totale <br/> Dimensione: EntityName|

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Hub eventi di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure. L'aggiunta di dimensioni alle metriche è facoltativa. Se non si aggiungono le dimensioni, le metriche vengono specificate a livello di spazio dei nomi. 

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|EntityName| Hub eventi supporta le entità degli hub eventi nello spazio dei nomi.|

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* Esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub. 

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



