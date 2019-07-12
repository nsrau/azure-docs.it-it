---
title: Supporto di Azure per macchine virtuali di seconda generazione 2 (anteprima) | Microsoft Docs
description: Panoramica del supporto tecnico di Azure per macchine virtuali di generazione 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 653d4baa89e28255f11df1c5e2d813d37535793a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667553"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Supporto per la generazione 2 macchine virtuali (anteprima) in Azure

> [!IMPORTANT]
> Supporto di Azure per macchine virtuali di generazione 2 è attualmente in anteprima. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni d'uso aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Supporto per le macchine virtuali di generazione 2 (VM) è ora disponibile in anteprima in Azure. Impossibile modificare la generazione di una macchina virtuale dopo averla creata, quindi rivedere le considerazioni in questa pagina prima di scegliere una generazione. 

Le VM Gen 2 supportano le funzionalità principali che non sono supportate nelle macchine virtuali di generazione 1. Queste funzionalità includono una maggiore memoria, Intel Software Guard Extensions (Intel SGX) e virtualizzato memoria persistente (vPMEM). Le VM di generazione 2 hanno anche alcune funzionalità che non sono ancora supportati in Azure. Per altre informazioni, vedere la [caratteristiche e funzionalità](#features-and-capabilities) sezione.

Le VM Gen 2 usano la nuova architettura basata su UEFI boot anziché l'architettura basata su BIOS utilizzato dalla generazione 1 VM. Rispetto alle macchine virtuali di generazione 1, le VM Gen 2 potrebbe essere migliorata tempi di avvio e installazione. Per una panoramica delle macchine virtuali di generazione 2 e alcune delle differenze tra la generazione 1 e generazione 2, vedere [è necessario creare una macchina virtuale di generazione 1 o 2 in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Dimensioni delle macchine Virtuali di generazione 2

Macchine virtuali di generazione 1 sono supportate da tutte le dimensioni di macchina virtuale in Azure. Azure offre ora il supporto di generazione 2 preview per la seguente serie VM selezionato:

* [Serie Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) e [serie Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Macchine virtuali serie Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Macchine virtuali serie Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Serie GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Serie ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) e [Lsv2 serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Immagini di macchina virtuale di generazione 2 in Azure Marketplace

Le VM Gen 2 supportano le immagini Marketplace seguenti:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Visual Studio in locale. Macchine virtuali di Azure di generazione 2

Azure attualmente non supporta alcune delle funzionalità che on-premises Hyper-V supporta per macchine virtuali di generazione 2.

| Funzionalità di generazione 2                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Avvio protetto                         | :heavy_check_mark:  | :x:   |
| Macchina virtuale schermata                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Sicurezza basata su virtualizzazione (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Caratteristiche e funzionalità

### <a name="generation-1-vs-generation-2-features"></a>Generazione 1 e le funzionalità di generazione 2

| Funzionalità | Prima generazione | Generazione 2 |
|---------|--------------|--------------|
| Avvio             | PCAT                      | UEFI                               |
| Controller del disco | IDE                       | SCSI                               |
| Dimensioni delle macchine virtuali         | Tutte le dimensioni di macchina virtuale | Solo le macchine virtuali che supportano archiviazione premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generazione 1 e le funzionalità di generazione 2

| Funzionalità | Prima generazione | Generazione 2 |
|------------|--------------|--------------|
| Disco del sistema operativo > 2 TB                    | :x:                        | :heavy_check_mark: |
| Personalizzato/immagine/lo scambio di dischi del sistema operativo         | :heavy_check_mark:         | :heavy_check_mark: |
| Supporto del set di scalabilità di macchine virtuali | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| Raccolta di immagini condivise              | :heavy_check_mark:         | :x:                |
| Crittografia dischi di Azure             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Creazione di una generazione 2 macchina virtuale

### <a name="marketplace-image"></a>Immagine del Marketplace

Nel portale di Azure o della riga di comando di Azure, è possibile creare generazione 2 macchine virtuali da un'immagine del Marketplace che supporta l'avvio UEFI.

Il `windowsserver-gen2preview` offerta contiene solo immagini della generazione 2 Windows. La creazione di pacchetti consente di evitare confusione tra generazione 1 e le immagini di generazione 2. Per creare una generazione 2 macchina virtuale, selezionare **immagini** da questa offerta e seguire il processo standard per creare la macchina virtuale.

Attualmente, 2 immagini Marketplace offre la generazione di Windows seguente:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

Vedere le [caratteristiche e funzionalità](#features-and-capabilities) sezione per un elenco aggiornato delle immagini di Marketplace supportate.

### <a name="managed-image-or-managed-disk"></a>Immagine gestita o un disco gestito

È possibile creare una generazione 2 macchina virtuale da un'immagine gestita o un disco gestito allo stesso modo, si creerà una generazione 1 macchina virtuale.

### <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali

È anche possibile creare generazione 2 macchine virtuali usando il set di scalabilità di macchine virtuali. Nella CLI di Azure, usare la scalabilità di Azure imposta creare 2 macchine virtuali di generazione.

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Sono generazione 2 macchine virtuali disponibili in tutte le aree di Azure?**  
    Sì. Ma non tutte le [dimensioni delle macchine Virtuali di generazione 2](#generation-2-vm-sizes) sono disponibili in ogni area. La disponibilità della generazione che 2 macchina virtuale dipende dalla disponibilità delle dimensioni della macchina virtuale.

* **È presente una differenza di prezzo tra generazione 1 e generazione 2 macchine virtuali?**  
    No.

* **Come è possibile aumentare le dimensioni del disco del sistema operativo?**  
  Dischi del sistema operativo di dimensioni superiori a 2 TB sono di nuovi alla generazione 2 macchine virtuali. Per impostazione predefinita, i dischi del sistema operativo sono inferiori a 2 TB per le macchine virtuali di generazione 2. È possibile aumentare le dimensioni del disco fino a un massimo consigliato di 4 TB. Usare il comando di Azure o il portale di Azure per aumentare le dimensioni del disco del sistema operativo. Per informazioni su come espandere i dischi a livello di codice, vedere [ridimensionare un disco](expand-disks.md).

  Per aumentare le dimensioni del disco del sistema operativo dal portale di Azure:

  1. Nel portale di Azure, passare alla pagina delle proprietà della macchina virtuale.
  1. Per arrestare e deallocare la macchina virtuale, selezionare la **arrestare** pulsante.
  1. Nel **dischi** , selezionare il disco del sistema operativo che si desidera aumentare.
  1. Nel **dischi** sezione, selezionare **Configuration**e aggiornare il **dimensioni** sul valore desiderato.
  1. Tornare alla pagina delle proprietà della macchina virtuale e **avviare** la macchina virtuale.

  È possibile visualizzare un avviso per i dischi del sistema operativo superiori a 2 TB. L'avviso non è applicabile alle macchine virtuali di generazione 2. Tuttavia, sono di dimensioni del disco del sistema operativo superiori a 4 TB *non consigliato.*

* **Eseguire l'operazione di generazione 2 macchine virtuali di supporto della rete accelerata?**  
    Sì. Per altre informazioni, vedere [creare una macchina virtuale con rete accelerata](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **VHDX è supportato nella generazione 2?**  
    No, le VM Gen 2 supportano solo disco rigido virtuale.

* **Le VM Gen 2 supportato di archiviazione su disco extra Azure?**  
    Sì.

* **È possibile migrare una VM dalla generazione 1 alla generazione 2?**  
    No, è possibile modificare la generazione di una macchina virtuale dopo averla creata. Se si richiede di spostarsi tra generazioni di macchine Virtuali, creare una nuova macchina virtuale di una generazione diversi.

## <a name="next-steps"></a>Passaggi successivi

* Scopri [macchine virtuali di generazione 2 in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
