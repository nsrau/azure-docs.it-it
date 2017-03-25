---
title: Modelli di rete per Azure Service Fabric | Microsoft Docs
description: "Questo articolo descrive i modelli di rete comuni per Service Fabric e illustra come creare un cluster con le funzionalità di rete di Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3021ebbe88705b0c5a1691a45ed0ba400af6dd34
ms.lasthandoff: 03/09/2017


---
# <a name="service-fabric-networking-patterns"></a>Modelli di rete di Service Fabric
È possibile integrare il cluster di Azure Service Fabric con altre funzionalità di rete di Azure. Questo articolo illustra come creare cluster che fanno uso delle funzionalità seguenti:

- [Rete virtuale o subnet esistente](#existingvnet)
- [Indirizzo IP pubblico statico](#staticpublicip)
- [Bilanciamento del carico esclusivamente interno](#internallb)
- [Bilanciamento del carico interno ed esterno](#internalexternallb)

Service Fabric viene eseguito in un set di scalabilità di macchine virtuali standard. In un cluster di Service Fabric si possono usare tutte le funzionalità che è possibile usare in un set di scalabilità di macchine virtuali. Le sezioni di rete dei modelli di Azure Resource Manager per i set di scalabilità di macchine virtuali e quelle per Service Fabric sono identiche. Dopo aver eseguito la distribuzione in una rete virtuale esistente, è facile incorporare altre funzionalità di rete, come Azure ExpressRoute, il gateway VPN di Azure, un gruppo di sicurezza di rete e il peering di rete virtuale.

Service Fabric si distingue dalle altre funzionalità di rete per un solo aspetto. Il [portale di Azure](https://portal.azure.com) usa internamente il provider di risorse di Service Fabric per chiamare un cluster e ottenere informazioni sui nodi e le applicazioni. Il provider di risorse di Service Fabric richiede l'accesso in ingresso accessibile pubblicamente alla porta del gateway HTTP nell'endpoint di gestione. Per impostazione predefinita si tratta della porta 19080. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) usa l'endpoint di gestione per gestire il cluster. Anche il provider di risorse di Service Fabric usa questa porta per richiedere informazioni sul cluster, che vengono visualizzate nel portale di Azure. 

Se la porta 190980 non è accessibile dal provider di risorse di Service Fabric, nel portale viene visualizzato un messaggio che indica che *non sono stati trovati nodi* e l'elenco dei nodi e delle applicazioni risulta vuoto. Per visualizzare il cluster nel portale di Azure, il servizio di bilanciamento del carico deve esporre un indirizzo IP pubblico e il gruppo di sicurezza di rete deve consentire il traffico in ingresso dalla porta 19080. Se la configurazione non soddisfa questi requisiti, lo stato del cluster non viene visualizzato nel portale di Azure.

## <a name="templates"></a>Modelli

Tutti i modelli di Service Fabric si trovano in [un unico file scaricabile](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). Dovrebbe essere possibile distribuire i modelli così come sono usando i comandi di PowerShell riportati di seguito. Se si distribuisce il modello di rete virtuale di Azure esistente o il modello IP pubblico statico, leggere prima la sezione [Configurazione iniziale](#initialsetup) di questo articolo.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configurazione iniziale

### <a name="existing-virtual-network"></a>Rete virtuale esistente

L'esempio seguente parte da una rete virtuale esistente denominata ExistingRG-vnet, nel gruppo di risorse **ExistingRG**. Il nome della subnet è quello predefinito. Queste risorse predefinite vengono create quando si usa il portale di Azure per creare una macchina virtuale (VM) standard. È possibile creare la rete virtuale e la subnet senza creare la macchina virtuale, ma l'obiettivo principale dell'aggiunta di un cluster a una rete virtuale esistente è fornire la connettività di rete ad altre macchine virtuali. La creazione della macchina virtuale costituisce un buon esempio dell'uso tipico di una rete virtuale esistente. Se il cluster di Service Fabric usa soltanto un servizio di bilanciamento del carico interno, senza un indirizzo IP pubblico, è possibile usare la macchina virtuale e il relativo indirizzo IP pubblico come un *jumpbox* protetto.

### <a name="static-public-ip-address"></a>Indirizzo IP pubblico statico

In genere, un indirizzo IP pubblico è una risorsa dedicata che viene gestita separatamente rispetto alla macchina virtuale o alle macchine virtuali a cui è assegnato. Il provisioning viene effettuato in un gruppo di risorse di rete dedicato e non nel gruppo di risorse stesso del cluster di Service Fabric. Creare un indirizzo IP pubblico statico denominato staticIP1 nello stesso gruppo di risorse ExistingRG, nel portale di Azure oppure tramite PowerShell:

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Modello di Service Fabric

Negli esempi riportati in questo articolo viene usato il file template.json di Service Fabric. Per scaricare il modello dal portale prima di creare un cluster, è possibile usare la procedura guidata standard del portale. È anche possibile usare uno dei modelli della [raccolta modelli](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), come il [cluster a cinque nodi di Service Fabric](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-network-or-subnet"></a>Rete virtuale o subnet esistente

1. Impostare il parametro subnet sul nome della subnet esistente e quindi aggiungere due nuovi parametri per fare riferimento alla rete virtuale esistente:

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. Modificare la variabile `vnetID` in modo che punti alla rete virtuale esistente:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Rimuovere `Microsoft.Network/virtualNetworks` dalle risorse in modo che Azure non crei una nuova rete virtuale:

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. Impostare come commento la rete virtuale dell'attributo `dependsOn` di `Microsoft.Compute/virtualMachineScaleSets` in modo da non dipendere dalla creazione di una nuova rete virtuale:

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. Distribuire il modello:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    Dopo la distribuzione, la rete virtuale dovrebbe includere le nuove macchine virtuali del set di scalabilità. Il tipo di nodo del set di scalabilità di macchine virtuali dovrebbe mostrare la rete virtuale esistente e la subnet. È anche possibile usare il protocollo RDP (Remote Desktop Protocol) per accedere alla macchina virtuale già esistente nella rete virtuale e per effettuare il ping delle nuove macchine virtuali del set di scalabilità:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

Per altre informazioni, vedere un [esempio non specifico di Service Fabric](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet).


<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Indirizzo IP pubblico statico

1. Aggiungere i parametri per il gruppo di risorse dell'indirizzo IP statico esistente, il nome e il nome di dominio completo (FQDN):

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. Rimuovere il parametro `dnsName`. L'indirizzo IP statico ne ha già uno.

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

3. Aggiungere una variabile per fare riferimento all'indirizzo IP statico esistente:

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Rimuovere `Microsoft.Network/publicIPAddresses` dalle risorse in modo che Azure non crei un nuovo indirizzo IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. Impostare come commento l'indirizzo IP dell'attributo `dependsOn` di `Microsoft.Network/loadBalancers` in modo da non dipendere dalla creazione di un nuovo indirizzo IP:

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. Nella risorsa `Microsoft.Network/loadBalancers` modificare l'elemento `publicIPAddress` di `frontendIPConfigurations` in modo che faccia riferimento l'indirizzo IP statico esistente anziché a uno appena creato:

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. Nella risorsa `Microsoft.ServiceFabric/clusters` impostare `managementEndpoint` sul nome di dominio completo del DNS dell'indirizzo IP statico. Se si usa un cluster protetto, assicurarsi di modificare *http://* in *https://*. Si noti che questo passaggio si applica solo ai cluster di Service Fabric. Se si usa un set di scalabilità di macchine virtuali, ignorare il passaggio.

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. Distribuire il modello:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

Dopo la distribuzione, il servizio di bilanciamento del carico è associato all'indirizzo IP statico pubblico dell'altro gruppo di risorse. L'endpoint della connessione client di Service Fabric e l'endpoint di [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) puntano al nome di dominio completo del DNS dell'indirizzo IP statico.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Bilanciamento del carico esclusivamente interno

Questo scenario sostituisce il servizio di bilanciamento del carico esterno nel modello di Service Fabric predefinito con un bilanciamento del carico esclusivamente interno. Per informazioni sulle implicazioni per il portale di Azure e per il provider di risorse di Service Fabric, vedere la sezione precedente.

1. Rimuovere il parametro `dnsName`. Non è necessario.

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Facoltativamente, se si usa un metodo di allocazione statica è possibile aggiungere un parametro per l'indirizzo IP statico. Se si usa un metodo di allocazione dinamica, non è necessario eseguire questo passaggio.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Rimuovere `Microsoft.Network/publicIPAddresses` dalle risorse in modo che Azure non crei un nuovo indirizzo IP:

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. Rimuovere l'indirizzo IP dell'attributo `dependsOn` di `Microsoft.Network/loadBalancers` in modo da non dipendere dalla creazione di un nuovo indirizzo IP. Dal momento che ora il servizio di bilanciamento del carico dipende dalla subnet della rete virtuale, aggiungere l'attributo `dependsOn` della rete virtuale:

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. Modificare l'impostazione `frontendIPConfigurations` del servizio di bilanciamento del carico passando dall'uso di `publicIPAddress` all'uso di una subnet e di `privateIPAddress`. `privateIPAddress` usa un indirizzo IP interno statico predefinito. Per usare un indirizzo IP dinamico, rimuovere l'elemento `privateIPAddress` e modificare `privateIPAllocationMethod` in **dinamico**.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. Nella risorsa `Microsoft.ServiceFabric/clusters` modificare `managementEndpoint` in modo che punti all'indirizzo del servizio di bilanciamento del carico interno. Se si usa un cluster protetto, assicurarsi di modificare *http://* in *https://*. Si noti che questo passaggio si applica solo ai cluster di Service Fabric. Se si usa un set di scalabilità di macchine virtuali, ignorare il passaggio.

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. Distribuire il modello:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

Dopo la distribuzione, il servizio di bilanciamento del carico usa l'indirizzo IP privato statico 10.0.0.250. Se nella stessa rete virtuale è disponibile un altro computer, è possibile passare all'endpoint interno di [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Si noti che questo si connette a uno dei nodi del servizio di bilanciamento del carico.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Bilanciamento del carico interno ed esterno

In questo scenario si inizia con il servizio di bilanciamento del carico esterno a nodo singolo esistente e si aggiunge un servizio di bilanciamento del carico interno per lo stesso tipo di nodo. È possibile assegnare una porta back-end collegata a un pool di indirizzi back-end a un solo servizio di bilanciamento del carico. Scegliere a quale servizio di bilanciamento del carico assegnare le porte dell'applicazione e a quale assegnare gli endpoint di gestione (porte 19000 e 19080). Se si sceglie di inserire gli endpoint di gestione nel servizio di bilanciamento del carico interno, tenere presenti le limitazioni del provider di risorse di Service Fabric illustrate in precedenza in questo articolo. Nell'esempio usato gli endpoint di gestione rimangono nel servizio di bilanciamento del carico esterno. Aggiungere una porta 80 dell'applicazione e posizionarla nel servizio di bilanciamento del carico interno.

In un cluster a due tipi di nodo, un tipo di nodo si trova nel servizio di bilanciamento del carico esterno. L'altro tipo di nodo si trova nel servizio di bilanciamento del carico interno. Per usare un cluster a due tipi di nodo, nel modello a due tipi di nodo creato nel portale, che include due servizi di bilanciamento del carico, impostare il secondo come servizio di bilanciamento del carico interno. Per altre informazioni, vedere la sezione [Bilanciamento del carico esclusivamente interno](#internallb).

1. Aggiungere il parametro per l'indirizzo IP statico del servizio di bilanciamento del carico interno. Per le note relative all'uso di un indirizzo IP dinamico, vedere le sezioni precedenti di questo articolo.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Aggiungere un parametro della porta 80 dell'applicazione.

3. Per aggiungere versioni interne delle variabili di rete esistenti, copiarle e incollarle e aggiungere "-Int" al nome:

    ```
    /* Add internal load balancer networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* Internal load balancer networking variables end */
    ```

4. Se si parte dal modello generato nel portale, che usa la porta 80 dell'applicazione, il modello predefinito del portale aggiunge AppPort1 (porta 80) al servizio di bilanciamento del carico esterno. In tal caso, rimuovere AppPort1 dall'oggetto `loadBalancingRules` e dai probe del servizio di bilanciamento del carico esterno, in modo che sia possibile aggiungerlo al servizio di bilanciamento del carico interno:

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* Remove AppPort1 from the external load balancer.
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ],
    "inboundNatPools": [
    ```

5. Aggiungere una seconda risorsa `Microsoft.Network/loadBalancers`. Ha un aspetto simile al servizio di bilanciamento del carico interno creato nella sezione [Bilanciamento del carico esclusivamente interno](#internallb), ma fa uso delle variabili "-Int" del servizio di bilanciamento del carico e implementa solo la porta 80 dell'applicazione. Rimuove anche `inboundNatPools`, per mantenere gli endpoint RDP nel servizio di bilanciamento del carico pubblico. Per usare il protocollo RDP nel servizio di bilanciamento del carico interno, spostare `inboundNatPools` dal servizio di bilanciamento del carico esterno a quello interno:

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" load balancer variables. */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add "-Internal" to the name. */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule. Be sure to reference the "-Int" versions of backendAddressPool, frontendIPConfiguration, and the probe variables. */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port. */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. In `networkProfile` per la risorsa `Microsoft.Compute/virtualMachineScaleSets` aggiungere il pool di indirizzi back-end interno:

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. Distribuire il modello:

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

Dopo la distribuzione, nel gruppo di risorse vengono visualizzati due servizi di bilanciamento del carico. Esplorando tali servizi è possibile visualizzare l'indirizzo IP pubblico e gli endpoint di gestione (porte 19000 e 19080) assegnati all'indirizzo IP pubblico. È anche possibile visualizzare l'indirizzo IP interno statico e l'endpoint dell'applicazione (porta 80) assegnati al servizio di bilanciamento del carico interno. Entrambi i servizi di bilanciamento del carico usano lo stesso pool back-end del set di scalabilità di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi
[Creare un cluster](service-fabric-cluster-creation-via-arm.md)

