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
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90563446"
---
Tutti i dati archiviati in File di Azure vengono crittografati quando sono inattivi usando la crittografia del servizio di archiviazione di Azure. La crittografia del servizio di archiviazione funziona in modo analogo a BitLocker in Windows in quanto i dati vengono crittografati sotto il livello del file system. Dal momento che i dati vengono crittografati sotto il file system della condivisione file di Azure, perché sono codificati su disco, non è necessario avere accesso alla chiave sottostante nel client per leggere o scrivere nella condivisione file di Azure. La crittografia dati inattivi si applica a entrambi i protocolli SMB e NFS.

Per impostazione predefinita, i dati archiviati in File di Azure vengono crittografati con chiavi gestite da Microsoft. Con le chiavi gestite da Microsoft, Microsoft detiene le chiavi per crittografare/decrittografare i dati ed è responsabile della loro rotazione a intervalli regolari. È anche possibile scegliere di gestire le proprie chiavi, in modo da controllare manualmente il processo di rotazione. Se si sceglie di crittografare le condivisioni file con chiavi gestite dal cliente, File di Azure è autorizzato ad accedere alle chiavi per soddisfare le richieste di lettura e scrittura ricevute dai client. Con le chiavi gestite dal cliente è possibile revocare questa autorizzazione in qualsiasi momento, ma questo significa che la condivisione file di Azure non sarà più accessibile tramite SMB o l'API REST per file.

File di Azure usa lo stesso schema di crittografia di altri servizi di archiviazione di Azure, ad esempio Archiviazione BLOB di Azure. Per altre informazioni sulla crittografia del servizio di archiviazione di Azure, vedere [Crittografia del servizio di archiviazione di Azure per i dati inattivi](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).