---
title: Panoramica di Servizi multimediali di Azure v3 | Microsoft Docs
description: Questo articolo include una panoramica dettagliata di Servizi multimediali e include collegamenti ad articoli che contengono informazioni più dettagliate.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: servizi multimediali di Azure, streaming, trasmissione, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 489801852202163ef40d57da0082e39793196d85
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264095"
---
# <a name="what-is-azure-media-services-v3"></a>Che cos'è Servizi multimediali di Azure v3?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versione 2 - Disponibilità generale](../previous/media-services-overview.md)
> * [Versione 3 - Anteprima](media-services-overview.md)

> [!NOTE]
> La versione più recente di Servizi multimediali di Azure è disponibile in anteprima e può essere indicata come v3.

Servizi multimediali di Azure è una piattaforma basata su cloud che consente di creare soluzioni che offrono streaming video di alta qualità, migliorano accessibilità e distribuzione, analizzano il contenuto e molto altro ancora. Servizi multimediali consente agli sviluppatori di applicazioni, ai call center, alle agenzie governative o alle società di intrattenimento di creare applicazioni che offrono esperienze multimediali di qualità eccezionale a numerosi destinatari sui browser e sui dispositivi mobili moderni più diffusi. 

## <a name="what-can-i-do-with-media-services"></a>Cosa è possibile fare con Servizi multimediali?

Servizi multimediali consente di creare vari flussi di lavoro multimediali nel cloud e di seguito sono riportati alcuni esempi di ciò che è possibile realizzare con Servizi multimediali.  

* Distribuire video in vari formati in modo da poterli riprodurre su una vasta gamma di browser e dispositivi. Per la distribuzione in streaming live e on demand su vari client (dispositivi mobili, TV, PC e così via), è necessario codificare e creare pacchetti di contenuti audio e video in modo appropriato. Per informazioni su come distribuire ed eseguire lo streaming di questo tipo di contenuti, vedere [Guida introduttiva: Eseguire lo streaming di file video](stream-files-dotnet-quickstart.md).
* Eseguire lo streaming live di eventi sportivi per un vasto pubblico online, ad esempio calcio, baseball, eventi sportivi universitari e liceali e altro ancora. 
* Trasmettere riunioni pubbliche ed eventi, quali riunioni comunali o municipali e di enti legislativi.
* Analizzare contenuti audio o video registrati. Per migliorare la soddisfazione dei clienti, le organizzazioni possono ad esempio eseguire la conversione della voce in testo scritto e creare indici di ricerca e dashboard. Possono quindi estrarre informazioni su reclami comuni, origini dei reclami e altri dati rilevanti. 
* Creare un servizio di sottoscrizione video ed eseguire lo streaming di contenuti protetti con DRM quando un cliente (ad esempio, uno studio cinematografico) deve limitare l'accesso e l'uso di opere proprietarie tutelate da copyright.
* Distribuire contenuti offline per la riproduzione su aerei, treni e automobili. È possibile che un cliente debba scaricare contenuti sullo smartphone o sul tablet per la riproduzione durante la disconnessione dalla rete.
* Aggiungere sottotitoli e didascalie a video per soddisfare le esigenze di più destinatari, ad esempio degli utenti con disabilità uditive o degli utenti che vogliono leggere il testo in una lingua diversa. 
* Implementare una piattaforma video di apprendimento didattico con Servizi multimediali di Azure e [API Servizi cognitivi di Azure](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) per la conversione della voce in testo scritto, la traduzione in più lingue e così via.
* Abilitare la rete CDN di Azure per ottenere notevole scalabilità e gestire al meglio un carico elevato immediato, ad esempio all'inizio di un evento per il lancio di un prodotto. 

## <a name="v3-capabilities"></a>Funzionalità v3

v3 si basa su una superficie API unificata, che espone funzionalità operative e di gestione basate su Azure Resource Manager. 

Questa versione offre le funzionalità seguenti:  

* **Trasformazioni** che consentono di definire flussi di lavoro semplici di elaborazione multimediale o attività analitiche. Una trasformazione è una serie di istruzioni per l'elaborazione di file audio e video. È possibile applicarla più volte per elaborare tutti i file della raccolta contenuto inviando i processi alla trasformazione.
* **Processi** per elaborare (codificare o analizzare) i video. È possibile specificare il contenuto di input in un processo usando URL HTTP(s), URL di firma di accesso condiviso o percorsi ai file che si trovano nell'archivio BLOB di Azure. 
* **Notifiche** che monitorano lo stato o l'avanzamento del processo oppure eventi di errore e di avvio/arresto del canale live. Le notifiche sono integrate con il sistema di notifica Griglia di eventi di Azure. È possibile sottoscrivere facilmente eventi in diverse risorse in Servizi multimediali di Azure. 
* I modelli di **Gestione delle risorse di Azure** possono essere usati per creare e distribuire trasformazioni, endpoint di streaming, canali e altro ancora.
* È possibile impostare il **controllo degli accessi in base al ruolo** a livello di risorsa, consentendo di bloccare l'accesso a specifiche risorse, ad esempio trasformazioni, canali e altro ancora.
* **SDK client** in più linguaggi: .NET, .NET Core, Python, Go, Java e Node.js.

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID). 

I nomi delle risorse di Servizi multimediali non possono includere "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", virgolette singole o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri. 

Per altre informazioni sulla denominazione di Azure Resource Manager, vedere [Requisiti di denominazione](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenzioni di denominazione](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="how-can-i-get-started-with-v3"></a>Come si procede per iniziare a usare v3?

Gli sviluppatori possono usare l'[API REST](https://go.microsoft.com/fwlink/p/?linkid=873030) di Servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e mantenere facilmente flussi di lavoro multimediali personalizzati. Un esempio Postman REST è disponibile [qui](https://github.com/Azure-Samples/media-services-v3-rest-postman). È anche possibile usare l'[API REST basata su Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates).

Microsoft genera e supporta le librerie client seguenti: 

|Libreria client|Esempi|
|---|---|
|[SDK dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Esempi dell'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[Esempi .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[.NET Core SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (scegliere la scheda **.NET CLI**)|[Esempi di .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[Node.js SDK](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Esempi per Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[Python SDK](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

Servizi multimediali offre un [file Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) che è possibile usare per la generazione degli SDK per la tecnologia o il linguaggio preferito.  

## <a name="next-steps"></a>Passaggi successivi

Per scoprire quanto è semplice eseguire la codifica e lo streaming di file video, vedere l'articolo relativo allo [streaming di file](stream-files-dotnet-quickstart.md). 

