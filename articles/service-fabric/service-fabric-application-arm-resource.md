---
title: Distribuire e aggiornare applicazioni e servizi con Azure Resource Manager | Microsoft Docs
description: Informazioni su come distribuire applicazioni e servizi in un cluster di Service Fabric usando un modello di Azure Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: a49c40efd1bab83dd274fd126c383a2d68354f75
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Gestire applicazioni e servizi come risorse di Azure Resource Manager

È possibile distribuire applicazioni e servizi in un cluster di Service Fabric tramite Azure Resource Manager. Di conseguenza, invece di distribuire e gestire le applicazioni tramite PowerShell o l'interfaccia della riga di comando dopo aver atteso che il cluster sia pronto, è ora possibile esprimere applicazioni e servizi in codice JSON e distribuirli nello stesso modello di Resource Manager usato per il cluster. L'intero processo di registrazione, provisioning e distribuzione delle applicazioni viene eseguito in un unico passaggio.

È consigliabile distribuire in questo modo tutte le applicazioni di configurazione, governance o gestione cluster necessarie nel cluster, come [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), i watchdog e tutte le applicazioni che devono essere eseguite nel cluster prima della distribuzione di altre applicazioni o servizi. 

Se possibile, gestire le applicazioni come risorse di Resource Manager in modo da usufruire dei vantaggi seguenti.
* Audit trail: Resource Manager controlla ogni operazione e mantiene un *log attività* dettagliato che consente di tenere traccia di tutte le modifiche apportate a queste applicazioni e al cluster.
* Controllo degli accessi in base al ruolo: è possibile gestire l'accesso ai cluster e alle applicazioni distribuite nel cluster tramite lo stesso modello di Resource Manager.
* Azure Resource Manager, tramite il portale di Azure, diventa un punto di accesso centralizzato per la gestione del cluster e delle distribuzioni di applicazioni critiche.

Il frammento seguente illustra le diverse tipologie di risorse che è possibile gestire tramite un modello:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Aggiungere una nuova applicazione al modello di Resource Manager

1. Preparare il modello di Resource Manager del cluster per la distribuzione. Per altre informazioni in proposito, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).
2. Esaminare alcune delle applicazioni che si intende distribuire nel cluster. Esistono applicazioni che saranno sempre in esecuzione e con cui altre applicazioni potrebbero stabilire dipendenze? È prevista la distribuzione di applicazioni di configurazione o governance del cluster? Questi tipi di applicazioni possono essere gestiti in modo ottimale tramite un modello di Resource Manager, come illustrato in precedenza. 
3. Dopo aver individuato le applicazioni da distribuire in questo modo, è necessario crearne un pacchetto, comprimerlo e inserirlo in una condivisione file. La condivisione deve essere accessibile tramite un endpoint REST per poter essere utilizzata da Azure Resource Manager durante la distribuzione.
4. Nel modello di Resource Manager, sotto la dichiarazione del cluster descrivere le proprietà di ogni applicazione. Tali proprietà includono il numero di repliche o istanze e tutte le catene di dipendenze tra le risorse (altre applicazioni o servizi). Per un elenco completo delle proprietà, vedere le [specifiche dell'API REST Swagger](https://aka.ms/sfrpswaggerspec). Si noti che ciò non sostituisce i manifesti dell'applicazione o del servizio, ma descrive parte del loro contenuto all'interno del modello di Resource Manager del cluster. Di seguito è riportato un modello di esempio che include la distribuzione di un servizio senza stato *Service1* e un servizio con stato *Service2* come parte di *Application1*:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > La proprietà *apiVersion* deve essere impostata su `"2017-07-01-preview"`. Questo modello può essere distribuito anche indipendentemente dal cluster, a condizione che il cluster sia già stato distribuito.

5. Eseguire la distribuzione. 

## <a name="manage-an-existing-application-via-resource-manager"></a>Gestire un'applicazione esistente tramite Resource Manager

Se il cluster è già attivo e si vogliono gestire come risorse di Resource Manager alcune applicazioni già distribuite nel cluster, invece di rimuovere le applicazioni e ridistribuirle è possibile eseguire una chiamata PUT usando le stesse API affinché le applicazioni vengano riconosciute come risorse di Resource Manager. 


## <a name="next-steps"></a>Passaggi successivi

* Usare l'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md) o [PowerShell](service-fabric-deploy-remove-applications.md) per distribuire altre applicazioni nel cluster. 
* [Aggiornare il cluster di Service Fabric](service-fabric-cluster-upgrade.md)

