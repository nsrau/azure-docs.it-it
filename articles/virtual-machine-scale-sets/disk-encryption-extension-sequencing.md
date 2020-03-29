---
title: Crittografia disco di Azure e scalabilità della macchina virtuale di Azure imposta la sequenza di estensioneAzure Disk Encryption and Azure virtual machine scale sets extension sequencing
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279023"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Usare Crittografia dischi di Azure con sequenziazione delle estensioni del set di scalabilità di macchine virtuali

Estensioni come la crittografia del disco di Azure possono essere aggiunte a un set di scalabilità delle macchine virtuali di Azure in un ordine specificato. A tale scopo, utilizzare [la sequenza delle estensioni](virtual-machine-scale-sets-extension-sequencing.md). 

In generale, la crittografia deve essere applicata a un disco:In general, encryption should be applied to a disk:

- Dopo estensioni o script personalizzati che preparano i dischi o i volumi.
- Prima delle estensioni o degli script personalizzati che accedono o utilizzano i dati nei volumi o nei dischi crittografati.

In entrambi i `provisionAfterExtensions` casi, la proprietà indica quale estensione deve essere aggiunta in un secondo momento nella sequenza.

## <a name="sample-azure-templates"></a>Modelli di Azure di esempio

Se si vuole applicare Crittografia disco di Azure `provisionAfterExtensions` dopo un'altra estensione, inserire la proprietà nel blocco di estensione AzureDiskEncryption.If you wish to have Azure Disk Encryption applied after another extension, put the property in the AzureDiskEncryption extension block. 

Di seguito è riportato un esempio di utilizzo di "CustomScriptExtension", uno script di PowerShell che inizializza e formatta un disco di Windows, seguito da "AzureDiskEncryption":Here is an example using "CustomScriptExtension", a Powershell script that initializes and formats a Windows disk, followed by "AzureDiskEncryption":

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

Se si vuole applicare Crittografia disco di Azure `provisionAfterExtensions` prima di un'altra estensione, inserire la proprietà nel blocco dell'estensione da seguire.

Ecco un esempio di utilizzo di "AzureDiskEncryption" seguito da "VMDiagnosticsSettings", un'estensione che fornisce funzionalità di monitoraggio e diagnostica in una macchina virtuale di Azure basata su Windows:Here is an example using "AzureDiskEncryption" followed by "VMDiagnosticsSettings", an extension that provides monitoring and diagnostics capabilities on a Windows-based Azure VM:


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

Per un modello più approfondito, vedere:For a more in-depth template, see:
* Applicare l'estensione di Crittografia disco di Azure dopo uno script della shell personalizzato che formatta il disco (Linux): [deploy-extseq-linux-ADE-after-customscript.jsonApply](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json) the Azure Disk Encryption extension after a custom shell script that formats the disk (Linux): deploy-extseq-linux-ADE-after-customscript.json


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla sequenza delle estensioni: sequenza di provisioning delle estensioni nei set di [scalabilità delle macchine virtuali.](virtual-machine-scale-sets-extension-sequencing.md)
- Ulteriori informazioni `provisionAfterExtensions` sulla proprietà: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Crittografia dischi di Azure per set di scalabilità di macchine virtuali](disk-encryption-overview.md)
- [Crittografare set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure](disk-encryption-cli.md)
- [Crittografare set di scalabilità di macchine virtuali con Azure PowerShell](disk-encryption-powershell.md)
- [Creare e configurare un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md)
