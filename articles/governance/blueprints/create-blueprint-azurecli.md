---
title: "Avvio rapido: Creare un progetto con l'interfaccia della riga di comando di Azure"
description: In questa guida di avvio rapido si userà Azure Blueprints per creare, definire e distribuire artefatti con l'interfaccia della riga di comando di Azure.
ms.date: 06/02/2020
ms.topic: quickstart
ms.openlocfilehash: 3dfb74469bc186c25b918bfe87c7ce6f1930f2e1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91667708"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Avvio rapido: Definire e assegnare un progetto Azure Blueprint con l'interfaccia della riga di comando di Azure

Informazioni su come creare e assegnare progetti consente di definire modelli comuni per sviluppare configurazioni riutilizzabili e rapidamente distribuibili in base a modelli di Azure Resource Manager (modelli ARM), criteri, sicurezza e altro ancora. In questa esercitazione viene descritto come usare Azure Blueprint per eseguire alcune della attività comuni di creazione, pubblicazione e assegnazione di un progetto all'interno dell'organizzazione, ad esempio:

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Aggiungere l'estensione Blueprint

Per abilitare l'interfaccia della riga di comando di Azure per gestire le definizioni e le assegnazioni del progetto, è necessario aggiungere l'estensione.
Questa estensione funziona ovunque sia possibile usare l'interfaccia della riga di comando di Azure, fra cui [bash in Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) sia autonomo che nel portale e l'[immagine Docker di Azure PowerShell](https://hub.docker.com/_/microsoft-azure-cli), oppure installata in locale.

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.0.76**. Se non è ancora installato, seguire [queste istruzioni](/cli/azure/install-azure-cli-windows).

1. Importarla nell'ambiente della riga di comando di Azure scelto usando il comando seguente:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Verificare che l'estensione sia stata installata e che la versione sia quella prevista (almeno **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Creare un progetto

Il primo passaggio nella definizione di un modello standard per la conformità è la creazione di un progetto dalle risorse disponibili. Verrà creato un progetto denominato 'MyBlueprint' per configurare le assegnazioni di ruolo e di criteri per la sottoscrizione. Verranno quindi aggiunti un gruppo di risorse, un modello di Resource Manager e un'assegnazione di ruolo nel gruppo di risorse.

> [!NOTE]
> Quando si usa l'interfaccia della riga di comando di Azure, viene creato per primo l'oggetto _progetto_. Per ogni _elemento_ da aggiungere che include parametri, i parametri devono essere definiti in anticipo nel _progetto_ iniziale.

1. Creare l'oggetto _progetto_ iniziale. Il parametro **parameters** accetta un file JSON che include tutti i parametri a livello di progetto. I parametri vengono impostati durante l'assegnazione e usati dagli elementi aggiunti nei passaggi successivi.

   - File JSON - blueprintparms.json

     ```json
     {
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
     }
     ```

   - Comando dell'interfaccia della riga di comando di Azure

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Usare il nome file _blueprint.json_ per l'importazione delle definizioni di progetto.
     > Questo nome file viene usato quando si chiama [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext-blueprint-az-blueprint-import).

     Per impostazione predefinita, l'oggetto progetto viene creato nella sottoscrizione predefinita. Per specificare il gruppo di gestione, usare il parametro **managementgroup**. Per specificare la sottoscrizione, usare il parametro **subscription**.

1. Aggiungere il gruppo di risorse per gli artefatti di archiviazione alla definizione.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Aggiungere un'assegnazione di ruolo nella sottoscrizione. Nell'esempio seguente vengono configurate le identità delle entità cui è concesso il ruolo specificato in base a un parametro impostato durante l'assegnazione del progetto. Questo esempio usa il ruolo predefinito _Collaboratore_ con GUID `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Aggiungere un'assegnazione di criteri nella sottoscrizione. Questo esempio usa il criterio predefinito _Applica tag e relativo valore predefinito ai gruppi di risorse_ con GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - File JSON - artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Comando dell'interfaccia della riga di comando di Azure

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. Aggiungere un'altra assegnazione di criteri per il tag di archiviazione (riutilizzando il parametro _storageAccountType_) nella sottoscrizione. Questo elemento di assegnazione di criteri aggiuntivo indica che un parametro definito nel progetto può essere usato da più di un elemento. Nell'esempio **storageAccountType** viene usato per impostare un tag nel gruppo di risorse. Questo valore fornisce informazioni sull'account di archiviazione che verrà creato nel passaggio successivo. Questo esempio usa il criterio predefinito _Applica tag e relativo valore predefinito ai gruppi di risorse_ con GUID `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - File JSON - artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Comando dell'interfaccia della riga di comando di Azure

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. Aggiungere un modello nel gruppo di risorse. Il parametro **template** per un modello di Resource Manager include i normali componenti JSON del modello. Il modello riusa inoltre i parametri di progetto **storageAccountType**, **tagName** e **tagValue** passando ciascun parametro al modello. I parametri del progetto sono disponibili nel modello usando il parametro **parameters** e all'interno del file JSON del modello in cui viene usata questa coppia chiave-valore per inserire il valore. I nomi dei parametri del progetto e del modello possono essere gli stessi.

   - File JSON del modello di Resource Manager - artifacts\templateStorage.json

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

   - File JSON dei parametri del modello di Resource Manager - artifacts\templateStorageParams.json

     ```json
     {
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
     ```

   - Comando dell'interfaccia della riga di comando di Azure

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. Aggiungere un'assegnazione di ruolo nel gruppo di risorse. Analogamente all'immissione dell'assegnazione di ruolo precedente, l'esempio seguente usa l'identificatore della definizione per il ruolo **Proprietario** e fornisce un parametro diverso del progetto. Questo esempio usa il ruolo predefinito _Proprietario_ con GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Pubblicare un progetto

Dopo aver aggiunto gli elementi al progetto, è possibile pubblicare il progetto. La pubblicazione rende disponibile il progetto per l'assegnazione a una sottoscrizione.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

Il valore per `{BlueprintVersion}` è una stringa di lettere, numeri e trattini (senza spazi o altri caratteri speciali) con una lunghezza massima di 20 caratteri. Usare un nome univoco e informativo, ad esempio **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Assegnare un progetto

Dopo aver pubblicato un progetto usando l'interfaccia della riga di comando di Azure, è possibile assegnarlo a una sottoscrizione. Assegnare il progetto creato a una delle sottoscrizioni nella gerarchia dei gruppi di gestione. Se il progetto viene salvato in una sottoscrizione, può essere assegnato solo a tale sottoscrizione. Il parametro **blueprint-name** specifica il progetto da assegnare. Per specificare i parametri relativi a nome, posizione, identità, blocco e progetto, usare i parametri dell'interfaccia della riga di comando di Azure corrispondenti nel comando `az blueprint assignment create` o specificarli nel file JSON di **parametri**.

1. Eseguire la distribuzione del progetto assegnandolo a una sottoscrizione. Poiché i parametri **contributors** e **owners** richiedono una matrice di elementi objectId delle entità cui concedere l'assegnazione di ruolo, usare l'[API Graph di Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) per raccogliere gli elementi objectId da usare nei **parametri** per i propri utenti, gruppi o entità servizio.

   - File JSON - blueprintAssignment.json

     ```json
     {
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
     ```

   - Comando dell'interfaccia della riga di comando di Azure

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Identità gestita assegnata dall'utente

     L'assegnazione di un progetto può anche usare un'[identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/overview.md).
     In questo caso, il parametro **identity-type** è impostato su _UserAssigned_ e il parametro **user-assigned-identities** specifica l'identità. Sostituire `{userIdentity}` con il nome dell'identità gestita assegnata dall'utente.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     L'**identità gestita assegnata dall'utente** può trovarsi in qualsiasi sottoscrizione e gruppo di risorse per cui l'utente che assegna il progetto abbia le autorizzazioni.

     > [!IMPORTANT]
     > Azure Blueprints non gestisce l'identità gestita assegnata dall'utente. Gli utenti sono responsabili dell'assegnazione di ruoli e autorizzazioni sufficienti, altrimenti l'assegnazione del progetto non riesce.

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="unassign-a-blueprint"></a>Annullare l'assegnazione di un progetto

È possibile rimuovere un progetto da una sottoscrizione. La rimozione viene spesso eseguita quando le risorse dell'elemento non sono più necessarie. Quando un progetto viene rimosso, gli elementi assegnati nell'ambito del progetto vengono mantenuti. Per rimuovere l'assegnazione di un progetto, usare il comando `az blueprint assignment delete`:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è creato, assegnato e rimosso un progetto con l'interfaccia della riga di comando di Azure. Per altre informazioni su Azure Blueprints, passare all'articolo relativo al ciclo di vita di un progetto.

> [!div class="nextstepaction"]
> [Informazioni sul ciclo di vita di un progetto](./concepts/lifecycle.md)