---
title: Streaming a disponibilità elevata di Servizi multimediali di AzureAzure Media Services high availability streaming
description: Informazioni su come eseguire il failover a un account di Servizi multimediali secondario se si verifica un errore o un errore del data center regionale.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899230"
---
# <a name="media-services-high-availability-streaming"></a>Streaming ad alta disponibilità di Servizi multimediali

Servizi multimediali di Azure attualmente non fornisce il failover immediato del servizio se si verifica un'interruzione del data center regionale o un errore del componente sottostante o dei servizi dipendenti. Questo articolo fornisce indicazioni su come creare lo streaming tra aree video su richiesta.

## <a name="prerequisites"></a>Prerequisiti

Esaminare Come creare un sistema di [codifica interregionaleReview How to build a cross-regional encoding system](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Come creare lo streaming tra aree video su richiesta 

* Lo streaming tra aree video su richiesta comporta la duplicazione di [asset,](assets-concept.md) [criteri chiave contenuto](content-key-policy-concept.md) (se utilizzati), criteri di [streaming](streaming-policy-concept.md)e [localizzatori di flusso](streaming-locators-concept.md). 
* Sarà necessario creare i criteri in entrambe le aree e mantenerli aggiornati. 
* Quando si creano i localizzatori di streaming, è consigliabile utilizzare lo stesso valore ID locator, il valore ContentKey ID e il valore ContentKey.  
* Se si codifica il contenuto, si consiglia di codificare il contenuto nell'area A e pubblicarlo, quindi di copiare il contenuto codificato nell'area B e pubblicarlo utilizzando gli stessi valori dell'area A.
* È possibile usare Gestione traffico nei nomi host per l'origine e il servizio di distribuzione delle chiavi (nella configurazione di Servizi multimediali l'URL del server delle chiavi personalizzato sarà simile a quello di un server di chiavi personalizzato).

## <a name="next-steps"></a>Passaggi successivi

Controlla:

* [Esercitazione: Codificare un file remoto in base all'URL e trasmettere il video in streaming](stream-files-dotnet-quickstart.md)
* [esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
