---
title: Pubblicare un'app gestita del catalogo di servizi
description: Questo articolo descrive come creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: f6a3a16fe7fd6b0036b36520262e85a9066a4e63
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497855"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Avvio rapido: Creare e pubblicare una definizione di applicazione gestita

Questo argomento di avvio rapido fornisce un'introduzione all'uso delle [applicazioni gestite di Azure](overview.md). È possibile creare e pubblicare un'applicazione gestita studiata per i membri della propria organizzazione.

Per pubblicare un'applicazione gestita per il catalogo di servizi, è necessario:

* Creare un modello che definisce le risorse da distribuire con l'applicazione gestita.
* Definire gli elementi dell'interfaccia utente per il portale quando si distribuisce l'applicazione gestita.
* Creare un pacchetto con estensione _zip_ contenente i file modello necessari.
* Decidere quali utenti, gruppi o applicazioni devono accedere al gruppo di risorse nella sottoscrizione dell'utente.
* Creare la definizione di applicazione gestita che punta al pacchetto con estensione _zip_ e richiede l'accesso per l'identità.

## <a name="create-the-arm-template"></a>Creare il modello di Azure Resource Manager

Ogni definizione di applicazione gestita include un file denominato _mainTemplate.json_, in cui si definiscono le risorse di Azure da distribuire. Il modello non è diverso da un modello standard di Resource Manager.

Creare un file denominato _mainTemplate.json_. Per il nome è rilevante la distinzione tra maiuscole e minuscole.

Aggiungere il codice JSON seguente al file. Definisce i parametri per la creazione di un account di archiviazione e specifica le proprietà per l'account di archiviazione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
        }
    }
}
```

Salvare il file _mainTemplate.json_.

## <a name="define-your-create-experience"></a>Definire l'esperienza di creazione

Un editore definisce l'esperienza del portale per la creazione dell'applicazione gestita. Il file _createUiDefinition.json_ genera l'interfaccia del portale. È possibile definire il modo in cui gli utenti specificheranno l'input per ogni parametro usando [elementi di controllo](create-uidefinition-elements.md), ad esempio elenchi a discesa, caselle di testo e caselle per password.

Creare un file denominato _createUiDefinition.json_. Per il nome viene fatta distinzione tra maiuscole e minuscole.

Aggiungere il codice JSON di base seguente e salvare il file.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Per altre informazioni, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Creare il pacchetto dei file

Aggiungere i due file a un file con estensione _zip_ denominato _app.zip_. I due file devono trovarsi al livello radice del file con estensione _zip_. Se li si inserisce in una cartella, durante la creazione della definizione di applicazione gestita viene visualizzato un errore che indica che i file necessari non sono presenti.

Caricare il pacchetto in una posizione accessibile da dove può essere usato. È necessario specificare un nome univoco per l'account di archiviazione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Creare un'applicazione o un gruppo di utenti Azure Active Directory

Il passaggio successivo consiste nel selezionare un gruppo di utenti, un utente o un'applicazione per gestire le risorse per il cliente. Questa identità ha autorizzazioni per il gruppo di risorse gestito in base al ruolo assegnato. Il ruolo può essere qualsiasi ruolo predefinito di Azure, ad esempio Proprietario o Collaboratore. Per creare un nuovo gruppo di utenti di Active Directory, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

È necessario munirsi dell'ID oggetto del gruppo di utenti da usare per la gestione delle risorse. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Ottenere l'ID di definizione del ruolo

Ora è necessario l'ID di definizione del ruolo Controllo degli accessi in base al ruolo predefinito a cui si vuole concedere l'accesso all'utente, al gruppo utenti o all'applicazione. In genere si usa il ruolo Proprietario, Collaboratore o Lettore. Il comando seguente illustra come ottenere l'ID di definizione per il ruolo Proprietario:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Se non è già disponibile un gruppo di risorse per archiviare la definizione di applicazione gestita, crearne uno ora:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Creare a questo punto la definizione di applicazione gestita.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Al completamento del comando, è disponibile una definizione dell'applicazione gestita nel gruppo di risorse.

Ecco alcuni parametri usati nell'esempio precedente:

* **Gruppo di risorse**: nome del gruppo di risorse in cui viene creata la definizione di applicazione gestita.
* **Livello di blocco**: tipo di blocco inserito nel gruppo di risorse gestito. Impedisce al cliente di eseguire operazioni indesiderate su questo gruppo di risorse. ReadOnly è attualmente il solo livello di blocco supportato. Quando ReadOnly è specificato, il cliente può solo leggere le risorse presenti nel gruppo di risorse gestito. Le identità degli autori a cui è concesso l'accesso al gruppo di risorse gestito sono esenti dal blocco.
* **authorizations**: indica l'ID dell'entità di sicurezza e l'ID di definizione del ruolo usati per concedere l'autorizzazione al gruppo di risorse gestito. Viene specificato nel formato `<principalId>:<roleDefinitionId>`. Se sono necessari più valori, specificarli nel formato `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. I valori sono separati da una virgola.
* **URI del file del pacchetto**: la posizione di un pacchetto con estensione _zip_ che include i file necessari.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Usare la propria risorsa di archiviazione per la definizione di applicazione gestita

È possibile scegliere di archiviare la definizione di applicazione gestita all'interno di un account di archiviazione fornito durante la creazione, in modo che la posizione e l'accesso possano essere completamente gestiti dall'utente per le proprie esigenze normative.

> [!NOTE]
> La funzionalità Bring Your Own Storage è supportata solo con le distribuzioni di modelli di Resource Manager o API REST della definizione di applicazione gestita.

### <a name="select-your-storage-account"></a>Selezionare l'account di archiviazione

È necessario [creare un account di archiviazione](../../storage/common/storage-account-create.md) per contenere la definizione di applicazione gestita per l'uso con il catalogo di servizi.

Copiare l'ID risorsa dell'account di archiviazione. Verrà usato più avanti durante la distribuzione della definizione.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Impostare l'assegnazione di ruolo per "Provider di risorse di Appliance" nell'account di archiviazione

Prima che la definizione di applicazione gestita possa essere distribuita nell'account di archiviazione, è necessario concedere le autorizzazioni di collaboratore al ruolo **Provider di risorse di Appliance** in modo da poter scrivere i file di definizione nel contenitore dell'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account di archiviazione. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
1. Nella finestra **Aggiungi assegnazione di ruolo** selezionare il ruolo **Collaboratore**. 
1. Nel campo **Assegna accesso a** selezionare **Utente, gruppo o entità servizio di Azure AD**.
1. In **Seleziona** cercare il ruolo **Provider di risorse di Appliance** e selezionarlo.
1. Salvare l'assegnazione di ruolo.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Distribuire la definizione di applicazione gestita con un modello di Resource Manager

Usare il modello di Resource Manager seguente per distribuire l'applicazione gestita compressa come nuova definizione di applicazione gestita nel catalogo di servizi i cui file di definizione vengono archiviati e mantenuti nel proprio account di archiviazione:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
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
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

È stata aggiunta una nuova proprietà denominata `storageAccountId` alle proprietà di `applicationDefinitions` e la possibilità di specificare l'ID account di archiviazione in cui archiviare la definizione come valore:

È possibile verificare che i file di definizione dell'applicazione vengano salvati nell'account di archiviazione specificato in un contenitore denominato `applicationDefinitions`.

> [!NOTE]
> Per una maggiore sicurezza, è possibile creare una definizione di applicazione gestita e archiviarla in un [BLOB dell'account di archiviazione di Azure in cui è abilitata la crittografia](../../storage/common/storage-service-encryption.md). Il contenuto della definizione viene crittografato tramite le opzioni di crittografia dell'account di archiviazione. Solo gli utenti con autorizzazioni per il file possono visualizzare la definizione nel catalogo di servizi.

## <a name="make-sure-users-can-see-your-definition"></a>Assicurarsi che gli utenti possano visualizzare la definizione

Si ha accesso alla definizione dell'applicazione gestita, ma si vuole assicurarsi che gli altri utenti nell'organizzazione possano accedervi. Concedere loro almeno il ruolo di Lettore per la definizione. Gli utenti potrebbero aver ereditato questo livello di accesso dalla sottoscrizione o dal gruppo di risorse. Per controllare chi ha accesso alla definizione e aggiungere utenti o gruppi, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

La definizione di applicazione gestita è stata pubblicata. Ora si vedrà come distribuire un'istanza di tale definizione.

> [!div class="nextstepaction"]
> [Avvio rapido: Distribuire un'app del catalogo di servizi](deploy-service-catalog-quickstart.md)
