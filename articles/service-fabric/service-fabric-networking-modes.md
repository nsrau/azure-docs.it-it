---
title: Configurare le modalità di rete per i servizi del contenitore di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare le diverse modalità di rete supportate da Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: ecb7ac4d3359142d3aef247e4b918f517e10c3bb
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926123"
---
# <a name="service-fabric-container-networking-modes"></a>Modalità di rete del contenitore di Service Fabric

Per impostazione predefinita, un cluster di Azure Service Fabric per servizi contenitore usa la modalità di rete **nat**. Quando più servizi contenitore sono in ascolto sulla stessa porta ed è in uso la modalità nat, possono verificarsi errori di distribuzione. Per supportare più servizi contenitore in ascolto sulla stessa porta, Service Fabric offre la modalità di rete **Open** (versione 5.7 e successive). In modalità Open, ogni servizio contenitore è un indirizzo IP interno assegnato dinamicamente che supporta più servizi in ascolto sulla stessa porta.  

Se nel manifesto del servizio è presente un servizio contenitore con un endpoint statico, è possibile creare ed eliminare nuovi servizi usando la modalità Open senza errori di distribuzione. Lo stesso file docker-compose.yml può essere usato anche con il mapping delle porte statiche per la creazione di più servizi.

Quando un servizio contenitore viene riavviato o spostato in un altro nodo del cluster, l'indirizzo IP cambia. Per individuare i servizi contenitore, quindi, non è consigliabile usare l'indirizzo IP assegnato dinamicamente, ma solo Service Fabric Naming Service o il servizio DNS. 

>[!WARNING]
>Azure consente a un totale di indirizzi IP 65,356 per ogni rete virtuale. La somma del numero di nodi e il numero di istanze del servizio contenitore (che utilizzano la modalità Open) non può superare 65,356 gli indirizzi IP all'interno di una rete virtuale. Per gli scenari ad alta densità, è consigliabile la modalità di rete nat. Inoltre, le altre dipendenze, ad esempio servizio di bilanciamento del carico verranno sono presenti altri [limitazioni](https://docs.microsoft.com/azure/azure-subscription-service-limits) da prendere in considerazione. Attualmente fino a 50 indirizzi IP per ogni nodo sono stati testati e collaudati stabile. 
>

## <a name="set-up-open-networking-mode"></a>Configurare la modalità di rete Open

1. Configurare il modello Azure Resource Manager. Nella sezione **fabricSettings** della risorsa cluster abilitare il servizio DNS e il provider di IP: 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```
    
2. Configurare la sezione relativa al profilo di rete della risorsa set di scalabilità di macchine virtuali. Ciò consente la configurazione di più indirizzi IP in ogni nodo del cluster. Nell'esempio seguente vengono configurati cinque indirizzi IP per ogni nodo di un cluster di Service Fabric basato su Windows/Linux. Per ogni nodo, quindi, è possibile avere cinque istanze di servizio in ascolto sulla porta. Affinché i cinque indirizzi IP siano accessibili da Azure Load Balancer, registrare i cinque indirizzi IP nel Pool di indirizzi di back-end di Azure Load Balancer come mostrato di seguito.  Sarà anche necessario aggiungere le variabili all'inizio del modello nella sezione relativa alle variabili.

    Aggiungere questa sezione a Variables:

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    ```
    
    Aggiungere questa sezione alla risorsa set di scalabilità di macchine virtuali:

    ```json   
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
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
              }
   ```
 
3. Solo per i cluster Windows, configurare una regola del gruppo di sicurezza di rete che consenta di aprire la porta UDP/53 per la rete virtuale con i valori seguenti:

   |Impostazione |Value | |
   | --- | --- | --- |
   |Priorità |2000 | |
   |NOME |Custom_Dns  | |
   |`Source` |VirtualNetwork | |
   |Destination | VirtualNetwork | |
   |Service | DNS (UDP/53) | |
   |Azione | CONSENTI  | |
   | | |

4. Specificare la modalità di rete nel manifesto dell'applicazione per ogni servizio: `<NetworkConfig NetworkType="Open">`. La modalità di rete **Open** consente al servizio di ottenere un indirizzo IP dedicato. Se non è specificata alcuna modalità, viene impostata la modalità predefinita **nat**. Nell'esempio di manifesto seguente, i servizi `NodeContainerServicePackage1` e `NodeContainerServicePackage2` possono essere in ascolto sulla stessa porta (entrambi i servizi sono in ascolto su `Endpoint1`). Se è specificata la modalità di rete Open, non è possibile specificare configurazioni `PortBinding`.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="Open"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```

    È possibile combinare e abbinare diverse modalità di rete tra servizi all'interno di un'applicazione per un cluster di Windows. Alcuni servizi possono usare la modalità Open e altri la modalità nat. Se un servizio è configurato per usare la modalità nat, la porta su cui è in ascolto il servizio deve essere univoca.

    >[!NOTE]
    >Nei cluster Linux non è supportato l'uso di più modalità di rete per servizi diversi. 
    >

5. Se è selezionata la modalità **Open**, la definizione **Endpoint** nel manifesto del servizio deve puntare esplicitamente al pacchetto di codice corrispondente all'endpoint, anche se il pacchetto del servizio contiene un singolo pacchetto di codice. 
   
   ```xml
   <Resources>
     <Endpoints>
       <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" CodePackageRef="Code"/>
     </Endpoints>
   </Resources>
   ```
   
6. Per Windows, un riavvio della macchina virtuale causa una nuova creazione della rete aperta. Questo serve ad attenuare un problema sottostante dello stack di rete. Il comportamento predefinito corrisponde alla nuova creazione della rete. Se questo comportamento deve essere disattivato, è possibile usare la configurazione seguente e quindi un aggiornamento della configurazione.

```json
"fabricSettings": [
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "SkipContainerNetworkResetOnReboot",
                            "value": "true"
                    }
                    ]
                }
            ],          
 ``` 
 
## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sul modello applicativo di Service Fabric](service-fabric-application-model.md)
* [Informazioni sulle risorse del manifesto del servizio di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)
* [Distribuire un contenitore Windows in Service Fabric su Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuire un contenitore Docker in Service Fabric su Linux](service-fabric-get-started-containers-linux.md)
