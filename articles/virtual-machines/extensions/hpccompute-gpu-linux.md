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
ms.date: 02/11/2019
ms.author: roiyz
ms.openlocfilehash: d8b2b10f9a12fb32a522a9c87c67bb24d6d4475e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991851"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Estensione di driver GPU NVIDIA per Linux

## <a name="overview"></a>Panoramica

Questa estensione installa i driver GPU NVIDIA nelle macchine virtuali Linux serie N. A seconda della famiglia di macchine virtuali, l'estensione installa i driver CUDA o GRID. Quando si installano i driver NVIDIA con questa estensione, l'utente accetta e acconsente alle condizioni del [contratto di licenza dell'utente finale di NVIDIA](https://developer.download.nvidia.com/compute/cuda/5_5/rel/docs/EULA.pdf). Durante il processo di installazione, la macchina virtuale potrebbe venire riavviata per completare l'installazione del driver.

È anche disponibile un'estensione per installare i driver GPU NVIDIA nelle [macchine virtuali serie N di Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

Questa estensione supporta le distribuzioni dei sistemi operativi seguenti, in base al supporto dei driver per la versione del sistema operativo specifica.

| Distribuzione | Versione |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux. | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

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
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Properties

| NOME | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | stringa |
| type | NvidiaGpuDriverLinux | stringa |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Impostazioni

Tutte le impostazioni sono facoltative. In base al comportamento predefinito, il kernel non viene aggiornato se non è necessario per l'installazione del driver e vengono installati il driver supportato più recente e il toolkit CUDA (se applicabile).

| NOME | DESCRIZIONE | Default Value | Valori validi | Tipo di dati |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Aggiorna il kernel anche se non è necessario per l'installazione del driver | false | true, false | boolean |
| driverVersion | NV: Versione del driver GRID<br> NC/ND: Versione del toolkit CUDA. I driver più recenti del CUDA scelto vengono installati automaticamente. | più recenti | GRID: "410.92", "410.71", "390.75", "390.57", "390.42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | stringa |
| installCUDA | Installa il toolkit CUDA. Pertinente solo per le macchine virtuali serie NC/ND. | true | true, false | boolean |


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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'esempio seguente rispecchia gli esempi precedenti di Azure Resource Manager e PowerShell e inoltre aggiunge le impostazioni personalizzate come esempio per l'installazione di driver non predefiniti. Nello specifico, aggiorna il kernel del sistema operativo e installa il driver di una versione specifica del toolkit CUDA.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
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

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Codici di uscita

| Codice di uscita | Significato | Azione possibile |
| :---: | --- | --- |
| 0 | Operazione riuscita |
| 1 | Uso non corretto dell'estensione | Controllare il log di output dell'esecuzione |
| 10 | Linux Integration Services per Hyper-V e Azure non disponibile o installato | Controllare l'output di lspci |
| 11 | GPU NVIDIA non trovata in questa dimensione di macchina virtuale | Usare una [dimensione e un sistema operativo della macchina virtuale supportati](../linux/n-series-driver-setup.md) |
| 12 | Offerta di immagine non supportata |
| 13 | Dimensioni della macchina virtuale non supportate | Usare una Serie N per distribuire la macchina virtuale |
| 14 | Operazione conclusa | Controllare il log di output dell'esecuzione |


### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità delle macchine virtuali per Linux](features-linux.md).

Per altre informazioni sulle macchine virtuali di serie N, vedere [Dimensioni delle macchine virtuali ottimizzate per il GPU](../linux/sizes-gpu.md).
