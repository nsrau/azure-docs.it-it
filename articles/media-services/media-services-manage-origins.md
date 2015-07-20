<properties 
	pageTitle="Come gestire gli endpoint di streaming in un account di Servizi multimediali" 
	description="Questo argomento illustra come gestire gli endpoint di streaming mediante il portale di gestione di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Come gestire gli endpoint di streaming in un account di Servizi multimediali

Questo articolo fa parte delle serie relative al [flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) e al [flusso di lavoro Live Streaming di Servizi multimediali](media-services-live-streaming-workflow.md).


In Servizi multimediali di Microsoft Azure un **endpoint di streaming** rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) per la successiva distribuzione. Servizi multimediali fornisce inoltre un'integrazione completa della rete CDN di Azure. Il flusso in uscita da un servizio StreamingEndpoint può essere costituito da un flusso live o da un asset "video on demand" associato a un account di Servizi multimediali.

È possibile inoltre controllare la capacità del servizio endpoint di streaming in modo da poter gestire esigenze di larghezza di banda crescenti modificando le unità di scala (note anche come unità di streaming). Si consiglia di allocare una o più unità di scala per le applicazioni nell'ambiente di produzione. Le unità di scala forniscono capacità di uscita dedicata acquistabile in incrementi di 200 Mbps e una funzionalità aggiuntiva che include: [creazione dinamica dei pacchetti](https://msdn.microsoft.com/library/azure/jj889436.aspx), integrazione con la rete CDN e configurazione avanzata.

Si noti che il costo viene addebitato solo quando il canale è in stato di esecuzione.

Questo argomento fornisce informazioni generali sulle funzionalità principali fornite dagli endpoint di streaming. L'argomento illustra inoltre come usare il portale di gestione di Azure per gestire gli endpoint di streaming.


##Aggiunta ed eliminazione degli endpoint di streaming 

È possibile aggiungere o rimuovere gli endpoint di streaming usando .NET SDK, l'API REST o il portale di gestione di Azure.

Per aggiungere o eliminare gli endpoint di streaming tramite il portale, procedere come segue:

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Media Services**. Fare quindi clic sul nome del servizio multimediale.
2. Selezionare la pagina **ENDPOINT DI STREAMING**. 
3. Fare clic sul pulsante AGGIUNGI o ELIMINA nella parte inferiore della pagina. Gli endpoint di streaming predefiniti non possono essere eliminati. 
4. Fare clic sul pulsante AVVIA per avviare l'endpoint di streaming. 
5. Fare clic sul nome dell'endpoint di streaming per configurarlo.   

	![Pagina Endpoint di streaming][streaming-endpoint]


Per impostazione predefinita, è possibile disporre di un massimo di due endpoint di streaming. Se sono necessari più endpoint di streaming, vedere [Quote e limitazioni](media-services-quotas-and-limitations/).

##<a id="scale_streaming_endpoints"></a>Scalare l'endpoint di streaming

Le unità di streaming offrono sia capacità in uscita dedicata, acquistabile in incrementi di 200 Mbps, sia funzionalità aggiuntive che attualmente comprendono la [creazione dinamica dei pacchetti](http://go.microsoft.com/fwlink/?LinkId=276874). Per impostazione predefinita, lo streaming è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo, capacità in uscita e così via) vengono condivise con tutti gli altri utenti. Per migliorare la velocità effettiva dello streaming, si consiglia di acquistare unità di streaming.

È possibile implementare la scalabilità usando .NET SDK, l'API REST o il portale di gestione di Azure.

Per modificare il numero di unità di streaming tramite il portale, seguire questa procedura:

1. Per specificare il numero di unità di streaming, selezionare la scheda RIDIMENSIONA e spostare il dispositivo di scorrimento **capacità riservata**.

	![Pagina Scale](./media/media-services-manage-origins/media-services-origin-scale.png)

4. Fare clic sul pulsante SAVE per salvare le modifiche apportate.

	L'allocazione di nuove unità di streaming richiede circa 20 minuti.

	 
	>[AZURE.NOTE]Attualmente, se si riporta a zero qualsiasi valore positivo delle unità di streaming, è possibile che lo streaming on demand venga disabilitato per un periodo che può durare fino a un'ora.

	>[AZURE.NOTE]Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo. Per informazioni sui prezzi, vedere [Dettagli prezzi di Servizi multimediali](http://go.microsoft.com/fwlink/?LinkId=275107).
	
##<a id="configure_streaming_endpoints"></a>Configurare l'endpoint di streaming

L'endpoint di streaming consente di configurare le seguenti proprietà quando si dispone di almeno una unità di scala:

- Controllo di accesso
- Nomi host personalizzati
- Controllo cache
- Criteri di accesso tra siti

Per informazioni dettagliate su queste proprietà, vedere [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

È possibile configurare queste proprietà usando .NET SDK, l'API REST o il portale di gestione di Azure.

Per modificare il numero di unità di streaming tramite il portale, seguire questa procedura:

1. Selezionare l'endpoint di streaming che si desidera configurare.
1. Selezionare la scheda CONFIGURA.
  
Seguirà una breve descrizione dei campi.

![Configure origin][configure-origin]
  

1. Impostare il periodo di memorizzazione nella cache massimo che verrà specificato nell'intestazione del controllo cache delle risposte HTTP. Questo valore non sovrascriverà le impostazioni cache massime impostate direttamente sul contenuto Blob.

2. Specificare gli indirizzi IP a cui è consentito connettersi all'endpoint di streaming pubblicato. Se non viene specificato alcun indirizzo IP, la connessione è consentita a qualsiasi indirizzo IP.

3. Specificare una configurazione per l'autenticazione dell'intestazione firma Akamai.

4. È possibile specificare un criterio di accesso tra domini per i client di Adobe Flash. Per altre informazioni, vedere la pagina relativa alle [specifiche del criterio di accesso tra domini](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). È altresì possibile specificare il criterio di accesso per i client di Microsoft Silverlight. Per altre informazioni, vedere [Rendere un servizio disponibile attraverso i limiti del dominio] (https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).

5. È inoltre possibile configurare nomi host personalizzati facendo clic sul pulsante **Configura**. Per altre informazioni, vedere la proprietà **CustomHostNames** nell'argomento [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).


##<a id="enable_cdn"></a>Abilitare l'integrazione della rete CDN di Azure

È possibile specificare di abilitare l'integrazione della rete CDN di Azure per un endpoint di streaming. Tale opzione è disattivata per impostazione predefinita.

Per impostare l'integrazione della rete CDN di Azure su true:

- L'endpoint di streaming deve disporre di almeno una unità di streaming (scala). Se si desidera impostare le unità di scala su 0 in un secondo momento, è innanzitutto necessario disattivare l'integrazione della rete CDN. Per impostazione predefinita, quando si crea un nuovo flusso, viene impostata automaticamente un'unità di streaming endpoint.

- L'endpoint di streaming deve essere in stato di interruzione. Una volta abilitata la rete CDN, è possibile avviare l'endpoint di streaming.

L'abilitazione dell'integrazione della rete CDN di Azure può richiedere fino a 90 minuti. L'attivazione delle modifiche in tutti i POP della rete CDN richiede fino a due ore.


L'integrazione con la rete CDN è abilitata in tutti i data center di Azure: Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale, Europa occidentale, Giappone occidentale, Giappone orientale, Asia sudorientale e Asia orientale.

Una volta abilitata, le seguenti configurazioni vengono disabilitate: **Nomi host personalizzati** e **Controllo di accesso**.

![Abilitazione della rete CDN per gli endpoint di streaming][streaming-endpoint-enable-cdn]


###Considerazione aggiuntive

- Quando la rete CDN è abilitata per un endpoint di streaming, i client non possono richiedere il contenuto direttamente dall'origine. Se è necessario testare il contenuto con o senza la rete CDN, è possibile creare un altro endpoint di streaming per la rete CDN.
- Il nome host dell'endpoint di streaming rimane invariato dopo l'abilitazione della rete CDN. Non è necessario apportare modifiche al flusso di lavoro di Servizi multimediali dopo l'abilitazione della rete CDN. Ad esempio, se il nome host dell'endpoint di streaming è strasbourg.streaming.mediaservices.windows.net, dopo avere abilitato la rete CDN, viene usato lo stesso identico nome host.
- Per i nuovi endpoint di streaming, è possibile abilitare la rete CDN semplicemente creando un nuovo endpoint. Per gli endpoint di streaming esistenti, sarà necessario arrestare prima l'endpoint e quindi abilitare la rete CDN.
 

Per altre informazioni, vedere [Annuncio dell'integrazione di Servizi multimediali di Azure con la rete per la distribuzione di contenuti (CDN) Azure](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
 

<!---HONumber=July15_HO2-->