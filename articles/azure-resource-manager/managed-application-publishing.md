---
title: Creare e pubblicare un&quot;applicazione gestita di Azure | Microsoft Docs
description: Illustra come un fornitore di software indipendente o un partner crea un&quot;applicazione gestita di Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>Creare e pubblicare un'applicazione gestita di Azure 

Come illustrato nell'articolo [Panoramica di Applicazione gestita di Azure](managed-application-overview.md), gli scenari nell'esperienza end-to-end sono due. Nel primo l'entità di pubblicazione o il fornitore di software indipendente vuole creare un'applicazione gestita che verrà usata dai clienti. Il secondo è relativo al cliente o all'utente dell'applicazione gestita. Questo articolo illustra il primo scenario e spiega come un fornitore di software indipendente può creare e pubblicare un'applicazione gestita. 

Per creare un'applicazione gestita, è necessario creare:

* Un pacchetto contenente i file modello
* L'utente, gruppo o applicazione che ha accesso al gruppo di risorse nella sottoscrizione del cliente
* La definizione dell'appliance

Per gli esempi dei file, vedere [Managed Application samples](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) (Esempi di applicazione gestita).

## <a name="create-managed-application-package"></a>Creare il pacchetto dell'applicazione gestita

Il primo passaggio è la creazione del pacchetto dell'applicazione gestita contenente i file modello principali. L'entità di pubblicazione o il fornitore di software indipendente crea tre file. 

* Il primo file è denominato **applianceMainTemplate.json**. Questo file modello definisce le risorse effettive di cui viene effettuato il provisioning come parte dell'applicazione gestita. Ad esempio, per creare un account di archiviazione usando un'applicazione gestita, applianceMainTemplate.json contiene: 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
            "storageAccountName": {
                "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {        
          }
      }],
      "outputs": {        
      }
  }
  ```

* Il secondo file che l'entità di pubblicazione deve creare è **mainTemplate.json**. Il file modello contiene solo la risorsa dell'appliance (Microsoft.Solutions/appliances). Contiene anche tutti i parametri necessari per le risorse in applianceMainTemplate.json. 

  Due importanti proprietà sono necessarie come input durante la creazione dell'applicazione gestita. La proprietà **managedResourceGroupId** è l'ID del gruppo di risorse in cui vengono create le risorse definite in applianceMainTemplate.json. Il formato dell'ID è: `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  La proprietà **applianceDefinitionId** è l'ID della risorsa di definizione di applicazione gestita. L'ID è nel formato: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  Fornire i parametri per questi due valori in modo che l'utente possa specificarli quando crea un'applicazione gestita. Nell'esempio seguente i due parametri corrispondenti a queste proprietà sono managedByResourceGroup e applianceDefinitonId.

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }                
              }
          }
      }]
  }
  ```

* Il terzo file necessario nel pacchetto è **createUiDefinition.json**. Il portale di Azure usa questo file per generare l'interfaccia utente che consente agli utenti di creare l'applicazione gestita. Si definiscono i parametri per l'applicazione gestita e il modo in cui gli utenti possono ottenere l'input per ogni parametro. È possibile usare opzioni come un selettore a discesa, una casella di testo, una casella per la password e altri strumenti di input. Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).

Quando tutti i file necessari sono pronti, si carica il pacchetto in una posizione accessibile da dove può essere utilizzato.


## <a name="create-azure-ad-user-group-or-application"></a>Creare l'applicazione o il gruppo utenti di Azure AD
Creare quindi un gruppo utenti o un'applicazione che si vuole usare per gestire le risorse per conto del cliente. Questo gruppo utenti o questa applicazione ha le autorizzazioni per il gruppo di risorse gestito come indicato dal ruolo. Il ruolo può essere un ruolo Controllo degli accessi in base al ruolo predefinito, ad esempio **Proprietario** o **Collaboratore**. È anche possibile concedere a un singolo utente le autorizzazioni per gestire le risorse, ma in genere si assegna questa autorizzazione per usare un gruppo utenti. Per creare un nuovo gruppo utenti di Active Directory, usare:

```azurecli
az ad group create –display-name "name" –mail-nickname "nickname"
```

È anche possibile usare un gruppo esistente. È necessario l'ID oggetto del nuovo gruppo utenti creato o di uno esistente. L'esempio seguente illustra come ottenere l'ID oggetto dal nome visualizzato usato per creare il gruppo.

```azurecli
az ad group show –group "groupName"
```

Esempio:

```azurecli
az ad group show --group ravAppliancetestADgroup
```

Viene restituito l'output seguente:

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
È necessario il valore objectId precedente. 

## <a name="get-the-role-definition-id"></a>Ottenere l'ID di definizione del ruolo

Ora è necessario l'ID di definizione del ruolo Controllo degli accessi in base al ruolo predefinito a cui si vuole concedere l'accesso all'utente, al gruppo utenti o all'applicazione. In genere si usa il ruolo "Proprietario", "Collaboratore" o "Lettore". Il comando seguente illustra come ottenere l'ID di definizione per il ruolo "Proprietario":

```azurecli
az role definition list --name owner
```

Viene restituito l'output seguente:

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

È necessario il valore della proprietà "name" dell'esempio precedente.


## <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Il passaggio finale è la creazione della risorsa di definizione di applicazione gestita. Dopo avere creato il pacchetto dell'appliance e le autorizzazioni, creare la definizione dell'appliance usando il comando seguente: 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

I parametri usati nell'esempio precedente sono i seguenti:

- resource-group: nome del gruppo di risorse in cui viene creata la definizione dell'appliance.
- lock-level: tipo di blocco inserito nel gruppo di risorse gestito. Impedisce al cliente di eseguire operazioni indesiderate su questo gruppo di risorse. **ReadOnly** è attualmente il solo livello di blocco supportato. Quando ReadOnly è specificato, il cliente può solo leggere le risorse presenti nel gruppo di risorse gestito.
- authorizations: indica l'ID dell'entità di sicurezza e l'ID di definizione del ruolo usati per concedere l'autorizzazione al gruppo di risorse gestito. Viene specificato nel formato `<principalId>:<roleDefinitionId>`. Per questa proprietà si possono specificare anche altri valori. Se sono necessari più valori, deve essere specificata nel formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. I valori multipli sono separati da uno spazio.
- package-file-uri: posizione del pacchetto dell'appliance contenente i file modello, che può essere un BLOB del servizio di archiviazione di Azure. 


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](managed-application-overview.md).
* Per gli esempi dei file, vedere [Managed Application samples](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) (Esempi di applicazione gestita).
* Per informazioni sull'esperienza dell'utente, vedere [Utilizzare un'applicazione gestita di Azure](managed-application-consumption.md).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
