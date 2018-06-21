---
title: Procedura di aggiornamento/migrazione dell'unità SKU per PrimaryNodeType a SKU di livello superiore | Microsoft Docs
description: Informazioni sull'aggiornamento o la migrazione dell'unità SKU per PrimaryNodeType a SKU di livello superiore usando i comandi PowerShell e dell'interfaccia della riga di comando.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 96956543a44b6d5d967e3bae3fd833b08baf3d6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642734"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Aggiornare/eseguire la migrazione dell'unità SKU per il tipo di nodo primario a SKU di livello superiore

In questo articolo viene descritto come aggiornare/eseguire la migrazione dell'unità SKU per il tipo di nodo primario del cluster Service Fabric a SKU di livello superiore usando Azure PowerShell

## <a name="add-a-new-virtual-machine-scale-set"></a>Aggiungere un nuovo set di scalabilità di macchine virtuali

Distribuire un nuovo set di scalabilità di macchine virtuali e Load Balancer. La configurazione dell'estensione Service Fabric, in particolare il tipo di nodo, del nuovo set di scalabilità di macchine virtuali deve essere la stessa del set di scalabilità precedente che si sta tentando di aggiornare. Verificare in Service Fabric Explorer che i nuovi nodi siano disponibili

#### <a name="azure-powershell"></a>Azure PowerShell

Nell'esempio seguente si usa Azure PowerShell per distribuire il modello di Resource Manager aggiornato *template.json* tramite il gruppo di risorse denominato *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile template.json -TemplateParameterFile parameters.json
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per distribuire il modello di Resource Manager aggiornato *template.json* tramite il gruppo di risorse denominato *myResourceGroup*:

```CLI
az group deployment create --resource-group myResourceGroup --template-file template.json --parameters parameters.json
```

Fare riferimento all'esempio seguente per modificare il modello JSON in modo da aggiungere una nuova risorsa di set di scalabilità di macchine virtuali con il tipo di nodo primario nel cluster esistente.

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```

## <a name="remove-old-virtual-machine-scale-set"></a>Rimuovere il set di scalabilità di macchine virtuali precedente

1. Disabilitare le istanze di macchina virtuale del set di scalabilità di macchine virtuali precedente con la finalità di rimuovere il nodo. Il completamento di questa operazione potrebbe richiedere molto tempo. È possibile disabilitare tutti i nodi contemporaneamente e questi verranno inseriti nella coda. Attendere che tutti i nodi siano disabilitati. 
#### <a name="azure-powershell"></a>Azure PowerShell
Nell'esempio seguente si usa PowerShell per Azure Service Fabric per disabilitare l'istanza del nodo denominato *NTvm1_0* dal set di scalabilità di macchine virtuali precedente denominato *NTvm1*:
```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode
```
#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per disabilitare l'istanza del nodo denominato *NTvm1_0* dal set di scalabilità di macchine virtuali precedente denominato *NTvm1*:
```CLI
sfctl node disable --node-name "_NTvm1_0" --deactivation-intent RemoveNode
```
2. Rimuovere il set di scalabilità completo. Attendere finché lo stato del provisioning del set di scalabilità indica che l'operazione è riuscita
#### <a name="azure-powershell"></a>Azure PowerShell
Nell'esempio seguente si usa Azure PowerShell per rimuovere il set di scalabilità completo denominato *NTvm1* dal gruppo di risorse denominato *myResourceGroup*:
```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```
#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per rimuovere il set di scalabilità completo denominato *NTvm1* dal gruppo di risorse denominato *myResourceGroup*:

```CLI
az vmss delete --name NTvm1 --resource-group myResourceGroup
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Rimuovere il servizio Load Balancer correlato al set di scalabilità precedente

Rimuovere il servizio Load Balancer correlato al set di scalabilità precedente. Questo passaggio comporta un breve tempo di inattività per il cluster

#### <a name="azure-powershell"></a>Azure PowerShell

Nell'esempio seguente si usa Azure PowerShell per rimuovere il servizio denominato *LB-myCluster-NTvm1* relativo al set di scalabilità precedente dal gruppo di risorse denominato *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per rimuovere il servizio di bilanciamento del carico denominato *LB-myCluster-NTvm1* relativo al set di scalabilità precedente dal gruppo di risorse denominato *myResourceGroup*:

```CLI
az network lb delete --name LB-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Rimuovere l'indirizzo IP pubblico correlato al set di scalabilità precedente

Archiviare le impostazioni DNS dell'indirizzo IP pubblico correlato al set di scalabilità precedente nella variabile, quindi rimuovere l'indirizzo IP pubblico

#### <a name="azure-powershell"></a>Azure PowerShell

Nell'esempio seguente si usa Azure PowerShell per archiviare le impostazioni DNS dell'indirizzo IP pubblico denominato *LBIP-myCluster-NTvm1* nella variabile e rimuovere l'indirizzo IP:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per ottenere le impostazioni DNS dell'indirizzo IP pubblico denominato *LBIP-myCluster-NTvm1* e rimuovere l'indirizzo IP:

```CLI
az network public-ip show --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
az network public-ip delete --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Aggiornare l'indirizzo IP pubblico correlato al nuovo set di scalabilità

Aggiornare le impostazioni DNS dell'indirizzo IP pubblico correlato al nuovo set di scalabilità con le impostazioni DNS dell'indirizzo IP pubblico correlato al set di scalabilità precedente

#### <a name="azure-powershell"></a>Azure PowerShell
Nell'esempio seguente si usa Azure PowerShell per aggiornare le impostazioni DNS dell'indirizzo IP pubblico denominato *LBIP-myCluster-NTvm3* con le impostazioni DNS archiviate nelle variabili nel passaggio precedente:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm3  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per aggiornare le impostazioni DNS dell'indirizzo IP pubblico denominato *LBIP-myCluster-NTvm3* con le impostazioni DNS del vecchio IP pubblico raccolte nel passaggio precedente:

```CLI
az network public-ip update --name LBIP-myCluster-NTvm3 --resource-group myResourceGroup --dns-name myCluster
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Rimuovere le informazioni del nodo di Service Fabric da FM

Notificare a Service Fabric che i nodi che non sono attivi sono stati rimossi dal cluster. Eseguire questo comando per tutte le istanze di macchine virtuali del set di scalabilità precedente. Se il livello di durabilità del set di scalabilità di macchine virtuali precedente è Silver o Gold, è possibile che questo passaggio non sia necessario. Questa operazione viene infatti eseguita automaticamente dal sistema.

#### <a name="azure-powershell"></a>Azure PowerShell
Nell'esempio seguente si usa PowerShell per Azure Service Fabric per notificare a Service Fabric che il nodo denominato *NTvm1_0* è stato rimosso:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Nel comando seguente si usa l'interfaccia della riga di comando di Azure Service Fabric per notificare a Service Fabric che il nodo denominato *NTvm1_0* è stato rimosso:

```CLI
sfctl node remove-state --node-name _NTvm1_0
```
