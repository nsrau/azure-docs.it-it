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
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303614"
---
# <a name="assets-in-azure-media-services"></a>Asset in servizi multimediali di Azure

In servizi multimediali di Azure, un [Asset](https://docs.microsoft.com/rest/api/media/assets) è un concetto di base. È il punto in cui vengono inseriti i supporti (ad esempio, tramite il caricamento o l'inserimento Live), i supporti di output (dall'output di un processo) e la pubblicazione di supporti da (per il flusso). 

Un asset viene mappato a un contenitore BLOB nell'[account di archiviazione di Azure](storage-account-concept.md) e i file contenuti nell'asset vengono archiviati come BLOB in blocchi in tale contenitore. Gli asset contengono informazioni sui file digitali archiviati in archiviazione di Azure (inclusi video, audio, immagini, raccolte di anteprime, tracce di testo e file di didascalia chiusi).

Servizi multimediali supporta i livelli BLOB quando l'account usa l'archiviazione Utilizzo generico v2 (GPv2). Con GPv2, è possibile spostare i file in uno [spazio di archiviazione ad accesso sporadico o archivio](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Storage **Archive** è adatto per l'archiviazione di file di origine quando non è più necessario, ad esempio dopo la codifica.

Il livello di archiviazione **archivio** è consigliato solo per file di origine di dimensioni molto estese già codificati e con l'output del processo di codifica inserito in un contenitore BLOB di output. I BLOB nel contenitore di output che si vuole associare a un asset e usare per eseguire lo streaming o l'analisi del contenuto devono esistere **in un livello di archiviazione** **ad** accesso frequente o sporadico.

## <a name="naming"></a>Denominazione 

### <a name="assets"></a>Asset

I nomi degli asset devono essere univoci. I nomi di risorsa di servizi multimediali V3, ad esempio asset, processi, trasformazioni, sono soggetti a Azure Resource Manager vincoli di denominazione. Per altre informazioni, vedere [convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>BLOB

I nomi di file/BLOB all'interno di un asset devono rispettare i requisiti del [nome del BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e i [requisiti del nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Il motivo di questi requisiti è che i file possono essere copiati dall'archiviazione BLOB a un disco NTFS locale per l'elaborazione.

## <a name="map-v3-asset-properties-to-v2"></a>Mappare le proprietà Asset V3 alla versione V2

La tabella seguente illustra in che modo le proprietà dell' [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)in V3 sono mappate alle proprietà dell'asset nella versione V2.

|Proprietà V3|V2 (proprietà)|
|---|---|
|`id`-(univoco) il percorso completo Azure Resource Manager, vedere esempi nell' [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`-(univoco) vedere [convenzioni di denominazione](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|il valore `Id`-(Unique) inizia con il prefisso di `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opzioni di creazione)|
|`type`||

## <a name="storage-side-encryption"></a>Crittografia lato archiviazione

Per proteggere gli asset inattivi, è necessario crittografarli tramite crittografia lato archiviazione. La tabella seguente illustra il funzionamento della crittografia lato archiviazione in Servizi multimediali:

|Opzione di crittografia|Descrizione|Servizi multimediali v2|Servizi multimediali v3|
|---|---|---|---|
|Crittografia di archiviazione di Servizi multimediali|Crittografia AES-256, chiave gestita da servizi multimediali.|Supportata<sup>(1)</sup>|Non supportata<sup>(2)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Crittografia lato server offerta da archiviazione di Azure, chiave gestita da Azure o dal cliente.|Supportato|Supportato|
|[Crittografia lato client di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Crittografia lato client offerta da archiviazione di Azure, la chiave gestita dal cliente in Key Vault.|Non supportate|Non supportate|

<sup>1</sup> anche se servizi multimediali supporta la gestione del contenuto in chiaro o senza alcuna forma di crittografia, questa operazione non è consigliata.

<sup>2</sup> In Servizi multimediali versione 3, la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. Il significato di V3 funziona con asset crittografati di archiviazione esistenti ma non consente la creazione di nuove risorse.

## <a name="next-steps"></a>Passaggi successivi

[Gestire gli asset in servizi multimediali](manage-asset-concept.md)

## <a name="see-also"></a>Vedere anche

[Differenze tra Servizi multimediali v2 e v3](migrate-from-v2-to-v3.md)
