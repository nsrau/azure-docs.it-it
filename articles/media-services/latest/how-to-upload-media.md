---
title: Caricare file multimediali
titleSuffix: Azure Media Services
description: Informazioni su come caricare supporti per lo streaming o la codifica.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0bdb2c36bc895c9229e4c04e9e0d76aa852bd139
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297307"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Caricare supporti per lo streaming o la codifica

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Servizi multimediali è possibile caricare i file digitali in un contenitore BLOB associato a un asset. L'entità [Asset](/rest/api/media/operations/asset) può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati, oltre ai metadati relativi a questi file. Dopo il caricamento dei file nel contenitore dell'asset, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

Prima di iniziare, è necessario raccogliere o considerare alcuni valori.

1. Il percorso del file locale da caricare
1. ID asset (contenitore)
1. Nome da usare per il file caricato compresa l'estensione
1. Nome dell'account di archiviazione usato
1. Chiave di accesso per l'account di archiviazione

## <a name="portal"></a>[Portale](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Dopo aver [creato un asset usando Postman o un altro metodo REST e aver ottenuto l'URL di firma di accesso condiviso per l'asset](how-to-create-asset.md?tabs=rest), usare le API di Archiviazione di Azure o gli SDK (ad esempio, l'[API REST di Archiviazione](../../storage/common/storage-rest-api-auth.md) o [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Per altri metodi, vedere la [documentazione di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/) per l'uso di BLOB in [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python) e [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Passaggi successivi

> [Panoramica di Servizi multimediali](media-services-overview.md)
