---
title: Ridimensionare un cluster di Service Fabric in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come ridimensionare un cluster di Service Fabric in Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 40e372b779d06656b111ad3d7de435b99c401dc3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669504"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Esercitazione: Ridimensionare un cluster di Service Fabric in Azure

Questa esercitazione è la terza di una serie e illustra come aumentare e ridurre il numero di istanze del cluster esistente. Al termine, si riuscirà a ridimensionare il cluster e a pulire le risorse rimaste.  Per altre informazioni sul ridimensionamento di un cluster in esecuzione in Azure, vedere [Ridimensionamento dei cluster di Service Fabric](service-fabric-cluster-scaling.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere e rimuovere nodi (aumento e riduzione)
> * Aggiungere e rimuovere tipi di nodo (aumento e riduzione)
> * Aumentare le risorse dei nodi (aumento)

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure usando un modello
> * [Eseguire il monitoraggio di un cluster](service-fabric-tutorial-monitor-cluster.md)
> * Aumentare o ridurre un cluster
> * [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminare un cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installare il [modulo Azure PowerShell 4.1 o versioni successive](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) o [l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure

## <a name="important-considerations-and-guidelines"></a>Considerazioni e indicazioni importanti

I carichi di lavoro delle applicazioni cambiano nel tempo: i servizi esistenti hanno bisogno di più (o meno) risorse?  [Aggiungere o rimuovere nodi](#add-nodes-to-or-remove-nodes-from-a-node-type) da un tipo di nodo per aumentare o diminuire le risorse del cluster.

È necessario aggiungere più di 100 nodi al cluster?  Un singolo tipo di nodo/set di scalabilità di Service Fabric non può contenere più di 100 nodi/macchine virtuali.  Per ridimensionare un cluster oltre i 100 nodi, [aggiungere altri tipi di nodo](#add-nodes-to-or-remove-nodes-from-a-node-type).

L'applicazione ha più servizi, alcuni dei quali devono essere pubblici o per Internet?  Le applicazioni tipiche contengono un servizio gateway front-end che riceve l'input da un client e uno o più servizi back-end che comunicano con i servizi front-end. In questo caso, è consigliabile [aggiungere almeno due tipi di nodo](#add-nodes-to-or-remove-nodes-from-a-node-type) al cluster.  

I servizi hanno esigenze diverse per l'infrastruttura, ad esempio cicli di CPU più elevati o una quantità maggiore di RAM? Ad esempio, si supponga che l'applicazione contenga un servizio front-end e un servizio back-end. Il servizio front-end può essere eseguito in VM più piccole (ad esempio, di dimensioni D2), con porte aperte a Internet, Il servizio back-end è invece a elevato utilizzo di calcolo e deve essere eseguito in VM più grandi (ad esempio di dimensione D4, D6, D15) senza connessione a Internet. In questo caso, è consigliabile [aggiungere due o più tipi di nodo](#add-nodes-to-or-remove-nodes-from-a-node-type) al cluster. perché in questo modo ogni tipo di nodo può avere proprietà distinte, ad esempio la connettività Internet o le dimensioni delle VM. Il numero di VM può essere ridimensionato anche in modo indipendente.

Quando si ridimensiona un cluster di Azure, tenere presenti le linee guida seguenti:

* Un singolo tipo di nodo/set di scalabilità di Service Fabric non può contenere più di 100 nodi/macchine virtuali.  Per ridimensionare un cluster oltre i 100 nodi, aggiungere altri tipi di nodo.
* I tipi di nodo primari che eseguono carichi di lavoro di produzione devono avere un [livello di durabilità][durability] Gold o Silver e contenere sempre cinque o più nodi.
* I tipi di nodo non primari che eseguono carichi di lavoro di produzione con stato devono contenere sempre cinque o più nodi.
* I tipi di nodo non primari che eseguono carichi di lavoro di produzione senza stato devono contenere sempre due o più nodi.
* Qualsiasi tipo di nodo del [livello di durabilità][durability] Gold o Silver deve sempre contenere cinque o più nodi.
* Se si ridimensiona un tipo di nodo primario rimuovendo alcuni nodi, è consigliabile non ridurre mai il numero di istanze a un valore inferiore a quello richiesto dal [livello di affidabilità][reliability].

Per altre informazioni, vedere la [guida alla capacità del cluster](service-fabric-cluster-capacity.md).

## <a name="export-the-template-for-the-resource-group"></a>Esportare il modello per il gruppo di risorse

Dopo aver creato un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) sicuro e configurato correttamente il gruppo di risorse, esportare il modello di Resource Manager per il gruppo di risorse. L'esportazione del modello consente di automatizzare le future distribuzioni del cluster e delle relative risorse perché il modello contiene tutta l'infrastruttura completa.  Per altre informazioni sull'esportazione dei modelli, vedere [Gestire i gruppi di risorse di Azure Resource Manager con il portale di Azure](/azure/azure-resource-manager/manage-resource-groups-portal).

1. Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse che contiene il cluster (**sfclustertutorialgroup**, se si segue questa esercitazione). 

2. Nel riquadro sinistro selezionare **Distribuzioni** o selezionare il collegamento sotto **Distribuzioni**. 

3. Selezionare l'ultima distribuzione riuscita dall'elenco.

4. Nel riquadro sinistro selezionare **Modello** e quindi selezionare **Download** per esportare il modello come file ZIP.  Salvare il modello e i parametri nel computer locale.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Aggiungere o rimuovere nodi da un tipo di nodo

La scalabilità orizzontale modifica il numero di nodi nel cluster. Con la scalabilità orizzontale si aggiungono altre istanze di macchina virtuale al set di scalabilità. Queste istanze diventano i nodi usati da Service Fabric. Service Fabric determina quando vengono aggiunte altre istanze al set di scalabilità e reagisce automaticamente. È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.

### <a name="update-the-template"></a>Aggiornare il modello

[Esportare un file di modello e parametri](#export-the-template-for-the-resource-group) dal gruppo di risorse per la distribuzione più recente.  Aprire il file *parameters.json*.  Se il cluster è stato distribuito con il [modello di esempio][template] di questa esercitazione, sono disponibili tre tipi di nodo nel cluster e tre parametri che impostano il numero di nodi per ogni tipo di nodo: *nt0InstanceCount*, *nt1InstanceCount* e *nt2InstanceCount*.  Il parametro *nt1InstanceCount*, ad esempio, imposta il numero di istanze per il secondo tipo di nodo e imposta il numero di macchine virtuali nel set di scalabilità di macchine virtuali associato.

Aggiornando quindi il valore di *nt1InstanceCount* si modifica il numero di nodi nel secondo tipo di nodo.  Tenere presente che non è possibile scalare orizzontalmente un tipo di nodo aggiungendo più di 100 nodi.  I tipi di nodo non primari che eseguono carichi di lavoro di produzione con stato devono contenere sempre cinque o più nodi. I tipi di nodo non primari che eseguono carichi di lavoro di produzione senza stato devono contenere sempre due o più nodi.

Se si ridimensiona un tipo di nodo con il [livello di durabilità][durability] Bronze rimuovendo alcuni nodi, è necessario [rimuovere manualmente lo stato di tali nodi](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set).  Per il livello di durabilità Silver e Gold, questi passaggi vengono eseguiti automaticamente dalla piattaforma.

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato
Salvare le modifiche nei file *template.json* e *parameters.json*.  Per distribuire il modello aggiornato, eseguire il comando seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
In alternativa, usare il comando dell'interfaccia della riga di comando di Azure seguente:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Aggiungere un tipo di nodo al cluster

Ogni tipo di nodo definito in un cluster di Service Fabric in esecuzione in Azure viene configurato come [set di scalabilità di macchine virtuali distinto](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. È possibile aumentare o ridurre in modo indipendente ogni nodo, avere diversi set di porte aperte e usare metriche per la capacità diverse. È possibile anche modificare in modo indipendente lo SKU del sistema operativo in esecuzione in ogni nodo del cluster, ma tenere presente che non è possibile avere una combinazione di Windows e Linux in esecuzione nel cluster di esempio. Un singolo tipo di nodo/set di scalabilità non può contenere più di 100 nodi.  È possibile scalare orizzontalmente un cluster fino a più di 100 nodi aggiungendo altri tipi di nodo/set di scalabilità. È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.

### <a name="update-the-template"></a>Aggiornare il modello

[Esportare un file di modello e parametri](#export-the-template-for-the-resource-group) dal gruppo di risorse per la distribuzione più recente.  Aprire il file *parameters.json*.  Se il cluster è stato distribuito con il [modello di esempio][template] di questa esercitazione, sono disponibili tre tipi di nodo nel cluster.  In questa sezione viene aggiunto un quarto tipo di nodo aggiornando e distribuendo un modello di Resource Manager. 

Oltre al nuovo tipo di nodo, viene aggiunto anche il set di scalabilità di macchine virtuali associato (che viene eseguito in una subnet separata della rete virtuale) e un gruppo di sicurezza di rete.  È possibile scegliere di aggiungere un indirizzo IP pubblico nuovo o esistente e le risorse di bilanciamento del carico di Azure per il nuovo set di scalabilità.  Il nuovo tipo di nodo ha un [livello di durabilità][durability] Silver e dimensioni "Standard_D2_V2".

Nel file *template.json* aggiungere i nuovi parametri seguenti:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

Nel file *template.json* aggiungere le nuove variabili seguenti:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

Nel file *template.json* aggiungere una nuova subnet per la risorsa di rete virtuale:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

Nel file *template.json* aggiungere un nuovo indirizzo IP pubblico e le risorse di bilanciamento del carico:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

Nel file *template.json* aggiungere un nuovo gruppo di sicurezza di rete e le risorse del set di scalabilità di macchine virtuali.  La proprietà NodeTypeRef all'interno delle proprietà dell'estensione Service Fabric del set di scalabilità di macchine virtuali mappa il tipo di nodo specificato al set di scalabilità.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
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
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
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
                "computernamePrefix": "[variables('vmNodeType3Name')]",
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
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

Nel file *template.json* aggiornare la risorsa cluster e aggiungere un nuovo tipo di nodo:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

Nel file *parameters.json* aggiungere i nuovi parametri e valori seguenti:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato
Salvare le modifiche nei file *template.json* e *parameters.json*.  Per distribuire il modello aggiornato, eseguire il comando seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
In alternativa, usare il comando dell'interfaccia della riga di comando di Azure seguente:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Rimuovere un tipo di nodo dal cluster
Dopo aver creato un cluster di Service Fabric, è possibile ridimensionare un cluster orizzontalmente rimuovendo un tipo di nodo (set di scalabilità di macchine virtuali) e tutti i relativi nodi. È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster. Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

> [!WARNING]
> Non è consigliabile usare frequentemente il comando Remove-AzureRmServiceFabricNodeType per rimuovere un tipo di nodo da un cluster di produzione. È un comando molto pericoloso perché elimina la risorsa set di scalabilità di macchine virtuali presente dietro il tipo di nodo. 

Per rimuovere il tipo di nodo, eseguire il cmdlet [Remove-AzureRmServiceFabricNodeType](/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype).  Il [livello di durabilità][durability] del tipo di nodo deve essere Silver o Gold. Il cmdlet elimina il set di scalabilità associato al tipo di nodo e il completamento del cmdlet tempo.  Eseguire quindi il cmdlet [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) in ciascuno dei nodi da rimuovere, che consente di eliminare lo stato del nodo e di rimuovere i nodi dal cluster. Se nei nodi sono presenti servizi, questi vengono prima spostati in un altro nodo. Se Cluster Manager non è in grado di trovare un nodo per una replica o un servizio, l'operazione viene posticipata o bloccata.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzureRmServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Aumentare le risorse dei nodi 
Dopo aver creato un cluster di Service Fabric, è possibile scalare un tipo di nodo del cluster in verticale (modificare le risorse dei nodi) o aggiornare il sistema operativo delle macchine virtuali del tipo di nodo.  

> [!WARNING]
> È consigliabile non modificare lo SKU della macchina virtuale di un tipo di nodo o set di scalabilità, a meno che non sia in esecuzione al livello di durabilità Silver o superiore. La modifica delle dimensioni dello SKU della macchina virtuale è un'operazione dell'infrastruttura sul posto distruttiva per i dati. Senza la capacità di ritardare o monitorare questa modifica, è possibile che l'operazione causi una perdita di dati per i servizi con stato o provochi altri problemi operativi non previsti, anche per i carichi di lavoro senza stato.

> [!WARNING]
> È consigliabile non modificare lo SKU della macchina virtuale del tipo di nodo primario essendo un'operazione pericolosa e non supportata.  Se occorre maggiore capacità del cluster, è possibile aggiungere altre istanze di macchine virtuali o altri tipi di nodo.  Se ciò non è possibile, creare un nuovo cluster e [ripristinare lo stato dell'applicazione](service-fabric-reliable-services-backup-restore.md) (se applicabile) dal cluster precedente.  Se ciò non è possibile, [modificare lo SKU della macchina virtuale del tipo di nodo primario](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Aggiornare il modello

[Esportare un file di modello e parametri](#export-the-template-for-the-resource-group) dal gruppo di risorse per la distribuzione più recente.  Aprire il file *parameters.json*.  Se il cluster è stato distribuito con il [modello di esempio][template] di questa esercitazione, sono disponibili tre tipi di nodo nel cluster.  

Le dimensioni delle macchine virtuali nel secondo tipo di nodo vengono impostate nel parametro *vmNodeType1Size*.  Modificare il valore del parametro *vmNodeType1Size* da Standard_D2_V2 a [Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series), raddoppiando le risorse di ogni istanza di macchina virtuale.

Lo SKU della macchina virtuale per tutti e tre i tipi di nodo è impostato nel parametro *vmImageSku*.  Anche in questo caso, la modifica dello SKU della macchina virtuale di un tipo di nodo deve essere eseguita con attenzione e non è consigliata per il tipo di nodo primario.

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato
Salvare le modifiche nei file *template.json* e *parameters.json*.  Per distribuire il modello aggiornato, eseguire il comando seguente:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
In alternativa, usare il comando dell'interfaccia della riga di comando di Azure seguente:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere e rimuovere nodi (aumento e riduzione)
> * Aggiungere e rimuovere tipi di nodo (aumento e riduzione)
> * Aumentare le risorse dei nodi (aumento)

Procedere quindi con l'esercitazione seguente per scoprire come aggiornare il runtime di un cluster.
> [!div class="nextstepaction"]
> [Aggiornare il runtime di un cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
