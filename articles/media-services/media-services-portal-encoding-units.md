<properties
	pageTitle="Come applicare scalabilità all'elaborazione di contenuti mediante il portale di Azure classico"
	description="Informazioni su come scalare servizi multimediali specificando il numero di unità riservate di streaming on demand e di unità riservate di codifica di cui eseguire il provisioning nell'account in uso."
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="juliako"/>


# Come applicare scalabilità all'elaborazione di contenuti mediante il portale di Azure classico

## Altre tecnologie per eseguire questa attività

Questa pagina offre una panoramica su come applicare la scalabilità all'elaborazione di contenuti mediali usando il portale di Azure classico. Per questa attività, è possibile usare anche altre tecnologie:

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portale](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Overview

Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità dei processi di elaborazione dei multimedia. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. Per altre informazioni, vedere la pagina dedicata ai [tipi di unità riservate](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

## Scelta tra i tipi diversi di unità riservata

La tabella seguente consente di scegliere tra diverse velocità di codifica. Indica inoltre alcuni casi di benchmark e riporta gli URL di firma di accesso condiviso che è possibile usare per scaricare i video in cui eseguire test personali:

Scenari|**S1**|**S2**|**S3**|
----------|------------|----------|------------
Caso d'uso previsto| Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo.|Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD. |Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido. 
Benchmark|[File di input: della durata di 5 minuti, con risoluzione 640x360p, a 29,97 fotogrammi al secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>La codifica di un file MP4 con velocità in bit singola con la stessa risoluzione richiede circa 11 minuti.|[File di input: della durata di 5 minuti, con risoluzione 1280x720p, a 29,97 fotogrammi al secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>La codifica con set di impostazioni "Codec video H.264 a bitrate singolo con risoluzione 720p" richiede circa 5 minuti.<br/><br/>La codifica con set di impostazioni "Codec video H.264 a bitrate multiplo con risoluzione 720p" richiede circa 11,5 minuti.|[File di input: della durata di 5 minuti, con risoluzione 1920x1080p, a 29,97 fotogrammi al secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>La codifica con "Codec video H.264 a bitrate singolo con risoluzione 1080p" preconfigurato richiede circa 2,7 minuti.<br/><br/>La codifica con "Codec video H.264 a bitrate multiplo con risoluzione 1080p" preconfigurato richiede circa 5,7 minuti.

##Considerazioni

>[AZURE.IMPORTANT] Si applicano le considerazioni seguenti:

- Unità riservate di lavoro per la parallelizzazione di tutta l'elaborazione di supporti di memorizzazione, tra cui l'indicizzazione di processi tramite Azure Media Indexer. Tuttavia, a differenza della codifica, l'indicizzazione di processi non elaborerà più velocemente con unità riservate più veloci.

- Se si usa il pool condiviso, vale a dire senza unità riservate, le attività di codifica avranno le stesse prestazioni di quando si usano le unità riservate S1. Non vi è tuttavia alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e in qualsiasi momento può essere eseguita una sola attività al massimo.

- I data center di Brasile meridionale, India occidentale, India centrale e India meridionale non offrono il tipo di unità riservata **S2**.

- I data center di Brasile meridionale, India occidentale e India centrale non offrono il tipo di unità riservata **S3**.

- Il numero più alto di unità specificato in un periodo di 24 ore è quello che verrà usato per il calcolo del costo.

## Modificare il tipo di unità riservata

Per modificare il tipo di unità riservata e il numero di unità riservate, effettuare le operazioni seguenti:

1. Nel [portale di Azure classico](https://manage.windowsazure.com/) fare clic su **Servizi multimediali**. Fare quindi clic sul nome del servizio multimediale.

2. Selezionare la pagina **CODIFICA**.

	Per cambiare il **TIPO DI UNITÀ RISERVATA**, scegliere S1, S2 o S3.

	Per cambiare il numero di unità riservate per il tipo selezionato, usare il dispositivo di scorrimento **CODIFICA**.


	![Pagina relativa ai processori](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

3. Fare clic sul pulsante SAVE per salvare le modifiche apportate.

	Le nuove unità riservate vengono allocate quando si fa clic su SALVA.
 

##Quote e limitazioni

Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).



##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0817_2016-->