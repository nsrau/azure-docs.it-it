---
title: Panoramica di Microsoft Azure FXT Edge Filer
description: Descrive la cache di archiviazione ibrida Azure FXT Edge Filer, un archivio attivo e una soluzione di accelerazione dell'accesso ai file per l'elaborazione High Performance Computing
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542894"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Informazioni sulla cache di archiviazione ibrida Azure FXT Edge Filer

Azure FXT Edge Filer è un'appliance di memorizzazione nella cache per l'archiviazione ibrida che fornisce un accesso veloce ai file e un archivio attivo per le attività HPC (High Performance Computing).

Funziona con più origini dati, archiviate in un data center locale, in remoto o nel cloud. Azure FXT Edge Filer può fornire uno spazio dei nomi unificato per i dati contenuti in sistemi di archiviazione eterogenei.

Tre o più dispositivi hardware FXT Edge Filer operano insieme come un file system in cluster per fornire la cache. Per informazioni dettagliate sull'acquisto dell'hardware richiesto, contattare il rappresentante Microsoft. 

Per altre informazioni, leggere le informazioni sul prodotto e il foglio dati di [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Casi d'uso

Azure FXT Edge Filer aumenta in modo particolare la produttività per i flussi di lavoro simili ai seguenti:

* Flusso di lavoro di accesso ai file con intensa attività di lettura 
* Protocolli NFSv3 o SMB2
* Farm di calcolo da 1000 a 100.000 core CPU

### <a name="nas-optimization-and-scaling"></a>Ottimizzazione e scalabilità NAS

È possibile usare la cache di Azure FXT Edge Filer per semplificare l'accesso ai sistemi NAS NetApp e Dell EMC Isilon esistenti. È anche possibile aggiungere BLOB di Azure o altre risorse di archiviazione cloud per offrire scalabilità senza dover rielaborare i processi di accesso ai dati sul lato client. 

### <a name="wan-caching"></a>Memorizzazione nella cache WAN

Azure FXT Edge Filer può essere usato per supportare l'accesso rapido ai file da parte di utenti esperti, quando i dati necessari sono archiviati in un'altra posizione. Fornire l'accesso, mantenendo al tempo stesso il backup e altri sistemi di gestione dati in un data center centralizzato. 

### <a name="active-archive-in-azure-blob"></a>Archivio attivo nel BLOB di Azure

Espandere il data center nell'archiviazione cloud con Azure FXT Edge Filer come punto di accesso. 

## <a name="features"></a>Funzionalità 

Sono disponibili due modelli di hardware. 

| Modello | DRAM | SSD NVMe | Porte di rete | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Azure FXT Edge Filer, vedere le [specifiche](fxt-specs.md) o l'[esercitazione sull'installazione](fxt-install.md).
* Per informazioni su come acquistare Azure FXT Edge Filer, visitare la [pagina del prodotto Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).