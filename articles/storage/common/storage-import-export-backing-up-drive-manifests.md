---
title: Backup dei manifesti delle unità di Importazione/Esportazione | Documentazione Microsoft
description: Informazioni su come impostare il backup automatico dei manifesti delle unità per il servizio Importazione/Esportazione di Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 933c0121a4f718ff812fc921bd6e04983fc69931
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520538"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Backup dei manifesti delle unità per i processi di Importazione/Esportazione di Azure

È possibile eseguire il backup automatico dei manifesti delle unità in BLOB impostando la proprietà `BackupDriveManifest` su `true` nelle operazioni dell'API REST [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). Per impostazione predefinita non viene eseguito il backup dei manifesti delle unità. I relativi backup vengono archiviati come BLOB in blocchi in un contenitore nell'account di archiviazione associato al processo. Per impostazione predefinita, il nome del contenitore è `waimportexport`, ma è possibile specificare un nome diverso nella proprietà `DiagnosticsPath` quando si chiamano le operazioni `Put Job` o `Update Job Properties`. Il BLOB dei manifesti di backup vengono denominati con il seguente formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 È possibile recuperare l'URI dei manifesti delle unità di backup per un processo chiamando l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). L'URI del BLOB viene restituito nella proprietà `ManifestUri` per ciascuna unità.

## <a name="next-steps"></a>Passaggi successivi

* [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)
