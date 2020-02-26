---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597820"
---
Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, sebbene il limite di condivisione possa essere aumentato a 100 TiB. A tale scopo, è necessario abilitare la funzionalità di *condivisione file di grandi dimensioni* a livello di account di archiviazione. Gli account di archiviazione Premium (account di archiviazione*filestorage* ) non hanno il flag di funzionalità di condivisione file di grandi dimensioni perché tutte le condivisioni file Premium sono già abilitate per il provisioning fino alla capacità massima di 100 tib.

È possibile abilitare solo condivisioni file di grandi dimensioni in account di archiviazione standard con ridondanza locale o con ridondanza della zona. Dopo aver abilitato il flag di funzionalità di condivisione file di grandi dimensioni, non è possibile modificare il livello di ridondanza per l'archiviazione con ridondanza geografica o con ridondanza geografica.

Per abilitare le condivisioni file di grandi dimensioni in un account di archiviazione esistente, passare alla visualizzazione **configurazione** nel sommario dell'account di archiviazione e impostare l'opzione per la condivisione file di grandi dimensioni su abilitato:

![Screenshot dell'opzione di abilitazione del rocker per la condivisione file di grandi dimensioni nella portale di Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

È anche possibile abilitare le condivisioni file 100 TiB tramite il cmdlet [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell e il comando dell'interfaccia della riga di comando di [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure.

Per altre informazioni su come abilitare le condivisioni file di grandi dimensioni nei nuovi account di archiviazione, vedere [creazione di una condivisione file di Azure](../articles/storage/files/storage-how-to-create-file-share.md).