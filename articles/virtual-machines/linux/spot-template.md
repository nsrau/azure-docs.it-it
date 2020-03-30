---
title: Usare un modello per distribuire macchine virtuali di Azure Spot (anteprima)Use a template to deploy Azure Spot VMs (Preview)
description: Informazioni su come usare un modello per distribuire macchine virtuali Spot per risparmiare sui costi.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 0e635fe7ce9b442a9cc8f0fdf614feef5a3a756a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082796"
---
# <a name="deploy-spot-vms-using-a-resource-manager-template"></a>Distribuire macchine virtuali spot usando un modello di Resource ManagerDeploy Spot VMs using a Resource Manager template

L'uso di [Spot VMs](spot-vms.md) consente di sfruttare la nostra capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere le macchine virtuali Spot.At any in time when Azure needs the capacity back, the Azure infrastructure will evict Spot VMs. Pertanto, le macchine virtuali Spot sono ideali per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di elaborazione di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Spot sono variabili, in base all'area e all'SKU. Per altre informazioni, vedere Prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

È possibile impostare un prezzo massimo che si è disposti a pagare, all'ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Spot può essere impostato in dollari USA (USD), usando fino a 5 cifre decimali. Ad esempio, `0.98765`il valore sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il `-1`prezzo massimo su , la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo per la macchina virtuale sarà il prezzo corrente per Spot o il prezzo per una macchina virtuale standard, che è sempre inferiore, purché siano disponibili capacità e quote. Per altre informazioni sull'impostazione del prezzo massimo, vedere [Spot VMs - Pricing](spot-vms.md#pricing).

> [!IMPORTANT]
> Le istanze spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="use-a-template"></a>Usare un modello

Per le distribuzioni`"apiVersion": "2019-03-01"` di modelli Spot, usare o versione successiva. Aggiungere `priority`le `evictionPolicy` `billingProfile` proprietà e a nel modello:

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Ecco un modello di esempio con le proprietà aggiunte per una macchina virtuale Spot.Here is a sample template with the added properties for a Spot VM. Sostituire i nomi delle `<password>` risorse con nomi personalizzati e con una password per l'account amministratore locale nella macchina virtuale.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una macchina virtuale Spot usando [Azure PowerShell](../windows/spot-powershell.md) o l'interfaccia della riga di comando di Azure.You can also create a Spot VM using Azure PowerShell or the [Azure CLI.](spot-cli.md)

Se si verifica un errore, vedere [Codici di errore](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).