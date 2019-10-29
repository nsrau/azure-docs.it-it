---
title: Pubblicare un'applicazione gestita del catalogo di servizi di Azure
description: Questo articolo descrive come creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 62d8de4f36ab110f7bd871c9de0e5d843516970c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529005"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Creare e pubblicare una definizione di applicazione gestita

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile creare e pubblicare [applicazioni gestite](overview.md) di Azure studiate per i membri della propria organizzazione. Un reparto IT può, ad esempio, pubblicare applicazioni gestite conformi agli standard aziendali. Queste applicazioni gestite sono disponibili nel catalogo dei servizi, non in Azure Marketplace.

Per pubblicare un'applicazione gestita per il catalogo dei servizi, è necessario:

* Creare un modello che definisce le risorse da distribuire con l'applicazione gestita.
* Definire gli elementi dell'interfaccia utente per il portale quando si distribuisce l'applicazione gestita.
* Creare un pacchetto con estensione zip contenente i file modello necessari.
* Decidere quali utenti, gruppi o applicazioni devono accedere al gruppo di risorse nella sottoscrizione dell'utente.
* Creare la definizione di applicazione gestita che punta al pacchetto con estensione zip e richiede l'accesso per l'identità.

Per questo articolo, l'applicazione gestita contiene solo un account di archiviazione. Lo scopo è quello di illustrare i passaggi necessari per pubblicare un'applicazione gestita. Per esempi completi, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).

Gli esempi di PowerShell questo articolo richiedono la versione 6.2 o successiva di Azure PowerShell. Se necessario, [aggiornare la versione](/powershell/azure/install-Az-ps).

## <a name="create-the-resource-template"></a>Creare il modello di risorsa

Ogni definizione di applicazione gestita include un file denominato **mainTemplate.json**, in cui si definiscono le risorse di Azure da distribuire. Il modello non è diverso da un modello standard di Resource Manager.

Creare un file denominato **mainTemplate.json**. Il nome distingue tra maiuscole e minuscole.

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
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Salvare il file mainTemplate.json.

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>Definizione dell'esperienza di creazione con createUiDefinition.json

L'editore definisce l'esperienza di creazione usando il file **createUiDefinition.json**, che genera l'interfaccia per gli utenti che creano applicazioni gestite. È possibile definire il modo in cui gli utenti specificheranno l'input per ogni parametro usando [elementi di controllo] (create-uidefinition-elements.md), ad esempio elenchi a discesa, caselle di testo e caselle per password.

Creare un file denominato **createUiDefinition.json**. Per il nome viene fatta distinzione tra maiuscole e minuscole.

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

Aggiungere i due file a un file ZIP denominato app.zip. I due file devono essere a livello di radice nel file con estensione zip. Se li si inserisce in una cartella, durante la creazione della definizione di applicazione gestita viene visualizzato un errore che indica che i file necessari non sono presenti. 

Caricare il pacchetto in una posizione accessibile da dove può essere usato. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Creare un'applicazione o un gruppo di utenti Azure Active Directory

Il passaggio successivo consiste nel selezionare un gruppo di utenti o un'applicazione per gestire le risorse per conto del cliente. Questo gruppo di utenti o questa applicazione dispone di autorizzazioni per il gruppo di risorse gestito in base al ruolo assegnato. Il ruolo può essere un ruolo di controllo degli accessi in base al ruolo predefinito, ad esempio Proprietario o Collaboratore. È anche possibile concedere a un singolo utente l'autorizzazione per gestire le risorse, ma in genere si assegna questa autorizzazione a un gruppo utenti. Per creare un nuovo gruppo di utenti di Active Directory, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

È necessario munirsi dell'ID oggetto del gruppo di utenti da usare per la gestione delle risorse. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Ottenere l'ID di definizione del ruolo

Ora è necessario l'ID di definizione del ruolo Controllo degli accessi in base al ruolo predefinito a cui si vuole concedere l'accesso all'utente, al gruppo utenti o all'applicazione. In genere si usa il ruolo Proprietario, Collaboratore o Lettore. Il comando seguente illustra come ottenere l'ID di definizione per il ruolo Proprietario:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Se non è già disponibile un gruppo di risorse per archiviare la definizione di applicazione gestita, crearne uno ora:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Creare a questo punto la definizione di applicazione gestita.

```powershell
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

### <a name="make-sure-users-can-see-your-definition"></a>Assicurarsi che gli utenti possano visualizzare la definizione

Si ha accesso alla definizione dell'applicazione gestita, ma si vuole assicurarsi che gli altri utenti nell'organizzazione possano accedervi. Concedere loro almeno il ruolo di Lettore per la definizione. Gli utenti potrebbero aver ereditato questo livello di accesso dalla sottoscrizione o dal gruppo di risorse. Per controllare chi ha accesso alla definizione e aggiungere utenti o gruppi, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Per pubblicare l'applicazione gestita in Azure Marketplace, vedere [Applicazioni gestite di Azure nel Marketplace](publish-marketplace-app.md).
* Per distribuire un'istanza di applicazione gestita, vedere [Deploy service catalog app through Azure portal](deploy-service-catalog-quickstart.md) (Distribuire l'app catalogo di servizi tramite il portale di Azure).
