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
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536489"
---
Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, anche se il limite di azioni può essere aumentato a 100 TiB. A tale scopo, è necessario abilitare la funzionalità di *condivisione file* di grandi dimensioni a livello di account di archiviazione. Gli account di archiviazione Premium (account di archiviazione*FileStorage)* non hanno il flag della funzionalità di condivisione file di grandi dimensioni poiché tutte le condivisioni file premium sono già abilitate per il provisioning fino alla capacità TiB completa di 100.

È possibile abilitare condivisioni file di grandi dimensioni solo in account di archiviazione standard ridondanti localmente o di zona. Dopo aver abilitato il flag di funzionalità di condivisione file di grandi dimensioni, non è possibile modificare il livello di ridondanza in archiviazione con ridondanza geografica o zona geografica.

Per abilitare condivisioni file di grandi dimensioni in un account di archiviazione esistente, passare alla visualizzazione Configurazione nel sommario dell'account di archiviazione e passare all'opzione rocker di condivisione file di grandi dimensioni su abilitato:To enable large file shares on an existing storage account, navigate to the **Configuration** view in the storage account's table of contents, and switch the large share rocker switch to enabled:

![Screenshot dell'opzione rocker di abilitazione di condivisione file di grandi dimensioni nel portale di Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

È anche possibile abilitare 100 [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) condivisioni file [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) TiB tramite il cmdlet PowerShell e il comando dell'interfaccia della riga di comando di Azure.You can also enable 100 TiB file shares through the PowerShell cmdlet and the Azure CLI command. Per istruzioni dettagliate sull'abilitazione di condivisioni file di grandi dimensioni, vedere [abilitare e creare condivisioni file](../articles/storage/files/storage-files-how-to-create-large-file-share.md)di grandi dimensioni.

Per altre informazioni su come creare condivisioni file in nuovi account di archiviazione, vedere Creazione di una [condivisione file](../articles/storage/files/storage-how-to-create-file-share.md)di Azure.To learn more about how to create file shares on new storage accounts, see creating an Azure file share .
