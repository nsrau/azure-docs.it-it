---
title: Distribuire un cluster tra zone di disponibilitàDeploy a cluster across Availability Zones
description: Informazioni su come creare un cluster di Azure Service Fabric tra zone di disponibilità.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609979"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuire un cluster di Azure Service Fabric tra le zone di disponibilitàDeploy an Azure Service Fabric cluster across Availability zones
Le zone di disponibilità in Azure sono un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori del data center. Una zona di disponibilità è una posizione fisica unica dotata di alimentazione, raffreddamento e rete indipendenti all'interno di un'area di Azure.An Availability zone is a unique physical location with independent power, cooling, and networking within an Azure region.

Service Fabric supporta i cluster che si estendono tra le zone di disponibilità distribuendo tipi di nodo aggiunti a zone specifiche. Ciò garantirà un'elevata disponibilità delle applicazioni. Le zone di disponibilità di Azure sono disponibili solo in aree selezionate. Per altre informazioni, vedere [Panoramica delle zone](https://docs.microsoft.com/azure/availability-zones/az-overview)di disponibilità di Azure.For more information, see Azure Availability zones Overview .

Sono disponibili modelli di esempio: modello di zona a [disponibilità incrociata di Service FabricSample](https://github.com/Azure-Samples/service-fabric-cluster-templates) templates are available: Service Fabric cross availability zone template

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia consigliata per il tipo di nodo primario dei cluster di Azure Service Fabric che si estendono tra le zone di disponibilitàRecommended topology for primary node type of Azure Service Fabric clusters spanning across Availability zones
Un cluster di Service Fabric distribuito tra zone di disponibilità garantisce la disponibilità elevata dello stato del cluster. Per estendersi su un cluster di Service Fabric tra zone, è necessario creare un tipo di nodo primario in ogni zona di disponibilità supportata dall'area. In questo modo i nodi testa di seguito verranno distribuiti in modo uniforme in ognuno dei tipi di nodo primario.

La topologia consigliata per il tipo di nodo primario richiede le risorse descritte di seguito:The recommended topology for the primary node type requires the resources outlined below:

* Il livello di affidabilità del cluster è impostato su Platinum.
* Tre tipi di nodo contrassegnati come primari.
    * Ogni tipo di nodo deve essere mappato al proprio set di scalabilità di macchine virtuali situato in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (Durabilità argento).
* Una singola risorsa IP pubblica che usa lo SKU standard.
* Una singola risorsa di bilanciamento del carico usando lo SKU Standard.A Single Load Balancer Resource using Standard SKU.
* Un gruppo di sicurezza di rete a cui fa riferimento la subnet in cui si distribuiscono i set di scalabilità delle macchine virtuali.

>[!NOTE]
> La proprietà del singolo gruppo di posizionamento del set di scalabilità della macchina virtuale deve essere impostata su true, poiché Service Fabric non supporta un singolo set di scalabilità di macchine virtuali che si estende sulle zone.

 ![Azure Service Fabric Availability Zone Architecture][sf-architecture]

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>IP pubblico e risorsa di bilanciamento del caricoPublic IP and Load Balancer Resource
Per abilitare la proprietà zone in una risorsa set di scalabilità di macchine virtuali, il servizio di bilanciamento del carico e la risorsa IP a cui fa riferimento il set di scalabilità della macchina virtuale devono usare entrambi uno SKU *Standard.To* enable the zones property on a virtual machine scale set resource, the load balancer and IP resource referenced by that virtual machine scale set must both using a Standard SKU. La creazione di un servizio di bilanciamento del carico o di una risorsa IP senza la proprietà SKU creerà uno SKU di base, che non supporta le zone di disponibilità. Un servizio di bilanciamento del carico SKU Standard bloccherà tutto il traffico dall'esterno per impostazione predefinita. per consentire il traffico esterno, un gruppo di sicurezza di rete deve essere distribuito nella subnet.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Non è possibile eseguire una modifica sul posto dello SKU nelle risorse dell'IP pubblico e del servizio di bilanciamento del carico. Se si esegue la migrazione da risorse esistenti con uno SKU di base, vedere la sezione relativa alla migrazione di questo articolo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regole NAT del set di scalabilità della macchina virtualeVirtual machine scale set NAT rules
Le regole NAT in ingresso del servizio di bilanciamento del carico devono corrispondere ai pool NAT del set di scalabilità di macchine virtuali. Ogni set di scalabilità di macchine virtuali deve avere un pool di NAT in ingresso univoco.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Regole in uscita di Load Balancer SKU standardStandard SKU Load Balancer outbound rules
Il servizio di bilanciamento del carico standard e l'IP pubblico standard introducono nuove capacità e comportamenti diversi per la connettività in uscita rispetto all'uso degli SKU di base. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Per altre informazioni, vedere [Connessioni in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e Azure Standard Load Balancer.For more information, see Outbound connections and Azure Standard Load [Balancer.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

>[!NOTE]
> Il modello standard fa riferimento a un gruppo di sicurezza di sicurezza di gruppo che consente tutto il traffico in uscita per impostazione predefinita. Il traffico in ingresso è limitato alle porte necessarie per le operazioni di gestione di Service Fabric.Inbound traffic is limited to the ports that are required for Service Fabric management operations. Le regole del gruppo di sicurezza di rete possono essere modificate per soddisfare le proprie esigenze.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Abilitazione delle zone in un set di scalabilità di macchine virtualiEnabling zones on a virtual machine scale set
Per abilitare una zona, in un set di scalabilità di macchine virtuali è necessario includere i tre valori seguenti nella risorsa del set di scalabilità della macchina virtuale.

* Il primo valore è la proprietà **zones,** che specifica in quale zona di disponibilità verrà distribuito il set di scalabilità della macchina virtuale.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true.
* Il terzo valore è la proprietà "faultDomainOverride" nell'estensione del set di scalabilità della macchina virtuale Di Service Fabric.The third value is the "faultDomainOverride" property in the Service Fabric virtual machine scale set extension. Il valore di questa proprietà deve includere l'area e la zona in cui verrà inserito il set di scalabilità della macchina virtuale. Esempio: "faultDomainOverride": "eastus/az1" Tutte le risorse del set di scalabilità di macchine virtuali devono essere inserite nella stessa area perché i cluster di Azure Service Fabric non dispongono del supporto tra aree.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Abilitazione di più tipi di nodo primari nella risorsa Cluster di Service FabricEnabling multiple primary Node Types in the Service Fabric Cluster resource
Per impostare uno o più tipi di nodo come primari in una risorsa cluster, impostare la proprietà "isPrimary" su "true". Quando si distribuisce un cluster di Service Fabric tra zone di disponibilità, è necessario avere tre tipi di nodo in zone distinte.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Eseguire la migrazione all'utilizzo di zone di disponibilità da un cluster usando un bilanciamento del carico SKU di base e un IP SKU di baseMigrate to using Availability Zones from a cluster using a Basic SKU Load Balancer and a Basic SKU IP
Per eseguire la migrazione di un cluster, che usava un servizio di bilanciamento del carico e un IP con uno SKU di base, è innanzitutto necessario creare una risorsa di bilanciamento del carico e IP completamente nuova usando lo SKU standard. Non è possibile aggiornare queste risorse sul posto.

È necessario fare riferimento ai nuovi tipi di nodo LB e IP nei nuovi tipi di nodo zona di disponibilità incrociata che si desidera utilizzare. Nell'esempio precedente sono state aggiunte tre nuove risorse del set di scalabilità di macchine virtuali nelle zone 1,2 e 3.In the example above, three new virtual machine scale set resources were added in zones 1,2, and 3. Questi set di scalabilità delle macchine virtuali fanno riferimento all'Indirizzo LB e all'indirizzo IP appena creati e sono contrassegnati come tipi di nodo primario nella risorsa cluster dell'infrastruttura del servizio.

Per iniziare, è necessario aggiungere le nuove risorse al modello di Resource Manager esistente. Queste risorse includono:
* Una risorsa IP pubblica che usa lo SKU standard.
* Una risorsa di bilanciamento del carico con SKU standard.
* Un gruppo di sicurezza di rete a cui fa riferimento la subnet in cui si distribuiscono i set di scalabilità delle macchine virtuali.
* Tre tipi di nodo contrassegnati come primari.
    * Ogni tipo di nodo deve essere mappato al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (Durabilità argento).

Un esempio di queste risorse è disponibile nel [modello di esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Al termine della distribuzione delle risorse, è possibile iniziare a disabilitare i nodi nel tipo di nodo primario dal cluster originale. Quando i nodi sono disabilitati, i servizi di sistema eseguiranno la migrazione al nuovo tipo di nodo primario che era stato distribuito nel passaggio precedente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Una volta che i nodi sono tutti disabilitati, i servizi di sistema verranno eseguiti sul tipo di nodo primario, che viene distribuito tra le zone. È quindi possibile rimuovere i nodi disabilitati dal cluster. Dopo aver rimosso i nodi, è possibile rimuovere le risorse IP, Load Balancer e set di scalabilità di macchine virtuali originali.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

È quindi necessario rimuovere i riferimenti a queste risorse dal modello di Resource Manager distribuito.

Il passaggio finale prevede l'aggiornamento del nome DNS e dell'IP pubblico.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
