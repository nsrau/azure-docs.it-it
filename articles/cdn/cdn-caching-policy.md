<properties
	pageTitle="Criterio di memorizzazione nella cache della rete CDN nell’estensione Servizi multimediali"
	description="Questo argomento illustra una panoramica di un criterio di memorizzazione nella cache della rete CDN nell'estensione Servizi multimediali."
	services="media-services,cdn"
	documentationCenter=".NET"
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="juliako"/>
 
#Criterio di memorizzazione nella cache della rete CDN nell’estensione Servizi multimediali

Servizi multimediali di Azure fornisce lo streaming adattivo e il download progressivo basati su HTTP. Lo streaming basato su HTTP è altamente scalabile con i vantaggi della cache nei livelli proxy e di rete CDN, nonché della cache sul lato client. Gli endpoint di streaming forniscono funzionalità di streaming generale e configurazione per le intestazioni di cache HTTP. Gli endpoint di streaming impostano le intestazioni HTTP Cache-Control: max-age ed Expires. È possibile ottenere ulteriori informazioni per le intestazioni della cache HTTP da [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##Intestazioni di memorizzazione nella cache predefinite

Per impostazione predefinita, gli endpoint di streaming applicano intestazioni di cache di 3 giorni per i dati di streaming on demand (frammenti multimediali effettivi/blocchi) e manifest(playlist). Per lo streaming live, gli endpoint di streaming applicano intestazioni di cache di 3 giorni per i dati (frammenti multimediali effettivi/blocchi) e intestazioni di cache di 2 secondi per le richieste manifest(playlist). Quando il programma live (archivio live) diventa on demand vengono applicate le intestazioni della cache di streaming on demand.

##Integrazione della rete CDN di Azure

Servizi multimediali di Azure fornisce [la rete CDN integrata](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) per gli endpoint di streaming. Le intestazioni cache-control si applicano nello stesso modo in cui gli endpoint di streaming si applicano agli endpoint di streaming abilitati dalla rete CDN. La rete CDN di Azure utilizza i valori di cache configurati degli endpoint di streaming per definire la durata degli oggetti memorizzati nella cache internamente e inoltre utilizza questo valore per impostare le intestazioni della cache per i recapiti. Quando si utilizzano gli endpoint di streaming abilitati alla rete CDN non è consigliabile impostare i valori della cache piccoli. In tal modo, infatti si verifica una riduzione delle prestazioni e del vantaggio della rete CDN. Non è consentito impostare intestazioni cache inferiori a 600 secondi per gli endpoint di streaming abilitati alla rete CDN.

>[AZURE.IMPORTANT] L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon**. Se si vuole usare la **rete CDN di Azure fornita da Akamai** per Servizi multimediali di Azure, è necessario [configurare manualmente l'endpoint](cdn-create-new-endpoint.md). Per altre informazioni sulle funzionalità della rete CDN di Azure, vedere la [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md).

##Configurazione delle intestazioni della cache con Servizi multimediali di Azure

È possibile utilizzare il portale di gestione di Azure o le API di Servizi multimediali di Azure per configurare i valori di intestazione della cache.

1. Per configurare le intestazioni delle cache mediante il portale di gestione, vedere la sezione [Come gestire gli endpoint di streaming](../media-services/media-services-manage-origins.md) Configurazione dell'endpoint di streaming.
2. API REST di Servizi multimediali di Azure, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. SDK .NET di Servizi multimediali di Azure, [Proprietà StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

##Ordine di precedenza di configurazione della cache

1. Il valore della cache configurato di Servizi multimediali di Azure sostituisce il valore predefinito.
2. Se non è presente alcuna configurazione manuale, vengono applicati i valori predefiniti.
3. Per impostazione predefinita si applicano le intestazioni di cache di 2 secondi, a prescindere dalla configurazione di servizi multimediali di Azure o archiviazione di Azure, e l'override di questo valore non è disponibile.

<!---HONumber=AcomDC_0518_2016-->