---
title: Estensione driver GPU AMD-VM Windows di Azure
description: Microsoft Azure estensione per l'installazione di driver GPU AMD in macchine virtuali della serie NVv4 che eseguono Windows.
services: virtual-machines-windows
documentationcenter: ''
author: vikancha
manager: jkabat
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 0819eec5275fa9783fff363cabbd8c3ed22b7cd6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119553"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Estensione driver GPU AMD per Windows

Questo articolo fornisce una panoramica dell'estensione della macchina virtuale per distribuire driver GPU AMD in macchine virtuali della [serie NVv4](https://docs.microsoft.com/azure/virtual-machines/nvv4-series) di Windows. Quando si installano driver AMD con questa estensione, si accettano e si accettano le condizioni del contratto di [licenza con l'utente finale di AMD](https://amd.com/radeonsoftwarems). Durante il processo di installazione, la macchina virtuale potrebbe venire riavviata per completare l'installazione del driver.

Le istruzioni per l'installazione manuale dei driver e le versioni supportate correnti sono disponibili [qui](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup).

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

Questa estensione supporta i seguenti sistemi operativi:

| Distribuzione | Version |
|---|---|
| Windows 10 EMS | Compilazione 1903 |
| Windows 10 | Compilazione 1809 |
| Windows Server 2016 | Core |
| Windows Server 2019 | Core |

### <a name="internet-connectivity"></a>Connettività Internet

Per l'estensione Microsoft Azure per i driver GPU AMD è necessario che la macchina virtuale di destinazione sia connessa a Internet e che disponga dell'accesso.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| type | AmdGpuDriverWindows | string |
| typeHandlerVersion | 1.0 | INT |


## <a name="deployment"></a>Distribuzione

### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager 

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli rappresentano la scelta migliore quando si distribuiscono una o più macchine virtuali per cui è necessaria una configurazione post-distribuzione.

La configurazione JSON per un'estensione macchina virtuale può essere annidata nella risorsa della macchina virtuale o posizionata nel livello radice o nel livello superiore di un modello JSON di Gestione risorse. Il posizionamento della configurazione JSON influisce sul valore del nome e del tipo di risorsa. Per altre informazioni, vedere [impostare il nome e il tipo per le risorse figlio](../../azure-resource-manager/resource-manager-template-child-resource.md). 

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
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

L'output dell'esecuzione dell'estensione viene registrato nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Codici di errore

| Codice di errore | Significato | Azione possibile |
| :---: | --- | --- |
| 0 | Operazione riuscita |
| 1 | Operazione riuscita. Riavvio richiesto. |
| 100 | Operazione non supportata o non completata. | Possibili cause: versione di PowerShell non supportata, la macchina virtuale non è una macchina virtuale serie N, errore di download dei dati. Controllare i file di log per determinare la causa dell’errore. |
| 240, 840 | Timeout dell'operazione. | Ripetere l'operazione. |
| -1 | Si è verificata un'eccezione. | Controllare i file di log per determinare la causa dell'eccezione. |
| -5x | Operazione interrotta a causa di un riavvio in sospeso. | Riavviare la macchina virtuale. L'installazione continuerà dopo il riavvio. La disinstallazione deve essere richiamata manualmente. |


### <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, è possibile contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare Ottieni supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle estensioni, vedere [Estensioni e funzionalità della macchina virtuale per Windows](features-windows.md).

Per altre informazioni sulle macchine virtuali di serie N, vedere [Dimensioni delle macchine virtuali ottimizzate per il GPU](../windows/sizes-gpu.md).
