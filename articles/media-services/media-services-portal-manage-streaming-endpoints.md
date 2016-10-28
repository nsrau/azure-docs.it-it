<properties 
	pageTitle="Gestire gli endpoint di streaming con il portale di Azure | Microsoft Azure" 
	description="Questo argomento illustra come gestire gli endpoint di streaming mediante il portale di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/29/2016"
	ms.author="juliako"/>


#Gestire gli endpoint di streaming con il portale di Azure

## Overview

> [AZURE.NOTE] Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

In Servizi multimediali di Microsoft Azure un **endpoint di streaming** rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) per la successiva distribuzione. Servizi multimediali fornisce inoltre un'integrazione completa della rete CDN di Azure. Il flusso in uscita da un servizio StreamingEndpoint può essere costituito da un flusso live o da un asset "video on demand" associato a un account di Servizi multimediali.

È possibile inoltre controllare la capacità del servizio endpoint di streaming in modo da poter gestire esigenze di larghezza di banda crescenti modificando le unità di streaming. Si consiglia di allocare una o più unità di scala per le applicazioni nell'ambiente di produzione. Le unità di streaming forniscono capacità di uscita dedicata acquistabile in incrementi di 200 Mbps e una funzionalità aggiuntiva che include: [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md), integrazione con la rete CDN e configurazione avanzata.

>[AZURE.NOTE]Il costo verrà addebitato solo quando StreamingEndpoint è in stato di esecuzione.

Questo argomento riporta informazioni generali sulle funzionalità principali fornite dagli endpoint di streaming. L'argomento illustra inoltre come usare il portale di Azure per gestire gli endpoint di streaming. Per informazioni su come ridimensionare l'endpoint di streaming, vedere [questo](media-services-portal-scale-streaming-endpoints.md) argomento.

## Iniziare a gestire gli endpoint di streaming

Per iniziare a gestire gli endpoint di streaming per l'account, procedere come segue.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nella finestra **Impostazioni** selezionare **Endpoint di streaming**.

	![Endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

##Aggiungere o eliminare un endpoint di streaming

Per aggiungere o eliminare gli endpoint di streaming tramite il portale di Azure, procedere come segue:

1. Per aggiungere un endpoint di streaming, fare clic su **+ Endpoint** nella parte superiore della pagina.
2. Per eliminare un endpoint di streaming, fare clic sul pulsante **Elimina**.

	Gli endpoint di streaming predefiniti non possono essere eliminati.
2. Fare clic sul pulsante **Avvia** per avviare l'endpoint di streaming.

	![Endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)

Per impostazione predefinita, è possibile disporre di un massimo di due endpoint di streaming. Se sono necessari più endpoint di streaming, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).
	
##<a id="configure_streaming_endpoints"></a>Configurare l'endpoint di streaming

L'endpoint di streaming consente di configurare le seguenti proprietà quando si dispone di almeno una unità di scala:

- Controllo di accesso
- Controllo cache
- Criteri di accesso tra siti

Per informazioni dettagliate su queste proprietà, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

È possibile configurare un endpoint di streaming eseguendo le operazioni seguenti:

1. Selezionare l'endpoint di streaming che si desidera configurare.
1. Fare clic su **Impostazioni**.
  
Seguirà una breve descrizione dei campi.

![Endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)
  
1. Criteri della cache massima: consente di configurare la durata della cache per gli asset serviti tramite questo endpoint di streaming. Se non si imposta alcun valore, viene usato il valore predefinito. I valori predefiniti possono anche essere definiti direttamente in Archiviazione di Azure. Se la rete CDN di Azure è abilitata per l'endpoint di streaming, non impostare il valore dei criteri della cache a meno di 600 secondi.

2. Indirizzi IP consentiti: consente di specificare gli indirizzi IP che possono connettersi all'endpoint di streaming pubblicato. Se non viene specificato alcun indirizzo IP, la connessione è consentita a qualsiasi indirizzo IP. È possibile specificare gli indirizzi IP come un singolo indirizzo IP (ad esempio "10.0.0.1"), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio "10.0.0.1/22") o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio "10.0.0.1(255.255.255.0)").

3. Configurazione per l'autenticazione dell'intestazione firma Akamai: consente di specificare la configurazione della richiesta di autenticazione intestazione firma proveniente dai server Akamai. La scadenza è in formato UTC.



##<a id="enable_cdn"></a>Abilitare l'integrazione della rete CDN di Azure

È possibile specificare di abilitare l'integrazione della rete CDN di Azure per un endpoint di streaming. Tale opzione è disattivata per impostazione predefinita.

Per impostare l'integrazione della rete CDN di Azure su true:

- L'endpoint di streaming deve disporre di almeno un'unità di streaming. Se si desidera impostare le unità di scala su 0 in un secondo momento, è innanzitutto necessario disattivare l'integrazione della rete CDN. Per impostazione predefinita, quando si crea un nuovo flusso, viene impostata automaticamente un'unità di streaming endpoint.

- L'endpoint di streaming deve essere in stato di interruzione. Una volta abilitata la rete CDN, è possibile avviare l'endpoint di streaming.

L'abilitazione dell'integrazione della rete CDN di Azure può richiedere fino a 90 minuti. L'attivazione delle modifiche in tutti i POP della rete CDN richiede fino a due ore.

L'integrazione con la rete CDN è abilitata in tutti i data center di Azure: Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale, Europa occidentale, Giappone occidentale, Giappone orientale, Asia sudorientale e Asia orientale.

Una volta attivata, la configurazione del **controllo di accesso** viene disabilitata.

![Endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints5.png)

>[AZURE.IMPORTANT] L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon**. Se si vuole usare la **rete CDN di Azure fornita da Akamai** per Servizi multimediali di Azure, è necessario [configurare manualmente l'endpoint](../cdn/cdn-create-new-endpoint.md). Per altre informazioni sulle funzionalità della rete CDN di Azure, vedere la [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](../cdn/cdn-overview.md).

###Considerazioni aggiuntive

- Quando la rete CDN è abilitata per un endpoint di streaming, i client non possono richiedere il contenuto direttamente dall'origine. Se è necessario testare il contenuto con o senza la rete CDN, è possibile creare un altro endpoint di streaming per la rete CDN.
- Il nome host dell'endpoint di streaming rimane invariato dopo l'abilitazione della rete CDN. Non è necessario apportare modifiche al flusso di lavoro di Servizi multimediali dopo l'abilitazione della rete CDN. Ad esempio, se il nome host dell'endpoint di streaming è strasbourg.streaming.mediaservices.windows.net, dopo avere abilitato la rete CDN, viene usato lo stesso identico nome host.
- Per i nuovi endpoint di streaming, è possibile abilitare la rete CDN semplicemente creando un nuovo endpoint. Per gli endpoint di streaming esistenti, è necessario arrestare prima l'endpoint e quindi abilitare la rete CDN.
 

Per altre informazioni, vedere [Annuncio dell'integrazione di Servizi multimediali di Azure con la rete per la distribuzione di contenuti (CDN) Azure](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##Passaggi successivi

Analizzare i percorsi di apprendimento di Servizi multimediali.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 

<!---HONumber=AcomDC_0831_2016-->