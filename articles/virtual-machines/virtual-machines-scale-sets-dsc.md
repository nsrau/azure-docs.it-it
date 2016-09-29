<properties
   pageTitle="Utilizzo della configurazione di stato scelta con i set di scalabilità di macchine virtuali | Microsoft Azure"
   description="Utilizzo dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="zachal"/>

# Utilizzo dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure

I [set di scalabilità di macchine virtuali (VMSS)](virtual-machines-windows-vmss-powershell-creating.md) possono essere usati con il gestore dell'estensione [Configurazione dello stato desiderato (DSC)](virtual-machines-windows-extensions-dsc-overview.md). VMSS viene usato per configurare la distribuzione delle macchine virtuali per soddisfare il carico previsto. DSC viene usato per la configurazione delle VM mano a mano che sono in linea, in modo che eseguano il software di produzione.

## Differenze tra la distribuzione di VM e VMSS

La struttura del modello sottostante alle VMSS è diversa da quella di una singola VM. In particolare, una singola VM distribuisce le estensioni nel nodo "virtualMachines". È presente una voce del tipo "extensions" nella quale DSC viene aggiunto al modello:

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Il nodo VMSS ha una sezione "properties" con gli attributi "VirtualMachineProfile", "extensionProfile". DSC viene aggiunto sotto "extensions":

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.9",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## Comportamento di VMSS

Il comportamento di VMSS è identico a quello di una singola macchina virtuale. Quando viene creata una nuova VM, il provisioning viene automaticamente eseguito con l'estensione DSC. Se l'estensione richiede una versione più recente del file WMF, la VM verrà riavviata prima di passare online. Una volta online, il file con estensione zip di configurazione DSC viene scaricato e ne viene eseguito il provisioning nella VM. Altre informazioni sono reperibili nella [panoramica sull'estensione DSC di Azure](virtual-machines-windows-extensions-dsc-overview.md).

<!---HONumber=AcomDC_0914_2016-->