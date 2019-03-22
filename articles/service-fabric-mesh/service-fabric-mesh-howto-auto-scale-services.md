---
title: Scalabilità automatica di un'app in esecuzione in Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni su come configurare i criteri di scalabilità automatica per i servizi di un'applicazione di Service Fabric Mesh.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2233dffabd7c76ca55cf215f8bc04e66134f5799
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338803"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Creare criteri di scalabilità automatica per un'applicazione di Service Fabric Mesh
Uno dei principali vantaggi della distribuzione di applicazioni in Service Fabric Mesh è la possibilità di ridurre o aumentare facilmente il numero di istanze dei servizi. Questo consente di gestire diverse quantità di carico nei servizi o migliorare la disponibilità. È possibile ridurre o aumentare manualmente le istanze dei servizi o impostare criteri di scalabilità automatica.

La [scalabilità automatica](service-fabric-mesh-scalability.md#autoscaling-service-instances) è una funzionalità aggiuntiva di Service Fabric che consente di ridimensionare dinamicamente il numero di istanze del servizio (scalabilità orizzontale). Offre una notevole elasticità e consente di effettuare il provisioning o la rimozione delle istanze del servizio in base all'utilizzo della CPU o della memoria.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opzioni per la creazione di un criterio, un trigger e meccanismo di scalabilità automatica
Un criterio di scalabilità automatica viene definito per ogni servizio che si desidera ridimensionare. Il criterio viene definito nel file di risorse del servizio YAML o nel modello di distribuzione JSON. Ogni criterio di scalabilità automatica è costituito da due parti: un trigger e un meccanismo di ridimensionamento.

Il trigger consente di definire quando viene richiamato un criterio di scalabilità automatica.  Specificare il tipo di trigger (carico medio) e la metrica da monitorare (CPU o memoria).  Le soglie di carico massimo e minimo vengono specificate come percentuale. L'intervallo di scala definisce la frequenza di controllo, in secondi, l'utilizzo specificato (ad esempio, carico medio della CPU), per tutte le istanze di servizio attualmente distribuite.  Il meccanismo si attiva quando la metrica monitorata scende al di sotto della soglia inferiore o aumenta al di sopra della soglia superiore.  

Il meccanismo di ridimensionamento definisce la modalità con cui eseguire l'operazione di ridimensionamento quando viene attivato il criterio.  Specificare il tipo di meccanismo (aggiungi/rimuovi replica); vengono considerate le repliche minima e massima (come numeri interi).  Il numero di repliche del servizio non verrà mai ridimensionato al di sopra del conteggio massimo o minimo.  Specificare anche l'incremento di ridimensionamento come un numero intero, ovvero il numero di repliche che verranno aggiunte o rimosse in un'operazione di ridimensionamento.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definire un criterio di scalabilità automatica in un modello JSON

L'esempio seguente mostra un criterio di scalabilità automatica in un modello di distribuzione JSON.  I criteri di scalabilità automatica vengono dichiarati in una proprietà del servizio da ridimensionare.  In questo esempio viene definito un trigger di carico medio della CPU.  Il meccanismo si attiva se il carico medio della CPU di tutte le istanze distribuite, scende sotto lo 0,2 (20%) o supera lo 0,8 (80%).  Il carico della CPU viene controllato ogni 60 secondi.  Il meccanismo di ridimensionamento aggiunge o rimuove istanze se il criterio viene attivato.  Verranno aggiunte o rimosse istanze del servizio in incrementi di uno.  Viene inoltre definito un numero minimo di istanze pari a uno e un numero massimo di istanze pari a 40.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definire un criterio di scalabilità automatica in un file di risorse service.yaml
L'esempio seguente mostra un criterio di scalabilità automatica in un file di risorse del servizio YAML.  I criteri di scalabilità automatica vengono dichiarati come proprietà del servizio da ridimensionare.  In questo esempio viene definito un trigger di carico medio della CPU.  Il meccanismo si attiva se il carico medio della CPU di tutte le istanze distribuite, scende sotto lo 0,2 (20%) o supera lo 0,8 (80%).  Il carico della CPU viene controllato ogni 60 secondi.  Il meccanismo di ridimensionamento aggiunge o rimuove istanze se il criterio viene attivato.  Verranno aggiunte o rimosse istanze del servizio in incrementi di uno.  Viene inoltre definito un numero minimo di istanze pari a uno e un numero massimo di istanze pari a 40.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [ridimensionare manualmente un servizio](service-fabric-mesh-tutorial-template-scale-services.md)
