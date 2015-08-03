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
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# Operazioni avanzate con i modelli

Questo argomento descrive l'operazione di copia e i modelli annidati che è possibile usare per eseguire le attività più avanzate quando si distribuiscono le risorse in Azure.

## copy

Consente di iterare un determinato numero di volte durante la distribuzione di una risorsa.
   
L'operazione di copia è particolarmente utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. La funzione **copyIndex()** restituisce il valore corrente per l'iterazione. È possibile distribuire tre siti Web denominati:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

con il modello seguente.

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

È inoltre possibile utilizzare l'operazione di copia senza una matrice. Ad esempio, è possibile aggiungere un numero incrementale alla fine del nome di ogni risorsa distribuita. È possibile distribuire tre siti Web denominati:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

con il modello seguente.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

Si noterà nell'esempio precedente che il valore di indice va da 0 a 2. Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione **copyIndex()**, ad esempio **copyIndex(1)**. Il numero di iterazioni da eseguire viene comunque specificato nell'elemento copia, ma il valore di copyIndex è compensato dal valore specificato. Quindi, utilizzando lo stesso modello dell'esempio precedente, ma specificando **copyIndex(1)** si distribuirebbero tre siti Web denominati:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Modello annidato

In alcuni casi potrebbe essere necessario unire due modelli o avviare un modello figlio da un modello padre. Per farlo, usare una risorsa di distribuzione all'interno del modello master che punta al modello nidificato. Impostare la proprietà **templateLink** sull'URI del modello nidificato. È possibile fornire i valori dei parametri per il modello nidificato specificando i valori direttamente nel modello o mediante il collegamento a un file di parametri. Nel primo esempio viene utilizzata la proprietà **parameters** per specificare un valore di parametro direttamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

Nell'esempio successivo viene utilizzata la proprietà **parametersLink** per il collegamento a un file di parametri.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Passaggi successivi
- [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md)
- [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md)
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](azure-portal/resource-group-template-deploy.md)
- [Panoramica di Gestione risorse di Microsoft Azure](./resource-group-overview.md)

<!---HONumber=July15_HO4-->