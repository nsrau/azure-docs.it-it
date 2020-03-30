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
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597823"
---
Tutti i dati archiviati in File di Azure vengono crittografati inattivi usando la crittografia del servizio di archiviazione di Azure.All data stored in Azure Files is encrypted at rested at Azure storage service encryption (SSE). La crittografia del servizio di archiviazione funziona in modo simile a BitLocker in Windows: i dati vengono crittografati al di sotto del livello del file system. Poiché i dati vengono crittografati sotto il file system della condivisione file di Azure, come codificato su disco, non è necessario avere accesso alla chiave sottostante nel client per leggere o scrivere nella condivisione file di Azure.Because data is encrypted sotto the Azure file share's file system, as's encoded to disk, you don't have to have access to the underlying key on the client to read or write to the Azure file share.

Per impostazione predefinita, i dati archiviati in File di Azure vengono crittografati con chiavi gestite da Microsoft.By default, data stored in Azure Files is encrypted with Microsoft-managed keys. Con le chiavi gestite da Microsoft, Microsoft detiene le chiavi per crittografare/decrittografare i dati ed è responsabile della rotazione regolare. È inoltre possibile scegliere di gestire le proprie chiavi, che consente di controllare il processo di rotazione. Se si sceglie di crittografare le condivisioni file con chiavi gestite dal cliente, File di Azure è autorizzato ad accedere alle chiavi per soddisfare le richieste di lettura e scrittura dai client. Con le chiavi gestite dal cliente, è possibile revocare questa autorizzazione in qualsiasi momento, ma ciò significa che la condivisione file di Azure non sarà più accessibile tramite SMB o l'API FileREST.With customer-managed keys, you can revoke this authorization at any time, but this means that that your Azure file share will no longer be accessible via SMB or the FileREST API.

File di Azure usa lo stesso schema di crittografia degli altri servizi di archiviazione di Azure, ad esempio Archiviazione BLOB di Azure.Azure Files uses the same schema of the other Azure storage services such as Azure Blob storage. Per altre informazioni sulla crittografia del servizio di archiviazione di Azure, vedere Crittografia di archiviazione di [Azure per i dati inattivi.](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)