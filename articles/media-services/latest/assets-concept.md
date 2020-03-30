---
title: Asset
titleSuffix: Azure Media Services
description: Informazioni sulle risorse e su come vengono usate da Servizi multimediali di Azure.Learn about what assets are and how're used by Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087918"
---
# <a name="assets-in-azure-media-services-v3"></a>Asset in Servizi multimediali di Azure v3Assets in Azure Media Services v3

In Servizi multimediali di Azure, un [asset](https://docs.microsoft.com/rest/api/media/assets) è un concetto di base. È la posizione in cui inserisci i file multimediali (ad esempio, tramite upload o inserimento dal vivo), i contenuti multimediali di output (da un output del processo) e pubblichi contenuti multimediali da (per lo streaming). 

Un asset viene mappato a un contenitore BLOB [nell'account di archiviazione di Azure](storage-account-concept.md) e i file nell'asset vengono archiviati come BLOB di blocchi in tale contenitore. Le risorse contengono informazioni sui file digitali archiviati in Archiviazione di Azure (inclusi video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati).

Servizi multimediali supporta i livelli BLOB quando l'account usa l'archiviazione Utilizzo generico v2 (GPv2). Con GPv2, è possibile spostare i file in uno [spazio di archiviazione ad accesso sporadico o archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **L'archiviazione** degli archivi è adatta per l'archiviazione dei file di origine quando non è più necessaria (ad esempio, dopo che sono stati codificati).

Il livello di archiviazione **archivio** è consigliato solo per file di origine di dimensioni molto estese già codificati e con l'output del processo di codifica inserito in un contenitore BLOB di output. I BLOB nel contenitore di output che si vuole associare a un Asset e che si usano per lo streaming o l'analisi del contenuto devono esistere in un livello di archiviazione **a caldo** o **freddo.**

## <a name="naming"></a>Denominazione 

### <a name="assets"></a>Asset

I nomi delle risorse devono essere univoci. I nomi delle risorse di Servizi multimediali v3 (ad esempio, Asset, Processi, Trasformazioni) sono soggetti avincoli di Azure Resource Manager.Media Services v3 resource names (for example, Assets, Jobs, Transforms) are subject to Azure Resource Manager naming constraints. Per ulteriori informazioni, vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>BLOB

I nomi dei file/BLOB all'interno di un asset devono rispettare sia [i requisiti](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) del nome del BLOB che i requisiti dei nomi [NTFS.](https://docs.microsoft.com/windows/win32/fileio/naming-a-file) Il motivo di questi requisiti è che i file possono essere copiati dall'archiviazione BLOB in un disco NTFS locale per l'elaborazione.

## <a name="next-steps"></a>Passaggi successivi

[Gestire le risorse in Servizi multimediali](manage-asset-concept.md)

## <a name="see-also"></a>Vedere anche

[Differenze tra Servizi multimediali v2 e v3](migrate-from-v2-to-v3.md)
