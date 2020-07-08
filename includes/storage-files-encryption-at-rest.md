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
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597823"
---
Tutti i dati archiviati in File di Azure vengono crittografati a riposo usando la crittografia del servizio di archiviazione di Azure (SSE). La crittografia del servizio di archiviazione funziona in modo analogo a BitLocker per Windows: i dati vengono crittografati sotto il livello file system. Poiché i dati vengono crittografati sotto la file system della condivisione file di Azure, perché sono codificati su disco, non è necessario avere accesso alla chiave sottostante sul client per leggere o scrivere nella condivisione file di Azure.

Per impostazione predefinita, i dati archiviati in File di Azure vengono crittografati con le chiavi gestite da Microsoft. Con le chiavi gestite da Microsoft, Microsoft possiede le chiavi per crittografare/decrittografare i dati ed è responsabile della rotazione a intervalli regolari. È anche possibile scegliere di gestire le proprie chiavi, il che consente di controllare il processo di rotazione. Se si sceglie di crittografare le condivisioni file con chiavi gestite dal cliente, File di Azure è autorizzato ad accedere alle chiavi per soddisfare le richieste di lettura e scrittura dai client. Con le chiavi gestite dal cliente è possibile revocare l'autorizzazione in qualsiasi momento, ma ciò significa che la condivisione file di Azure non sarà più accessibile tramite SMB o l'API filerest.

File di Azure usa lo stesso schema di crittografia degli altri servizi di archiviazione di Azure, ad esempio archiviazione BLOB di Azure. Per altre informazioni su crittografia del servizio di archiviazione di Azure (SSE), vedere [crittografia di archiviazione di Azure per dati](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)inattivi.