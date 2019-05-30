---
title: Distribuire un cluster Azure Service Fabric le zone di disponibilità | Microsoft Docs
description: Informazioni su come creare un cluster Azure Service Fabric le zone di disponibilità.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077456"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuire un cluster Azure Service Fabric le zone di disponibilità
Le zone di disponibilità in Azure è a disponibilità elevata offerta che protegge le applicazioni e dati da errori dei Data Center. Una zona di disponibilità è una posizione fisica univoca dotata di alimentazione, raffreddamento e rete all'interno di un'area di Azure.

Service Fabric supporta i cluster che si estendono su zone di disponibilità distribuendo i tipi di nodi che sono stati bloccati a zone specifiche. Questa operazione assicurerà la disponibilità elevata delle applicazioni. Le zone di disponibilità di Azure sono disponibili solo in aree selezionate. Per altre informazioni, vedere [Panoramica di zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

I modelli di esempio sono disponibili: [Service Fabric tra il modello di zona di disponibilità](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Consiglia di topologia per il tipo di nodo primario del cluster di Service Fabric che si estende su zone di disponibilità
Un cluster di Service Fabric distribuito tra zone di disponibilità garantisce la disponibilità elevata dello stato del cluster. Per le zone si estendono su un cluster di Service Fabric, è necessario creare un tipo di nodo primario in ogni zona di disponibilità supportate dall'area. I nodi di inizializzazione verrà ripartita in modo uniforme tra ognuno dei tipi di nodo primario.

La topologia consigliata per il tipo di nodo primario sono necessarie le risorse indicate di seguito:

* Il livello di affidabilità del cluster è impostato su Platinum.
* Tre tipi di nodo contrassegnato come primari.
    * Ogni tipo di nodo deve essere mappato a un proprio set di scalabilità di macchine virtuali che si trova in aree diverse.
    * Ogni set di scalabilità di macchine virtuali devono contenere almeno cinque nodi (durabilità Silver).
* Una singola risorsa IP pubblico con SKU Standard.
* Una singola risorsa di bilanciamento del carico con SKU Standard.
* Un NSG a cui fanno riferimento alla subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.

>[!NOTE]
> Il set di scalabilità di macchine virtuali single placement gruppo proprietà deve essere impostata su true, poiché Service Fabric non supporta un set di scalabilità di macchina virtuale singola che si estende su zone.

 ![Architettura di zona di disponibilità di Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>Indirizzo IP pubblico e risorsa di bilanciamento del carico
Per abilitare le zone impostare proprietà su una scala di macchine virtuali, risorse, il servizio di bilanciamento del carico e risorsa IP fa riferimento il set di scalabilità di macchine virtuali devono entrambi essere usando un *Standard* SKU. Creazione di un servizio di bilanciamento del carico o una risorsa IP senza la proprietà SKU creerà uno SKU Basic, che non supporta le zone di disponibilità. Un servizio di bilanciamento del carico dello SKU Standard blocca tutto il traffico dall'esterno per impostazione predefinita. per consentire di fuori del traffico, un gruppo di sicurezza deve essere distribuito alla subnet.

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
> Non è possibile eseguire una modifica sul posto dello SKU per le risorse di servizio di bilanciamento carico e IP pubbliche. Se si esegue la migrazione da risorse esistenti che hanno uno SKU Basic, vedere la sezione di migrazione di questo articolo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Le regole NAT di set di scalabilità di macchine virtuali
Il servizio di bilanciamento del carico regole NAT in ingresso devono corrispondere i pool NAT dal set di scalabilità di macchine virtuali. Ogni set di scalabilità di macchine virtuali deve avere un pool NAT in ingresso univoco.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>Regole in uscita di bilanciamento del carico dello SKU standard
Standard di Load Balancer e IP pubblico Standard introduce nuove funzionalità e comportamenti differenti per la connettività in uscita rispetto all'uso gli SKU di base. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Per altre informazioni, vedere [le connessioni in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e [Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Il modello standard di fa riferimento a un gruppo di sicurezza che consente tutto il traffico in uscita per impostazione predefinita. Il traffico in ingresso è limitato alle porte necessarie per operazioni di gestione di Service Fabric. È possibile modificare le regole NSG per soddisfare i requisiti.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Impostare l'abilitazione di zone nella scalabilità di macchine virtuali
Per abilitare una zona, su una scala di macchina virtuale impostata è necessario includere tre valori seguenti nella risorsa di set di scalabilità di macchine virtuali.

* Il primo valore è il **zone** proprietà, che consente di specificare quale zona di disponibilità verrà distribuito il set di scalabilità di macchine virtuali.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true.
* Il terzo valore è la proprietà "faultDomainOverride" nell'estensione di set di scalabilità di Service Fabric macchina virtuale. Il valore di questa proprietà deve includere l'area e zona in cui verrà collocato questo set di scalabilità di macchine virtuali. Esempio: "faultDomainOverride": "Stati Uniti orientali/az1" risorse del set di scalabilità di macchine virtuali tutte deve trovarsi nella stessa area perché i cluster Azure Service Fabric non è tra il supporto di area.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Abilitazione di più tipi di nodo primario nella risorsa Cluster di Service Fabric
Per impostare uno o più tipi di nodo come primario in una risorsa cluster, impostare la proprietà "isPrimary" su "true". Quando si distribuisce un cluster di Service Fabric le zone di disponibilità, è necessario disporre di tre tipi di nodo nelle zone distinte.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>La migrazione per usare le zone di disponibilità da un cluster con bilanciamento del carico dello SKU Basic e un indirizzo IP lo SKU Basic
Per eseguire la migrazione di un cluster, che usava un IP e il servizio di bilanciamento del carico con uno SKU basic, è necessario creare innanzitutto una completamente nuova risorsa di Load Balancer e IP Usa lo SKU standard. Non è possibile aggiornare queste risorse in loco.

Il nuovo bilanciamento del carico e IP deve fare riferimento i tipi nuovi cross-nodo di zona di disponibilità che si desidera utilizzare. Nell'esempio precedente, tre nuove scalabilità di macchine virtuali nelle zone 1, 2 e 3 sono stati aggiunti set di risorse. Questi scalabilità di macchine virtuali Imposta riferimento l'IP e il bilanciamento del carico appena creato e sono contrassegnati come tipi di nodo primario nella risorsa Cluster di Service Fabric.

Per iniziare, è necessario aggiungere le nuove risorse al modello di Resource Manager esistente. Queste risorse includono:
* Una risorsa IP pubblico con SKU Standard.
* Una risorsa di servizio di bilanciamento del carico con SKU Standard.
* Un NSG a cui fanno riferimento alla subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.
* Tre tipi di nodo contrassegnato come primari.
    * Ogni tipo di nodo deve essere mappato a un proprio set di scalabilità di macchine virtuali che si trova in aree diverse.
    * Ogni set di scalabilità di macchine virtuali devono contenere almeno cinque nodi (durabilità Silver).

Un esempio di queste risorse è reperibile nel [modello di esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Una volta terminata la distribuzione di risorse, è possibile iniziare a disabilitare i nodi nel tipo di nodo primario dal cluster originale. Poiché i nodi sono disabilitati, i servizi di sistema esegue la migrazione al nuovo tipo di nodo primario che era stato distribuito nel passaggio precedente.

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

Dopo che i nodi sono tutti disabilitati, i servizi di sistema verranno eseguiti nel tipo di nodo primario, che verrà distribuito tra zone. È quindi possibile rimuovere i nodi disabilitati dal cluster. Dopo aver rimosso i nodi, è possibile rimuovere l'indirizzo IP originale, servizio di bilanciamento del carico e delle risorse del set di scalabilità di macchine virtuali.

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

È quindi necessario rimuovere i riferimenti a queste risorse dal modello di Resource Manager che è stato distribuito.

Il passaggio finale comporta l'aggiornamento del nome DNS e indirizzo IP pubblico.

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
