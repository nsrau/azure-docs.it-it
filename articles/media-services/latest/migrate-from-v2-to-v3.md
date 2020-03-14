---
title: Eseguire la migrazione di Servizi multimediali di Azure dalla versione 2 alla versione 3 | Microsoft Docs
description: In questo articolo vengono descritte le modifiche introdotte nella versione 3 di Servizi multimediali di Azure e sono illustrate le differenze tra le due versioni. Questo articolo fornisce inoltre materiale sussidiario sulla migrazione per aggiornare Servizi multimediali dalla versione 2 alla versione 3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5083dc79b146598142ac27eb6ac7ef9ed436f37d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251557"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Materiale sussidiario sulla migrazione per aggiornare Servizi multimediali da v2 a v3

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` nel lettore di feed RSS.

Questo articolo fornisce indicazioni sulla migrazione da Media Services V2 a V3.

In caso di sviluppo di un servizio video basato sulle [API legacy di Servizi multimediali v2](../previous/media-services-overview.md), è necessario rivedere le linee guida e considerazioni seguenti prima della migrazione alle API v3. L'API v3 include numerosi vantaggi e nuove funzionalità che migliorano l'esperienza di sviluppo e le funzionalità di Servizi multimediali. Tuttavia, come definito nella sezione [Problemi noti](#known-issues) di questo articolo, esistono anche alcune limitazioni causate da differenze tra le versioni delle API. Questa pagina viene aggiornata man mano che il team di Servizi multimediali apporta costanti miglioramenti alle API v3 e colma le lacune tra le versioni. 

## <a name="prerequisites"></a>Prerequisites

* Esaminare i [servizi multimediali V2 e V3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Vantaggi di Servizi multimediali v3
  
### <a name="api-is-more-approachable"></a>API più accessibile

*  v3 si basa su una superficie API unificata, che espone funzionalità operative e di gestione basate su Azure Resource Manager. I modelli di Azure Resource Manager possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, eventi live e altro ancora.
* Documento di [specifica openapi (noto in precedenza come spavalderia)](https://aka.ms/ams-v3-rest-sdk) .
    Espone lo schema per tutti i componenti del servizio, tra cui la codifica basata su file.
* SDK disponibili per [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref) e Ruby.
* Integrazione dell'[interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli-ref) per semplificare il supporto di scripting.

### <a name="new-features"></a>Nuove funzionalità

* Per l'elaborazione di processi basata su file è possibile usare un URL HTTP(S) come input.<br/>Non è necessario avere contenuto già archiviato in Azure né creare asset.
* Introduce il concetto di [trasformazioni](transforms-jobs-concept.md) per l'elaborazione di processi basata su file. Una trasformazione consente di creare configurazioni riutilizzabili, creare modelli di Azure Resource Manager e isolare le impostazioni di elaborazione tra più clienti o tenant.
* Un asset può avere più [localizzatori di streaming](streaming-locators-concept.md), ognuno con impostazioni diverse di [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e crittografia dinamica.
* La [protezione del contenuto](content-key-policy-concept.md) supporta le funzionalità con più chiavi.
* È possibile trasmettere eventi live per una durata massima di 24 ore quando si usa Servizi multimediali per la transcodifica di un feed di contributi a bitrate singolo in un flusso di output a bitrate multipli.
* Nuovo supporto per streaming live a bassa latenza per eventi live. Per altre informazioni, vedere [Latenza](live-event-latency.md).
* L'anteprima degli eventi live supporta la [creazione dinamica dei pacchetti](dynamic-packaging-overview.md) e la crittografia dinamica. Questo consente la protezione del contenuto nell'anteprima, nonché la creazione di pacchetti DASH e HLS.
* L'output live è più semplice da usare rispetto all'entità Program nelle API v2. 
* Supporto RTMP migliorato (maggiore stabilità e più supporto per i codificatori di origine).
* Inserimento RTMPS sicuro.<br/>Quando si crea un evento live, si ottengono 4 URL di inserimento. pressoché identici: hanno lo stesso token di streaming (AppId) e solo la parte del numero di porta è diversa. Due URL sono primari e due sono di backup per RTMPS.   
* Controllo degli accessi in base al ruolo sulle entità. 

## <a name="known-issues"></a>Problemi noti

*  Attualmente, è possibile utilizzare il [portale di Azure](https://portal.azure.com/) per:

    * gestione [degli eventi live](live-events-outputs-concept.md)di servizi multimediali V3 
    * visualizzare (non gestire) gli [Asset](assets-concept.md)V3, 
    * [ottenere informazioni sull'accesso alle API](access-api-portal.md). 

    Per tutte le altre attività di gestione (ad esempio, [trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto](content-protection-overview.md)), usare l' [API REST](https://aka.ms/ams-v3-rest-ref), l' [interfaccia](https://aka.ms/ams-v3-cli-ref)della riga di comando o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.
* È necessario effettuare il provisioning di unità riservate di codifica nell'account per controllare la concorrenza e le prestazioni dei processi, in particolare quelli che includono l'analisi audio o video. Per altre informazioni, vedere [Panoramica del ridimensionamento dell'elaborazione multimediale](../previous/media-services-scale-media-processing-overview.md). È possibile gestire le unità riservate di codifica usando l'[interfaccia della riga di comando 2.0 per Servizi multimediali v3](media-reserved-units-cli-how-to.md), il [portale di Azure](../previous/media-services-portal-scale-media-processing.md) o le [API v2](../previous/media-services-dotnet-encoding-units.md). È necessario effettuare il provisioning delle unità riservate di codifica, indipendentemente dall'uso di Servizi multimediali v2 o delle API v3.
* Le entità di Servizi multimediali create con l'API v3 non possono essere gestite dall'API v2.  
* Non tutte le entità nell'API v2 vengono visualizzate automaticamente nell'API V3.  Di seguito sono riportati esempi di entità nelle due versioni incompatibili:  
    * I processi e le attività creati in v2 non vengono visualizzati in v3 in quanto non sono associati a una trasformazione. È consigliabile passare a trasformazioni e processi v3. Per un periodo di tempo relativamente breve è necessario monitorare i processi v2 in fase di elaborazione durante il passaggio.
    * I canali e i programmi creati con v2 (corrispondenti a eventi live e output live in v3) non possono continuare a essere gestiti con v3. È consigliabile passare agli eventi live e agli output live v3 in corrispondenza di un arresto canale pratico.<br/>Non è attualmente possibile eseguire la migrazione di canali in continua esecuzione.  

> [!NOTE]
> Questa pagina viene aggiornata man mano che il team di Servizi multimediali apporta costanti miglioramenti alle API v3 e colma le lacune tra le versioni.

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione: codificare un file remoto in base all'URL e trasmettere il video-.NET](stream-files-dotnet-quickstart.md)
