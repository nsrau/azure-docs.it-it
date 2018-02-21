---
title: Panoramica del ridimensionamento nell'elaborazione multimediale | Microsoft Docs
description: Questo argomento presenta una panoramica del ridimensionamento dell'elaborazione multimediale con Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: a82481c4995bfb078e88d7096dff37b52312a296
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="scaling-media-processing-overview"></a>Panoramica del ridimensionamento dell'elaborazione multimediale
Questa pagina presenta una panoramica di come e perché ridimensionare l'elaborazione multimediale. 

## <a name="overview"></a>Panoramica
Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità dei processi di elaborazione dei multimedia. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. Per altre informazioni, vedere la pagina dedicata ai [tipi di unità riservate](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Scelta tra i tipi diversi di unità riservata
La tabella seguente consente di scegliere tra diverse velocità di codifica. Indica inoltre alcuni casi di benchmark e riporta gli URL di firma di accesso condiviso che è possibile usare per scaricare i video in cui eseguire test personali:

| Scenari | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Caso d'uso previsto |Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo. |Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD. |Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido. |
| Benchmark |[File di input: della durata di 5 minuti, con risoluzione 640x360p, a 29,97 fotogrammi al secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>La codifica di un file MP4 con velocità in bit singola con la stessa risoluzione richiede circa 11 minuti. |[File di input: della durata di 5 minuti, con risoluzione 1280 x 720p, a 29,97 fotogrammi al secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>La codifica con set di impostazioni "Codec video H264 a bitrate singolo con risoluzione 720p" richiede circa 5 minuti.<br/><br/>La codifica con set di impostazioni "Codec video H.264 a bitrate multipli con risoluzione 720p" richiede circa 11,5 minuti. |[File di input: della durata di 5 minuti, con risoluzione 1920x1080p, a 29,97 fotogrammi al secondo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>La codifica con set di impostazioni "Codec video H264 a bitrate singolo con risoluzione 1080p" richiede circa 2,7 minuti.<br/><br/>La codifica con "Codec video H.264 a bitrate multiplo con risoluzione 1080p" preconfigurato richiede circa 5,7 minuti. |

## <a name="considerations"></a>Considerazioni
> [!IMPORTANT]
> Vedere le considerazioni descritte in questa sezione.  
> 
> 

* Unità riservate di lavoro per la parallelizzazione di tutta l'elaborazione di supporti di memorizzazione, tra cui l'indicizzazione di processi tramite Azure Media Indexer.  Tuttavia, a differenza della codifica, l'indicizzazione di processi non viene elaborata più velocemente con unità riservate più veloci.
* Se si usa il pool condiviso, vale a dire senza unità riservate, le attività di codifica hanno le stesse prestazioni di quando si usano le unità riservate S1. Non c'è tuttavia alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e in qualsiasi momento può essere eseguita al massimo una sola attività.
* I data center Brasile meridionale e India occidentale non offrono il tipo di unità riservata **S2**.
* Il data center India occidentale non offre il tipo di unità riservata **S3**.

## <a name="billing"></a>Fatturazione

Vengono addebitati i minuti di uso effettivo di Media Reserved Unit. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="quotas-and-limitations"></a>Quote e limitazioni
Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Passaggio successivo
Ottenere l'attività di elaborazione dei media scalabili con una di queste tecnologie: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portale](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Per ottenere la versione più recente di Java SDK e iniziare a sviluppare con Java, vedere [Introduzione a Java Client SDK per Servizi multimediali](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Per scaricare la versione più recente di PHP SDK per Servizi multimediali, cercare la versione 0.5.7 del pacchetto Microsoft/WindowAzure nel [repository Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

