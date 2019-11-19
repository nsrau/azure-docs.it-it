---
title: Creare un progetto con PowerShell
description: Usare Azure Blueprints per creare, definire e distribuire artefatti con PowerShell.
ms.date: 08/21/2019
ms.topic: quickstart
ms.openlocfilehash: 4cc181a7e0b07448d6dd538cfb1621d1efa43a80
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960433"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Guida introduttiva: Definire e assegnare un progetto Azure Blueprint con PowerShell

Imparare a creare e assegnare progetti consente di definire modelli comuni per sviluppare configurazioni riutilizzabili e rapidamente distribuibili in base a modelli di Resource Manager, criteri, sicurezza e altro ancora. In questa esercitazione viene descritto come usare Azure Blueprint per eseguire alcune della attività comuni di creazione, pubblicazione e assegnazione di un progetto all'interno dell'organizzazione, ad esempio:

> [!div class="checklist"]
> - Creare un nuovo progetto e aggiungere diversi elementi supportati
> - Apportare modifiche a un progetto esistente ancora in fase di **bozza**
> - Contrassegnare un progetto come pronto per l'assegnazione con stato **Pubblicato**
> - Assegnare un progetto a una sottoscrizione esistente
> - Controllare lo stato e l'avanzamento di un progetto assegnato
> - Rimuovere un progetto assegnato a una sottoscrizione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Se il modulo **Az.Blueprint** non è già installato, seguire le istruzioni contenute in [Aggiungere il modulo Az.Blueprint](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) per installarlo e convalidarlo da PowerShell Gallery.

## <a name="create-a-blueprint"></a>Creare un progetto

Il primo passaggio nella definizione di un modello standard per la conformità è la creazione di un progetto dalle risorse disponibili. Verrà creato un progetto denominato 'MyBlueprint' per configurare le assegnazioni di ruolo e di criteri per la sottoscrizione. Verrà quindi aggiunto un nuovo gruppo di risorse, un modello di Resource Manager e un'assegnazione di ruolo nel gruppo di risorse.

> [!NOTE]
> Quando si usa PowerShell, viene creato per primo l'oggetto _progetto_. Per ogni _elemento_ da aggiungere che include parametri, i parametri devono essere definiti in anticipo nel _progetto_ iniziale.

1. Creare l'oggetto _progetto_ iniziale. Il parametro **BlueprintFile** accetta un file JSON che include proprietà relative al progetto, tutti i gruppi di risorse da creare e tutti i parametri a livello di progetto. I parametri vengono impostati durante l'assegnazione e usati dagli elementi aggiunti nei passaggi successivi.

   - File JSON - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
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

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Usare il nome file _blueprint.json_ per la creazione di definizioni di progetto a livello di codice.
     > Questo nome file viene usato quando si chiama [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Per impostazione predefinita, l'oggetto progetto viene creato nella sottoscrizione predefinita. Per specificare il gruppo di gestione, usare il parametro **ManagementGroupId**. Per specificare la sottoscrizione, usare il parametro **SubscriptionId**.

1. Aggiungere un'assegnazione di ruolo nella sottoscrizione. **ArtifactFile** definisce il _tipo_ di artefatto, le proprietà vengono allineate all'identificatore della definizione del ruolo e le identità dell'entità vengono passate come matrice di valori. Nell'esempio seguente vengono configurate le identità delle entità cui è concesso il ruolo specificato in base a un parametro impostato durante l'assegnazione del progetto. Questo esempio usa il ruolo predefinito _Collaboratore_ con GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - File JSON - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Aggiungere un'assegnazione di criteri nella sottoscrizione. **ArtifactFile** definisce il _tipo_ di artefatto e le proprietà allineate un criterio o alla definizione dell'iniziativa, e configura l'assegnazione di criteri per l'uso dei parametri del progetto definiti da configurare durante l'assegnazione del progetto. Questo esempio usa il criterio predefinito _Applica tag e relativo valore predefinito ai gruppi di risorse_ con GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - File JSON - \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
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

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Aggiungere un'altra assegnazione di criteri per il tag di archiviazione (riutilizzando il parametro _storageAccountType_) nella sottoscrizione. Questo elemento di assegnazione di criteri aggiuntivo indica che un parametro definito nel progetto può essere usato da più di un elemento. Nell'esempio **storageAccountType** viene usato per impostare un tag nel gruppo di risorse. Questo valore fornisce informazioni sull'account di archiviazione che verrà creato nel passaggio successivo. Questo esempio usa il criterio predefinito _Applica tag e relativo valore predefinito ai gruppi di risorse_ con GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - File JSON - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
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

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Aggiungere un modello nel gruppo di risorse. **TemplateFile** per un modello di Resource Manager include il normale componente JSON del modello. Il modello riusa inoltre i parametri di progetto **storageAccountType**, **tagName** e **tagValue** passando ciascun parametro al modello. I parametri del progetto sono disponibili nel modello usando il parametro **TemplateParameterFile** e all'interno del file JSON del modello in cui viene usata questa coppia chiave-valore per inserire il valore. I nomi dei parametri del progetto e del modello possono essere gli stessi.

   - File JSON del modello di Azure Resource Manager - \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
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
     }
     ```

   - File JSON dei parametri del modello di Azure Resource Manager - \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
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
     ```

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Aggiungere un'assegnazione di ruolo nel gruppo di risorse. Analogamente all'immissione dell'assegnazione di ruolo precedente, l'esempio seguente usa l'identificatore della definizione per il ruolo **Proprietario** e fornisce un parametro diverso del progetto. Questo esempio usa il ruolo predefinito _Proprietario_ con GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - File JSON - \artifacts\roleOwner.json

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

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Pubblicare un progetto

Dopo aver aggiunto gli elementi al progetto, è possibile pubblicare il progetto. La pubblicazione rende disponibile il progetto per l'assegnazione a una sottoscrizione.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Il valore per `{BlueprintVersion}` è una stringa di lettere, numeri e trattini (senza spazi o altri caratteri speciali) con una lunghezza massima di 20 caratteri. Usare un nome univoco e informativo, ad esempio **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Assegnare un progetto

Dopo aver pubblicato un progetto usando PowerShell, è possibile assegnarlo a una sottoscrizione. Assegnare il progetto creato a una delle sottoscrizioni nella gerarchia dei gruppi di gestione. Se il progetto viene salvato in una sottoscrizione, può essere assegnato solo a tale sottoscrizione. Il parametro **Blueprint** specifica il progetto da assegnare. Per specificare i parametri relativi a nome, posizione, identità, blocco e progetto, usare i parametri di PowerShell corrispondenti nel cmdlet `New-AzBlueprintAssignment` o specificarli nel file JSON di parametri **AssignmentFile**.

1. Eseguire la distribuzione del progetto assegnandolo a una sottoscrizione. Poiché i parametri **contributors** e **owners** richiedono una matrice di elementi objectId delle entità cui concedere l'assegnazione di ruolo, usare l'[API Graph di Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) per raccogliere gli elementi objectId da usare in **AssignmentFile** per i propri utenti, gruppi o entità servizio.

   - File JSON - blueprintAssignment.json

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

   - Comando di PowerShell

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Identità gestita assegnata dall'utente

     L'assegnazione di un progetto può anche usare un'[identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/overview.md).
     In questo caso, la parte **identity** del file di assegnazione JSON cambia come segue. Sostituire `{tenantId}`, `{subscriptionId}`, `{yourRG}` e `{userIdentity}` rispettivamente con l'ID tenant, l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'identità gestita assegnata dall'utente.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     L'**identità gestita assegnata dall'utente** può trovarsi in qualsiasi sottoscrizione e gruppo di risorse per cui l'utente che assegna il progetto abbia le autorizzazioni.

     > [!IMPORTANT]
     > I progetti non gestiscono l'identità gestita assegnata dall'utente. Gli utenti sono responsabili dell'assegnazione di ruoli e autorizzazioni sufficienti, altrimenti l'assegnazione del progetto non riesce.

## <a name="unassign-a-blueprint"></a>Annullare l'assegnazione di un progetto

È possibile rimuovere un progetto da una sottoscrizione. La rimozione viene spesso eseguita quando le risorse dell'elemento non sono più necessarie. Quando un progetto viene rimosso, gli elementi assegnati nell'ambito del progetto vengono mantenuti. Per rimuovere l'assegnazione di un progetto, usare il cmdlet `Remove-AzBlueprintAssignment`:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](./concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](./concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](./concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](./concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](./how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](./troubleshoot/general.md).