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
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109249"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Esempi di interfaccia della riga di comando di Azure per Servizi multimediali di Azure

La tabella seguente include collegamenti a esempi dell'interfaccia della riga di comando di Azure per Servizi multimediali di Azure.

## <a name="examples"></a>Esempi

|  |  |
|---|---|
|**Ridimensionare**||
| [Ridimensionare le Media Reserved Units (S1/S2/S3)](media-reserved-units-cli-how-to.md)|Per i processi di analisi audio e video generati da Servizi multimediali v3 o Video Indexer, è fortemente consigliato effettuare il provisioning dell'account con 10 MRU S3. <br/>Lo script mostra come usare l'interfaccia della riga di comando per ridimensionare le Media Reserved Units (S1/S2/S3).|
|**Account**||
| [Creare un account di Servizi multimediali di Azure.](./scripts/cli-create-account.md) | Crea un account di Servizi multimediali di Azure. Crea anche un'entità servizio che può essere usata per accedere alle API e gestire l'account a livello di codice. |
| [Reimpostare le credenziali dell'account](./scripts/cli-reset-account-credentials.md)|Reimposta le credenziali dell'account e ripristina le impostazioni di app.config.|
|**Asset**||
| [Creare gli asset](./scripts/cli-create-asset.md)|Crea un asset di Servizi multimediali dove caricare il contenuto.|
| [Caricare un file](./scripts/cli-upload-file-asset.md)|Carica un file locale in un contenitore di archiviazione.|
| **Trasformazioni** e **processi**||
| [Creare trasformazioni](./scripts/cli-create-transform.md)|Mostra come creare trasformazioni. Le trasformazioni descrivono un semplice flusso di lavoro di attività per l'elaborazione dei file video o audio, detto spesso "recipe".<br/> È necessario verificare sempre se esiste già una trasformazione con il nome e il "recipe" desiderati. Se esiste, riutilizzarla. |
| [Creare processi](./scripts/cli-create-jobs.md)|Invia un processo a una semplice trasformazione di codifica usando URL HTTPs.|
| [Creare una Griglia di eventi](./scripts/cli-create-event-grid.md)|Crea una sottoscrizione di Griglia di eventi a livello di account per le modifiche dello stato del processo.|
| **Distribuire**||
| [Pubblicare un asset](./scripts/cli-publish-asset.md)| Crea un localizzatore di streaming e recupera gli URL di streaming. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Configura un filtro per un asset Video on-demand e mostra come usare l'interfaccia della riga di comando per creare [Filtri account](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Filtri asset](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Vedere anche 

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
