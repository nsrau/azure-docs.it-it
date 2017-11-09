---
title: Vantaggio Azure Hybrid per Windows Server | Microsoft Docs
description: Informazioni su come ottimizzare i vantaggi di Software Assurance per Windows per trasferire le licenze locali in Azure
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/02/2017
ms.author: kmouss
ms.openlocfilehash: d47b8ab2cd6391e937fe7f9ba6eded3b89fe2c40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Vantaggio Azure Hybrid per Windows Server
Per i clienti con Software Assurance, il vantaggio Azure Hybrid per Windows Server consente di usare le licenze di Windows Server locali e di eseguire macchine virtuali di Windows in Azure a costi ridotti. È possibile usare il vantaggio Azure Hybrid per Windows Server per distribuire nuove macchine virtuali da qualsiasi immagine di Windows Server o immagine personalizzata di Windows come piattaforma supportata da Azure. È necessario tuttavia che l'immagine non venga fornita con software aggiuntivo come SQL Server o con immagini di Marketplace di terze parti. Questo articolo illustra la procedura necessaria per distribuire nuove macchine virtuali con il vantaggio Azure Hybrid per Windows Server. Per altre informazioni sulle licenze e i risparmi associati al vantaggio Azure Hybrid per Windows Server, vedere la pagina sulle [licenze disponibili per il vantaggio Azure Hybrid per Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Le immagini Windows Server "[HUB]" legacy pubblicate per i clienti con contratto Enterprise Agreement in Azure Marketplace sono state ritirate a partire dall'11/09/2017. Usare un'immagine Windows Server standard con l'opzione di risparmio sui costi del portale per il vantaggio Azure Hybrid per Windows Server. Per altre informazioni, vedere questo [articolo](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions).
>

> [!NOTE]
> Non è possibile usare il vantaggio Azure Hybrid per Windows Server con macchine virtuali su cui sono già addebitati costi per altre applicazioni software come SQL Server o per un'immagine di Marketplace di terze parti. Se si prova a convertire una macchina virtuale di Windows Server su cui sono già addebitati altri costi software, si verifica un errore 409 di questo tipo: "La modifica della proprietà 'LicenseType' non è consentita". 
>


> [!NOTE]
> Per le macchine virtuali classiche, è supportata solo la distribuzione di nuove macchine virtuali da immagini personalizzate locali. Per usufruire delle funzionalità illustrate in questo articolo, è necessario prima eseguire la migrazione delle macchine virtuali classiche al modello Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Modalità di utilizzo del vantaggio Azure Hybrid per Windows Server
Esistono più modi per usare macchine virtuali di Windows con il vantaggio Azure Hybrid:

1. È possibile distribuire le macchine virtuali da una delle [immagini di Windows Server in Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview) fornite
2. È possibile [caricare una macchina virtuale personalizzata](#upload-a-windows-vhd) e [distribuirla usando un modello Resource Manager](#deploy-a-vm-via-resource-manager) o [Azure PowerShell](#detailed-powershell-deployment-walkthrough)
4. È possibile anche distribuire un nuovo set di scalabilità di macchine virtuali con il vantaggio Azure Hybrid per Windows Server

> [!NOTE]
> Non è attualmente supportata la conversione di una macchina virtuale esistente o di un set di scalabilità di macchine virtuali per l'utilizzo con il vantaggio Azure Hybrid per Windows Server
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Distribuire una macchina virtuale da un'immagine di Windows Server Marketplace
Tutte le immagini di Windows Server disponibili in Azure Marketplace sono abilitate con il vantaggio Azure Hybrid per Windows Server; ad esempio: Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008SP1 e così via. È possibile usare queste immagini per distribuire le macchine virtuali direttamente dal portale di Azure, da modelli Resource Manager, da Azure PowerShell o da altri SDK.

È possibile distribuire queste immagini direttamente dal portale di Azure. Per l'utilizzo in modelli di Resource Manager e con Azure PowerShell, visualizzare l'elenco delle immagini come segue:

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
È possibile seguire la procedura illustrata in [Creare una macchina virtuale Windows con PowerShell](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) e passare LicenseType = "Windows_Server". Questa opzione consente di usare la licenza di Windows Server esistente in Azure.

### <a name="portal"></a>di Microsoft Azure
È possibile seguire la procedura illustrata in [Creare una macchina virtuale Windows con il portale di Azure](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) e selezionare l'opzione che consente di usare la licenza di Windows Server esistente.

## <a name="upload-a-windows-server-vhd"></a>Caricare un disco rigido virtuale di Windows Server
Per distribuire una macchina virtuale di Windows Server in Azure è prima necessario creare un disco rigido virtuale contenente la build di base di Windows. Questo disco rigido virtuale deve essere correttamente preparato con Sysprep prima di caricarlo in Azure. Sono disponibili [altre informazioni sui requisiti dei dischi rigidi virtuali e sul processo Sysprep](upload-generalized-managed.md) e [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Eseguire il backup della VM prima di eseguire Sysprep. 

Dopo aver preparato il disco rigido virtuale, caricarlo nell'account di Archiviazione di Azure come illustrato di seguito:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server e Dynamics possono usare anche le licenza di Software Assurance. È necessario preparare l'immagine di Windows Server installando i componenti dell'applicazione e specificando i codici di licenza corrispondenti e quindi caricando l'immagine del disco in Azure. Esaminare la documentazione appropriata per l'esecuzione di SysPrep con l'applicazione, ad esempio [Considerazioni sull'installazione di SQL Server tramite SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) o [Creare un'immagine di riferimento di SharePoint Server 2016 con Sysprep](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Altre informazioni sul [caricamento del disco rigido virtuale in Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)


## <a name="deploy-a-vm-via-resource-manager-template"></a>Distribuire una macchina virtuale con il modello di Resource Manager
Nei modelli Resource Manager è necessario specificare il parametro aggiuntivo `licenseType`. Altre informazioni sulla [creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md). Dopo aver caricato il disco rigido virtuale in Azure, modificare il modello di Resource Manager per includere il tipo di licenza come parte del provider di calcolo e distribuire il modello come di consueto:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Distribuire una macchina virtuale con l'avvio rapido di PowerShell
Quando si distribuisce la macchina virtuale Windows Server con PowerShell, è presente un parametro aggiuntivo `-LicenseType`. Dopo aver caricato il disco rigido virtuale in Azure, creare una nuova VM usando `New-AzureRmVM` e specificare il tipo di licenza come segue:

Per Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

È disponibile una guida più descrittiva con i diversi passaggi per [creare una macchina virtuale Windows usando Resource Manager e PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale usi il vantaggio della licenza
Dopo aver distribuito la macchina virtuale con PowerShell, il modello Resource Manager o il portale, verificare il tipo di licenza con `Get-AzureRmVM` come indicato di seguito:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

L'output è simile all'esempio seguente per Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

L'output è diverso da quello della seguente macchina virtuale distribuita senza licenza per il vantaggio Azure Hybrid per Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Elencare tutte le macchine virtuali con il vantaggio Azure Hybrid per Windows Server presenti in una sottoscrizione

Per visualizzare e contare tutte le macchine virtuali distribuite con il vantaggio Azure Hybrid per Windows Server, è possibile eseguire il comando seguente dalla sottoscrizione:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuire un set di scalabilità di macchine virtuali con il vantaggio Azure Hybrid per Windows Server
Nei modelli Resource Manager per set di scalabilità di macchine virtuali, è necessario specificare il parametro aggiuntivo `licenseType`. Altre informazioni sulla [creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md). Modificare il modello Resource Manager per includere la proprietà licenseType come parte dell'elemento virtualMachineProfile del set di scalabilità e distribuire il modello come di consueto. Vedere l'esempio seguente che usa l'immagine di Windows Server 2016:


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
È possibile anche [Creare e distribuire un set di scalabilità di macchine virtuali](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) e impostare la proprietà LicenseType.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [licenze per il vantaggio Azure Hybrid per Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Altre informazioni sull' [uso dei modelli di Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Altre informazioni su come il [vantaggio Azure Hybrid e Azure Site Recovery rendono la migrazione delle applicazioni in Azure ancora più conveniente](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

Altre informazioni sulle [Domande frequenti](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
