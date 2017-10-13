---
title: "Configurare le modalità di rete per i servizi del contenitore di Azure Service Fabric | Microsoft Docs"
description: "Informazioni su come configurare le diverse modalità di rete supportate da Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 1ecded3af6396f50e67dc5d2a9ef8337699046ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Modalità di rete del contenitore di Service Fabric

La modalità del servizio di rete predefinita offerta nel cluster di Service Fabric per i servizi del contenitore è la modalità di rete `nat`. Con la modalità di rete `nat`, la presenza di più di un servizio di contenitori in ascolto per gli stessi risultati di porta comporta errori di distribuzione. Per eseguire diversi servizi in ascolto sulla stessa porta, Service Fabric supporta la modalità di rete `open` (versione 5.7 o versioni successive). Con la modalità di rete `open`, ogni servizio del contenitore ottiene un indirizzo IP assegnato dinamicamente internamente, consentendo più servizi in ascolto sulla stessa porta.   

Pertanto, con un singolo tipo di servizio con un endpoint statico definito nel manifesto del servizio, è possibile creare ed eliminare nuovi servizi senza errori di distribuzione tramite la modalità di rete `open`. Analogamente, è possibile usare lo stesso file `docker-compose.yml` con il mapping delle porte statiche per la creazione di più servizi.

Usare l'IP assegnato dinamicamente per individuare i servizi non è consigliabile poiché l'indirizzo IP cambia quando il servizio viene riavviato o viene spostato in un altro nodo. Usare solo **Service Fabric Naming Service** o il **servizio DNS** per l'individuazione del servizio. 


> [!WARNING]
> È consentito solo un totale di 4096 indirizzi IP per ogni rete virtuale in Azure. Di conseguenza, la somma del numero di nodi e il numero di istanze del servizio contenitore (con rete `open`) non può superare i 4096 all'interno di una rete virtuale. Per questi scenari ad alta densità, si consiglia la modalità di rete `nat`.
>

## <a name="setting-up-open-networking-mode"></a>Configurazione della modalità di rete aperta

1. Configurare il modello di Azure Resource Manager, abilitando il servizio DNS e il provider di IP in `fabricSettings`. 

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
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
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

2. Configurare la sezione del profilo di rete per consentire la configurazione di più indirizzi IP in ogni nodo del cluster. Nell'esempio seguente si configurano cinque indirizzi IP per ogni nodo (pertanto è possibile avere cinque istanze del servizio in ascolto della porta su ogni nodo) per un cluster di Windows/Linux Service Fabric.

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
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
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
 

3. Solo per i cluster di Windows, configurare una regola del gruppo di sicurezza di rete aprendo la porta UDP/53 per la rete virtuale con i valori seguenti:

   | Priorità |    Nome    |    Sorgente      |  Destination   |   Service    | Azione |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | CONSENTI  |


4. Specificare la modalità di rete nel manifesto dell'app per ogni servizio `<NetworkConfig NetworkType="open">`.  La modalità `open` consente al servizio di ottenere un indirizzo IP dedicato. Se non si specifica una modalità, viene impostata la modalità predefinita di base `nat`. Pertanto, nell'esempio di manifesto seguente, `NodeContainerServicePackage1` e `NodeContainerServicePackage2` possono essere in ascolto sulla stessa porta (entrambi i servizi sono in ascolto su `Endpoint1`).

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
È possibile combinare e abbinare diverse modalità di rete tra servizi all'interno di un'applicazione per un cluster di Windows. Pertanto, è possibile avere alcuni servizi sulla modalità `open` e alcuni sulla modalità di rete `nat`. Quando un servizio è configurato con `nat`, la porta su cui è in ascolto deve essere univoca. La combinazione di modalità di rete per diversi servizi non è supportata nei cluster di Linux. 


## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono state fornite informazioni sulle modalità di rete offerte da Service Fabric.  

* [Modello di applicazione di Service Fabric](service-fabric-application-model.md)
* [Risorse del manifesto del servizio di Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Distribuire un contenitore Windows in Service Fabric su Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuire un contenitore Docker in Service Fabric su Linux](service-fabric-get-started-containers-linux.md)
