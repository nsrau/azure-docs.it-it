---
title: Scalabilità di macchine virtuali di Azure e crittografia dischi di Azure imposta la sequenziazione di estensione
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498146"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Usare crittografia dischi di Azure con scalabilità di macchine virtuali impostato la sequenziazione di estensione

Le estensioni, ad esempio crittografia dischi di Azure possono essere aggiunto a un scalabilità di macchine virtuali di Azure impostata in un ordine specifico. A tale scopo, usare [sequenziazione di estensione](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

In generale, la crittografia deve essere applicata a un disco:

- Dopo le estensioni o gli script personalizzati per preparare i dischi o volumi.
- Prima di estensioni o gli script personalizzati che accedono o utilizzano i dati nel dischi crittografati o i volumi.

In entrambi i casi il `provisionAfterExtensions` proprietà definisce quale estensione deve essere aggiunto in un secondo momento nella sequenza.

## <a name="sample-azure-templates"></a>Modelli di Azure di esempio

Se si vuole che applicare crittografia dischi di Azure dopo un'altra estensione, inserire il `provisionAfterExtensions` proprietà nel blocco di estensione AzureDiskEncryption. 

Ecco un esempio di utilizzo "CustomScriptExtension", uno script di Powershell che consente di inizializzare e formatta un disco di Windows, seguito da "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Se si vuole che applicare crittografia dischi di Azure prima di un'altra estensione, inserire il `provisionAfterExtensions` proprietà nel blocco dell'estensione da seguire.

Ecco un esempio di utilizzo "AzureDiskEncryption" seguito da "VMDiagnosticsSettings", un'estensione che fornisce il monitoraggio e le funzionalità di diagnostica in una VM di Azure basata su Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Per altri modelli approfonditi, vedere:
* Applicare l'estensione crittografia dischi di Azure dopo uno script shell personalizzata che formatta il disco (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Applicare l'estensione crittografia dischi di Azure dopo uno script Powershell personalizzato che consente di inizializzare e formatta il disco floppy (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Applicare l'estensione crittografia dischi di Azure prima di uno script Powershell personalizzato che consente di inizializzare e formatta il disco floppy (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla sequenziazione di estensioni: [Sequenziazione di estensione per il provisioning nel set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Altre informazioni sul `provisionAfterExtensions` proprietà: [Riferimento di modello virtualMachineScaleSets/le estensioni Microsoft. COMPUTE](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
