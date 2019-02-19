---
title: Panoramica di Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo include una panoramica dettagliata di Servizi multimediali e include collegamenti ad articoli che contengono informazioni più dettagliate.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 1911b851e4e219ec4c6d2d4872b75e9c18706feb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893324"
---
# <a name="what-is-azure-media-services-v3"></a>Che cos'è Servizi multimediali di Azure v3?

Servizi multimediali di Azure è una piattaforma basata su cloud che consente di creare soluzioni che offrono streaming video di alta qualità, migliorano accessibilità e distribuzione, analizzano il contenuto e molto altro ancora. Servizi multimediali consente agli sviluppatori di applicazioni, ai call center, alle agenzie governative o alle società di intrattenimento di creare applicazioni che offrono esperienze multimediali di qualità eccezionale a numerosi destinatari sui browser e sui dispositivi mobili moderni più diffusi. 

## <a name="what-can-i-do-with-media-services"></a>Cosa è possibile fare con Servizi multimediali?

Servizi multimediali consente di creare vari flussi di lavoro multimediali nel cloud e di seguito sono riportati alcuni esempi di ciò che è possibile realizzare con Servizi multimediali.  

* Distribuire video in vari formati in modo da poterli riprodurre su una vasta gamma di browser e dispositivi. Per la distribuzione in streaming live e on demand su vari client (dispositivi mobili, TV, PC e così via), è necessario codificare e creare pacchetti di contenuti audio e video in modo appropriato. Per informazioni su come distribuire e trasmettere questo tipo di contenuto, vedere [Avvio rapido: Codificare e trasmettere i file](stream-files-dotnet-quickstart.md).
* Eseguire lo streaming live di eventi sportivi per un vasto pubblico online, ad esempio calcio, baseball, eventi sportivi universitari e liceali e altro ancora. 
* Trasmettere riunioni pubbliche ed eventi, quali riunioni comunali o municipali e di enti legislativi.
* Analizzare contenuti audio o video registrati. Per migliorare la soddisfazione dei clienti, le organizzazioni possono ad esempio eseguire la conversione della voce in testo scritto e creare indici di ricerca e dashboard. Possono quindi estrarre informazioni su reclami comuni, origini dei reclami e altri dati rilevanti.
* Creare un servizio di sottoscrizione video ed eseguire lo streaming di contenuti protetti con DRM quando un cliente (ad esempio, uno studio cinematografico) deve limitare l'accesso e l'uso di opere proprietarie tutelate da copyright.
* Distribuire contenuti offline per la riproduzione su aerei, treni e automobili. È possibile che un cliente debba scaricare contenuti sullo smartphone o sul tablet per la riproduzione durante la disconnessione dalla rete.
* Implementare una piattaforma video di apprendimento didattico con Servizi multimediali di Azure e [API Servizi cognitivi di Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) per la conversione della voce in testo scritto, la traduzione in più lingue e così via. 
* Usare Servizi multimediali di Azure insieme alle [API Servizi cognitivi di Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) per aggiungere sottotitoli e didascalie ai video e soddisfare le esigenze di un pubblico più ampio, ad esempio persone con disabilità uditive o che vogliono leggere il contenuto in una lingua diversa.
* Abilitare la rete CDN di Azure per ottenere notevole scalabilità e gestire al meglio un carico elevato immediato, ad esempio all'inizio di un evento per il lancio di un prodotto. 

## <a name="v3-capabilities"></a>Funzionalità v3

v3 si basa su una superficie API unificata, che espone funzionalità operative e di gestione basate su Azure Resource Manager. 

Questa versione offre le funzionalità seguenti:  

* **Trasformazioni** che consentono di definire flussi di lavoro semplici di elaborazione multimediale o attività analitiche. Una trasformazione è una serie di istruzioni per l'elaborazione di file audio e video. È possibile applicarla più volte per elaborare tutti i file della raccolta contenuto inviando i processi alla trasformazione.
* **Processi** per elaborare (codificare o analizzare) i video. È possibile specificare il contenuto di input in un processo usando URL HTTPS, URL di firma di accesso condiviso o percorsi ai file che si trovano nell'archivio BLOB di Azure. Attualmente AMS v3 non supporta la codifica di trasferimenti in blocchi su URL HTTPS.
* **Notifiche** che monitorano lo stato o l'avanzamento del processo oppure eventi di errore e di avvio/arresto del canale live. Le notifiche sono integrate con il sistema di notifica Griglia di eventi di Azure. È possibile sottoscrivere facilmente eventi in diverse risorse in Servizi multimediali di Azure. 
* I modelli di **Gestione delle risorse di Azure** possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, canali e altro ancora.
* È possibile impostare il **controllo degli accessi in base al ruolo** a livello di risorsa, consentendo di bloccare l'accesso a specifiche risorse, ad esempio trasformazioni, canali e altro ancora.
* **SDK client** in più linguaggi: .NET, .NET Core, Python, Go, Java e Node.js.

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID). 

I nomi delle risorse di Servizi multimediali non possono includere "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", virgolette singole o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri. 

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere: [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenzioni di denominazione](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="media-services-v3-api-design-principles"></a>Principi di progettazione delle API Servizi multimediali v3

Uno dei principi chiave nella progettazione della versione 3 delle API è renderle più sicure. Le API della versione 3 non restituiscono segreti o credenziali per un'operazione **Get** o **List**. Le chiavi sono sempre Null, vuote o purificate dalla risposta. È necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Azioni distinte consentono di impostare autorizzazioni di sicurezza di controllo degli accessi in base al ruolo diverse, nel caso alcune API recuperino o visualizzino i segreti, mentre altre non lo fanno. Per informazioni su come gestire l'accesso con il controllo degli accessi in base al ruolo, vedere [Gestire l'accesso tramite il controllo degli accessi in base al ruolo e l'API REST](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Alcuni esempi sono 

* non restituire valori ContentKey in Get per StreamingLocator, 
* non restituire le chiavi di restrizione in Get per ContentKeyPolicy, 
* non restituire la parte della stringa di query dell'URL (per rimuovere la firma) negli URL di input HTTP per i processi.

Vedere l'esempio incluso in [Ottenere i criteri di chiave simmetrica - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="how-can-i-get-started-with-v3"></a>Come si procede per iniziare a usare v3?

Gli sviluppatori possono usare l'[API REST](https://go.microsoft.com/fwlink/p/?linkid=873030) Servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e mantenere facilmente flussi di lavoro multimediali personalizzati. L'API Servizi multimediali v3 è basata sulla [specifica OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) (in precedenza nota come Swagger).

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) è uno strumento disponibile per i clienti di Windows che vogliono informazioni su Servizi multimediali. AMSE è un'applicazione Winforms/C# che esegue operazioni di caricamento, download, codifica, streaming di VOD e contenuti live con Servizi multimediali. Lo strumento AMSE è stato pensato per i clienti che vogliono testare Servizi multimediali senza scrivere codice. Il codice AMSE viene fornito come risorsa per i clienti che vogliono sviluppare con Servizi multimediali.

AMSE è un progetto open source il cui supporto viene fornito dalla community (i problemi possono essere segnalati a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Questo progetto ha adottato il [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Codice di comportamento Open Source di Microsoft). Per altre informazioni, vedere [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Domande frequenti sul Codice di comportamento Open Source di Microsoft) oppure contattare opencode@microsoft.com per eventuali altre domande o commenti.
 
Servizi multimediali di Azure supporta le librerie client seguenti: 

|Informazioni di riferimento sulle API|SDK/Strumenti|Esempi|
|---|---|---|---|
|[Informazioni di riferimento su REST](https://aka.ms/ams-v3-rest-ref)|[REST SDK](https://aka.ms/ams-v3-rest-sdk)|[Esempi di REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman)<br/>[API REST basate su Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)|
|[Informazioni di riferimento sull'interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli-ref)|[Interfaccia della riga di comando di Azure](https://aka.ms/ams-v3-cli)|[Esempi di interfaccia della riga di comando di Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)||
|[Informazioni di riferimento su .NET](https://aka.ms/ams-v3-dotnet-ref)|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Esempi di .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)||
||[.NET Core SDK](https://aka.ms/ams-v3-dotnet-sdk) (scegliere la scheda **.NET CLI**)|[Esempi di .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)||
|[Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref)|[SDK per Java](https://aka.ms/ams-v3-java-sdk)||
|[Informazioni di riferimento su Node.js](https://aka.ms/ams-v3-nodejs-ref)|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)|[Esempi per Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)||
|[Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref)|[Python SDK](https://aka.ms/ams-v3-python-sdk)||
|[Informazioni di riferimento su Go](https://aka.ms/ams-v3-go-ref)|[Go SDK](https://aka.ms/ams-v3-go-sdk)||
|Ruby|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

## <a name="next-steps"></a>Passaggi successivi

Per scoprire quanto è semplice eseguire la codifica e lo streaming di file video, vedere l'articolo relativo allo [streaming di file](stream-files-dotnet-quickstart.md). 

