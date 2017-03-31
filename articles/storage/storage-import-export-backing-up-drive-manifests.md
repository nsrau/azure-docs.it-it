---
title: "Backup dei manifesti delle unità di Importazione/Esportazione | Documentazione Microsoft"
description: "Informazioni su come impostare il backup automatico dei manifesti delle unità per il servizio Importazione/Esportazione di Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: c215112cc73f1378ba3ec2c7099b102aaf56db63
ms.openlocfilehash: 43c31b965f9f1471bce9fe4b9ac32a392b6ecbb1
ms.lasthandoff: 02/16/2017


---

# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Backup dei manifesti delle unità per i processi di Importazione/Esportazione di Azure
È possibile eseguire il backup automatico dei manifesti delle unità in BLOB impostando la proprietà `BackupDriveManifest` su `true` nelle operazioni [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update). Per impostazione predefinita non viene eseguito il backup dei manifesti delle unità. I relativi backup vengono archiviati come BLOB in blocchi in un contenitore nell'account di archiviazione associato al processo. Per impostazione predefinita, il nome del contenitore è `waimportexport`, ma è possibile specificare un nome diverso nella proprietà `DiagnosticsPath` quando si chiamano le operazioni `Put Job` o `Update Job Properties`. Il BLOB dei manifesti di backup vengono denominati con il seguente formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 È possibile recuperare l'URI dei manifesti delle unità di backup per un processo chiamando l'operazione [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). L'URI del BLOB viene restituito nella proprietà `ManifestUri` per ciascuna unità.

## <a name="see-also"></a>Vedere anche
 [Uso dell'API REST del servizio Importazione/Esportazione](storage-import-export-using-the-rest-api.md)

