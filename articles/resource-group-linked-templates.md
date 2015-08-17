<properties
   pageTitle="Uso di modelli collegati con Gestione risorse di Azure"
   description="Descrive come usare i modelli collegati in un modello di Gestione risorse di Azure per creare una soluzione basata su un modello modulare. Mostra come passare i valori dei parametri, specificare un file di parametri e gli URL creati in modo dinamico."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="tomfitz"/>

# Uso di modelli collegati con Gestione risorse di Azure

Dall'interno di un modello di Gestione risorse di Azure, è possibile collegarsi a un altro modello che consente di scomporre la distribuzione in un set di modelli di destinazione specifici. In modo analogo alla scomposizione di un'applicazione in un numero di classi di codice, la scomposizione offre vantaggi in termini di test, riuso e leggibilità.

È possibile passare parametri da un modello principale a un modello collegato. Tali parametri possono venire associati direttamente ai parametri e alle variabili esposti dal modello chiamante. Il modello collegato può inoltre passare una variabile di output al modello di origine, consentendo un scambio bidirezionale di dati tra modelli.

## Collegamento a un modello

Per creare un collegamento tra due modelli, aggiungere una risorsa di distribuzione all'interno del modello principale che punta al modello collegato. Impostare la proprietà **templateLink** sull'URI del modello collegato. È possibile fornire i valori dei parametri per il modello nidificato specificando i valori direttamente nel modello o mediante il collegamento a un file di parametri. Nel primo esempio viene usata la proprietà **parameters** per specificare direttamente un valore di parametro.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Collegamento a un file di parametri

Nell'esempio successivo viene utilizzata la proprietà **parametersLink** per il collegamento a un file di parametri.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Uso di variabili per collegare i modelli

Negli esempi precedenti sono illustrati i valori di URL hard-coded per i collegamenti del modello. Questo approccio potrebbe funzionare per un modello semplice ma non funziona correttamente in caso di uso di un ampio set di modelli modulari. In alternativa, è possibile creare una variabile statica contenente un URL di base per il modello principale e quindi creare dinamicamente gli URL per i modelli collegati da tale URL di base. Il vantaggio di questo approccio è rappresentato dal fatto che risulta più semplice spostare o scomporre il modello perché è sufficiente modificare la variabile statica nel modello principale. Il modello principale passa gli URI corretti a tutto il modello scomposto.

Nell'esempio seguente viene illustrato come usare un URL di base per creare due URL per i modelli collegati (**sharedTemplateUrl** e **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

## Passaggio di valori da un modello collegato

Se è necessario passare un valore dal modello collegato al modello principale, è possibile creare un valore nella sezione **outputs** del modello collegato. Per un esempio vedere[Condivisione dello stato in modelli di Gestione risorse di Azure](best-practices-resource-manager-state.md).

## Passaggi successivi
- [Creazione di modelli](./resource-group-authoring-templates.md)
- [Distribuzione di modelli](azure-portal/resource-group-template-deploy.md)

<!---HONumber=August15_HO6-->