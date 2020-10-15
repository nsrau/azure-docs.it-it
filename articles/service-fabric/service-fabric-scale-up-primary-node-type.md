---
title: Ridimensionare un tipo di nodo primario di Azure Service Fabric
description: Informazioni su come ridimensionare un cluster di Service Fabric aggiungendo un tipo di nodo.
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89228716"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>Scalare verticalmente un tipo di nodo primario del cluster Service Fabric aggiungendo un tipo di nodo
Questo articolo descrive come aumentare la scalabilità verticale di un Service Fabric tipo di nodo primario del cluster aggiungendo un tipo di nodo aggiuntivo al cluster. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente.

I modelli di esempio nell'esercitazione seguente sono disponibili qui: [Service Fabric esempi di scalabilità del tipo di nodo primario](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> Non tentare una procedura di scalabilità verticale del tipo di nodo primario se lo stato del cluster non è integro, perché il cluster verrà ulteriormente destabilizzato.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>Processo per aggiornare le dimensioni e il sistema operativo del tipo di nodo primario
Di seguito è riportato il processo di aggiornamento delle dimensioni della macchina virtuale e del sistema operativo delle VM del tipo di nodo primario.  Dopo l'aggiornamento, le VM del tipo di nodo primario sono dimensioni standard D4_V2 ed eseguono Windows Server 2019 Datacenter con i contenitori.

> [!WARNING]
> Prima di provare a eseguire questa procedura su un cluster di produzione, è consigliabile esaminare i modelli di esempio e verificare il processo su un cluster di test. È inoltre possibile che il cluster non sia disponibile per un breve periodo di tempo. 

### <a name="deploy-the-initial-service-fabric-cluster"></a>Distribuire il cluster Service Fabric iniziale 
Se si vuole seguire l'esempio, distribuire il cluster iniziale con un unico tipo di nodo primario e un singolo set di scalabilità [Service Fabric cluster iniziale](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json). È possibile ignorare questo passaggio se è già stato distribuito un cluster di Service Fabric esistente. 

1. Accedere all'account Azure. 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. Creare un nuovo gruppo di risorse. 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. Immettere i valori dei parametri nei file di modello. 
4. Distribuire il cluster nel gruppo di risorse creato nel passaggio 2. 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Aggiungere un nuovo tipo di nodo primario al cluster
> [!Note]
> Le risorse create nei passaggi seguenti diventeranno il nuovo tipo di nodo primario nel cluster al termine dell'operazione di ridimensionamento. Assicurarsi di usare nomi univoci dalla subnet iniziale, dall'IP pubblico, da Load Balancer, dal set di scalabilità di macchine virtuali e dal tipo di nodo. 

È possibile trovare un modello con tutti i passaggi seguenti completati qui: [Service Fabric-nuovo cluster di tipi di nodo](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json). La procedura seguente contiene frammenti di risorse parziali che evidenziano le modifiche nelle nuove risorse.  

1. Creare una nuova subnet nella rete virtuale esistente.
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. Creare una nuova risorsa IP pubblico con un domainNameLabel univoco. 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. Creare una nuova risorsa Load Balancer che dipende dall'indirizzo IP pubblico creato in precedenza. 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. Creare un nuovo set di scalabilità di macchine virtuali che usa il nuovo SKU di VM e lo SKU del sistema operativo a cui si vuole applicare la scalabilità verticale. 

Ref del tipo di nodo 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU di VM
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU DEL SISTEMA OPERATIVO 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Il frammento di codice seguente è un esempio di una nuova risorsa del set di scalabilità di macchine virtuali utilizzata per creare un nuovo tipo di nodo per un cluster Service Fabric. È necessario assicurarsi di includere eventuali estensioni aggiuntive necessarie per il carico di lavoro. 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
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
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
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
            "computernamePrefix": "[variables('vmNodeType1Name')]",
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
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
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
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. Aggiungere un nuovo tipo di nodo al cluster, che fa riferimento al set di scalabilità di macchine virtuali creato in precedenza. La **Proprietà IsTrue** di questo tipo di nodo deve essere impostata su true. 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. Distribuire il modello ARM aggiornato. 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Al termine della distribuzione, il cluster Service Fabric avrà ora due tipi di nodo. 

### <a name="remove-the-existing-node-type"></a>Rimuovere il tipo di nodo esistente 
Una volta terminata la distribuzione delle risorse, è possibile iniziare a disabilitare i nodi nel tipo di nodo primario originale. Quando i nodi sono disabilitati, viene eseguita la migrazione dei servizi di sistema al nuovo tipo di nodo primario distribuito nel passaggio precedente.

1. Impostare la proprietà del tipo di nodo primario nella risorsa del cluster Service Fabric su false. 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. Distribuire il modello con la proprietà unprimary aggiornata nel tipo di nodo originale. È possibile trovare un modello con il flag primario impostato su false nel tipo di nodo originale qui: [Service Fabric-tipo di nodo primario false](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. Disabilitare i nodi nel tipo di nodo 0. 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* Per la durabilità Bronze, attendere che tutti i nodi ottengano lo stato disabilitato.
* Per la durabilità Silver e Gold, alcuni nodi entrano in disabilitati e il resto si troverà nello stato disabilitato. Controllare la scheda Details (Dettagli) dei nodi nello stato disabilitato. se sono tutti bloccati per garantire il quorum per le partizioni del servizio di infrastruttura, è possibile continuare.

> [!Note]
> Il completamento di questo passaggio può richiedere alcuni minuti. 

4. Arrestare i dati sul tipo di nodo 0. 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. Deallocare i nodi nel set di scalabilità di macchine virtuali originale 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> I passaggi 6 e 7 sono facoltativi se si usa già un IP pubblico con SKU standard e il servizio di bilanciamento del carico dello SKU standard. In questo caso, è possibile avere più tipi di nodo/set di scalabilità di macchine virtuali nello stesso servizio di bilanciamento del carico. 

6. È ora possibile eliminare l'indirizzo IP originale e Load Balancer risorse. In questo passaggio verrà aggiornato anche il nome DNS. 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. Aggiornare l'endpoint di gestione nel cluster per fare riferimento al nuovo indirizzo IP. 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. Rimuovere lo stato del nodo dal tipo di nodo 0.
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. Rimuovere il riferimento al tipo di nodo originale dalla risorsa Service Fabric nel modello ARM. 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
Solo per i cluster di durabilità Silver e versioni successive, aggiornare la risorsa cluster nel modello e configurare i criteri di integrità per ignorare l'integrità dell'applicazione Fabric:/System aggiungendo applicationDeltaHealthPolicies in proprietà risorse cluster come indicato di seguito. Il criterio seguente deve ignorare gli errori esistenti ma non consentire nuovi errori di integrità.
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. Rimuovere tutte le altre risorse correlate al tipo di nodo originale dal modello ARM. Vedere [Service Fabric-nuovo cluster di tipi di nodo](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json) per un modello con tutte queste risorse originali rimosse.

11. Distribuire il modello di Azure Resource Manager modificato. * * Questa operazione richiederà un po' di tempo, in genere fino a due ore. Questo aggiornamento modificherà le impostazioni in InfrastructureService; Pertanto, è necessario riavviare un nodo. In questo caso, forceRestart viene ignorato. Il parametro upgradeReplicaSetCheckTimeout specifica il tempo massimo che Service Fabric attende che una partizione sia in uno stato sicuro, se non è già in uno stato sicuro. Una volta superati i controlli di sicurezza per tutte le partizioni in un nodo, Service Fabric procede con l'aggiornamento su tale nodo. Il valore del parametro upgradeTimeout può essere ridotto a 6 ore, ma è consigliabile usare per la massima sicurezza 12 ore.
Quindi verificare che la risorsa Service Fabric nel portale sia visualizzata come pronta. 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

Il tipo di nodo primario del cluster è stato aggiornato. Verificare che tutte le applicazioni distribuite funzionino correttamente e che l'integrità del cluster sia accettabile.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [aggiungere un tipo di nodo a un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).
