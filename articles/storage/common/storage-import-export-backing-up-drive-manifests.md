---
title: Backup dei manifesti delle unità di Importazione/Esportazione | Documentazione Microsoft
description: Informazioni su come impostare il backup automatico dei manifesti delle unità per il servizio Importazione/Esportazione di Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483129"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Backup dei manifesti delle unità per i processi di Importazione/Esportazione di Azure

È possibile eseguire il backup automatico dei manifesti delle unità in BLOB impostando la proprietà `BackupDriveManifest` su `true` nelle operazioni dell'API REST [Put Job](/rest/api/storageimportexport/jobs) o [Update Job Properties](/rest/api/storageimportexport/jobs). Per impostazione predefinita non viene eseguito il backup dei manifesti delle unità. I relativi backup vengono archiviati come BLOB in blocchi in un contenitore nell'account di archiviazione associato al processo. Per impostazione predefinita, il nome del contenitore è `waimportexport`, ma è possibile specificare un nome diverso nella proprietà `DiagnosticsPath` quando si chiamano le operazioni `Put Job` o `Update Job Properties`. Il BLOB dei manifesti di backup vengono denominati con il seguente formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 È possibile recuperare l'URI dei manifesti delle unità di backup per un processo chiamando l'operazione [Get Job](/rest/api/storageimportexport/jobs). L'URI del BLOB viene restituito nella proprietà `ManifestUri` per ciascuna unità.

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)
