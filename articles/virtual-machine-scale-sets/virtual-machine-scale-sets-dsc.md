<properties
   pageTitle="Utilizzo della configurazione di stato scelta con i set di scalabilità di macchine virtuali | Microsoft Azure"
   description="Utilizzo dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>  

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>  

# Utilizzo dei set di scalabilità di macchine virtuali con l'estensione DSC di Azure

I [set di scalabilità di macchine virtuali (VMSS)](virtual-machine-scale-sets-overview.md) possono essere usati con il gestore dell'estensione [Configurazione dello stato desiderato (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). Il set di scalabilità di macchine virtuali consente di distribuire e gestire un numero elevato di macchine virtuali e di aumentare o ridurre la dimensione in risposta al carico. DSC viene usato per la configurazione delle VM mano a mano che sono in linea, in modo che eseguano il software di produzione.

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

Il comportamento di VMSS è identico a quello di una singola macchina virtuale. Quando viene creata una nuova VM, il provisioning viene automaticamente eseguito con l'estensione DSC. Se l'estensione richiede una versione più recente del file WMF, la VM verrà riavviata prima di passare online. Una volta online, il file con estensione zip di configurazione DSC viene scaricato e ne viene eseguito il provisioning nella VM. Altre informazioni sono reperibili nella [panoramica sull'estensione DSC di Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## Passaggi successivi ##
Esaminare il [modello di Azure Resource Manager per l'estensione DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Informazioni su come l'[estensione DSC gestisce in modo sicuro le credenziali](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md).

Per altre informazioni sul gestore dell'estensione DSC, vedere [Introduzione al gestore dell'estensione DSC (Desired State Configuration) di Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

Per altre informazioni su PowerShell DSC, [vedere il centro di documentazione di PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

<!---HONumber=AcomDC_0928_2016-->