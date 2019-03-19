---
title: Vantaggio Azure Hybrid per Windows Server | Microsoft Docs
description: Informazioni su come ottimizzare i vantaggi di Software Assurance per Windows per trasferire le licenze locali in Azure
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: 64e9350606748116d2eef247790e88ed0d576c3f
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570369"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Vantaggio Azure Hybrid per Windows Server
Per i clienti con Software Assurance, il vantaggio Azure Hybrid per Windows Server consente di usare le licenze di Windows Server locali e di eseguire macchine virtuali di Windows in Azure a costi ridotti. È possibile usare il vantaggio Azure Hybrid per Windows Server per distribuire nuove macchine virtuali con il sistema operativo Windows. Questo articolo illustra la procedura necessaria per distribuire nuove macchine virtuali con il vantaggio Azure Hybrid per Windows Server e per aggiornare le macchine virtuali in esecuzione esistenti. Per altre informazioni sulle licenze e i risparmi associati al vantaggio Azure Hybrid per Windows Server, vedere la pagina sulle [licenze disponibili per il vantaggio Azure Hybrid per Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Ogni licenza con due processori oppure ogni set di licenze con 16 core ha diritto a due istanze di un massimo di 8 core o a un'istanza di un massimo di 16 core. Il vantaggio Azure Hybrid per le licenze per l'edizione Standard può essere usato solo una volta in locale o in Azure. I vantaggi dell'edizione Datacenter permettono l'utilizzo simultaneo in locale e in Azure.
>

> [!Important]
> L'utilizzo del vantaggio Azure Hybrid per Windows Server con qualsiasi macchina virtuale che esegue il sistema operativo Windows Server è ora supportato in tutte le regioni, incluse le macchine virtuali con software aggiuntivo come SQL Server o un software di terze parti del marketplace. 
>

> [!NOTE]
> Per le macchine virtuali classiche, è supportata solo la distribuzione nuova macchina virtuale da su immagini personalizzate locali. Per usufruire delle funzionalità illustrate in questo articolo, è necessario prima eseguire la migrazione delle macchine virtuali classiche al modello Resource Manager.
>

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Modalità di utilizzo del vantaggio Azure Hybrid per Windows Server
Esistono più modi per usare macchine virtuali di Windows con il vantaggio Azure Hybrid:

1. È possibile distribuire le macchine virtuali da una delle immagini fornite di Windows Server in Azure Marketplace
2. È possibile caricare una macchina virtuale personalizzata e distribuirla usando un modello Resource Manager o Azure PowerShell
3. È possibile alternare la macchina virtuale esistente tra l'esecuzione con il vantaggio Azure Hybrid e il pagamento del costo in base al consumo per Windows Server
4. È possibile anche applicare il vantaggio Azure Hybrid per Windows Server a un set di scalabilità di macchine virtuali


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Creare una macchina virtuale con il vantaggio Azure Hybrid per Windows Server
Tutte le immagini basate sul sistema operativo Windows Server sono supportate con il vantaggio Azure Hybrid per Windows Server. È possibile usare le immagini di supporto della piattaforma Azure o caricare le immagini di Windows Server personalizzate. 

### <a name="portal"></a>Portale
Per creare una macchina virtuale con il vantaggio Azure Hybrid per Windows Server, usare l'interruttore nella sezione Risparmio sui costi.

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Modello
Nei modelli Resource Manager è necessario specificare il parametro aggiuntivo `licenseType`. Altre informazioni sulla [creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md).
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Convertire una macchina virtuale esistente per l'utilizzo con il vantaggio Azure Hybrid per Windows Server
Se si vuole convertire una macchina virtuale esistente per l'utilizzo con il Vantaggio Azure Hybrid per Windows Server, è possibile aggiornare il tipo di licenza della macchina virtuale attenendosi alle istruzioni riportate di seguito.

> [!NOTE]
> La modifica del tipo di licenza nella macchina virtuale non determina il riavvio del sistema o un'interruzione del servizio,  ma è semplicemente un aggiornamento su un flag di metadati.
> 

### <a name="portal"></a>Portale
Nel pannello della macchina virtuale del portale è possibile aggiornare la macchina virtuale per l'utilizzo con il vantaggio Azure Hybrid selezionando l'opzione "Configurazione" e modificando l'opzione "Vantaggio Azure Hybrid"

### <a name="powershell"></a>PowerShell
- Convertire una macchina virtuale Windows Server esistente affinché usi il vantaggio Azure Hybrid per Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Convertire le macchine virtuali Windows Server per ottenere il vantaggio del pagamento in base al consumo

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Convertire una macchina virtuale Windows Server esistente affinché usi il vantaggio Azure Hybrid per Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Come verificare che la macchina virtuale usi il vantaggio della licenza
Dopo aver distribuito la macchina virtuale con PowerShell, il modello Resource Manager o il portale, è possibile verificare l'impostazione con i metodi seguenti.

### <a name="portal"></a>Portale
Dal pannello della macchina virtuale nel portale, è possibile visualizzare l'interruttore del vantaggio Azure Hybrid per Windows Server selezionando la scheda "Configurazione".

### <a name="powershell"></a>PowerShell
L'esempio seguente illustra il tipo di licenza per una singola macchina virtuale
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Output:
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

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> La modifica del tipo di licenza nella macchina virtuale non determina il riavvio del sistema o un'interruzione del servizio, ma riguarda solo un flag delle licenze nei metadati.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Elenca tutte le macchine virtuali con il vantaggio Azure Hybrid per Windows Server presenti in una sottoscrizione
Per visualizzare e contare tutte le macchine virtuali distribuite con il vantaggio Azure Hybrid per Windows Server, è possibile eseguire il comando seguente dalla sottoscrizione:

### <a name="portal"></a>Portale
Dal pannello della risorsa della macchina virtuale o dei set di scalabilità di macchine virtuali è possibile visualizzare un elenco di tutte le macchine virtuali e dei tipi di licenza configurando la colonna della tabella affinché includa "Vantaggio Azure Hybrid". L'impostazione per la macchina virtuale può essere sullo stato "Abilitato", "Non è abilitato" o "Non supportato".

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Distribuire un set di scalabilità di macchine virtuali con il vantaggio Azure Hybrid per Windows Server
Nei modelli Resource Manager per set di scalabilità di macchine virtuali, è necessario specificare il parametro aggiuntivo `licenseType` nella proprietà VirtualMachineProfile. È possibile eseguire questa operazione durante la creazione o aggiornamento per il set di scalabilità tramite il modello ARM, PowerShell, CLI Azure o REST.

Nell'esempio seguente viene usato il modello ARM con un'immagine di Windows Server 2016 Datacenter:
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
Altre informazioni su come [Modificare un set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) e aggiornare il set di scalabilità.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Come risparmiare con il Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Altre informazioni sulle [Domande frequenti sul vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Altre informazioni sulle [indicazioni dettagliate delle licenze per il vantaggio Azure Hybrid per Windows Server](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Altre informazioni su come il [Vantaggio Azure Hybrid e Azure Site Recovery rendano la migrazione delle applicazioni in Azure ancora più conveniente](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Altre informazioni su [Windows 10 in Azure con Multitenant Hosting Rights](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Altre informazioni sull' [uso dei modelli di Resource Manager](../../azure-resource-manager/resource-group-overview.md)
