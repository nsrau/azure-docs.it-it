---
title: Panoramica delle entità di Servizi multimediali di Azure | Microsoft Docs
description: Questo articolo offre una panoramica sulle entità di Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1d309bb550282d5245a2fbf74f14a931cf5c2279
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53745731"
---
# <a name="azure-media-services-entities-overview"></a>Panoramica delle entità di Servizi multimediali di Azure

Questo articolo offre una breve panoramica delle entità di Servizi multimediali di Azure e rimanda a un articolo per altre informazioni su ogni entità. 

| Argomento | DESCRIZIONE |
|---|---|
| [Filtri account e filtri asset](filters-dynamic-manifest-overview.md)|Quando si distribuiscono contenuti ai clienti (streaming di eventi live o di video on demand) il client potrebbe avere bisogno di una maggiore flessibilità rispetto a quanto descritto nel file manifesto dell'asset predefinito. Servizi multimediali di Azure consente di definire i [Filtri account](https://docs.microsoft.com/rest/api/media/accountfilters) e i [Filtri asset](https://docs.microsoft.com/rest/api/media/assetfilters). Quindi, usare **Manifesti dinamici** in base ai filtri predefiniti. |
| [Asset](assets-concept.md)|Un'entità [Asset](https://docs.microsoft.com/rest/api/media/assets) contiene file digitali (tra cui video, audio, immagini, raccolte di anteprima, tracce di testo e file di sottotitoli codificati) e i relativi metadati. Una volta caricati in un asset, i file digitali possono essere usati nei flussi di lavoro di codifica, trasmissione e analisi di Servizi multimediali di Azure.|
| [Criteri di chiave simmetrica](content-key-policy-concept.md)|È possibile usare Servizi multimediali di Azure per proteggere i file multimediali dal momento in cui escono dal computer fino alle fasi di archiviazione, elaborazione e recapito. Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.|
| [Elementi LiveEvent e LiveOutput](live-events-outputs-concept.md)|Servizi multimediali di Azure consente di offrire eventi live per i clienti nel cloud di Azure. Per configurare gli eventi di streaming live in Servizi multimediali v3, è necessario conoscere [Eventi live](https://docs.microsoft.com/rest/api/media/liveevents) e [Output live](https://docs.microsoft.com/rest/api/media/liveoutputs).|
| [Endpoint di streaming](streaming-endpoint-concept.md)|Un [endpoint di streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione del lettore client o a una rete CDN (Content Delivery Network) per la successiva distribuzione. Il flusso in uscita da un servizio endpoint di streaming può essere costituito da un flusso live o da una risorsa video on demand associata all'account di Servizi multimediali. Quando si crea un account di Servizi multimediali viene creato un endpoint di streaming **predefinito** nello stato Arrestato. L'endpoint di streaming **predefinito** non può essere eliminato. Nell'account è possibile creare altri endpoint di streaming. Per avviare lo streaming di video, è necessario avviare l'endpoint di streaming da cui si desidera trasmettere il video. |
| [Localizzatori di streaming](streaming-locators-concept.md)|Per rendere disponibile ai client un URL da usare per riprodurre file audio o video codificati, è necessario creare un [localizzatore di streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e compilare gli URL di streaming.|
| [Criteri di streaming](streaming-policy-concept.md)| I [criteri di streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) consentono di definire protocolli di streaming e opzioni di crittografia per StreamingLocators. È possibile assegnare un nome ai criteri di streaming creati oppure usare uno dei criteri di streaming predefiniti offerti da Servizi multimediali. <br/><br/>Se si usano criteri di streaming personalizzati, è necessario progettare un set limitato di tali criteri per l'account di Servizi multimediali e riusare questi criteri per i localizzatori di streaming ogni volta che è necessario usare gli stessi protocolli e le stesse opzioni di crittografia. Evitare quindi di creare nuovi criteri di streaming per ogni localizzatore di streaming.|
| [Trasformazioni e processi](transforms-jobs-concept.md)|Usare [Trasformazioni](https://docs.microsoft.com/rest/api/media/transforms) per configurare attività comuni relative alla codifica o all'analisi dei video. Ogni **trasformazione** descrive un recipe o un flusso di lavoro di attività per l'elaborazione dei file video o audio.<br/><br/>Un [processo](https://docs.microsoft.com/rest/api/media/jobs) è la richiesta effettiva inviata a Servizi multimediali per applicare la **trasformazione** a determinati contenuti audio o video di input. Il **processo** specifica informazioni come la posizione del video di input e quella dell'output. È possibile specificare il percorso del video di input mediante: URL HTTPS, URL di firma di accesso condiviso o un asset.|

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
