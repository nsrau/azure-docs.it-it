---
title: Estensione driver InfiniBand-VM Windows di Azure
description: Microsoft Azure estensione per l'installazione di driver InfiniBand nelle VM di calcolo serie H e N che eseguono Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: c0ec18ae4a7d6020299660adbeba6f993cd4eeca
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966021"
---
# <a name="infiniband-driver-extension-for-windows"></a>Estensione driver InfiniBand per Windows

Questa estensione installa i driver InfiniBand ND (per i driver non SR-IOV abilitati) e OFED ( [per le macchine](../sizes-hpc.md) virtuali SR-IOV-Enabled) ("r" sizes) e le VM [serie N](../sizes-gpu.md) che eseguono Windows. A seconda della famiglia di VM, l'estensione installa i driver appropriati per la scheda di interfaccia di rete Connect-X.

È disponibile anche un'estensione per installare i driver InfiniBand per le [macchine virtuali Linux](hpc-compute-infiniband-linux.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

Questa estensione supporta le distribuzioni dei sistemi operativi seguenti, in base al supporto dei driver per la versione del sistema operativo specifica.

| Distribuzione | Versione |
|---|---|
| Windows 10 | Core |
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012 R2 | Core |
| Windows Server 2012 | Core |

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| type | InfiniBandDriverWindows | string |
| typeHandlerVersion | 1.2 | INT |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Aggiungere un'estensione a un set di scalabilità di macchine virtuali

L'esempio seguente installa la versione più recente dell'estensione InfiniBandDriverWindows 1,2 in tutte le VM con supporto per RDMA in un set di scalabilità di macchine virtuali esistente denominato *myVMSS* distribuito nel gruppo di risorse denominato *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
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

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Codici di uscita

La tabella seguente descrive il significato e le azioni consigliate in base ai codici di uscita del processo di installazione dell'estensione.

| Codice di errore | Significato | Azione possibile |
| :---: | --- | --- |
| 0 | Operazione riuscita |
| 3010 | Operazione riuscita. Riavvio richiesto. |
| 100 | Operazione non supportata o non completata. | Possibili cause: la versione di PowerShell non è supportata, le dimensioni della macchina virtuale non sono una macchina virtuale abilitata per InfiniBand. errore durante il download dei dati Controllare i file di log per determinare la causa dell’errore. |
| 240, 840 | Timeout dell'operazione. | Ripetere l'operazione. |
| -1 | Si è verificata un'eccezione. | Controllare i file di log per determinare la causa dell'eccezione. |

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tramite il [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/). Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle dimensioni abilitate per InfiniBand ("r"), vedere la [serie H](../sizes-hpc.md) e le VM [serie N](../sizes-gpu.md) .

> [!div class="nextstepaction"]
> [Altre informazioni sulle estensioni e sulle funzionalità delle macchine virtuali Linux](features-linux.md)