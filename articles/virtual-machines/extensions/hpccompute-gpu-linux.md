---
title: 'Estensione per il driver GPU NVIDIA: le macchine virtuali Linux di Azure | Microsoft Docs'
description: Estensione di Microsoft Azure per l'installazione dei driver GPU NVIDIA nelle macchine virtuali che eseguono Linux con risorse di calcolo della serie N.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: danis
ms.openlocfilehash: 1febf81745ed804de59113da4f48376e9564b68f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449189"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Estensione di driver GPU NVIDIA per Linux

## <a name="overview"></a>Panoramica

Questa estensione installa i driver GPU NVIDIA nelle macchine virtuali Linux serie N. A seconda della famiglia di macchine virtuali, l'estensione installa i driver CUDA o GRID. Quando si installano i driver NVIDIA con questa estensione, l'utente accetta e acconsente ai termini del contratto di licenza dell'utente finale di NVIDIA. Durante il processo di installazione, la macchina virtuale potrebbe venire riavviata per completare l'installazione del driver.

È anche disponibile un'estensione per installare i driver GPU NVIDIA nelle [macchine virtuali serie N di Windows](hpccompute-gpu-windows.md).

I termini del contratto di licenza dell'utente finale di NVIDIA si trovano qui: https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>prerequisiti

### <a name="operating-system"></a>Sistema operativo

Questa estensione supporta i seguenti sistemi operativi:

| Distribuzione | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4 |
| Linux: CentOS | 7.3, 7.4 |

### <a name="internet-connectivity"></a>Connettività Internet

L'estensione di Microsoft Azure per i driver GPU NVIDIA richiede che la macchina virtuale di destinazione sia connessa e abbia accesso a Internet.

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>Valori delle proprietà

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | stringa |
| type | NvidiaGpuDriverLinux | stringa |
| typeHandlerVersion | 1.0 | int |


## <a name="deployment"></a>Distribuzione


### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager 

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli rappresentano la scelta migliore quando si distribuiscono una o più macchine virtuali per cui è necessaria una configurazione post-distribuzione.

La configurazione JSON per un'estensione macchina virtuale può essere annidata nella risorsa della macchina virtuale o posizionata nel livello radice o nel livello superiore di un modello JSON di Gestione risorse. Il posizionamento della configurazione JSON influisce sul valore del nome e del tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/resource-manager-template-child-resource.md) (Impostare il nome e il tipo per le risorse figlio). 

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Codici di uscita

| Codice di uscita | Significato | Azione possibile |
| :---: | --- | --- |
| 0 | Operazione riuscita |
| 1 | Uso incorretto dell'estensione. | Contattare il supporto con il log di output di esecuzione. |
| 10 | Linux Integration Services per Hyper-V e Azure non disponibile o installato. | Controllare l'output di lspci. |
| 11 | GPU NVIDIA non trovato in questa dimensione di macchina virtuale. | Usare una [dimensione e un sistema operativo della macchina virtuale supportati](../linux/n-series-driver-setup.md). |
| 14 | Operazione conclusa | |
| 21 | Aggiornamento non riuscito in Ubuntu | Controllare l'output di "sudo apt-get update" |


### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Linux](features-linux.md).

Per altre informazioni sulle macchine virtuali di serie N, vedere [Dimensioni delle macchine virtuali ottimizzate per il GPU](../linux/sizes-gpu.md).