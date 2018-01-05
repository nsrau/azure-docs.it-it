---
title: "Ridimensionare l'elaborazione multimediale aggiungendo unità di codifica - Azure | Documentazione Microsoft"
description: "Informazioni su come aggiungere unità di codifica mediante .NET"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2017
ms.author: juliako;milangada;
ms.openlocfilehash: f45da151472efb4cc6297b005d5bc3844d664928
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Come scalare la codifica con .NET SDK
> [!div class="op_single_selector"]
> * [di Microsoft Azure](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> Per ottenere la versione più recente di Java SDK e iniziare a sviluppare con Java, vedere [Introduzione a Java Client SDK per Servizi multimediali](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Per scaricare la versione più recente di PHP SDK per Servizi multimediali, cercare la versione 0.5.7 del pacchetto Microsoft/WindowAzure nel [repository Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Panoramica
> [!IMPORTANT]
> Assicurarsi di esaminare il [Panoramica](media-services-scale-media-processing-overview.md) per ottenere ulteriori informazioni sulla scalabilità elaborazione multimediale.
> 
> 

Per cambiare il tipo di unità riservata e il numero di unità riservate di codifica mediante l'SDK per .NET, seguire questa procedura:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Apertura di un ticket di supporto
Per impostazione predefinita, ogni account di Media Services può includere fino a 25 unità di codifica riservate e cinque unità riservate di streaming on demand. È possibile richiedere l'applicazione di un limite superiore mediante l'apertura di un ticket di supporto.

### <a name="open-a-support-ticket"></a>Aprire un ticket di supporto
Nel portale di Azure, passare a [Guida e supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Se non è stato effettuato l'accesso ad Azure, viene chiesto di immettere le credenziali.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

