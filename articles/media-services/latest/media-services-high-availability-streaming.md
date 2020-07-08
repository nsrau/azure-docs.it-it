---
title: Streaming a disponibilità elevata di servizi multimediali di Azure
description: Informazioni su come eseguire il failover a un account di servizi multimediali secondari se si verifica un'interruzione del Data Center a livello di area o un errore.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78899230"
---
# <a name="media-services-high-availability-streaming"></a>Streaming a disponibilità elevata di servizi multimediali

Servizi multimediali di Azure non fornisce attualmente il failover immediato del servizio se si verifica un'interruzione del Data Center a livello di area o un errore del componente sottostante o dei servizi dipendenti. Questo articolo fornisce indicazioni su come creare flussi di video su richiesta tra più aree.

## <a name="prerequisites"></a>Prerequisiti

Vedere [come creare un sistema di codifica tra](media-services-high-availability-encoding.md) più aree

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Come creare flussi di video su richiesta tra aree 

* Il flusso tra aree video on demand comporta la duplicazione di [Asset](assets-concept.md), [Criteri chiave](content-key-policy-concept.md) simmetrica (se usati), [criteri di streaming](streaming-policy-concept.md)e [localizzatori di streaming](streaming-locators-concept.md). 
* Sarà necessario creare i criteri in entrambe le aree e mantenerli aggiornati. 
* Quando si creano i localizzatori di streaming, è necessario usare lo stesso valore di ID localizzatore, il valore ID ContentKey e il valore ContentKey.  
* Se si sta codificando il contenuto, è consigliabile codificare il contenuto nell'area A e pubblicarlo, quindi copiare il contenuto codificato nell'area B e pubblicarlo usando gli stessi valori dell'area A.
* È possibile usare gestione traffico nei nomi host per l'origine e il servizio di distribuzione delle chiavi (nella configurazione di servizi multimediali questo aspetto sarà simile a un URL del server di chiavi personalizzato).

## <a name="next-steps"></a>Passaggi successivi

Estrai:

* [Esercitazione: codificare un file remoto in base all'URL e trasmettere il video](stream-files-dotnet-quickstart.md)
* [esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
