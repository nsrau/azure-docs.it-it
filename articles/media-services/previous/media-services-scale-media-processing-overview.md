---
title: Panoramica del ridimensionamento nell'elaborazione multimediale | Microsoft Docs
description: Questo argomento presenta una panoramica del ridimensionamento dell'elaborazione multimediale con Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 25b2eb05a612b18eab1fbf790b119edbe9c4d8d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707400"
---
# <a name="scaling-media-processing-overview"></a>Panoramica del ridimensionamento dell'elaborazione multimediale 
Questa pagina presenta una panoramica di come e perché ridimensionare l'elaborazione multimediale. 

## <a name="overview"></a>Panoramica
Un account di Servizi multimediali è associato a un tipo di unità riservata che determina la velocità dei processi di elaborazione dei multimedia. È possibile scegliere uno dei seguenti tipi di unità riservata: **S1**, **S2** o **S3**. Lo stesso processo di codifica viene eseguito più velocemente quando si usa ad esempio il tipo di unità riservata **S2** rispetto al tipo **S1**. Per altre informazioni, vedere la pagina dedicata ai [tipi di unità riservate](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oltre al tipo di unità riservata, è possibile specificare il provisioning dell'account con unità riservate. Il numero delle unità riservate sottoposte a provisioning determina il numero di attività multimediali che possono essere elaborate contemporaneamente in un determinato account. Se, ad esempio, il proprio account dispone di cinque unità riservate, è possibile eseguire simultaneamente cinque attività multimediali, purché siano presenti attività da elaborare. Le attività rimanenti verranno messe in coda e prelevate in sequenza per l'elaborazione quando un'attività in esecuzione viene completata. Se per un account non sono state fornite unità riservate, le attività verranno prelevate in sequenza. In questo caso, il tempo di attesa tra il completamento di un'attività e l'avvio di quella successiva dipende dalle risorse disponibili nel sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Scelta tra i tipi diversi di unità riservata
La tabella seguente consente di scegliere tra diverse velocità di codifica. Fornisce inoltre alcuni casi di riferimento sul [un video che è possibile scaricare](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) per eseguire i propri test:

|Tipo di unità richiesta|Scenario|Risultati di esempio per il [video di 7 min con risoluzione 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo.|Codifica in file MP4 risoluzione SD di a bitrate singolo con "Codec video h.264 singolo a velocità in bit SD 16x9" richiede circa 7 minuti.|
| **S2**|Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD.|Codifica con "Codec video h.264 a bitrate singolo con 720p" set di impostazioni richiede circa 6 minuti.<br/><br/>Codifica con "codec video h.264 a Bitrate multipli 720p" set di impostazioni richiede circa 12 minuti.|
| **S3**|Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido.|Codifica con "Codec video h.264 a Bitrate singolo con risoluzione 1080p e" set di impostazioni richiede circa 3 minuti.<br/><br/>Codifica con "codec video h.264 a Bitrate multiplo con risoluzione 1080p e" set di impostazioni richiede circa 8 minuti.|

## <a name="considerations"></a>Considerazioni
> [!IMPORTANT]
> Vedere le considerazioni descritte in questa sezione.  
> 
> 

* Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, il tipo di unità di S3 è fortemente consigliato.
* Se si usa il pool condiviso, vale a dire senza unità riservate, le attività di codifica hanno le stesse prestazioni di quando si usano le unità riservate S1. Non c'è tuttavia alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e in qualsiasi momento può essere eseguita al massimo una sola attività.

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero di minuti che viene effettuato il provisioning di Media Reserved unit nel tuo account. Questo errore si verifica indipendentemente dal fatto che sono presenti tutti i processi in esecuzione nell'account. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).   

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

