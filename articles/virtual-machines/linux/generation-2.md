---
title: Le macchine virtuali di generazione 2 (anteprima) in Azure | Microsoft Docs
description: Panoramica delle macchine virtuali di Azure di generazione 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: lahugh
ms.openlocfilehash: 777486a64464c29e9014fdc3dbf94957b2e93aa5
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596337"
---
# <a name="generation-2-vms-preview-on-azure"></a>Macchine virtuali di generazione 2 (anteprima) in Azure

> [!IMPORTANT]
> Macchine virtuali di generazione 2 sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Supporto per le macchine virtuali di generazione 2 (VM) è ora disponibile in anteprima pubblica in Azure. È possibile modificare la generazione di una macchina virtuale dopo averla creata. Pertanto, è consigliabile rivedere le considerazioni [qui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) , nonché le informazioni in questa pagina prima di scegliere una generazione.

Funzionalità chiave di generazione 2 macchine virtuali supporto, ad esempio: maggiore memoria, Intel® Software Guard Extensions (SGX) e virtuale memoria persistente (vPMEM), che non sono supportati nelle macchine virtuali di generazione 1. Le VM Gen 2 hanno alcune funzionalità che non sono ancora supportati in Azure. Per altre informazioni, vedere la [caratteristiche e funzionalità](#features-and-capabilities) sezione. 

Le VM Gen 2 usano Visual Studio nuova architettura basata su UEFI Boot l'architettura basata su BIOS utilizzata dalle macchine virtuali di generazione 1. Rispetto alle macchine virtuali di generazione 1, le macchine virtuali di generazione 2 possono evidenziare miglioramenti tempi di avvio e installazione. Per una panoramica delle macchine virtuali di generazione 2 e alcune delle differenze principali tra la generazione 1 e generazione 2, vedere [è necessario creare una macchina virtuale di generazione 1 o 2 in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Dimensioni delle macchine Virtuali di generazione 2

Macchine virtuali di generazione 1 sono supportate da tutte le dimensioni di macchina virtuale in Azure. Azure offre ora il supporto di generazione 2 per la seguente serie VM selezionato in anteprima pubblica:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) e [serie Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Macchine virtuali serie Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Macchine virtuali serie Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Serie GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Serie ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) e [Lsv2 serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Immagini di macchina virtuale di generazione 2 in Azure Marketplace

Le VM Gen 2 supportano le immagini di Marketplace di Azure seguenti:

* Server Windows 2019 Datacenter
* Server Windows 2016 Datacenter
* Server Windows 2012 R2 Datacenter
* Server Windows 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Visual Studio in locale le macchine virtuali di Azure di generazione 2

Azure non supporta attualmente alcune delle funzionalità che supporta Hyper-V on premises per le macchine virtuali di generazione 2.

| Funzionalità di generazione 2                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Avvio protetto                         | :heavy_check_mark:  | :x:   |
| Macchina virtuale schermata                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Sicurezza basata su virtualizzazione (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Caratteristiche e funzionalità

### <a name="generation-1-vs-generation-2-features"></a>Funzionalità di generazione 2 di Visual Studio di generazione 1

| Funzionalità | Prima generazione | Generazione 2 |
|---------|--------------|--------------|
| Avvio             | PCAT                      | UEFI                               |
| Controller del disco | IDE                       | SCSI                               |
| Dimensioni delle VM         | Disponibile in tutte le dimensioni di macchina virtuale | Solo le macchine virtuali supportate da archiviazione Premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funzionalità di generazione 2 di Visual Studio di generazione 1

| Capacità | Prima generazione | Generazione 2 |
|------------|--------------|--------------|
| Disco del sistema operativo > 2 TB                    | :x:                        | :heavy_check_mark: |
| Sistema operativo di dischi/immagini/Swap personalizzato         | :heavy_check_mark:         | :heavy_check_mark: |
| Supporto del set di scalabilità di macchine virtuali | :heavy_check_mark:         | :heavy_check_mark: |
| Azure Site Recovery/Backup                        | :heavy_check_mark:         | :x:                |
| Raccolta immagini condivisa              | :heavy_check_mark:         | :x:                |
| Crittografia dischi di Azure             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Creazione di una generazione 2 macchina virtuale

### <a name="marketplace-image"></a>Immagine del Marketplace

Macchine virtuali di generazione 2 possono essere create da un'immagine del marketplace (che supporta l'avvio UEFI) tramite il portale di Azure o Azure CLI.

Il `windowsserver-gen2preview` offerta contiene solo immagini della generazione 2 Windows. Questo evita la confusione per quanto riguarda le immagini di generazione 1 e generazione 2. Per creare 2 macchine virtuali di generazione, selezionare **immagini** da questa offerta e seguire il processo di creazione della macchina virtuale standard.

Attualmente, la generazione di Windows seguente 2 immagini vengono pubblicate in Azure Marketplace:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Come si continuerà ad aggiungere immagini aggiuntive che supportano la generazione 2, vedere la sezione di funzionalità per un elenco di immagini del marketplace supportati.

### <a name="managed-image-or-managed-disk"></a>Immagine gestita o un disco gestito

Generazione 2 macchine virtuali possono essere create da immagini gestito o un disco gestito allo stesso modo, si creerà una generazione 1 macchina virtuale.

### <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali

È inoltre possibile creare 2 macchine virtuali usando il set di scalabilità di macchine virtuali della generazione. È possibile creare una generazione 2 macchine virtuali con set di scalabilità di macchine virtuali di Azure tramite la CLI di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Generazione 2 macchine virtuali supportano la rete accelerata?**  
    Sì, il supporto di macchine virtuali di generazione 2 [rete accelerata](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Vhdx è supportato nella generazione 2?**  
    No, solo i file con estensione vhd è supportata nelle macchine virtuali di generazione 2.

* **Le VM Gen 2 supportato extra solido stato unità (SSD)?**  
    Sì, le VM Gen 2 supportano SSD extra.

* **È possibile migrare dalla generazione 1 di generazione 2 macchine virtuali?**  
    No, è possibile modificare la generazione di una macchina virtuale dopo averla creata. Se si richiede di spostarsi tra generazioni di macchine Virtuali, è necessario creare una nuova macchina virtuale di una generazione diversi.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [macchine virtuali di generazione 2 in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).