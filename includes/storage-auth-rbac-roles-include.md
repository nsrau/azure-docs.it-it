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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179821"
---
Azure offre i seguenti ruoli RBAC predefiniti per autorizzare l'accesso ai dati blob e coda usando Azure AD e OAuth:

- [Proprietario dei dati Blob di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Consente di impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake Storage Gen2 (anteprima). Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore ai dati Blob di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle risorse di archiviazione BLOB.
- [Lettore di dati Blob di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Consente di concedere le autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Collaboratore ai dati di archiviazione coda](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle code di Azure.
- [Lettore di dati di archiviazione coda](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Consente di concedere le autorizzazioni di sola lettura alle code di Azure.
- [Dati della coda di archiviazione del messaggio processore](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Usare grant peek, recuperare e le autorizzazioni di eliminazione per i messaggi nelle code di archiviazione di Azure.
- [Mittente del messaggio dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Consente di concedere aggiungere autorizzazioni per i messaggi nelle code di archiviazione di Azure.

> [!NOTE]
> Tenere presente che le assegnazioni di ruolo RBAC possono richiedere fino a cinque minuti per propagare.
