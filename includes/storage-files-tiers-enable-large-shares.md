---
title: includere il file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81536489"
---
Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, sebbene il limite di condivisione possa essere aumentato a 100 TiB. A tale scopo, è necessario abilitare la funzionalità di *condivisione file di grandi dimensioni* a livello di account di archiviazione. Gli account di archiviazione Premium (account di archiviazione*filestorage* ) non hanno il flag di funzionalità di condivisione file di grandi dimensioni perché tutte le condivisioni file Premium sono già abilitate per il provisioning fino alla capacità massima di 100 tib.

È possibile abilitare solo condivisioni file di grandi dimensioni in account di archiviazione standard con ridondanza locale o con ridondanza della zona. Dopo aver abilitato il flag di funzionalità di condivisione file di grandi dimensioni, non è possibile modificare il livello di ridondanza per l'archiviazione con ridondanza geografica o con ridondanza geografica.

Per abilitare le condivisioni file di grandi dimensioni in un account di archiviazione esistente, passare alla visualizzazione **configurazione** nel sommario dell'account di archiviazione e impostare l'opzione per la condivisione file di grandi dimensioni su abilitato:

![Screenshot dell'opzione di abilitazione del rocker per la condivisione file di grandi dimensioni nella portale di Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

È anche possibile abilitare le condivisioni file 100 TiB tramite il [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) cmdlet di PowerShell e l' [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) interfaccia della riga di comando di Azure. Per istruzioni dettagliate sull'abilitazione di condivisioni di file di grandi dimensioni, vedere [abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Per altre informazioni su come creare condivisioni file nei nuovi account di archiviazione, vedere [creazione di una condivisione file di Azure](../articles/storage/files/storage-how-to-create-file-share.md).
