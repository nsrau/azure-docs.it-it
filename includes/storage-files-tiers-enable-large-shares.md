---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011730"
---
Per impostazione predefinita, le condivisioni file standard possono estendersi solo fino a 5 TiB, anche se il limite di condivisione può essere aumentato a 100 TiB. A questo scopo, è necessario abilitare la funzionalità *condivisione file di grandi dimensioni* a livello di account di archiviazione. Gli account di archiviazione Premium (account di archiviazione *FileStorage*) non hanno il flag di funzionalità della condivisione file di grandi dimensioni, perché tutte le condivisioni file Premium sono già abilitate per il provisioning fino alla capacità massima di 100 TiB.

È possibile abilitare le condivisioni file di grandi dimensioni solo in account di archiviazione Standard con ridondanza locale o ridondanza della zona. Dopo aver abilitato il flag di funzionalità della condivisione file di grandi dimensioni, non è possibile modificare il livello di ridondanza per l'archiviazione con ridondanza geografica o con ridondanza geografica della zona.

Per abilitare le condivisioni file di grandi dimensioni in un account di archiviazione esistente, passare alla visualizzazione **Configurazione** nel sommario dell'account di archiviazione e impostare l'opzione per la condivisione file di grandi dimensioni su abilitata:

![Screenshot dell'opzione selezionata per l'abilitazione della condivisione file di grandi dimensioni nel portale di Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

È anche possibile abilitare condivisioni file da 100 TiB tramite il cmdlet [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) di PowerShell e il comando [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) dell'interfaccia della riga di comando di Azure. Per istruzioni dettagliate sull'abilitazione di condivisioni file di grandi dimensioni, vedere [Abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Per altre informazioni su come creare condivisioni file in nuovi account di archiviazione, vedere [Creazione di una condivisione file di Azure](../articles/storage/files/storage-how-to-create-file-share.md).