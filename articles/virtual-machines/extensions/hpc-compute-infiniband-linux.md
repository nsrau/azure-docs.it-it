---
title: Estensione driver InfiniBand-VM Linux di Azure
description: Microsoft Azure estensione per l'installazione di driver InfiniBand nelle VM di calcolo serie H e N che eseguono Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: eb06f98d1a6e9b76c321e3d202043d656a2d94eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87829051"
---
# <a name="infiniband-driver-extension-for-linux"></a>Estensione driver InfiniBand per Linux

Questa estensione installa i driver InfiniBand OFED nelle VM InfiniBand e SR-IOV-Enabled (' r ') della [serie H](../sizes-hpc.md) e della [serie N](../sizes-gpu.md) che eseguono Linux. A seconda della famiglia di VM, l'estensione installa i driver appropriati per la scheda di interfaccia di rete Connect-X.

Le istruzioni per l'installazione manuale dei driver OFED sono disponibili [qui](../workloads/hpc/enable-infiniband.md#manual-installation).

È disponibile anche un'estensione per installare i driver InfiniBand per le [macchine virtuali Windows](hpc-compute-infiniband-windows.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

Questa estensione supporta le distribuzioni dei sistemi operativi seguenti, in base al supporto dei driver per la versione del sistema operativo specifica.

| Distribuzione | Versione |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

### <a name="internet-connectivity"></a>Connettività Internet

Per l'estensione Microsoft Azure per i driver InfiniBand è necessario che la macchina virtuale di destinazione sia connessa a e che disponga dell'accesso a Internet.

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON riportato di seguito mostra lo schema dell'estensione.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | Data |
| publisher | Microsoft.HpcCompute | string |
| type | InfiniBandDriverLinux | string |
| typeHandlerVersion | 1.1 | INT |



## <a name="deployment"></a>Distribuzione


### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager 

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli rappresentano la scelta migliore quando si distribuiscono una o più macchine virtuali per cui è necessaria una configurazione post-distribuzione.

La configurazione JSON per un'estensione macchina virtuale può essere annidata nella risorsa della macchina virtuale o posizionata nel livello radice o nel livello superiore di un modello JSON di Gestione risorse. Il posizionamento della configurazione JSON influisce sul valore del nome e del tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Impostare il nome e il tipo per le risorse figlio). 

L'esempio seguente presuppone che l'estensione sia annidata all'interno della risorsa della macchina virtuale. Quando la risorsa di estensione viene nidificata, JSON viene inserito nell'oggetto `"resources": []` della macchina virtuale.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Aggiungere un'estensione a un set di scalabilità di macchine virtuali

L'esempio seguente installa la versione più recente dell'estensione InfiniBandDriverLinux 1,1 in tutte le VM con supporto per RDMA in un set di scalabilità di macchine virtuali esistente denominato *myVMSS* distribuito nel gruppo di risorse denominato *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente. Fare riferimento a questo file per tenere traccia dello stato dell'installazione, nonché per la risoluzione di eventuali errori.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Codici di uscita

La tabella seguente descrive il significato e le azioni consigliate in base ai codici di uscita del processo di installazione dell'estensione.

| Codice di uscita | Significato | Azione possibile |
| :---: | --- | --- |
| 0 | Operazione riuscita |
| 1 | Uso non corretto dell'estensione | Controllare il log di output dell'esecuzione |
| 10 | Linux Integration Services per Hyper-V e Azure non disponibile o installato | Controllare l'output di lspci |
| 11 | Impossibile trovare InfiniBand Mellanox in questa dimensione della macchina virtuale | Usare una [dimensione e un sistema operativo della macchina virtuale supportati](../sizes-hpc.md) |
| 12 | Offerta di immagine non supportata |
| 13 | Dimensioni della macchina virtuale non supportate | Usare una VM serie [H](../sizes-hpc.md) abilitata per InfiniBand (' r ' [) e serie n per](../sizes-gpu.md)la distribuzione |
| 14 | Operazione conclusa | Controllare il log di output dell'esecuzione |


### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tramite il [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/). Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle dimensioni abilitate per InfiniBand ("r"), vedere la [serie H](../sizes-hpc.md) e le VM [serie N](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Altre informazioni sulle estensioni e sulle funzionalità delle macchine virtuali Linux](features-linux.md)