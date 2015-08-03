<properties 
	pageTitle="Informazioni generali su Servizi multimediali di Azure" 
	description="Questo argomento fornisce informazioni generali su Servizi multimediali di Azure" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Informazioni generali su Servizi multimediali di Azure

Servizi multimediali di Microsoft Azure costituisce una piattaforma estensibile basata sul cloud che consente agli sviluppatori di creare applicazioni di distribuzione e gestione di contenuti multimediali altamente scalabili. Servizi multimediali si basa su API REST che consentono di caricare, archiviare e codificare in sicurezza contenuti video o audio, nonché creare pacchetti di tali contenuti per la distribuzione su richiesta e in modalità streaming live a vari tipi di client (ad esempio, TV, PC e dispositivi mobili).

È possibile creare flussi di lavoro end-to-end usando unicamente Servizi multimediali. È anche possibile usare componenti di terze parti per alcune parti del flusso di lavoro, ad esempio, la codifica con un codificatore di terze parti. Inoltre, sono possibili operazioni di caricamento, protezione, creazione di pacchetti e invio tramite Servizi multimediali.

Per creare soluzioni di Servizi multimediali, è possibile usare:

- [API REST di Servizi multimediali](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno degli SDK client disponibili: [Azure Media Services SDK for .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services for Node.js](https://github.com/fritzy/node-azure-media), [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- Strumenti esistenti: [Portale di gestione di Azure](http://manage.windowsazure.com/) o [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


Il seguente poster illustra i flussi di lavoro di Servizi multimediali di Azure, dalla creazione dei contenuti multimediali fino alla loro fruizione. È possibile scaricare il poster da qui: [Azure Media Services poster](http://www.microsoft.com/download/details.aspx?id=38195).

![Panoramica][overview]

**Contratto di servizio**:

- Per il servizio di codifica di Servizi multimediali, è garantita una disponibilità al 99,9% delle transazioni delle API REST.
- Con l'acquisto di almeno un'unità di trasmissione in flusso, per il servizio di streaming, è garantita una disponibilità al 99,9% per i contenuti multimediali esistenti.
- Per i canali live, è garantito che i canali in esecuzione avranno connettività esterna per almeno il 99,9% del tempo.
- Per la protezione del contenuto, è garantita l'evasione delle richieste di chiavi per almeno il 99,9% del tempo.
- Per l'indicizzatore, è garantito che verranno soddisfatte le richieste di attività dell'indicizzatore elaborate con un'unità riservata di codifica per il 99,9% del tempo.

	Per altre informazioni, vedere [Contratto di servizio di Microsoft Azure](http://azure.microsoft.com/support/legal/sla/).

##Concetti

Per altre informazioni, vedere [Concetti](media-services-concepts.md).


##Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure

Il seguente argomento descrive i passaggi dei normali flussi di lavoro VOD (Video On Demand) di Servizi multimediali. L'argomento include collegamenti ad altri argomenti che illustrano come eseguire questi passaggi usando tecnologie supportate da Servizi multimediali.

[Distribuzione di contenuti multimediali su richiesta con Servizi multimediali di Azure](media-services-video-on-demand-workflow.md).

##Distribuzione di Live Streaming con Servizi multimediali di Azure

Il seguente argomento descrive i passaggi dei normali flussi di lavoro di streaming live di Servizi multimediali. L'argomento include collegamenti ad altri argomenti che illustrano come eseguire questi passaggi usando tecnologie supportate da Servizi multimediali.

[Distribuzione di Live Streaming con Servizi multimediali di Azure](media-services-live-streaming-workflow.md).

##Utilizzo di contenuto

Servizi multimediali di Azure fornisce gli strumenti necessari per creare applicazioni lettore client avanzate e dinamiche per la maggior parte delle piattaforme, inclusi dispositivi iOS, dispositivi Android, Windows, Windows Phone, Xbox e set-top box. Il seguente argomento fornisce collegamenti a SDK e Player Framework che è possibile usare per sviluppare le proprie applicazioni client in modo che utilizzino i flussi multimediali da Servizi multimediali.

[Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)

##Indicazioni su modelli e procedure

[Indicazioni su modelli e procedure](https://wamsg.codeplex.com/) [Documentazione online](https://msdn.microsoft.com/library/dn735912.aspx) [EBook scaricabile](https://www.microsoft.com/download/details.aspx?id=42629)

##Supporto

Il [supporto tecnico di Azure](http://azure.microsoft.com/support/options/) fornisce opzioni di supporto per Azure, compreso Servizi multimediali.

##Passaggi successivi

[Distribuzione di Live Streaming con Servizi multimediali di Azure](media-services-live-streaming-workflow.md)

[Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)
 
[Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=July15_HO4-->