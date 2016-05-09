<properties
	pageTitle="Gestire VM in un set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Gestire le macchine virtuali in un set di scalabilità di macchine virtuali tramite Azure PowerShell."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Gestire le macchine virtuali in un set di scalabilità di macchine virtuali

Azure PowerShell offre numerosi vantaggi e grande flessibilità nella gestione delle risorse in Microsoft Azure. Usare le attività descritte in questo articolo per gestire le risorse delle macchine virtuali del set di scalabilità.

- [Visualizzare informazioni su un set di scalabilità di macchine virtuali](#displayvm)
- [Avviare una macchina virtuale in un set di scalabilità](#start)
- [Arrestare una macchina virtuale in un set di scalabilità](#stop)
- [Riavviare una macchina virtuale in un set di scalabilità](#restart)
- [Eliminare una macchina virtuale da un set di scalabilità](#delete)

Tutte le attività che implicano la gestione di una macchina virtuale in un set di scalabilità richiedono che si conosca l'ID dell'istanza della macchina virtuale che si vuole gestire. È possibile usare [Esplora risorse di Azure](https://resources.azure.com) per trovare l'ID dell'istanza di una macchina virtuale in un set di scalabilità. È anche possibile usare Esplora risorse per verificare lo stato delle attività da completare.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Visualizzare informazioni su un set di scalabilità di macchine virtuali

È possibile ottenere informazioni generali su un set di scalabilità, ovvero una visualizzazione delle istanze. In alternativa, è possibile ottenere informazioni più specifiche, ad esempio informazioni sulle risorse nel set.

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali e *scale set name* con il nome del set di scalabilità, quindi eseguire il comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Verrà visualizzata una schermata simile alla seguente:

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

Per ottenere informazioni generali, sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali e *scale set name* con il nome del set di scalabilità, quindi eseguire il comando:

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

Verrà visualizzata una schermata simile alla seguente:

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>Avviare una macchina virtuale in un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, sostituire *VM scale set name* con il nome del set di scalabilità, sostituire *instance id* con l'ID della macchina virtuale che si vuole riavviare e quindi eseguire il comando:

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

In Esplora risorse, è possibile vedere che lo stato dell'istanza è **running**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

## <a id="stop"></a>Arrestare una macchina virtuale in un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, sostituire *scale set name* con il nome del set di scalabilità, sostituire *instance id* con l'ID della macchina virtuale che si vuole arrestare e quindi eseguire il comando:

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

In Esplora risorse, è possibile vedere che lo stato dell'istanza è **deallocated**:

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

## <a id="restart"></a>Riavviare una macchina virtuale in un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, sostituire *scale set name* con il nome del set di scalabilità, sostituire *instance id* con l'ID della macchina virtuale che si vuole riavviare e quindi eseguire il comando:

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>Rimuovere una macchina virtuale da un set di scalabilità

In questo comando sostituire *resource group name* con il nome del gruppo di risorse che include il set di scalabilità di macchine virtuali, sostituire *scale set name* con il nome del set di scalabilità, sostituire *instance id* con l'ID della macchina virtuale che si vuole rimuovere dal set di scalabilità e quindi eseguire il comando:

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->