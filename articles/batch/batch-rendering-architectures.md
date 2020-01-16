---
title: 'Rendering di Azure: architetture di riferimento - Azure Batch'
description: Architetture per l'uso di Azure Batch e altri servizi di Azure per estendere un farm di rendering locale tramite burst nel cloud
services: batch
ms.service: batch
author: davefellows
manager: gwallace
ms.author: jushiman
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: a04f59983aca4b7db1a58ab4e8b8a2da47a52783
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026306"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architetture di riferimento per il rendering di Azure

Questo articolo illustra i diagrammi di architettura di alto livello per gli scenari in cui estendere, o "eseguire il burst", il farm di rendering locale in Azure. Gli esempi includono opzioni diverse per i servizi di calcolo, rete e archiviazione di Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Configurazione ibrida con NFS o CFS

Il diagramma seguente illustra uno scenario ibrido che include i servizi di Azure seguenti:

* **Calcolo**: pool di Azure Batch o set di scalabilità di macchine virtuali.

* **Rete**: locale: Azure ExpressRoute o VPN. Azure: rete virtuale di Azure.

* **Archiviazione**: file di input e output: NFS o CFS usando macchine virtuali di Azure, sincronizzati con l'archiviazione locale tramite Sincronizzazione file di Azure o RSync. In alternativa, è possibile eseguire l'vFXT dei file di input o di output da dispositivi NAS locali usando NFS.

  ![Bursting del cloud: configurazione ibrida con NFS o CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Configurazione ibrida con Blobfuse

Il diagramma seguente illustra uno scenario ibrido che include i servizi di Azure seguenti:

* **Calcolo**: pool di Azure Batch o set di scalabilità di macchine virtuali.

* **Rete**: locale: Azure ExpressRoute o VPN. Azure: rete virtuale di Azure.

* **Archiviazione**: file di input e output: archiviazione BLOB, montata per calcolare le risorse tramite Azure Blobfuse.

  ![Bursting del cloud: configurazione ibrida con Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Calcolo e archiviazione ibridi

Il diagramma seguente illustra uno scenario ibrido completamente connesso per l'archiviazione e il calcolo e include i servizi di Azure seguenti:

* **Calcolo**: pool di Azure Batch o set di scalabilità di macchine virtuali.

* **Rete**: locale: Azure ExpressRoute o VPN. Azure: rete virtuale di Azure.

* **Archiviazione**: cross-premise: Avere vFXT. Archiviazione facoltativa di file locali tramite Azure Data Box nell'archiviazione BLOB oppure Avere FXT locale per l'accelerazione NAS.

  ![Bursting del cloud: configurazione e archiviazione ibride](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'uso dell'[Esecuzione del rendering dei manager](batch-rendering-render-managers.md) con Azure Batch.

* Altre informazioni sulle opzioni per il [Rendering in Azure](batch-rendering-service.md).