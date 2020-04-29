---
title: Asset
titleSuffix: Azure Media Services
description: Informazioni sulle risorse e sul modo in cui vengono usate da servizi multimediali di Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087918"
---
# <a name="assets-in-azure-media-services-v3"></a>Asset in servizi multimediali di Azure V3

In servizi multimediali di Azure, un [Asset](https://docs.microsoft.com/rest/api/media/assets) è un concetto di base. È il punto in cui vengono inseriti i supporti (ad esempio, tramite il caricamento o l'inserimento Live), i supporti di output (dall'output di un processo) e la pubblicazione di supporti da (per il flusso). 

Un asset viene mappato a un contenitore BLOB nell' [account di archiviazione di Azure](storage-account-concept.md) e i file nell'asset vengono archiviati come BLOB in blocchi in tale contenitore. Gli asset contengono informazioni sui file digitali archiviati in archiviazione di Azure (inclusi video, audio, immagini, raccolte di anteprime, tracce di testo e file di didascalia chiusi).

Servizi multimediali supporta i livelli BLOB quando l'account usa l'archiviazione Utilizzo generico v2 (GPv2). Con GPv2, è possibile spostare i file in uno [spazio di archiviazione ad accesso sporadico o archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Storage **Archive** è adatto per l'archiviazione di file di origine quando non è più necessario, ad esempio dopo la codifica.

Il livello di archiviazione **archivio** è consigliato solo per file di origine di dimensioni molto estese già codificati e con l'output del processo di codifica inserito in un contenitore BLOB di output. I BLOB nel contenitore di output che si vuole associare a un asset e usare per eseguire lo streaming o l'analisi del contenuto devono esistere **in un livello di archiviazione** **ad** accesso frequente o sporadico.

## <a name="naming"></a>Denominazione 

### <a name="assets"></a>Asset

I nomi degli asset devono essere univoci. I nomi di risorsa di servizi multimediali V3, ad esempio asset, processi, trasformazioni, sono soggetti a Azure Resource Manager vincoli di denominazione. Per altre informazioni, vedere [convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>BLOB

I nomi di file/BLOB all'interno di un asset devono rispettare i requisiti del [nome del BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e i [requisiti del nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Il motivo di questi requisiti è che i file possono essere copiati dall'archiviazione BLOB a un disco NTFS locale per l'elaborazione.

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli asset in servizi multimediali](manage-asset-concept.md)

## <a name="see-also"></a>Vedere anche

[Differenze tra Servizi multimediali v2 e v3](migrate-from-v2-to-v3.md)
