---
title: Benchmarking delle applicazioni nell'archiviazione su disco di Azure - dischi gestiti
description: Informazioni sul processo di benchmarking delle applicazioni in Azure.
services: virtual-machines-windows,storage
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 8db1fb3c9b3ed551cd668cf14105eb8bfb486251
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331810"
---
# <a name="benchmarking-a-disk"></a>Benchmarking di un disco

Il benchmarking è il processo di simulazione di diversi carichi di lavoro sull'applicazione e di misurazione delle prestazioni dell'applicazione per ogni carico di lavoro. Eseguendo la procedura illustrata nell'[articolo sulla progettazione per prestazioni elevate](premium-storage-performance.md) sono stati raccolti i requisiti relativi alle prestazioni dell'applicazione. Eseguendo gli strumenti di benchmarking nelle VM che ospitano l'applicazione sarà possibile determinare i livelli di prestazioni che l'applicazione è in grado di ottenere con l'Archiviazione Premium. In questo articolo sono disponibili esempi di benchmarking per una macchina virtuale DS14 Standard con provisioning con dischi di Archiviazione Premium di Azure.

Sono stati usati gli strumenti di benchmarking comuni Iometer e FIO, rispettivamente per Windows e Linux. Questi strumenti generano più thread che simulano un carico di lavoro analogo a quello di produzione e misurano le prestazioni del sistema. Questi strumenti consentono anche di configurare i parametri quali la dimensione dei blocchi e la profondità della coda, che in genere non possono essere modificati per un'applicazione. Ciò offre maggiore flessibilità per ottenere il livello massimo di prestazioni su una VM a scalabilità elevata con provisioning con dischi Premium per diversi tipi di carichi di lavoro dell'applicazione. Per altre informazioni su ogni strumento di benchmarking, vedere [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Per eseguire gli esempi seguenti, creare una VM DS14 Standard e collegare 11 dischi di Archiviazione Premium alla VM. Degli 11 dischi, configurare 10 dischi con memorizzazione nella cache dell'host impostata su "None" ed effettuarne lo striping in un volume denominato NoCacheWrites. Configurare la memorizzazione nella cache dell'host come "ReadOnly" sul disco rimanente e creare un volume denominato CacheReads con questo disco. Usando questa configurazione è possibile vedere le prestazioni massime di lettura e scrittura da una macchina virtuale DS14 Standard. Per informazioni dettagliate sulla creazione di una macchina virtuale DS14 con unità SSD Premium, passare a [Progettazione per prestazioni elevate](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Passaggi successivi

Procedere con l'articolo sulla progettazione per prestazioni elevate. In tale articolo viene descritto come creare un elenco di controllo analogo a quello per l'applicazione esistente per il prototipo. Usando gli strumenti di benchmarking è possibile simulare i carichi di lavoro e misurare le prestazioni nel prototipo dell'applicazione. Sarà quindi possibile determinare quale offerta di dischi può soddisfare o superare i requisiti relativi alle prestazioni per l'applicazione. Si potranno quindi implementare le stesse indicazioni per l'applicazione di produzione.

> [!div class="nextstepaction"]
> Per iniziare, vedere l'articolo sulla [progettazione per prestazioni elevate](premium-storage-performance.md).