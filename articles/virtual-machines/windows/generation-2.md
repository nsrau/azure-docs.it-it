---
title: Supporto di Azure per le macchine virtuali di seconda generazione (anteprima) | Microsoft Docs
description: Panoramica del supporto di Azure per le macchine virtuali di seconda generazione
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/16/2019
ms.author: lahugh
ms.openlocfilehash: 1b906a9e3ce803b7f319a920f29b5a60f62c47b1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550161"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Supporto per le macchine virtuali di seconda generazione (anteprima) in Azure

> [!IMPORTANT]
> Il supporto di Azure per le macchine virtuali di seconda generazione è attualmente in fase di anteprima.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per ulteriori informazioni, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il supporto per le macchine virtuali di seconda generazione (VM) è ora disponibile in anteprima in Azure. Non è possibile modificare la generazione di una macchina virtuale dopo averla creata, quindi esaminare le considerazioni in questa pagina prima di scegliere una generazione. 

Le macchine virtuali di seconda generazione supportano le funzionalità principali che non sono supportate nelle macchine virtuali di prima generazione. Queste funzionalità includono una maggiore memoria, Intel Software Guard Extensions (Intel SGX) e la memoria persistente virtualizzata (vPMEM). Le macchine virtuali di seconda generazione dispongono anche di alcune funzionalità che non sono ancora supportate in Azure. Per ulteriori informazioni, vedere la sezione [funzionalità e funzionalità](#features-and-capabilities) .

Le macchine virtuali di seconda generazione utilizzano la nuova architettura di avvio basata su UEFI anziché l'architettura basata su BIOS utilizzata dalle macchine virtuali di prima generazione. Rispetto alle macchine virtuali di prima generazione, le macchine virtuali di seconda generazione potrebbero avere un miglioramento del tempo di avvio e di installazione. Per una panoramica delle macchine virtuali di seconda generazione e alcune delle differenze tra la generazione 1 e la seconda generazione, vedere la pagina relativa alla [creazione di una macchina virtuale di generazione 1 o 2 in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Dimensioni delle macchine virtuali di seconda generazione

Le macchine virtuali di prima generazione sono supportate da tutte le dimensioni delle macchine virtuali in Azure. Azure offre ora il supporto per la generazione di anteprima 2 per le seguenti serie di macchine virtuali selezionate:

* [Serie B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Serie DC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* Serie [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series) e [serie Dsv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Serie Esv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Serie Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [Serie GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Serie HB](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [Serie HC](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* Serie [ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) e [serie Lsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Serie Mv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* Serie [NCv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series) e [serie NCv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [Serie ND](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [Serie NVv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

> [!NOTE]
> L'uso delle immagini di VM di seconda generazione per le macchine virtuali della serie Mv2 è disponibile a livello generale perché la serie Mv2 funziona solo con le immagini di macchina virtuale di seconda generazione. Le immagini di macchina virtuale di prima generazione non sono supportate nelle macchine virtuali della serie Mv2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Immagini di macchine virtuali di seconda generazione in Azure Marketplace

Le macchine virtuali di seconda generazione supportano le seguenti immagini del Marketplace:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

## <a name="on-premises-vs-azure-generation-2-vms"></a>Macchine virtuali in locale e Azure di seconda generazione

Azure attualmente non supporta alcune delle funzionalità supportate da Hyper-V in locale per le macchine virtuali di seconda generazione.

| Funzionalità di generazione 2                | Hyper-V locale | Azure |
|-------------------------------------|---------------------|-------|
| Avvio protetto                         | :heavy_check_mark:  | :x:   |
| VM schermata                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Sicurezza basata sulla virtualizzazione (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funzionalità e caratteristiche

### <a name="generation-1-vs-generation-2-features"></a>Funzionalità di generazione 1 e generazione 2

| Funzionalità | Prima generazione | Generazione 2 |
|---------|--------------|--------------|
| Avvio             | PCAT                      | UEFI                               |
| Controller del disco | IDE                       | SCSI                               |
| Dimensioni delle VM         | Tutte le dimensioni delle macchine virtuali | Solo le macchine virtuali che supportano archiviazione Premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funzionalità di generazione 1 e generazione 2

| Funzionalità | Prima generazione | Generazione 2 |
|------------|--------------|--------------|
| Disco del sistema operativo > 2 TB                    | :x:                | :heavy_check_mark: |
| Sistema operativo disco/immagine/scambio personalizzato         | :heavy_check_mark: | :heavy_check_mark: |
| Supporto del set di scalabilità di macchine virtuali | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :x:                |
| Backup/ripristino                    | :heavy_check_mark: | :heavy_check_mark: |
| Raccolta immagini condivise              | :heavy_check_mark: | :heavy_check_mark: |
| Crittografia dischi di Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Creazione di una macchina virtuale di seconda generazione

### <a name="marketplace-image"></a>Immagine del Marketplace

Nel portale di Azure o nell'interfaccia della riga di comando di Azure è possibile creare macchine virtuali di seconda generazione da un'immagine del Marketplace che supporta l'avvio UEFI.

#### <a name="azure-portal"></a>Portale di Azure

Le immagini di generazione 2 per Windows e SLES sono incluse nella stessa offerta server delle immagini Gen1. Ciò significa che, dal punto di vista del flusso, si seleziona l'offerta e lo SKU dal portale per la VM. Se lo SKU supporta le immagini di prima e di seconda generazione, è possibile scegliere di creare una VM di seconda generazione dalla scheda *Avanzate* nel flusso di creazione della macchina virtuale.

Attualmente, gli SKU seguenti supportano sia immagini di generazione 1 che di generazione 2:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

Quando si seleziona uno SKU di Windows Server come offerta, nella scheda **Avanzate** è disponibile un'opzione per creare una VM di **generazione 1** (BIOS) o di **generazione 2** (UEFI). Se si seleziona **generazione 2**, verificare che le dimensioni della macchina virtuale selezionate nella scheda **nozioni di base** siano [supportate per le macchine virtuali di seconda generazione](#generation-2-vm-sizes).

![Selezionare la VM di generazione 1 o di generazione 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

È anche possibile usare PowerShell per creare una VM facendo direttamente riferimento allo SKU di generazione 1 o di seconda generazione.

Ad esempio, usare il cmdlet di PowerShell seguente per ottenere un elenco degli SKU nell'offerta `WindowsServer`.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Se si crea una macchina virtuale con Windows Server 2012 come sistema operativo, verrà selezionato lo SKU della VM di generazione 1 (BIOS) o di generazione 2 (UEFI), che è simile al seguente:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Per un elenco aggiornato delle immagini del Marketplace supportate, vedere la sezione [funzionalità e funzionalità](#features-and-capabilities) .

### <a name="managed-image-or-managed-disk"></a>Immagine gestita o disco gestito

È possibile creare una macchina virtuale di seconda generazione da un'immagine gestita o da un disco gestito nello stesso modo in cui si crea una macchina virtuale di prima generazione.

### <a name="virtual-machine-scale-sets"></a>Set di scalabilità di macchine virtuali

È anche possibile creare macchine virtuali di seconda generazione usando i set di scalabilità di macchine virtuali. Nell'interfaccia della riga di comando di Azure usare i set di scalabilità di Azure per creare VM di seconda generazione.

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Le macchine virtuali di seconda generazione sono disponibili in tutte le aree di Azure?**  
    Sì. Tuttavia, non tutte le [dimensioni delle macchine virtuali di seconda generazione](#generation-2-vm-sizes) sono disponibili in ogni area. La disponibilità della macchina virtuale di seconda generazione dipende dalla disponibilità delle dimensioni della macchina virtuale.

* **Esiste una differenza di prezzo tra le macchine virtuali di prima e di seconda generazione?**  
   No.

* **Si dispone di un file con estensione VHD della macchina virtuale locale di seconda generazione. È possibile usare il file con estensione VHD per creare una macchina virtuale di seconda generazione in Azure?**
  Sì, è possibile importare il file con estensione VHD di generazione 2 in Azure e usarlo per creare una macchina virtuale di seconda generazione. Per eseguire questa operazione, attenersi alla procedura seguente:
    1. Caricare il file VHD in un account di archiviazione nella stessa area in cui si vuole creare la macchina virtuale.
    1. Creare un disco gestito dal file con estensione vhd. Impostare la proprietà di generazione Hyper-V su V2. I comandi di PowerShell seguenti impostano la proprietà di generazione Hyper-V durante la creazione del disco gestito.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Quando il disco è disponibile, creare una VM connettendo questo disco. La macchina virtuale creata sarà una macchina virtuale di seconda generazione.
    Quando viene creata la macchina virtuale di seconda generazione, è possibile generalizzare l'immagine della macchina virtuale. Generalizzando l'immagine, è possibile usarla per creare più macchine virtuali.

* **Ricerca per categorie aumentare le dimensioni del disco del sistema operativo?**  
  I dischi del sistema operativo di dimensioni superiori a 2 TB sono nuovi per le macchine virtuali di seconda generazione. Per impostazione predefinita, i dischi del sistema operativo sono inferiori a 2 TB per le macchine virtuali di seconda generazione. È possibile aumentare le dimensioni del disco fino a un massimo consigliato di 4 TB. Usare l'interfaccia della riga di comando di Azure o l'portale di Azure per aumentare le dimensioni del disco del sistema operativo. Per informazioni su come espandere i dischi a livello di codice, vedere [ridimensionare un disco](expand-os-disk.md).

  Per aumentare le dimensioni del disco del sistema operativo dal portale di Azure:

  1. Nella portale di Azure passare alla pagina delle proprietà della macchina virtuale.
  1. Per arrestare e deallocare la VM, selezionare il pulsante **Interrompi** .
  1. Nella sezione **dischi** selezionare il disco del sistema operativo che si vuole aumentare.
  1. Nella sezione **dischi** selezionare **configurazione**e aggiornare le **dimensioni** al valore desiderato.
  1. Tornare alla pagina delle proprietà della macchina virtuale e **avviare** la macchina virtuale.
  
  Potrebbe essere visualizzato un avviso per i dischi del sistema operativo con dimensioni maggiori di 2 TB. L'avviso non si applica alle macchine virtuali di seconda generazione. Tuttavia, *non è consigliabile* usare dischi del sistema operativo di dimensioni superiori a 4 TB.

* **Le macchine virtuali di seconda generazione supportano la rete accelerata?**  
    Sì. Per altre informazioni, vedere [creare una macchina virtuale con rete accelerata](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **VHDX è supportato sulla generazione 2?**  
    No, le macchine virtuali di seconda generazione supportano solo VHD.

* **Le macchine virtuali di seconda generazione supportano Azure ultra archiviazione su disco?**  
    Sì.

* **È possibile eseguire la migrazione di una macchina virtuale dalla generazione 1 alla seconda generazione?**  
    No, non è possibile modificare la generazione di una macchina virtuale dopo averla creata. Se è necessario passare tra le generazioni di macchine virtuali, creare una nuova macchina virtuale di una generazione diversa.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [macchine virtuali di seconda generazione in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Informazioni su come [preparare un disco rigido virtuale](prepare-for-upload-vhd-image.md) per il caricamento da sistemi locali ad Azure.
