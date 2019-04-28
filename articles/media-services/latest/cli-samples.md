---
title: Esempi di interfaccia della riga di comando di Azure - Servizi multimediali di Azure | Microsoft Docs
description: Esempi di interfaccia della riga di comando di Azure per Servizi multimediali di Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733182"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Esempi di interfaccia della riga di comando di Azure per Servizi multimediali di Azure

La tabella seguente include collegamenti a esempi dell'interfaccia della riga di comando di Azure per Servizi multimediali di Azure.

## <a name="examples"></a>Esempi

|  |  |
|---|---|
|**Ridimensionare**||
| [Ridimensionare le Media Reserved Units (S1/S2/S3)](media-reserved-units-cli-how-to.md)|Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. <br/>Lo script mostra come usare l'interfaccia della riga di comando per ridimensionare le Media Reserved Units (S1/S2/S3).|
|**Account**||
| [Creare un account di Servizi multimediali di Azure.](create-account-cli-how-to.md) | Lo script crea un account servizi multimediali di Azure. |
| [Reimpostare le credenziali dell'account](./scripts/cli-reset-account-credentials.md)|Reimposta le credenziali dell'account e ripristina le impostazioni di app.config.|
|**Asset**||
| [Creare gli asset](./scripts/cli-create-asset.md)|Crea un asset di Servizi multimediali dove caricare il contenuto.|
| [Caricare un file](./scripts/cli-upload-file-asset.md)|Carica un file locale in un contenitore di archiviazione.|
| **Trasformazioni** e **processi**||
| [Creare trasformazioni](./scripts/cli-create-transform.md)|Mostra come creare trasformazioni. Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, detto spesso "recipe".<br/> È consigliabile controllare sempre se una trasformazione con un nome desiderato e "ricetta" esiste già. Se esiste, riutilizzarla. |
| [Codificare contenuti con una trasformazione personalizzata](custom-preset-cli-howto.md) | Viene illustrato come creare un set di impostazioni per i requisiti specifici di uno scenario o un dispositivo di destinazione personalizzato.|
| [Creare processi](./scripts/cli-create-jobs.md)|Invia un processo a una semplice trasformazione di codifica usando URL HTTPs.|
| [Creare una Griglia di eventi](./scripts/cli-create-event-grid.md)|Crea una sottoscrizione di Griglia di eventi a livello di account per le modifiche dello stato del processo.|
| **Distribuire**||
| [Pubblicare un asset](./scripts/cli-publish-asset.md)| Crea un localizzatore di streaming e recupera gli URL di streaming. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Configura un filtro per un asset Video on-demand e mostra come usare l'interfaccia della riga di comando per creare [Filtri account](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Filtri asset](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Vedere anche 

- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Guida introduttiva: Eseguire lo streaming di file video - Interfaccia della riga di comando](stream-files-cli-quickstart.md)
