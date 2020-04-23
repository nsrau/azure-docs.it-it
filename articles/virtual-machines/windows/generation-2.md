---
title: Azure support for generation 2 VMs
description: Panoramica del supporto di Azure per le macchine virtuali di generazione 2Overview of Azure support for generation 2 VMs
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 53806a1a627c7ae6bc3470aa387be0982df914fa
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082259"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Supporto per le macchine virtuali di generazione 2 in AzureSupport for generation 2 VMs on Azure

Il supporto per le macchine virtuali di generazione 2 (VM) è ora disponibile in Azure.Support for generation 2 virtual machines (VMs) is now available on Azure. Non è possibile modificare la generazione di una macchina virtuale dopo averla creata, pertanto esaminare le considerazioni in questa pagina prima di scegliere una generazione.

Generation 2 VMs support key features that aren't supported in generation 1 VMs. Queste funzionalità includono una maggiore memoria, Intel Software Guard Extensions (Intel SGX) e memoria persistente virtualizzata (vPMEM). Le macchine virtuali di generazione 2 in esecuzione in locale, hanno alcune funzionalità che non sono ancora supportate in Azure.Generation 2 VMs running on-premises, have some features that aren't supported in Azure yet. Per ulteriori informazioni, vedere la sezione [Funzionalità e funzionalità.](#features-and-capabilities)

Le macchine virtuali di generazione 2 usano la nuova architettura di avvio basata su UEFI anziché l'architettura basata sul BIOS usata dalle macchine virtuali di generazione 1. Rispetto alle macchine virtuali di generazione 1, le macchine virtuali di generazione 2 potrebbero avere tempi di avvio e installazione migliorati. Per una panoramica delle macchine virtuali di generazione 2 e alcune delle differenze tra la generazione 1 e la generazione 2, vedere È consigliabile creare una macchina virtuale di [generazione 1 o 2 in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Dimensioni VM di generazione 2

Le macchine virtuali di generazione 1 sono supportate da tutte le dimensioni delle macchine virtuali in Azure (ad eccezione delle macchine virtuali di serie Mv2). Azure offre ora il supporto della generazione 2 per la serie di macchine virtuali selezionata seguente:Azure now offers generation 2 support for the following selected VM series:

* [Serie B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Serie DCsv2](../dcv2-series.md)
* [Serie DSv2](../dv2-dsv2-series.md) e [Dsv3](../dv3-dsv3-series.md)
* [Serie Dasv4](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)
* [Serie Esv3](../ev3-esv3-series.md)
* [Serie Easv4](https://docs.microsoft.com/azure/virtual-machines/eav4-easv4-series)
* [Serie Fsv2](../fsv2-series.md)
* [Serie GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Serie HB](../hb-series.md)
* [Serie HC](../hc-series.md)
* [Serie Ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) e [Serie Lsv2](../lsv2-series.md)
* [Serie Mv2](../mv2-series.md)
* [Serie NCv2](../ncv2-series.md) e [NCv3](../ncv3-series.md)
* [Serie ND](../nd-series.md)
* [Serie NVv3](../nvv3-series.md)

> [!NOTE]
> L'utilizzo delle immagini VM di generazione 2 per le macchine virtuali serie Mv2 è generalmente disponibile poiché la serie Mv2 funziona esclusivamente con immagini VM di generazione 2.The usage of generation 2 VM images for Mv2-series VMs is generally available since the Mv2-series works with generation 2 VM images exclusively. Le immagini delle macchine virtuali di generazione 1 non sono supportate nelle macchine virtuali serie Mv2.Generation 1 VM images are not supported on Mv2-series VMs. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Immagini di macchine virtuali di generazione 2 in Azure MarketplaceGeneration 2 VM images in Azure Marketplace

Le macchine virtuali di generazione 2 supportano le immagini Marketplace seguenti:Generation 2 VMs support the following Marketplace images:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>Macchine virtuali locali e di azure di generazione 2On-premises vs.

Azure doesn't currently support some of the features that on-premises Hyper-V supports for generation 2 VMs.

| Funzionalità Generazione 2                | Hyper-V locale | Azure |
|-------------------------------------|---------------------|-------|
| Avvio sicuro                         | :heavy_check_mark:  | :x:   |
| VM schermata                         | :heavy_check_mark:  | :x:   |
| VTPM (informazioni in base al sistema                                | :heavy_check_mark:  | :x:   |
| Sicurezza basata sulla virtualizzazione (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Caratteristiche e funzionalità

### <a name="generation-1-vs-generation-2-features"></a>Funzionalità di generazione 1 e generazione 2

| Funzionalità | Prima generazione | Seconda generazione |
|---------|--------------|--------------|
| Avvio             | Libreria DI classi PCAT                      | UEFI                               |
| Controller disco | IDE                       | SCSI                               |
| Dimensioni delle macchine virtuali         | Tutte le dimensioni delle macchine virtuali | Solo macchine virtuali che supportano l'archiviazione PremiumOnly VMs that support premium storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funzionalità di generazione 1 e generazione 2

| Funzionalità | Prima generazione | Seconda generazione |
|------------|--------------|--------------|
| Disco del sistema operativo > 2 TB                    | :x:                | :heavy_check_mark: |
| Disco/immagine/sistema operativo di scambio personalizzato         | :heavy_check_mark: | :heavy_check_mark: |
| Supporto del set di scalabilità delle macchine virtualiVirtual machine scale set support | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Backup/ripristino                    | :heavy_check_mark: | :heavy_check_mark: |
| Raccolta di immagini condivise              | :heavy_check_mark: | :heavy_check_mark: |
| Crittografia dischi di Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Creazione di una macchina virtuale di generazione 2Creating a generation 2 VM

### <a name="marketplace-image"></a>Immagine del Marketplace

Nel portale di Azure o nell'interfaccia della riga di comando di Azure è possibile creare macchine virtuali di generazione 2 da un'immagine del Marketplace che supporta l'avvio UEFI.

#### <a name="azure-portal"></a>Portale di Azure

Di seguito sono riportati i passaggi per creare una macchina virtuale di generazione 2 (Gen2) nel portale di Azure.Below are the steps to create a generation 2 (Gen2) VM in Azure portal.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Selezionare **Crea una risorsa**.
1. Fare clic su **Visualizza tutto** da Azure Marketplace a sinistra.
1. Selezionare un'immagine che supporti Gen2.
1. Fare clic su **Crea**.
1. Nella scheda **Avanzate,** nella sezione **Generazione macchina virtuale,** selezionare l'opzione **Generazione 2.In** the Advanced tab, under the VM generation section, select the Gen 2 option.
1. Nella scheda **Nozioni di base,** in **Dettagli istanza,** passare a **Dimensioni** e aprire il pannello Selezionare le dimensioni di una **macchina virtuale.**
1. Selezionare una [macchina virtuale di generazione 2 supportata](#generation-2-vm-sizes).
1. Passare attraverso il flusso di creazione del portale di [Azure](quick-create-portal.md) per completare la creazione della macchina virtuale.

![Selezionare la macchina virtuale Gen 1 o Gen 2Select Gen 1 or Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

È anche possibile usare PowerShell per creare una macchina virtuale facendo riferimento direttamente allo SKU di generazione 1 o generazione 2.You can also use PowerShell to create a VM by directly referencing the generation 1 or generation 2 SKU.

Ad esempio, utilizzare il cmdlet PowerShell seguente per ottenere `WindowsServer` un elenco degli SKU nell'offerta.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per visualizzare tutte le immagini di generazione 2 disponibili elencate da **Publisher.**

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Se si sta creando una macchina virtuale con Windows Server 2012 come sistema operativo, si selezionerà lo SKU di VM di generazione 1 (BIOS) o di generazione 2 (UEFI), simile al seguente:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Vedere la sezione [Funzionalità e funzionalità](#features-and-capabilities) per un elenco aggiornato delle immagini di Marketplace supportate.

### <a name="managed-image-or-managed-disk"></a>Immagine gestita o disco gestito

You can create a generation 2 VM from a managed image or managed disk in the same way you would create a generation 1 VM.

### <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali

È anche possibile creare macchine virtuali di generazione 2 usando set di scalabilità di macchine virtuali. Nell'interfaccia della riga di comando di Azure usare i set di scalabilità di Azure per creare macchine virtuali di generazione 2.In the Azure CLI, use Azure scale sets to create generation 2 VMs.

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Le macchine virtuali di generazione 2 sono disponibili in tutte le aree di Azure?**  
    Sì. Ma non tutte le [dimensioni di VM di generazione 2](#generation-2-vm-sizes) sono disponibili in ogni area. La disponibilità della macchina virtuale di generazione 2 dipende dalla disponibilità delle dimensioni della macchina virtuale.

* **Esiste una differenza di prezzo tra le macchine virtuali di generazione 1 e 2?**  
   No.

* **Ho un file con estensione vhd dalla mia macchina virtuale di generazione 2 locale. È possibile usare il file con estensione vhd per creare una macchina virtuale di generazione 2 in Azure?**
  Sì, è possibile portare il file con estensione vhd di generazione 2 in Azure e usarlo per creare una macchina virtuale di generazione 2.Yes, you can bring your generation 2 .vhd file to Azure and use that to create a generation 2 VM. A tale scopo, attenersi alla seguente procedura:
    1. Caricare il file con estensione vhd in un account di archiviazione nella stessa area in cui si vuole creare la macchina virtuale.
    1. Creare un disco gestito dal file con estensione vhd. Impostare la proprietà Generazione Hyper-V su V2. I comandi di PowerShell seguenti impostano la proprietà Generazione Hyper-V durante la creazione del disco gestito.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Quando il disco è disponibile, creare una macchina virtuale collegandolo. La macchina virtuale creata sarà una macchina virtuale di generazione 2.The VM created will be a generation 2 VM.
    Quando viene creata la macchina virtuale di generazione 2, è possibile generalizzare facoltativamente l'immagine di questa macchina virtuale. Generalizzando l'immagine, è possibile usarla per creare più macchine virtuali.

* **Come posso aumentare le dimensioni del disco del sistema operativo?**  
  I dischi del sistema operativo di dimensioni superiori a 2 TB sono nuovi per le macchine virtuali di generazione 2.OS disks larger than 2 TB are new to generation 2 VMs. Per impostazione predefinita, i dischi del sistema operativo sono inferiori a 2 TB per le macchine virtuali di generazione 2.By default, OS disks are smaller than 2 TB for generation 2 VMs. È possibile aumentare la dimensione del disco fino a un massimo consigliato di 4 TB. Usare l'interfaccia della riga di comando di Azure o il portale di Azure per aumentare le dimensioni del disco del sistema operativo. Per informazioni su come espandere i dischi a livello di codice, vedere [Ridimensionare un disco](expand-os-disk.md).

  Per aumentare le dimensioni del disco del sistema operativo dal portale di Azure:To increase the OS disk size from the Azure portal:

  1. Nel portale di Azure passare alla pagina delle proprietà della macchina virtuale.
  1. Per arrestare e deallocare la macchina virtuale, selezionare il pulsante **Arresta.To** shut down and deallocate the VM button, select the Stop button.
  1. Nella sezione **Dischi** selezionare il disco del sistema operativo che si desidera aumentare.
  1. Nella sezione **Dischi** selezionare **Configurazione**e aggiornare la **dimensione** al valore desiderato.
  1. Tornare alla pagina delle proprietà della macchina virtuale e **avviare** la macchina virtuale.
  
  È possibile che venga visualizzato un avviso per i dischi del sistema operativo di dimensioni superiori a 2 TB. L'avviso non si applica alle macchine virtuali di generazione 2.The warning doesn't apply to generation 2 VMs. Tuttavia, le dimensioni del disco del sistema operativo superiori a 4 TB non sono *consigliate.*

* **Le macchine virtuali di generazione 2 supportano la rete accelerata?**  
    Sì. Per altre informazioni, vedere [Creare una macchina virtuale con rete accelerata.](../../virtual-network/create-vm-accelerated-networking-cli.md)

* **VHDX è supportato nella generazione 2?**  
    No, le macchine virtuali di generazione 2 supportano solo il disco rigido virtuale.

* **Le macchine virtuali di generazione 2 supportano Archiviazione su disco Ultra di Azure?**  
    Sì.

* **È possibile eseguire la migrazione di una macchina virtuale dalla generazione 1 alla generazione 2?**  
    No, non è possibile modificare la generazione di una macchina virtuale dopo averla creata. Se è necessario passare da una generazione di macchine virtuali all'altra, creare una nuova macchina virtuale di una generazione diversa.

* **Perché le dimensioni della macchina virtuale non sono abilitate nel selettore delle dimensioni quando si tenta di creare una macchina virtuale Gen2?**

    Questo può essere risolto effettuando le seguenti operazioni:

    1. Verificare che la proprietà **di generazione della macchina virtuale** sia impostata su Generazione 2 nella scheda Avanzate.Verify that the VM generation property is set to **Gen 2** in the **Advanced** tab.
    1. Verificare di cercare una [dimensione di macchina virtuale che supporti le macchine virtuali Gen2.](#generation-2-vm-sizes)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [generazione 2 di macchine virtuali in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Informazioni su come preparare un disco rigido virtuale per il caricamento da sistemi locali in Azure.Learn how to prepare a [VHD](prepare-for-upload-vhd-image.md) to upload from on-premises systems to Azure.
