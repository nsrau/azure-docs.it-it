---
title: Supporto per le macchine virtuali di seconda generazione in Azure
description: Panoramica del supporto delle macchine virtuali di seconda generazione in Azure
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 2a40c6158b5d29f695360c04a0d4376efd4238f9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288472"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Supporto per le macchine virtuali di seconda generazione in Azure

Il supporto per le macchine virtuali di seconda generazione è ora disponibile in Azure. Non è possibile modificare la generazione di una macchina virtuale dopo averla creata, pertanto esaminare le considerazioni in questa pagina prima di scegliere una generazione.

Le macchine virtuali di seconda generazione (Gen 2) supportano funzionalità principali che non sono supportate nelle macchine virtuali di prima generazione. Queste funzionalità includono una maggior quantità di memoria, Intel Software Guard Extensions (Intel SGX) e la memoria persistente virtuale (vPMEM). Le macchine virtuali di seconda generazione eseguite in locale hanno alcune funzionalità che non sono ancora supportate in Azure. Per altre informazioni, vedere la sezione [Funzionalità e caratteristiche](#features-and-capabilities).

Le macchine virtuali di seconda generazione usano la nuova architettura di avvio basata su UEFI anziché l'architettura basata su BIOS usata dalle macchine virtuali di prima generazione. Rispetto alle macchine virtuali di prima generazione, le macchine virtuali di seconda generazione possono far registrare riduzioni dei tempi di avvio e installazione. Per una panoramica delle macchine virtuali di seconda generazione e informazioni su alcune differenze tra la prima e la seconda generazione, vedere [È necessario creare una macchina virtuale di generazione 1 o 2 in Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Dimensioni delle macchine virtuali di seconda generazione

Le macchine virtuali di prima generazione sono supportate da tutte le dimensioni di macchine virtuali in Azure (salvo le macchine virtuali serie Mv2). Azure ora offre ora il supporto della seconda generazione per le seguenti serie di macchine virtuali selezionate:

* [Serie B](../sizes-b-series-burstable.md)
* [Serie DCsv2](../dcv2-series.md)
* [Serie DSv2](../dv2-dsv2-series.md) e [Serie Dsv3](../dv3-dsv3-series.md)
* [Serie Esv3](../ev3-esv3-series.md)
* [Serie Fsv2](../fsv2-series.md)
* [Serie GS](../sizes-previous-gen.md#gs-series)
* [Serie HB](../hb-series.md)
* [Serie HC](../hc-series.md)
* [Serie Ls](../sizes-previous-gen.md#ls-series) e [Serie Lsv2](../lsv2-series.md)
* [Serie M](../m-series.md)
* [Serie Mv2](../mv2-series.md)<sup>1</sup>
* [Serie NCv2](../ncv2-series.md) e [Serie NCv3](../ncv3-series.md)
* [Serie ND](../nd-series.md)
* [Serie NVv3](../nvv3-series.md)

<sup>1</sup> La serie Mv2 non supporta le immagini di macchina virtuale di prima generazione e supporta solo un subset di immagini di seconda generazione. Per informazioni dettagliate, vedere la [documentazione della serie Mv2](../mv2-series.md).

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Immagini di macchine virtuali di seconda generazione in Azure Marketplace

Le macchine virtuali di seconda generazione supportano le seguenti immagini del Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>Macchine virtuali locali e macchine virtuali Azure di seconda generazione

Azure attualmente non supporta alcune funzionalità supportate da Hyper-V in locale per le macchine virtuali di seconda generazione.

| Funzionalità di seconda generazione                | Hyper-V in locale | Azure |
|-------------------------------------|---------------------|-------|
| Avvio protetto                         | :heavy_check_mark:  | :x:   |
| Macchina virtuale schermata                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Sicurezza basata sulla virtualizzazione (VBS) | :heavy_check_mark:  | :x:   |
| Formato VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funzionalità e caratteristiche

### <a name="generation-1-vs-generation-2-features"></a>Funzionalità di prima e seconda generazione

| Funzionalità | Prima generazione | Seconda generazione |
|---------|--------------|--------------|
| Avvio             | PCAT         | UEFI |
| Controller del disco | IDE          | SCSI |
| Dimensioni delle macchine virtuali         | Tutte le dimensioni delle macchine virtuali | Solo le macchine virtuali che supportano l'archiviazione Premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Funzionalità di prima e seconda generazione

| Funzionalità | Prima generazione | Seconda generazione |
|------------|--------------|--------------|
| Disco del sistema operativo superiore a 2 TB                    | :x:                | :heavy_check_mark: |
| Disco personalizzato/immagine/scambio sistema operativo         | :heavy_check_mark: | :heavy_check_mark: |
| Supporto del set di scalabilità di macchine virtuali | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Backup/ripristino                    | :heavy_check_mark: | :heavy_check_mark: |
| Raccolta di immagini condivise              | :heavy_check_mark: | :heavy_check_mark: |
| Crittografia dischi di Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Creazione di una macchina virtuale di seconda generazione

### <a name="marketplace-image"></a>Immagine del Marketplace

Nel portale di Azure o nell'interfaccia della riga di comando di Azure è possibile creare macchine virtuali di seconda generazione da un'immagine del Marketplace che supporta l'avvio UEFI.

#### <a name="azure-portal"></a>Portale di Azure

Di seguito sono elencati i passaggi per la creazione di una macchina virtuale di generazione 2 (Gen2) nel portale di Azure.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Selezionare **Crea una risorsa**.
1. Fare clic su **Vedi tutto** in Azure Marketplace sulla sinistra.
1. Selezionare un'immagine che supporta la seconda generazione.
1. Fare clic su **Crea**.
1. Nella scheda **Avanzate**, nella sezione **Generazione macchina virtuale** selezionare l'opzione **Gen 2**.
1. Nella scheda **Impostazioni di base**, in **Dettagli istanza** passare a **Dimensioni** e aprire il pannello **Selezionare le dimensioni di macchina virtuale**.
1. Selezionare una [macchina virtuale di seconda generazione supportata](#generation-2-vm-sizes).
1. Eseguire le operazioni del [flusso di creazione del portale di Azure](quick-create-portal.md) per completare la creazione della macchina virtuale.

![Selezionare la macchina virtuale di prima o seconda generazione](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

È anche possibile usare PowerShell per creare una macchina virtuale facendo direttamente riferimento allo SKU di prima o seconda generazione.

Ad esempio, usare il cmdlet di PowerShell seguente per ottenere un elenco degli SKU nell'offerta `WindowsServer`.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Se si crea una macchina virtuale con Windows Server 2012 come sistema operativo, si selezionerà lo SKU della macchina virtuale di prima generazione (BIOS) o di seconda generazione (UEFI), che è il seguente:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Per un elenco aggiornato delle immagini di Marketplace supportate, vedere la sezione [Funzionalità e caratteristiche](#features-and-capabilities).

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per visualizzare le immagini di seconda generazione disponibili, elencate per **entità di pubblicazione**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Immagine gestita o disco gestito

È possibile creare una macchina virtuale di seconda generazione da un'immagine gestita o da un disco gestito, con le stesse modalità usate per creare una macchina virtuale di prima generazione.

### <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali

È anche possibile creare macchine virtuali di seconda generazione usando i set di scalabilità di macchine virtuali. Nell'interfaccia della riga di comando di Azure usare i set di scalabilità di Azure per creare macchine virtuali di seconda generazione.

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Le macchine virtuali di seconda generazione sono disponibili in tutte le aree di Azure?**  
    Sì. Tuttavia, non tutte le [dimensioni delle macchine virtuali di seconda generazione](#generation-2-vm-sizes) sono disponibili in tutte le aree. La disponibilità della macchina virtuale di seconda generazione dipende dalla disponibilità delle dimensioni della macchina virtuale.

* **Esiste una differenza di prezzo tra le macchine virtuali di prima e di seconda generazione?**  
    No.

* **Se si ha un file con estensione vhd di macchina virtuale locale di seconda generazione, è possibile usarlo per creare una macchina virtuale di seconda generazione in Azure?**
  Sì, è possibile importare il file con estensione vhd di seconda generazione in Azure e usarlo per creare una macchina virtuale di seconda generazione. A tale scopo, seguire questa procedura:
    1. Caricare il file con estensione vhd in un account di archiviazione nella stessa area in cui si vuole creare la macchina virtuale.
    1. Creare un disco gestito dal file con estensione vhd. Impostare la proprietà Hyper-VGeneration su V2. I comandi di PowerShell seguenti impostano la proprietà Hyper-V Generation durante la creazione del disco gestito.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Quando il disco è disponibile, creare una macchina virtuale allegando questo disco. La macchina virtuale creata sarà una macchina virtuale di seconda generazione.
    Dopo aver creato la macchina virtuale di seconda generazione, è possibile generalizzare l'immagine di questa macchina virtuale. Generalizzando l'immagine è possibile usarla per creare più macchine virtuali.

* **Come si incrementano le dimensioni del disco del sistema operativo?**  
  I dischi del sistema operativo di dimensioni superiori a 2 TB sono una funzionalità nuova per le macchine virtuali di seconda generazione. Per impostazione predefinita, i dischi del sistema operativo per le macchine virtuali di seconda generazione hanno dimensioni inferiori a 2 TB. È possibile aumentare le dimensioni del disco fino a un massimo consigliato di 4 TB. Per incrementare le dimensioni del disco del sistema operativo, usare l'interfaccia della riga di comando di Azure o il portale di Azure. Per informazioni su come espandere i dischi a livello di codice, vedere [Ridimensionare un disco](expand-disks.md).

  Per incrementare le dimensioni del disco del sistema operativo dal portale di Azure:

  1. Nella portale di Azure passare alla pagina delle proprietà della macchina virtuale.
  1. Per arrestare e deallocare la macchina virtuale, selezionare il pulsante **Interrompi**.
  1. Nella sezione **Dischi** selezionare il disco del sistema operativo di cui si vuole aumentare la capacità.
  1. Nella sezione**Dischi** selezionare **Configurazione** e aggiornare **Dimensioni** al valore desiderato.
  1. Tornare alla pagina delle proprietà della macchina virtuale e selezionare **Avvia** per avviare la macchina virtuale.

  Potrebbe essere visualizzato un avviso per i dischi del sistema operativo con dimensioni superiori a 2 TB. L'avviso non è valido per le macchine virtuali di seconda generazione. Tuttavia, le dimensioni del disco del sistema operativo maggiori di 4 TB sono *sconsigliate*.

* **Le macchine virtuali di seconda generazione supportano la rete accelerata?**  
    Sì. Per altre informazioni, vedere [Creare una VM con rete accelerata](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **VHDX è supportato nelle macchine virtuali di seconda generazione?**  
    No, le macchine virtuali di seconda generazione supportano solo VHD.

* **Le macchine virtuali di seconda generazione supportano Archiviazione su disco Ultra di Azure?**  
    Sì.

* **È possibile eseguire la migrazione di una macchina virtuale dalla prima alla seconda generazione?**  
    No, non è possibile modificare la generazione di una macchina virtuale dopo averla creata. Se è necessario passare da una generazioni di macchine virtuali a un'altra, creare una nuova macchina virtuale di una generazione diversa.

* **Perché le dimensioni della macchina virtuale non sono abilitate nel selettore di dimensioni quando si prova a creare una macchina virtuale di seconda generazione?**

    Questo problema può essere risolto eseguendo le operazioni seguenti:

    1. Verificare che la proprietà **Generazione macchina virtuale** sia impostata su **Gen 2** nella scheda **Avanzate**.
    1. Assicurarsi di cercare [dimensioni della macchina virtuale che supportano le macchine virtuali di seconda generazione](#generation-2-vm-sizes).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [macchine virtuali di seconda generazione in Hyper-V](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
