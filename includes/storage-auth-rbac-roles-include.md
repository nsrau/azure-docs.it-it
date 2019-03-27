---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4c66f35537cff87eb53a6cfe9bc0389f038f1a10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449765"
---
Azure offre i seguenti ruoli RBAC predefiniti per l'accesso ai dati di archiviazione:

- [Proprietario dei dati Blob di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Consente di impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake Storage Gen2 (anteprima). Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore ai dati Blob di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle risorse di archiviazione BLOB.
- [Lettore di dati Blob di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Consente di concedere le autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Collaboratore ai dati di archiviazione coda](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle code di Azure.
- [Lettore di dati di archiviazione coda](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Consente di concedere le autorizzazioni di sola lettura alle code di Azure.
- [Dati della coda di archiviazione del messaggio processore](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Usare grant peek, recuperare e le autorizzazioni di eliminazione per i messaggi nelle code di archiviazione di Azure.
- [Mittente del messaggio dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Consente di concedere aggiungere autorizzazioni per i messaggi nelle code di archiviazione di Azure.

> [!IMPORTANT]
> Le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per propagare.

Per altre informazioni sul modo in cui vengono definiti i ruoli predefiniti per Archiviazione di Azure, vedere [Informazioni sulle definizioni di ruolo](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Per informazioni sulla creazione di ruoli personalizzati, vedere [creare ruoli personalizzati per il controllo di accesso](../articles/role-based-access-control/custom-roles.md). 
