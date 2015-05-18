<properties
   pageTitle="Operazioni avanzate con i modelli di Gestione risorse di Azure"
   description="Descrive come usare i modelli annidati e l'operazione di copia in un modello di Gestione risorse di Azure quando si distribuiscono app in Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Operazioni avanzate con i modelli

Questo argomento descrive l'operazione di copia e i modelli annidati che è possibile usare per eseguire le attività più avanzate quando si distribuiscono le risorse in Azure.

## copy

Consente di usare l'iterazione tramite una matrice e di usare tutti gli elementi durante la distribuzione di una risorsa.
   
L'esempio seguente consente di distribuire tre siti Web denominati examplecopy-Contoso, examplecopy-Fabrikam ed examplecopy-Coho.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Modello annidato

In alcuni casi potrebbe essere necessario unire due modelli o avviare un modello figlio da un modello padre. Per farlo, usare una risorsa di distribuzione all'interno del modello master per distribuire un modello figlio. Specificare l'URI del modello annidato, come mostrato di seguito.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Passaggi successivi
- [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md)
- [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md)
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](./resouce-group-template-deploy.md)
- [Panoramica di Gestione risorse di Azure](./resource-group-overview.md)

<!--HONumber=52-->
