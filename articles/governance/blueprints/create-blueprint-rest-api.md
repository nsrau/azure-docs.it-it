---
title: Creare un progetto con l'API REST
description: Usare Azure Blueprint per creare, definire e distribuire elementi.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/15/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b66a1c2c12a97ea8754377a138b51a4ca1739c21
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320685"
---
# <a name="define-and-assign-an-azure-blueprint-with-rest-api"></a>Definire e assegnare un progetto Azure Blueprint con l'API REST

Imparare a creare e assegnare progetti consente di definire modelli comuni per sviluppare configurazioni riutilizzabili e rapidamente distribuibili in base a modelli di Resource Manager, criteri, sicurezza e altro ancora. In questa esercitazione viene descritto come usare Azure Blueprint per eseguire alcune della attività comuni di creazione, pubblicazione e assegnazione di un progetto all'interno dell'organizzazione, ad esempio:

> [!div class="checklist"]
> - Creare un nuovo progetto e aggiungere diversi elementi supportati
> - Apportare modifiche a un progetto esistente ancora in fase di **bozza**
> - Contrassegnare un progetto come pronto per l'assegnazione con stato **Pubblicato**
> - Assegnare un progetto a una sottoscrizione esistente
> - Controllare lo stato e l'avanzamento di un progetto assegnato
> - Rimuovere un progetto assegnato a una sottoscrizione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="getting-started-with-rest-api"></a>Introduzione all'API REST

Se non si ha familiarità con l'API REST, iniziare consultando le [informazioni di riferimento sull'API REST di Azure](/rest/api/azure/) per acquisire una comprensione generale dell'API REST, in particolare dell'URI della richiesta e del corpo della richiesta. Questo articolo usa questi concetti per fornire indicazioni sull'uso di Azure Blueprint e ne presuppone una certa conoscenza. Strumenti come [ARMClient](https://github.com/projectkudu/ARMClient) e altri possono gestire automaticamente l'autorizzazione e sono consigliati per gli utenti meno esperti.

Per le specifiche di progetti Bluprint, vedere [API REST di Azure Blueprints](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>API REST e PowerShell

Se non si ha già uno strumento per effettuare chiamate API REST, provare a usare PowerShell per queste istruzioni. Di seguito è riportata un'intestazione di esempio per l'autenticazione con Azure. Generare un'intestazione di autenticazione, talvolta denominata **bearer token**e fornire l'URI dell'API REST cui connettersi con qualsiasi parametro o un **corpo della richiesta**:

```powershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Sostituire `{subscriptionId}` nella variabile **$restUri** precedente per ottenere informazioni sulla sottoscrizione. La variabile $response contiene il risultato del cmdlet `Invoke-RestMethod`, che può essere analizzato con cmdlet come [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Se l'endpoint servizio API REST si aspetta un **corpo della richiesta**, fornire una variabile in formato JSON per il parametro `-Body` di `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Creare un progetto

Il primo passaggio nella definizione di un modello standard per la conformità è la creazione di un progetto dalle risorse disponibili. Verrà creato un progetto denominato 'MyBlueprint' per configurare le assegnazioni di ruolo e di criteri per la sottoscrizione. Verrà quindi aggiunto un nuovo gruppo di risorse, un modello di Resource Manager e un'assegnazione di ruolo nel gruppo di risorse.

> [!NOTE]
> Quando si usa l'API REST, viene creato per primo l'oggetto _progetto_. Per ogni _elemento_ da aggiungere che include parametri, i parametri devono essere definiti in anticipo nel _progetto_ iniziale.

In ogni URI dell'API REST vengono usate variabili che è necessario sostituire con i propri valori:

- `{YourMG}`: sostituire con l'ID del gruppo di gestione
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

1. Creare l'oggetto _progetto_ iniziale. Il **corpo della richiesta** include proprietà relative al progetto, tutti i gruppi di risorse da creare e tutti i parametri a livello di progetto. I parametri vengono impostati durante l'assegnazione e usati dagli elementi aggiunti nei passaggi successivi.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. Aggiungere un'assegnazione di ruolo nella sottoscrizione. Il **corpo della richiesta** definisce il _tipo_ di elemento, le proprietà allineate all'identificatore della definizione del ruolo e le identità dell'entità passate come matrice di valori. Nell'esempio seguente vengono configurate le identità delle entità cui è concesso il ruolo specificato in base a un parametro impostato durante l'assegnazione del progetto. Questo esempio usa il ruolo predefinito _Collaboratore_ con GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Aggiungere un'assegnazione di criteri nella sottoscrizione. Il **corpo della richiesta** definisce il _tipo_ di elemento e le proprietà allineate alla definizione di un'iniziativa o di criteri e configura l'assegnazione di criteri per l'uso dei parametri del progetto definiti da configurare durante l'assegnazione del progetto. Questo esempio usa il criterio predefinito _Applica tag e relativo valore predefinito ai gruppi di risorse_ con GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Aggiungere un'altra assegnazione di criteri per il tag di archiviazione (riutilizzando il parametro _storageAccountType_) nella sottoscrizione. Questo elemento di assegnazione di criteri aggiuntivo indica che un parametro definito nel progetto può essere usato da più di un elemento. Nell'esempio **storageAccountType** viene usato per impostare un tag nel gruppo di risorse. Questo valore fornisce informazioni sull'account di archiviazione che verrà creato nel passaggio successivo. Questo esempio usa il criterio predefinito _Applica tag e relativo valore predefinito ai gruppi di risorse_ con GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. Aggiungere un modello nel gruppo di risorse. Il **corpo della richiesta** per un modello di Resource Manager include il componente JSON normale del modello e definisce il gruppo di risorse di destinazione con **properties.resourceGroup**. Il modello riusa inoltre i parametri di progetto **storageAccountType**, **tagName** e **tagValue** passando ciascun parametro al modello. I parametri del progetto sono disponibili nel modello definendo **properties.parameters** e all'interno del modello JSON in cui viene usata questa coppia chiave/valore per inserire il valore. I nomi dei parametri di progetto e di modello potrebbero essere identici, ma sono stati progettati in modo diverso per indicare come ciascuno viene passato dal progetto all'elemento del modello.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroup().location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Aggiungere un'assegnazione di ruolo nel gruppo di risorse. Analogamente all'immissione dell'assegnazione di ruolo precedente, l'esempio seguente usa l'identificatore della definizione per il ruolo **Proprietario** e fornisce un parametro diverso del progetto. Questo esempio usa il ruolo predefinito _Proprietario_ con GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>Pubblicare un progetto

Dopo aver aggiunto gli elementi al progetto, è possibile pubblicare il progetto. La pubblicazione rende disponibile il progetto per l'assegnazione a una sottoscrizione.

- URI DELL'API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2017-11-11-preview
  ```

Il valore per `{BlueprintVersion}` è una stringa di lettere, numeri e trattini (senza spazi o altri caratteri speciali) con una lunghezza massima di 20 caratteri. Usare un nome univoco e informativo, ad esempio **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Assegnare un progetto

Dopo aver pubblicato un progetto usando l'API REST, è possibile assegnarlo a una sottoscrizione. Assegnare il progetto creato a una delle sottoscrizioni nella gerarchia dei gruppi di gestione. Il **corpo della richiesta** specifica il progetto da assegnare, fornisce il nome e la località per tutti i gruppi di risorse nella definizione del progetto e fornisce tutti i parametri definiti nel progetto e usati da uno o più elementi collegati.

1. Fornire all'entità servizio di Azure Blueprint il ruolo **Proprietario** nella sottoscrizione di destinazione. L'ID app è statico (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), ma l'ID entità servizio varia in base al tenant. È possibile richiedere dettagli per il tenant usando l'API REST seguente. Viene usata l'[API Graph di Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md), che ha autorizzazione diverse.

   - URI DELL'API REST

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Eseguire la distribuzione del progetto assegnandolo a una sottoscrizione. Poiché i parametri **contributors** e **owners** richiedono una matrice di elementi objectId delle entità cui concedere l'assegnazione di ruolo, usare l'[API Graph di Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) per raccogliere gli elementi objectId da usare nel **corpo della richiesta** per i propri utenti, gruppi o entità servizio.

   - URI DELL'API REST

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
     ```

   - Request Body

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

## <a name="unassign-a-blueprint"></a>Annullare l'assegnazione di un progetto

È possibile rimuovere un progetto da una sottoscrizione. La rimozione viene spesso eseguita quando le risorse dell'elemento non sono più necessarie. Quando un progetto viene rimosso, gli elementi assegnati nell'ambito del progetto vengono mantenuti. Per rimuovere un'assegnazione del progetto, usare l'operazione API REST seguente:

- URI DELL'API REST

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="delete-a-blueprint"></a>Eliminare un progetto

Per rimuovere il progetto stesso, usare l'operazione API REST seguente:

- URI DELL'API REST

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](./concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](./concepts/parameters.md)
- Imparare a personalizzare l'[ordine in sequenza del progetto](./concepts/sequencing-order.md)
- Scoprire come usare in modo ottimale il [blocco delle risorse del progetto](./concepts/resource-locking.md)
- Informazioni su come [aggiornare assegnazioni esistenti](./how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](./troubleshoot/general.md)