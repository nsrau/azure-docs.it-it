---
title: Distribuire un cluster tra zone di disponibilità
description: Informazioni su come creare un cluster di Service Fabric di Azure tra zone di disponibilità.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609979"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Distribuire un cluster di Azure Service Fabric tra zone di disponibilità
Zone di disponibilità in Azure è un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori dei data center. Una zona di disponibilità è una posizione fisica univoca dotata di alimentazione, raffreddamento e rete indipendenti in un'area di Azure.

Service Fabric supporta i cluster che si estendono tra zone di disponibilità distribuendo i tipi di nodo aggiunti a zone specifiche. In questo modo si garantisce la disponibilità elevata delle applicazioni. Zone di disponibilità di Azure sono disponibili solo in aree selezionate. Per ulteriori informazioni, vedere [zone di disponibilità di Azure Overview](https://docs.microsoft.com/azure/availability-zones/az-overview).

Sono disponibili modelli di esempio: [Service Fabric modello di zona a disponibilità incrociata](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Topologia consigliata per il tipo di nodo primario dei cluster Service Fabric di Azure che si estendono tra zone di disponibilità
Un cluster Service Fabric distribuito tra zone di disponibilità garantisce la disponibilità elevata dello stato del cluster. Per estendere un cluster Service Fabric tra le zone, è necessario creare un tipo di nodo primario in ogni zona di disponibilità supportata dall'area. In questo modo i nodi di inizializzazione vengono distribuiti in modo uniforme in ogni tipo di nodo primario.

Per la topologia consigliata per il tipo di nodo primario sono necessarie le risorse descritte di seguito:

* Livello di affidabilità del cluster impostato su platino.
* Tre tipi di nodo contrassegnati come primari.
    * È necessario eseguire il mapping di ogni tipo di nodo al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (durabilità Silver).
* Una singola risorsa IP pubblico con SKU standard.
* Una singola risorsa Load Balancer usando lo SKU standard.
* Un NSG a cui fa riferimento la subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.

>[!NOTE]
> Il set di scalabilità di macchine virtuali proprietà del gruppo di posizionamento singolo deve essere impostato su true, poiché Service Fabric non supporta un singolo set di scalabilità di macchine virtuali che si estende su zone.

 ![Architettura della zona di disponibilità di Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Requisiti di rete
### <a name="public-ip-and-load-balancer-resource"></a>IP pubblico e risorsa Load Balancer
Per abilitare la proprietà zone in una risorsa del set di scalabilità di macchine virtuali, il servizio di bilanciamento del carico e la risorsa IP a cui fa riferimento il set di scalabilità di macchine virtuali devono entrambi usare uno SKU *standard* . La creazione di un servizio di bilanciamento del carico o di una risorsa IP senza la proprietà SKU creerà uno SKU di base che non supporta zone di disponibilità. Per impostazione predefinita, un servizio di bilanciamento del carico con SKU standard bloccherà tutto il traffico dall'esterno. per consentire il traffico esterno, una NSG deve essere distribuita nella subnet.

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
> Non è possibile eseguire una modifica sul posto dello SKU nell'IP pubblico e nelle risorse del servizio di bilanciamento del carico. Se si esegue la migrazione da risorse esistenti con SKU Basic, vedere la sezione relativa alla migrazione di questo articolo.

### <a name="virtual-machine-scale-set-nat-rules"></a>Regole NAT del set di scalabilità di macchine virtuali
Le regole NAT in ingresso del servizio di bilanciamento del carico devono corrispondere ai pool NAT del set di scalabilità di macchine virtuali. Ogni set di scalabilità di macchine virtuali deve avere un pool NAT in ingresso univoco.

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

### <a name="standard-sku-load-balancer-outbound-rules"></a>SKU standard Load Balancer regole in uscita
Load Balancer Standard e l'IP pubblico standard introducono nuove funzionalità e comportamenti diversi per la connettività in uscita rispetto all'uso di SKU di base. Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica. Per altre informazioni, vedere [connessioni in uscita](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) e [Load Balancer standard di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Il modello standard fa riferimento a un NSG che consente tutto il traffico in uscita per impostazione predefinita. Il traffico in ingresso è limitato alle porte richieste per Service Fabric operazioni di gestione. Le regole NSG possono essere modificate per soddisfare i requisiti.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Abilitazione di zone in un set di scalabilità di macchine virtuali
Per abilitare una zona, in un set di scalabilità di macchine virtuali è necessario includere i tre valori seguenti nella risorsa del set di scalabilità di macchine virtuali.

* Il primo valore è la proprietà **Zones** , che specifica la zona di disponibilità in cui verrà distribuito il set di scalabilità di macchine virtuali.
* Il secondo valore è la proprietà "singlePlacementGroup", che deve essere impostata su true.
* Il terzo valore è la proprietà "faultDomainOverride" nell'estensione del set di scalabilità di macchine virtuali Service Fabric. Il valore di questa proprietà deve includere l'area e la zona in cui verrà inserito il set di scalabilità di macchine virtuali. Esempio: "faultDomainOverride": "eastus/AZ1" tutte le risorse del set di scalabilità di macchine virtuali devono essere posizionate nella stessa area perché i cluster di Service Fabric di Azure non dispongono di supporto tra aree.

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Abilitazione di più tipi di nodo primari nella risorsa cluster Service Fabric
Per impostare uno o più tipi di nodo come primari in una risorsa cluster, impostare la proprietà "Primary" su "true". Quando si distribuisce un cluster Service Fabric tra zone di disponibilità, è necessario avere tre tipi di nodo in zone distinte.

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Eseguire la migrazione a usando zone di disponibilità da un cluster usando uno SKU di base Load Balancer e un IP dello SKU Basic
Per eseguire la migrazione di un cluster che stava usando un Load Balancer e un IP con uno SKU di base, è necessario creare prima di tutto una nuova Load Balancer e una risorsa IP usando lo SKU standard. Non è possibile aggiornare queste risorse sul posto.

È necessario fare riferimento ai nuovi LB e IP nei nuovi tipi di nodo della zona di disponibilità incrociata che si vuole usare. Nell'esempio precedente sono state aggiunte tre nuove risorse del set di scalabilità di macchine virtuali nelle zone 1, 2 e 3. Questi set di scalabilità di macchine virtuali fanno riferimento a LB e IP appena creati e sono contrassegnati come tipi di nodo primari nella risorsa del cluster Service Fabric.

Per iniziare, sarà necessario aggiungere le nuove risorse al modello di Gestione risorse esistente. Queste risorse includono:
* Una risorsa IP pubblico con SKU standard.
* Una risorsa Load Balancer usando lo SKU standard.
* Un NSG a cui fa riferimento la subnet in cui vengono distribuiti i set di scalabilità di macchine virtuali.
* Tre tipi di nodo contrassegnati come primari.
    * È necessario eseguire il mapping di ogni tipo di nodo al proprio set di scalabilità di macchine virtuali che si trova in zone diverse.
    * Ogni set di scalabilità di macchine virtuali deve avere almeno cinque nodi (durabilità Silver).

Un esempio di queste risorse è disponibile nel modello di [esempio](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Una volta terminata la distribuzione delle risorse, è possibile iniziare a disabilitare i nodi del tipo di nodo primario dal cluster originale. Quando i nodi sono disabilitati, viene eseguita la migrazione dei servizi di sistema al nuovo tipo di nodo primario distribuito nel passaggio precedente.

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

Una volta disabilitati tutti i nodi, i servizi di sistema verranno eseguiti nel tipo di nodo primario, che viene distribuito tra le zone. È quindi possibile rimuovere i nodi disabilitati dal cluster. Una volta rimossi i nodi, è possibile rimuovere le risorse dell'IP originale, della Load Balancer e del set di scalabilità di macchine virtuali.

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

È quindi necessario rimuovere i riferimenti a queste risorse dal modello di Gestione risorse distribuito.

Il passaggio finale prevede l'aggiornamento del nome DNS e dell'indirizzo IP pubblico.

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
