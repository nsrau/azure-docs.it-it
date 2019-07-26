---
title: Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure
description: Informazioni sui diversi modi di archiviare e accedere ai dati in Azure per aiutare a decidere quale tecnologia usare.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 702627a6307e1a6644dc41aeee947d33eb76522d
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501348"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure

Microsoft Azure offre molte funzionalità in Archiviazione di Azure per archiviare e accedere ai dati nel cloud. Questo articolo illustra le funzionalità File di Azure, BLOB di Azure e Dischi di Azure e aiuta a scegliere quella più adatta alle proprie esigenze.

## <a name="scenarios"></a>Scenari

La tabella seguente confronta File, BLOB e Dischi e illustra scenari di esempio appropriati per ognuna delle funzionalità.

| Funzionalità | Descrizione | Quando usare le autorizzazioni |
|--------------|-------------|-------------|
| **File di Azure** | Include un'interfaccia SMB, librerie client e un'[interfaccia REST](/rest/api/storageservices/file-service-rest-api) che consente l'accesso ai file archiviati ovunque ci si trovi. | Si intende aggiornare e spostare un'applicazione nel cloud che usa già le API del file system native per condividere i dati con altre applicazioni in esecuzione in Azure.<br/><br/>Si intende archiviare gli strumenti di sviluppo e di debug a cui deve essere possibile accedere da molte macchine virtuali. |
| **BLOB di Azure** | Include librerie client e un'[interfaccia REST](/rest/api/storageservices/blob-service-rest-api) che consente di archiviare i dati non strutturati e di accedervi su larga scala in BLOB in blocchi.<br/><br/>Supporta anche [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) per le soluzioni aziendali di analisi di Big Data. | Si desidera che la propria applicazione supporti scenari di accesso casuale e tramite flusso.<br/><br/>Si desidera poter accedere ai dati dell'applicazione ovunque ci si trovi.<br/><br/>Si vuole compilare un Data Lake aziendale in Azure ed eseguire l'analisi dei Big Data. |
| **Dischi di Azure** | Include librerie client e un'[interfaccia REST](/rest/api/compute/manageddisks/disks/disks-rest-api) che consente di archiviare in modo permanente i dati e di accedervi da un disco rigido virtuale collegato. | Si intende aggiornare e spostare le applicazioni che usano le API del file system native per leggere e scrivere dati su dischi permanenti.<br/><br/>Si intende archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco. |


## <a name="next-steps"></a>Passaggi successivi

Quando si decide la modalità di archiviazione e di accesso ai dati, è consigliabile valutare anche i costi coinvolti. Per altre informazioni, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Alcune funzionalità SMB non sono applicabili al cloud. Per altre informazioni, vedere [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Funzionalità non supportate da Servizio file di Azure).
 
Per altre informazioni sui BLOB di Azure, vedere l'articolo informazioni sull' [archiviazione BLOB di Azure](../blobs/storage-blobs-overview.md).

Per ulteriori informazioni su archiviazione su disco, vedere l' [Introduzione a Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

Per ulteriori informazioni su File di Azure, vedere l'articolo [pianificazione di una distribuzione di file di Azure](../files/storage-files-planning.md).