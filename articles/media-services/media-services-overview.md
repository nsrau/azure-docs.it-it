---
title: Panoramica di Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento fornisce informazioni generali su Servizi multimediali di Azure
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2017
ms.author: juliako;anilmur
ms.openlocfilehash: a1e56a6d31568d1d36e713b9a1000d84c6446bac
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="azure-media-services-overview"></a>Panoramica di Servizi multimediali di Azure 

Servizi multimediali di Microsoft Azure costituisce una piattaforma estensibile basata sul cloud che consente agli sviluppatori di creare applicazioni di distribuzione e gestione di contenuti multimediali altamente scalabili. Servizi multimediali si basa su API REST che consentono di caricare, archiviare e codificare contenuti video o audio in modo sicuro, nonché creare pacchetti di tali contenuti per la distribuzione in streaming live e on demand a vari client (ad esempio, TV, PC e dispositivi mobili).

È possibile creare flussi di lavoro end-to-end usando unicamente Servizi multimediali. È anche possibile usare componenti di terze parti per alcune parti del flusso di lavoro, ad esempio, la codifica con un codificatore di terze parti. Inoltre, sono possibili operazioni di caricamento, protezione, creazione di pacchetti e invio tramite Servizi multimediali.

È possibile optare per lo streaming dei contenuti live o on demand. L'argomento contiene inoltre collegamenti ad altri argomenti rilevanti.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
È possibile visualizzare i percorsi di apprendimento AMS qui:

* [Flusso di lavoro AMS Live Streaming](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Flusso di lavoro dello streaming on demand di AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a utilizzare Servizi multimediali di Azure, è necessario disporre di quanto segue:

* Un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com).
* Un account di Servizi multimediali di Azure. Per altre informazioni, vedere [Creare un account](media-services-portal-create-account.md).
* (Facoltativo) Configurare l'ambiente di sviluppo. Scegliere .NET o API REST per l'ambiente di sviluppo. Per altre informazioni, vedere [Configurare l'ambiente](media-services-dotnet-how-to-use.md).

    Sono anche disponibili informazioni su come [connettersi all'API AMS a livello di codice](media-services-use-aad-auth-to-access-ams-api.md).
* Un endpoint di streaming Standard o Premium con stato avviato.  Per altre informazioni, vedere [Gestione degli endpoint di streaming](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>SDK e strumenti

Per creare soluzioni di Servizi multimediali, è possibile usare:

* [API REST di Servizi multimediali](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Uno dei client SDK disponibili:
    * [Azure Media Services SDK per .NET](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Servizi multimediali di Azure per Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js). Questa è una versione non Microsoft di Node.js SDK. È gestita da una community e attualmente non copre al 100% le API AMS.
* Strumenti esistenti:
    * [Portale di Azure](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) è un'applicazione Winforms/C# per Windows)

## <a name="code-samples"></a>Esempi di codice

Gli esempi di codice sono disponibili nella raccolta di **Esempi di codice per Azure**: [Esempi di codice per Servizi multimediali di Azure](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Concetti

Per i concetti relativi ai Servizi multimediali di Azure, vedere [Concetti su Servizi multimediali di Azure](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Scenari supportati e disponibilità di Servizi multimediali nei data center

Per informazioni dettagliate, vedere [Scenari supportati e disponibilità di Servizi multimediali nei data center](scenarios-and-availability.md).

## <a name="service-level-agreement-sla"></a>Contratto di servizio (SLA)

* Per il servizio di codifica di Servizi multimediali, è garantita una disponibilità al 99,9% delle transazioni delle API REST.
* Con l'acquisto di un endpoint di streaming Standard o Premium, per il servizio di streaming, è garantita una disponibilità al 99,9% per i contenuti multimediali esistenti.
* Per i canali live, è garantito che i canali in esecuzione avranno connettività esterna per almeno il 99,9% del tempo.
* Per la protezione del contenuto, è garantita l'evasione delle richieste di chiavi per almeno il 99,9% del tempo.
* Per l'indicizzatore, è garantito che verranno soddisfatte le richieste di attività dell'indicizzatore elaborate con un'unità riservata di codifica per il 99,9% del tempo.

Per altre informazioni, vedere [Contratto di servizio di Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

Per informazioni sulla disponibilità nei data center, vedere la sezione relativa alla [disponibilità](scenarios-and-availability.md#availability).

## <a name="support"></a>Supporto

[supporto tecnico di Azure](https://azure.microsoft.com/support/options/) fornisce opzioni di supporto per Azure, compreso Servizi multimediali.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
