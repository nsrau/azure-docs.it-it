---
title: Le macchine virtuali di generazione 2 (anteprima) in Azure | Microsoft Docs
description: Panoramica delle macchine virtuali di Azure di generazione 2
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 1dcc0d3a652ccbf365a18ce734a54dc78515b1a7
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388357"
---
# <a name="generation-2-vms-preview-on-azure"></a>Macchine virtuali di generazione 2 (anteprima) in Azure

> [!IMPORTANT]
> Macchine virtuali di generazione 2 sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Supporto per le macchine virtuali di generazione 2 (VM) è ora disponibile in anteprima pubblica in Azure. È possibile modificare la generazione di una macchina virtuale dopo averla creata. Pertanto, è consigliabile rivedere le considerazioni [qui](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) , nonché le informazioni in questa pagina prima di scegliere una generazione.

Generazione 2 macchine virtuali di supporto per le funzionalità principali non sono supportate nelle macchine virtuali di generazione 1, ad esempio: maggiore memoria, Intel® Software Guard Extensions (SGX) e virtuale memoria persistente (vPMEM). Le VM di generazione 2 hanno anche alcune funzionalità che non sono ancora supportati in Azure. Per altre informazioni, vedere la [caratteristiche e funzionalità](#features-and-capabilities) sezione.

Le VM Gen 2 usano Visual Studio nuova architettura basata su UEFI Boot l'architettura basata su BIOS utilizzata dalle macchine virtuali di generazione 1. Rispetto alle macchine virtuali di generazione 1, le macchine virtuali di generazione 2 possono evidenziare miglioramenti tempi di avvio e installazione. Per una panoramica delle macchine virtuali di generazione 2 e alcune delle differenze principali tra la generazione 1 e generazione 2, vedere [è necessario creare una macchina virtuale di generazione 1 o 2 in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Dimensioni delle macchine Virtuali di generazione 2

Macchine virtuali di generazione 1 sono supportate da tutte le dimensioni di macchina virtuale in Azure. Azure offre ora il supporto di generazione 2 per la seguente serie VM selezionato in anteprima pubblica:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) e [serie Dsv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Macchine virtuali serie Esv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Macchine virtuali serie Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Serie GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Serie ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) e [Lsv2 serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Immagini di macchina virtuale di generazione 2 in Azure Marketplace

Le VM Gen 2 supportano le immagini di Marketplace di Azure seguenti:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

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

| Funzionalità | Prima generazione | Generazione 2 |
|------------|--------------|--------------|
| Disco del sistema operativo > 2 TB                    | :x:                        | :heavy_check_mark: |
| Sistema operativo di dischi/immagini/Swap personalizzato         | :heavy_check_mark:         | :heavy_check_mark: |
| Supporto del set di scalabilità di macchine virtuali | :heavy_check_mark:         | :heavy_check_mark: |
| Azure Site Recovery/Backup                        | :heavy_check_mark:         | :x:                |
| Raccolta di immagini condivise              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

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

* **Sono generazione 2 macchine virtuali disponibili in tutte le aree di Azure?**  
    Sì. Tuttavia, non tutti i [dimensioni delle macchine Virtuali di generazione 2](#generation-2-vm-sizes) sono disponibili in ogni area. La disponibilità di generazione 2 macchine virtuali dipende la disponibilità delle dimensioni della macchina virtuale.

* **È presente una differenza di prezzo tra generazione 1 e generazione 2 macchine virtuali?**  
    Non vi è alcuna differenza di prezzo tra generazione 1 e macchine virtuali di generazione 2.

* **Come è possibile aumentare le dimensioni del disco del sistema operativo?**  
  Dischi del sistema operativo di dimensioni superiori a 2 TB sono di nuovi alla generazione 2 macchine virtuali. Per impostazione predefinita, la maggior parte dei dischi del sistema operativo siano inferiori a 2 TB per le macchine virtuali di generazione 2, ma le dimensioni del disco possono essere aumentata a un valore massimo consigliato di 4 TB. È possibile aumentare le dimensioni del disco del sistema operativo tramite la CLI di Azure o il portale di Azure. Per ulteriori informazioni sull'espansione dei dischi a livello di codice, vedere [ridimensionare un disco](expand-os-disk.md).

  Per aumentare le dimensioni del disco del sistema operativo tramite il portale di Azure:

  * Passare alla pagina delle proprietà della macchina virtuale nel portale di Azure.

  * Arrestare e deallocare la VM usando il **arrestare** pulsante nel portale di Azure.

  * Nel **dischi** , selezionare il disco del sistema operativo che si vuole aumentare.

  * Selezionare **Configuration** nel **dischi** sezione e aggiornare il **dimensioni** sul valore desiderato.

  * Tornare alla pagina delle proprietà della macchina virtuale e **avviare** la macchina virtuale.
  
  È possibile notare un avviso per i dischi del sistema operativo superiori a 2 TB. L'avviso non si applica alle macchine virtuali di generazione 2; Tuttavia, sono di dimensioni del disco del sistema operativo superiori a 4 TB **non consigliato.**

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

* Informazioni su come [preparare un disco rigido virtuale](prepare-for-upload-vhd-image.md) per il caricamento da locale ad Azure.
