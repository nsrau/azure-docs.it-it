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
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Modelli di rete di Service Fabric
Una delle domande più comuni che viene posta durante la creazione di cluster di Service Fabric è come integrare il cluster con le varie funzionalità di rete di Azure.  In questo articolo viene illustrato come creare un cluster con le funzionalità seguenti:

- [Rete virtuale/subnet esistente](#existingvnet)
- [Indirizzo IP pubblico statico](#staticpublicip)
- [Bilanciamento del carico esclusivamente interno](#internallb)
- [Bilanciamento del carico interno ed esterno](#internalexternallb)

Un concetto chiave da tenere presente è che Service Fabric viene eseguito in un set di scalabilità di macchine virtuali standard, pertanto tutte le funzionalità che è possibile usare in un set di scalabilità di macchine virtuali possono essere usate anche con un cluster di Service di Fabric. Le porzioni di rete del modello di Resource Manager sono identiche.  In seguito alla distribuzione in una rete virtuale esistente, è facile incorporare altre funzionalità di rete, ad esempio ExpressRoute, Gateway VPN, Gruppo di sicurezza di rete (NSG) e Peering reti virtuali.

L'unico aspetto specifico di Service Fabric è che il [Portale di Azure](https://portal.azure.com) usa internamente il provider di risorse di Service Fabric (SFRP) per effettuare le chiamate in un cluster e ottenere informazioni sui nodi e le applicazioni.  Per SFRP è necessario l'accesso in ingresso accessibile pubblicamente alla porta Gateway HTTP (19080 per impostazione predefinita) nell'endpoint di gestione, che viene usata da [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) per gestire il cluster. Questa porta viene inoltre usata dal provider di risorse di Service Fabric per richiedere informazioni sul cluster da visualizzare nel Portale di Azure.  Se questa porta non è accessibile da SFRP, viene visualizzato un messaggio, ad esempio "Nodes Not Found" (Nodi non trovati), nel portale di gestione e l'elenco dei nodi e delle applicazioni appare vuoto.  Se si desidera visualizzare il cluster tramite il Portale di Azure, il bilanciamento del carico deve esporre un indirizzo IP pubblico e il Gruppo di sicurezza di rete deve consentire il traffico in ingresso attraverso la porta 19080.  Se questi requisiti non vengono rispettati, il Portale di Azure non mostra lo stato corrente del cluster.  In caso contrario il cluster non è interessato ed è possibile usare [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) per ottenere lo stato corrente, che può essere un limite accettabile in base ai requisiti di rete.  Si tratta di una limitazione temporanea che si intende rimuovere in un aggiornamento futuro, quando il cluster sarà essere pubblicamente accessibile senza alcuna perdita di funzionalità del portale di gestione.

## <a name="templates"></a>Modelli

Tutti i modelli sono disponibili [qui](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip). L'utente dovrebbe essere in grado di distribuire i modelli così come sono usando i seguenti comandi di PowerShell.  Se si distribuisce il modello di rete virtuale esistente o il modello IP pubblico statico, assicurarsi di seguire prima la sezione [Configurazione iniziale](#initialsetup).

<a id="initialsetup"></a>
## <a name="initial-setup"></a>Configurazione iniziale

### <a name="existing-virtual-network"></a>Rete virtuale esistente

Si parte da una rete virtuale esistente denominata "ExistingRG-vnet" nel gruppo di risorse *ExistingRG*, con una subnet denominata "predefinito".  Queste risorse sono quelle predefinite create quando è stato usato il Portale di Azure per creare una macchina virtuale standard.  È inoltre possibile creare la rete virtuale e la subnet senza creare la macchina virtuale. L'obiettivo principale dell'aggiunta di un cluster a una rete virtuale esistente è quello di offrire la connettività di rete ad altre macchine virtuali, dando, con la creazione della macchina virtuale, un esempio concreto di come il cluster venga generalmente usato.  Se il cluster di Service Fabric usa solo un servizio di bilanciamento del carico interno senza un indirizzo IP pubblico, la macchina virtuale con il relativo indirizzo IP pubblico può essere usata anche come un jumpbox.

### <a name="static-public-ip-address"></a>Indirizzo IP pubblico statico

Poiché in genere, un indirizzo IP pubblico statico è una risorsa dedicata gestita separatamente dalla macchina virtuale o dalle macchine virtuali a cui viene assegnato, vien sottoposto a provisioning in un gruppo di risorse di rete dedicato (invece del gruppo di risorse del cluster di Service Fabric).  Creare un indirizzo IP statico pubblico con il nome "staticIP1" nello stesso gruppo di risorse *ExistingRG*, tramite il Portale di Azure o PowerShell:

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

Viene usato un template.json di Service Fabric che può essere scaricato dal portale prima di creare un cluster mediante la procedura guidata del portale standard. È inoltre possibile usare uno dei modelli nel [raccolta modelli](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric), ad esempio il [cluster Service Fabric con cinque nodi](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/).

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>Rete virtuale/subnet esistente

[24 gennaio 2016: è disponibile un altro esempio di questo all'esterno dell'ambito di Service Fabric all'indirizzo [https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)]

1. Modificare il parametro subnet per il nome della subnet esistente e aggiungere due nuovi parametri per fare riferimento alla rete virtuale esistente:

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


2. Modificare la variabile *vnetID* in modo da puntare alla rete virtuale esistente:

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Rimuovere *Microsoft.Network/virtualNetworks* dalle risorse in modo che Azure non crei una nuova rete virtuale:

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

4. Inserire un commento sulla rete virtuale dall'attributo *dependsOn* di *Microsoft.Compute/virtualMachineScaleSets* in modo che l'utente non dipenda dalla creazione di una nuova rete virtuale:

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

    In seguito alla distribuzione, la rete virtuale deve includere le nuove macchine virtuali del set di scalabilità e il tipo di nodo del set di scalabilità di macchine virtuali deve mostrare la rete virtuale e la subnet esistenti.  È anche possibile usare RDP per la macchina virtuale esistente già nella rete virtuale ed effettuare il ping per le nuove macchine virtuali del set di scalabilità:

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>Indirizzo IP pubblico statico

1. Aggiungere i parametri per il nome del gruppo di risorse IP statico esistente, un nome e un nome di dominio completo:

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

2. Rimuovere il parametro *dnsName* poiché l'indirizzo IP statico ne include già uno:

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

4. Rimuovere *Microsoft.Network/publicIPAddresses* dalle *risorse* in modo che Azure non crei un nuovo indirizzo IP:

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

5. Inserire un commento sull'indirizzo IP dall'attributo *dependsOn* di *Microsoft.Network/loadBalancers* in modo che l'utente non dipenda dalla creazione di un nuovo indirizzo IP:

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

6. Modificare l'elemento *publicIPAddress* di *frontendIPConfigurations* nella risorsa *Microsoft.Network/loadBalancers* per fare riferimento all'indirizzo IP statico esistente anziché a un indirizzo appena creato:

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

7. Modificare *managementEndpoint* nella risorsa *Microsoft.ServiceFabric/clusters* per il nome di dominio completo DNS dell'indirizzo IP statico.  **Se si usa un cluster protetto, assicurarsi di modificare "http://" in "https://".** (Nota: questa istruzione è solo per i cluster di Service Fabric.  Se si usa un set di scalabilità di macchine virtuali ignorare questo passaggio):

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

In seguito alla distribuzione si nota che il bilanciamento del carico viene associato a un indirizzo IP statico pubblico dall'altro gruppo di risorse. L'endpoint della connessione client di Service Fabric e l'endpoint di [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) puntano al nome di dominio completo DNS dell'indirizzo IP statico.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>Bilanciamento del carico esclusivamente interno

Questo scenario sostituisce il bilanciamento del carico esterno nel modello di Service Fabric predefinito con un bilanciamento del carico esclusivamente interno.  Vedere la sezione precedente per il Portale di Azure e le implicazioni SFRP.

1. Rimuovere il parametro *dnsName* poiché non è necessario:

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. Facoltativamente, aggiungere un parametro per l'indirizzo IP statico, se si usa il metodo di allocazione statica. Se si usa il metodo di allocazione dinamica questo non è necessario:

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Rimuovere *Microsoft.Network/publicIPAddresses* dalle risorse in modo che Azure non crei un nuovo indirizzo IP:

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

4. Rimuovere l'indirizzo IP dall'attributo *dependsOn* di *Microsoft.Network/loadBalancers* in modo che l'utente non dipenda dalla creazione di un nuovo indirizzo IP.  Aggiungere l'attributo *dependsOn* della rete virtuale poiché il bilanciamento del carico ora dipende dalla subnet della rete virtuale:

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

5. Fare in modo che *frontendIPConfigurations* del servizio di bilanciamento del carico non usi *publicIPAddress*, ma usi invece una subnet *privateIPAddress*, che si serve di un indirizzo IP statico interno predefinito.  È possibile usare un indirizzo IP dinamico rimuovendo l'elemento *privateIPAddress* e modificando *privateIPAllocationMethod* in "Dinamico".

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

6. Nella risorsa *Microsoft.ServiceFabric/clusters* modificare *managementEndpoint* per fare in modo che punti all'indirizzo del bilanciamento del carico interno.  **Se si usa un cluster protetto, assicurarsi di modificare "http://" in "https://".** (Nota: questa istruzione è solo per i cluster di Service Fabric.  Se si usa un set di scalabilità di macchine virtuali ignorare questo passaggio):

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

In seguito alla distribuzione, il bilanciamento del carico usa l'indirizzo IP privato statico 10.0.0.250. Se si ha un altro computer nella stessa rete virtuale, è inoltre possibile accedere all'endpoint interno di [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e vedere che si connette a uno dei nodi del bilanciamento del carico.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>Bilanciamento del carico interno ed esterno

In questo scenario al bilanciamento del carico esterno del tipo di nodo singolo esistente viene aggiunto un bilanciamento del carico interno aggiuntivo per lo stesso tipo di nodo.  Una porta di back-end collegata a un pool di indirizzi di back-end può essere assegnata solo a un bilanciamento del carico singolo, pertanto è necessario decidere a quale bilanciamento del carico assegnare le porte dell'applicazione e a quale assegnare gli endpoint di gestione (porta 19000/19080).  Tenere presente le restrizioni SFRP precedentemente descritte se si decide di inserire gli endpoint di gestione sul bilanciamento del carico interno.  In questo esempio gli endpoint di gestione vengono mantenuti sul bilanciamento del carico esterno e viene aggiunta una porta applicazione 80 che viene poi collocata sul bilanciamento del carico interno.

Se desidera un cluster con due nodi, con un nodo sul bilanciamento del carico esterno e l'altro sul bilanciamento del carico interno, basta prendere il modello a due nodi creato dal portale (che include due bilanciamenti del carico) e spostare il secondo bilanciamento del carico su un bilanciamento del carico interno seguendo le istruzioni della sezione precedente "Bilanciamento del carico esclusivamente interno".

1. Aggiungere il parametro dell'indirizzo LB IP interno statico (vedere le note precedenti se si desidera l'indirizzo IP dinamico):

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. Aggiungere il parametro della porta dell'applicazione 80:

3. Aggiungere versioni interne delle variabili di rete esistenti, copiando, incollando e aggiungendo "-Int" alla denominazione:

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. Se il modello generato dal portale inizia con una porta dell'applicazione 80, il modello del portale predefinito aggiunge *AppPort1* (porta 80) sul bilanciamento del carico esterno.  In questo caso, rimuoverlo dal bilanciamento del carico esterno *loadBalancingRules* e probe così da poterlo aggiungere al bilanciamento del carico interno:

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
        } /* remove the AppPort1 from the external LB,
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
        } /* remove the AppPort1 from the external LB,,
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

5. Aggiungere una seconda risorsa *Microsoft.Network/loadBalancers*, che è molto simile al bilanciamento del carico interno creato nella precedente sezione [Bilanciamento del carico esclusivamente interno](#internallb), ma usando le variabili del bilanciamento del carico "-Int" e implementando solo la porta dell'applicazione 80.  Questa operazione consente di rimuovere anche *inboundNatPools* per mantenere gli endpoint RDP sul bilanciamento del carico pubblico: se si desidera che il protocollo RDP sia nel bilanciamento del carico interno, spostare *inboundNatPools* dal bilanciamento del carico esterno al bilanciamento del carico interno.

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
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
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
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
                    /* Add the probe for the app port */
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

6. In *networkProfile* per la risorsa *Microsoft.Compute/virtualMachineScaleSets* aggiungere il pool dell'indirizzo di back-end interno:

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

In seguito alla distribuzione nel gruppo di risorse saranno visibili due bilanciamenti del carico: navigando tra i bilanciamenti del carico si possono notare gli endpoit di gestione e l'indirizzo IP pubblico (porta 19000/19080) assegnati all'indirizzo IP pubblico, insieme all'endpoint dell'applicazione e all'indirizzo IP interno statico (porta 80) assegnati al bilanciamento del carico interno. Si nota inoltre che entrambi i bilanciamenti del carico usano lo stesso pool di back-end del set di scalabilità di macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito maggiori informazioni sull'integrazione dei cluster di Service Fabric con le funzionalità di rete di Azure, è possibile proseguire per [creare un cluster](service-fabric-cluster-creation-via-arm.md). 

