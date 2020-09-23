---
title: Crittografia dischi di Azure e sequenza di estensioni di set di scalabilità di macchine virtuali di Azure
description: Questo articolo illustra come abilitare la crittografia del disco Microsoft Azure per le macchine virtuali IaaS Linux.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1aff05e51bcbc99f33325efb905ade819ae22e02
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988030"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Usare Crittografia dischi di Azure con sequenziazione delle estensioni del set di scalabilità di macchine virtuali

È possibile aggiungere estensioni come crittografia dischi di Azure a un set di scalabilità di macchine virtuali di Azure in un ordine specificato. A tale scopo, usare la [sequenziazione delle estensioni](virtual-machine-scale-sets-extension-sequencing.md). 

In generale, la crittografia deve essere applicata a un disco:

- Dopo le estensioni o gli script personalizzati che preparano i dischi o i volumi.
- Prima delle estensioni o degli script personalizzati che accedono o utilizzano i dati nei dischi o volumi crittografati.

In entrambi i casi, la `provisionAfterExtensions` proprietà indica quale estensione deve essere aggiunta successivamente nella sequenza.

## <a name="sample-azure-templates"></a>Modelli di Azure di esempio

Se si vuole che crittografia dischi di Azure venga applicato dopo un'altra estensione, inserire la `provisionAfterExtensions` proprietà nel blocco di estensione AzureDiskEncryption. 

Di seguito è riportato un esempio che usa "CustomScriptExtension", uno script di PowerShell che Inizializza e formatta un disco di Windows, seguito da "AzureDiskEncryption":

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

Se si vuole che crittografia dischi di Azure venga applicato prima di un'altra estensione, inserire la `provisionAfterExtensions` proprietà nel blocco dell'estensione da seguire.

Di seguito è riportato un esempio che usa "AzureDiskEncryption" seguito da "VMDiagnosticsSettings", un'estensione che fornisce funzionalità di monitoraggio e diagnostica in una macchina virtuale di Azure basata su Windows:


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

Per un modello più dettagliato, vedere:
* Applicare l'estensione di crittografia dischi di Azure dopo uno script della shell personalizzato che formatta il disco (Linux): [deploy-extseq-linux-ADE-after-customscript.js](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla sequenziazione delle estensioni: [provisioning dell'estensione di sequenza nei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-extension-sequencing.md).
- Altre informazioni sulla `provisionAfterExtensions` proprietà: [Microsoft. Compute virtualMachineScaleSets/Extensions template Reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Crittografia dischi di Azure per set di scalabilità di macchine virtuali](disk-encryption-overview.md)
- [Crittografare set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure](disk-encryption-cli.md)
- [Crittografare set di scalabilità di macchine virtuali con Azure PowerShell](disk-encryption-powershell.md)
- [Creare e configurare un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md)
