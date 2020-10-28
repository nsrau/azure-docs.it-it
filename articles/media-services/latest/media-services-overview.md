---
title: Panoramica di Servizi multimediali di Azure v3
titleSuffix: Azure Media Services
description: Panoramica di alto livello di Servizi multimediali di Azure v3 con collegamenti a guide introduttive, esercitazioni ed esempi di codice.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: mvc
ms.openlocfilehash: c7bdf3e0300bffaffb7191aea019bed719cdc6fa
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220619"
---
# <a name="azure-media-services-v3-overview"></a>Panoramica di Servizi multimediali di Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Servizi multimediali di Azure è una piattaforma basata su cloud che consente di creare soluzioni che offrono streaming video di alta qualità, migliorano accessibilità e distribuzione, analizzano il contenuto e molto altro ancora. Servizi multimediali consente agli sviluppatori di app, ai call center, alle agenzie governative o alle società di intrattenimento di creare app che offrono esperienze multimediali di qualità eccezionale a numerosi destinatari sui browser e sui dispositivi mobili moderni più diffusi.

Gli SDK di Servizi multimediali v3 sono basati sulla [specifica OpenAPI (Swagger) per Servizi multimediali v3](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> Attualmente, è possibile usare il [portale di Azure](https://portal.azure.com/) per gestire gli [eventi live](live-events-outputs-concept.md) si Servizi multimediali v3, visualizzare (non gestire) gli [asset](assets-concept.md) v3 e [ottenere informazioni sull'accesso alle API](./access-api-howto.md). Per tutte le altre attività di gestione, ad esempio, [trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto](content-protection-overview.md), usare l'[API REST](/rest/api/media/), l'[interfaccia della riga di comando](/cli/azure/ams) o uno degli [SDK](media-services-apis-overview.md#sdks) supportati.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Conformità, privacy e sicurezza

È importante ricordare che è necessario usare Servizi multimediali di Azure in conformità con tutte le leggi applicabili e non è possibile usare Servizi multimediali né qualsiasi altro servizio di Azure in un modo che violi i diritti di altri o possa arrecare danni ad altre persone.

Prima di caricare un video o un'immagine in Servizi multimediali, è necessario avere tutti i diritti appropriati per il relativo uso, tra cui, se richiesto per legge, tutti i consensi necessari delle persone (se presenti) incluse nel video o nell'immagine, per l'uso, l'elaborazione e l'archiviazione dei loro dati in Servizi multimediali e in Azure. Alcune giurisdizioni possono imporre specifici requisiti legali per la raccolta, l'elaborazione online e l'archiviazione di determinate categorie di dati, ad esempio i dati biometrici. Prima di usare Servizi multimediali e Azure per l'elaborazione e l'archiviazione di dati soggetti a requisiti legali speciali, è necessario garantire la conformità a tali requisiti legali che possono essere applicabili al caso specifico.

Per informazioni su conformità, privacy e sicurezza in Servizi multimediali, visitare il [Centro protezione](https://www.microsoft.com/trust-center/?rtc=1) Microsoft. Per informazioni sugli obblighi relativi alla privacy e sulle procedure di Microsoft per il trattamento e la conservazione dei dati, tra cui come eliminare i propri dati, vedere l'[informativa sulla privacy](https://privacy.microsoft.com/PrivacyStatement), le [condizioni per l'utilizzo dei servizi online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) e l'[addendum sull'elaborazione dati](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) di Microsoft. Usando Servizi multimediali, si accetta di essere vincolati dalle condizioni per l'utilizzo dei servizi online, dall'addendum sull'elaborazione dati e dall'informativa sulla privacy.
 
## <a name="what-can-i-do-with-media-services"></a>Cosa è possibile fare con Servizi multimediali?

Servizi multimediali consente di creare un'ampia gamma di flussi di lavoro multimediali nel cloud. Di seguito vengono indicati alcuni esempi di ciò che è possibile fare con Servizi multimediali:

* Distribuire video in vari formati in modo da poterli riprodurre su una vasta gamma di browser e dispositivi. Per la distribuzione in streaming live e on demand su vari client (dispositivi mobili, TV, PC e così via), è necessario codificare e creare pacchetti di contenuti audio e video in modo appropriato. Per informazioni su come distribuire ed eseguire lo streaming di questo tipo di contenuti, vedere [Guida introduttiva: Eseguire lo streaming di file video](stream-files-dotnet-quickstart.md).
* Eseguire lo streaming live di eventi sportivi per un vasto pubblico online, ad esempio calcio, baseball, eventi sportivi universitari e liceali e altro ancora.
* Trasmettere riunioni pubbliche ed eventi, quali riunioni comunali o municipali e di enti legislativi.
* Analizzare contenuti audio o video registrati. Per migliorare la soddisfazione dei clienti, le organizzazioni possono ad esempio eseguire la conversione della voce in testo scritto e creare indici di ricerca e dashboard. Possono quindi estrarre informazioni su reclami comuni, origini dei reclami e altri dati rilevanti.
* Creare un servizio di sottoscrizione video ed eseguire lo streaming di contenuti protetti con DRM quando un cliente (ad esempio, uno studio cinematografico) deve limitare l'accesso e l'uso di opere proprietarie tutelate da copyright.
* Distribuire contenuti offline per la riproduzione su aerei, treni e automobili. È possibile che un cliente debba scaricare contenuti sullo smartphone o sul tablet per la riproduzione durante la disconnessione dalla rete.
* Implementare una piattaforma video di apprendimento didattico con Servizi multimediali di Azure e [API Servizi cognitivi di Azure](../../index.yml?pivot=products&panel=ai) per la conversione della voce in testo scritto, la traduzione in più lingue e così via.
* Usare Servizi multimediali di Azure insieme alle [API Servizi cognitivi di Azure](../../index.yml?pivot=products&panel=ai) per aggiungere sottotitoli e didascalie ai video e soddisfare le esigenze di un pubblico più ampio, ad esempio persone con disabilità uditive o che vogliono leggere il contenuto in una lingua diversa.
* Abilitare la rete CDN di Azure per ottenere notevole scalabilità e gestire al meglio un carico elevato immediato, ad esempio all'inizio di un evento per il lancio di un prodotto.

## <a name="how-can-i-get-started-with-v3"></a>Come si procede per iniziare a usare v3? 

Informazioni su come codificare e creare pacchetti del contenuto, trasmettere video on demand in streaming, trasmettere live e analizzare video con Servizi multimediali v3. Esercitazioni, informazioni di riferimento sulle API e altri documenti mostrano come fornire in modo sicuro flussi su audio o video live e on demand a milioni di utenti.

> [!TIP]
> Prima di iniziare lo sviluppo, esaminare:<br/>* [Concetti fondamentali](concepts-overview.md) (include concetti importanti come la creazione di pacchetti, la codifica e la protezione)<br/>* [Sviluppo con le API Servizi multimediali v3](media-services-apis-overview.md) (sono incluse informazioni sull'accesso alle API, sulle convenzioni di denominazione e così via)

### <a name="sdks"></a>SDK

Iniziare a sviluppare con gli [SDK client di Servizi multimediali di Azure v3](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Avvi rapidi  

Questi argomenti di avvio rapido Illustrano le istruzioni fondamentali di base per i nuovi clienti che vogliono provare rapidamente Servizi multimediali.

* [Eseguire lo streaming di file video - .NET](stream-files-dotnet-quickstart.md)
* [Eseguire lo streaming di file video - Interfaccia della riga di comando](stream-files-cli-quickstart.md)
* [Eseguire lo streaming di file video - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Esercitazioni

Queste esercitazioni illustrano le procedure basate su scenari per alcune delle principali attività di Servizi multimediali.

* [Codificare il file remoto e trasmettere il video in streaming - REST](stream-files-tutorial-with-rest.md)
* [Codificare il file caricato e trasmettere il video in streaming - .NET](stream-files-tutorial-with-api.md)
* [Streaming live - .NET](stream-live-tutorial-with-api.md)
* [Analizzare i video - .NET](analyze-videos-tutorial-with-api.md)
* [Crittografia dinamica AES-128 - .NET](protect-with-aes128.md)

### <a name="samples"></a>Esempi

Usare [Esplora gli esempi di codice](/samples/browse/?products=azure-media-services) per esplorare gli esempi di codice di Servizi multimediali di Azure.

### <a name="how-to-guides"></a>Guide pratiche

Le guide pratiche contengono esempi di codice che illustrano come completare un'attività. In questa sezione sono disponibili molti esempi. Alcuni esempi sono i seguenti:

* [Creare un account - Interfaccia della riga di comando](./create-account-howto.md)
* [Accedere alle API - Interfaccia della riga di comando](./access-api-howto.md)
* [Codificare con HTTPS come input del processo - .NET](job-input-from-http-how-to.md)  
* [Monitorare gli eventi - Portale](monitor-events-portal-how-to.md)
* [Crittografare in modo dinamico il contenuto con più tecnologie DRM - .NET](protect-with-drm.md) 
* [Come codificare con una trasformazione personalizzata - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui concetti fondamentali](concepts-overview.md)
