---
title: Gestire i criteri di memorizzazione nella cache della rete CDN di Azure in Servizi multimediali di Azure | Documentazione Microsoft
description: Informazioni su come gestire i criteri di memorizzazione nella cache della rete CDN di Azure in Servizi multimediali di Azure.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: bcb5a6e118c779e7e13ba8472d5bba510cddeb12
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gestire i criteri di memorizzazione nella cache della rete CDN di Azure in Servizi multimediali di Azure
Servizi multimediali di Azure fornisce lo streaming adattivo e il download progressivo basati su HTTP. Il flusso basato su HTTP è altamente scalabile con vantaggi della memorizzazione nella cache nel proxy e livelli di rete CDN, nonché di memorizzazione nella cache sul lato client. Gli endpoint di streaming forniscono funzionalità di streaming generale e configurazione per le intestazioni di cache HTTP. Gli endpoint di streaming impostano le intestazioni HTTP Cache-Control: max-age ed Expires. È possibile ottenere ulteriori informazioni per le intestazioni della cache HTTP da [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Intestazioni di memorizzazione nella cache predefinite
Per impostazione predefinita, gli endpoint di streaming applicano intestazioni di cache di 3 giorni per i dati di streaming on demand (frammenti multimediali effettivi/blocchi) e manifest(playlist). Per lo streaming live, gli endpoint di streaming applicano intestazioni di cache di 3 giorni per i dati (frammenti multimediali effettivi/blocchi) e intestazioni di cache di 2 secondi per le richieste manifest(playlist). Quando si programma in diretta si trasforma in (archivio in tempo reale) su richiesta, quindi applicano le intestazioni della cache di streaming su richiesta.

## <a name="azure-cdn-integration"></a>Integrazione della rete CDN di Azure
Servizi multimediali di Azure fornisce [la rete CDN integrata](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) per gli endpoint di streaming. Le intestazioni cache-control si applicano nello stesso modo in cui gli endpoint di streaming si applicano agli endpoint di streaming abilitati dalla rete CDN. La rete CDN di Azure utilizza i valori di cache configurati degli endpoint di streaming per definire la durata degli oggetti memorizzati nella cache internamente e inoltre utilizza questo valore per impostare le intestazioni della cache per i recapiti. Quando si utilizzano gli endpoint di streaming abilitati alla rete CDN non è consigliabile impostare i valori della cache piccoli. Impostazione dei valori di piccole dimensioni riducono le prestazioni e ridurre il vantaggio della rete CDN. Non è consentito impostare intestazioni cache inferiori a 600 secondi per gli endpoint di streaming abilitati alla rete CDN.

> [!IMPORTANT]
>Servizi multimediali di Azure assicura una perfetta integrazione con la rete CDN di Azure. Con un solo clic, è possibile integrare tutti il rete CDN di Azure i provider disponibili (Akamai e Verizon) all'endpoint di streaming, compresi i prodotti della rete CDN Standard e Premium. Per altre informazioni, vedere questo [annuncio](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Gli addebiti di dati dall'endpoint rete CDN di streaming Ottiene disabilitata solo se la rete CDN è abilitata tramite le API di endpoint di streaming o utilizzando una sezione di endpoint di streaming del portale di Azure. Integrazione manuale o direttamente la creazione di un endpoint rete CDN utilizzando le API di rete CDN o sezione portale non disabilitare i costi di dati.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurazione delle intestazioni della cache con Servizi multimediali di Azure
È possibile utilizzare il portale di Azure o le API di servizi multimediali di Azure per configurare i valori di intestazione della cache.

1. Per configurare intestazioni cache tramite il portale di Azure, vedere [come gestire gli endpoint di Streaming](../media-services/media-services-portal-manage-streaming-endpoints.md) sezione la configurazione dell'Endpoint di Streaming.
2. API REST di Servizi multimediali di Azure, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. SDK .NET di Servizi multimediali di Azure, [Proprietà StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordine di precedenza di configurazione della cache
1. Il valore della cache configurato di Servizi multimediali di Azure sostituisce il valore predefinito.
2. Se è presente alcuna configurazione manuale, si applicano i valori predefiniti.
3. Per impostazione predefinita si applicano le intestazioni di cache di 2 secondi, a prescindere dalla configurazione di servizi multimediali di Azure o archiviazione di Azure, e l'override di questo valore non è disponibile.

