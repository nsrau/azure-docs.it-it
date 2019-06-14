---
title: Configurare un Gateway per indirizzare le richieste | Microsoft Docs
description: Informazioni su come configurare il gateway che gestisce il traffico in ingresso per le applicazioni in esecuzione in Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2e2502e35b3720ddbfe5950b89e2388de378f2ba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583642"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>Configurare una risorsa del Gateway per indirizzare le richieste

Una risorsa del Gateway viene usata per indirizzare il traffico in ingresso alla rete che ospita l'applicazione. Configurare la risorsa in modo da specificare le regole tramite cui le richieste vengono indirizzate a endpoint o servizi specifici basati sulla struttura della richiesta. Per altre informazioni sulle reti e i gateway in Mesh, vedere l’[Introduzione alla funzionalità di rete in Service Fabric Mesh](service-fabric-mesh-networks-and-gateways.md). 

Le risorse del gateway devono essere dichiarate come parte del modello di distribuzione (JSON o yaml) e dipendono da una risorsa di rete. Questo documento descrive le varie proprietà che possono essere impostate per il gateway e illustra un esempio di configurazione dello stesso.

## <a name="options-for-configuring-your-gateway-resource"></a>Opzioni per configurare la risorsa del Gateway

Poiché la risorsa del Gateway funge da ponte tra la rete dell'applicazione e la rete dell'infrastruttura sottostante (la rete `open`), è necessario configurarne soltanto una (nell'anteprima di Mesh è previsto il limite di un gateway per ogni app). La dichiarazione per la risorsa è costituita da due parti principali: metadati delle risorse e proprietà. 

### <a name="gateway-resource-metadata"></a>Metadati della risorsa del gateway

Un gateway viene dichiarato con i metadati seguenti:
* `apiVersion` - deve essere impostato su "2018-09-01-preview" (o, successivamente, in una data futura)
* `name` - un nome di stringa per questo gateway
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` - deve essere impostato sulla posizione dell'app o della rete; sarà in genere un riferimento al parametro di posizione nella distribuzione
* `dependsOn` - la rete per la quale questo gateway funge da punto di ingresso

Ecco cosa accade in un modello di distribuzione Azure Resource Manager (JSON): 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>Proprietà del gateway

La sezione proprietà viene usata per definire le reti tra cui si trova il gateway e le regole per le richieste di routing. 

#### <a name="source-and-destination-network"></a>Rete di origine e di destinazione 

Ogni gateway richiede una `sourceNetwork` e `destinationNetwork`. La rete di origine è definita come la rete da cui l'app riceverà le richieste in ingresso. La proprietà nome deve essere sempre impostata su "Open". La rete di destinazione è la rete a cui sono destinate le richieste. Il valore del nome per quest'ultima deve essere impostato sul nome della risorsa di rete locale dell'app (deve includere un riferimento completo alla risorsa). Vedere qui di seguito una configurazione di esempio per questo tipo di distribuzione in una rete denominata "myNetwork".

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>Regole 

Un gateway può avere più regole di routing che specificano come verrà gestito il traffico in ingresso. Una regola di routing definisce la relazione tra la porta di ascolto e l'endpoint di destinazione per una determinata applicazione. Per le regole di routing TCP, è disponibile un mapping 1:1 tra porta ed Endpoint. Per le regole di routing HTTP, è possibile impostare regole di routing più complesse che esaminano il percorso della richiesta e, facoltativamente, le intestazioni, per decidere come verrà indirizzata la richiesta. 

Le regole di routing vengono definite in base alla porta. Ogni porta in ingresso possiede una propria matrice di regole all'interno della sezione proprietà della configurazione del gateway. 

#### <a name="tcp-routing-rules"></a>Le regole di routing TCP 

Una regola di routing TCP include le seguenti proprietà: 
* `name` - riferimento alla regola, costituita da qualsiasi stringa a scelta 
* `port` - porta su cui ascoltare le richieste in ingresso 
* `destination` - specifica degli endpoint, inclusi `applicationName`, `serviceName`, e `endpointName`, a cui dovranno essere indirizzate le richieste

Di seguito un esempio di regola di instradamento TCP:

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>Le regole di routing HTTP 

Una regola di routing HTTP include le seguenti proprietà: 
* `name` - riferimento alla regola, costituita da qualsiasi stringa a scelta 
* `port` - porta su cui ascoltare le richieste in ingresso 
* `hosts` - una matrice di criteri che si applicano alle richieste in arrivo ai vari "host" sulla porta specificata in precedenza. Gli host sono il set di applicazioni e servizi che potrebbero essere in esecuzione nella rete e che possono servire le richieste in ingresso, ad esempio un'app web. I criteri host vengono interpretati in ordine, pertanto è consigliabile creare i seguenti livelli di specificità decrescenti
    * `name` - il nome DNS dell'host per il quale vengono specificate le seguenti regole di routing. Usare qui "*" vorrebbe dire creare regole di routing per tutti gli host.
    * `routes` - una matrice di criteri per l'host specifico
        * `match` - specifica della struttura delle richieste in ingresso per l'applicazione di questa regola, in base a un `path`
            * `path` - contiene un `value` (URI in ingresso), `rewrite` (come si desidera che la richiesta venga inoltrata) e un `type` (attualmente può essere solo "Prefix")
            * `header` - è una matrice facoltativa dei valori di intestazioni da confrontare nell'intestazione della richiesta se questa corrisponde alla specifica del percorso (sopra).
              * ogni voce contiene `name` (nome di stringa dell'intestazione da confrontare), `value` (valore dell'intestazione nella richiesta di stringhe) e `type` (attualmente può essere solo "Exact")
        * `destination` - se la richiesta corrisponde, verrà indirizzato a questa destinazione, che viene specificata usando un `applicationName`, `serviceName`, e `endpointName`

Di seguito un esempio di regola di routing HTTP esempio che si applicherebbe alle richieste in arrivo sulla porta 80, a tutti gli host serviti dalle app nella rete. Se l'URL della richiesta ha una struttura che corrisponde alla specifica del percorso, ad esempio, `<IPAddress>:80/pickme/<requestContent>`, verrà quindi indirizzato all'endpoint `myListener`.  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>Esempio di configurazione della risorsa Gateway 

Ecco come appare una configurazione completa della risorsa Gateway (adattato dall'esempio in ingresso disponibile nel [repository di esempi di Mesh](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)):

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

Questo gateway è configurato per un'applicazione Linux "meshAppLinux", costituita da almeno due servizi: "helloWorldService" e "counterService", in ascolto sulla porta 80. A seconda della struttura dell'URL della richiesta in ingresso, la richiesta verrà indirizzata a uno di questi servizi. 
* "\<IndirizzoIP >: 80/helloWorld/\<richiesta\>" comporterebbe una richiesta viene indirizzata a "helloWorldListener" nel helloWorldService. 
* "\<IndirizzoIP >: 80/contatore/\<richiesta\>" comporterebbe una richiesta viene indirizzata a "counterListener" nel counterService. 

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare i gateway in azione, distribuire l'[esempio in ingresso](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress)
