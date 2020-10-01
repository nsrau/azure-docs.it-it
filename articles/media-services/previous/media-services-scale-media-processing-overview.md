---
title: Panoramica di media reserved unit | Microsoft Docs
description: Questo articolo è una panoramica del ridimensionamento dell'elaborazione di contenuti multimediali con servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 8867e680be0aba187daf83bc538dd47c582c71fe
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618506"
---
# <a name="media-reserved-units"></a>Media reserved unit

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Servizi multimediali di Azure consente di ridimensionare l'elaborazione multimediale gestendo media reserved Unit (MRU). Un MRU fornisce capacità di calcolo aggiuntiva necessaria per la codifica dei supporti. Il numero di MRU determina la velocità con cui le attività multimediali vengono elaborate e il numero di attività multimediali che possono essere elaborate contemporaneamente in un account. Se, ad esempio, l'account ha cinque MRU e sono presenti attività da elaborare, è possibile eseguire simultaneamente cinque attività multimediali. Tutte le attività rimanenti verranno accodate e possono essere prelevate per l'elaborazione in sequenza al termine di un'attività in esecuzione. Ogni MRU di cui si esegue il provisioning comporta una prenotazione di capacità ma non fornisce risorse dedicate. Durante i periodi di richiesta estremamente elevata, è possibile che l'elaborazione non venga avviata immediatamente da tutti i MRU.

## <a name="choosing-between-different-reserved-unit-types"></a>Scelta tra i tipi diversi di unità riservata

La tabella seguente consente di scegliere tra diverse velocità di codifica.  Mostra la durata della codifica per un video di 7 minuti a 1080p, a seconda dell'uso di MRU.

|Tipo di unità richiesta|Scenario|Risultati di esempio per il video di 7 min con risoluzione 1080p |
|---|---|---|
| **S1**|Codifica con velocità in bit singola. <br/>File SD o con risoluzione inferiore, non dipendenti dall'ora, a basso costo.|La codifica in un file MP4 di risoluzione SD a bitrate singolo con "H264 Single bitrate SD 16x9" richiede circa 7 minuti.|
| **S2**|Codifica con velocità in bit singola e multipla.<br/>Uso normale per la codifica SD e HD.|La codifica con il set di impostazioni "H264 a bitrate singolo 720p" richiede circa 6 minuti.<br/><br/>La codifica con il set di impostazioni "H264 a bitrate multipli 720p" richiede circa 12 minuti.|
| **S3**|Codifica con velocità in bit singola e multipla.<br/>Video Full HD e con risoluzione 4K. Codifica dipendente dall'ora con completamento più rapido.|La codifica con il set di impostazioni "H264 Single bitrate 1080p" richiede circa 3 minuti.<br/><br/>La codifica con "Codec video H.264 a bitrate multiplo con risoluzione 1080p" preconfigurato richiede circa 8 minuti.|

> [!NOTE]
> Se non si esegue il provisioning di MRU per il proprio account, le attività multimediali verranno elaborate con le prestazioni di un file con estensione S1 e le attività verranno prelevate in sequenza. Nessuna capacità di elaborazione è riservata, quindi il tempo di attesa tra un completamento delle attività e il successivo avvio dipende dalla disponibilità delle risorse nel sistema.

## <a name="considerations"></a>Considerazioni

* Per i processi di analisi audio e analisi video attivati da Media Services V3 o Video Indexer, è consigliabile eseguire il provisioning dell'account con dieci unità S3. Se sono necessarie più di 10 MRU S3, aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com/).
* Per le attività di codifica senza MRU, non esiste alcun limite superiore al tempo che le attività possono trascorrere nello stato in coda e al massimo una sola attività verrà eseguita alla volta.

## <a name="billing"></a>Fatturazione

L'addebito è basato sul numero di minuti per cui viene effettuato il provisioning di media reserved Unit nell'account. Ciò si verifica indipendentemente dalla presenza di processi in esecuzione nell'account. Per una spiegazione dettagliata, vedere la sezione delle domande frequenti nella pagina dei [prezzi dei Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="quotas-and-limitations"></a>Quote e limitazioni

Per informazioni su quote e limitazioni e su come aprire un ticket di supporto, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Passaggi successivi

Provare a ridimensionare l'elaborazione di contenuti multimediali con una di queste tecnologie:

[.NET](media-services-dotnet-encoding-units.md) 
 [Portale](media-services-portal-scale-media-processing.md) 
 di [Rest](/rest/api/media/operations/encodingreservedunittype) 
 [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples) 
 [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)