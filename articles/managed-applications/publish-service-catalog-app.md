---
title: Creare e pubblicare un'applicazione gestita del catalogo dei servizi di Azure | Microsoft Docs
description: Questo articolo descrive come creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 6b1d609b7b1b21e80cc7f68f05e16e3c1e8eebba
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Pubblicare un'applicazione gestita per uso interno

È possibile creare e pubblicare [applicazioni gestite](overview.md) di Azure studiate per i membri della propria organizzazione. Un reparto IT può, ad esempio, pubblicare applicazioni gestite che garantiscano la conformità agli standard aziendali. Queste applicazioni gestite sono disponibili nel catalogo dei servizi, non in Azure Marketplace.

Per pubblicare un'applicazione gestita per il catalogo dei servizi, è necessario:

* Creare un modello che definisce le risorse da distribuire con l'applicazione gestita.
* Definire gli elementi dell'interfaccia utente per il portale quando si distribuisce l'applicazione gestita.
* Creare un pacchetto con estensione zip contenente i file modello necessari.
* Decidere quali utenti, gruppi o applicazioni devono accedere al gruppo di risorse nella sottoscrizione dell'utente.
* Creare la definizione di applicazione gestita che punta al pacchetto con estensione zip e richiede l'accesso per l'identità.

Per questo articolo, l'applicazione gestita contiene solo un account di archiviazione. Lo scopo è quello di illustrare i passaggi necessari per pubblicare un'applicazione gestita. Per esempi completi, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).

## <a name="create-the-resource-template"></a>Creare il modello di risorsa

Ogni definizione di applicazione gestita contiene un file denominato **mainTemplate.json**, in cui si definiscono le risorse di Azure di cui effettuare il provisioning. Il modello non è diverso da un modello standard di Resource Manager.

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
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString('storage'))]"
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

## <a name="create-the-user-interface-definition"></a>Creare la definizione dell'interfaccia utente

Il portale di Azure usa il file **createUiDefinition.json** per generare l'interfaccia utente per gli utenti che creano l'applicazione gestita. È possibile definire la modalità con cui gli utenti inseriscono l'input per ogni parametro. È possibile usare opzioni come un elenco a discesa, una casella di testo, una casella per la password e altri strumenti di input. Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).

Creare un file denominato **createUiDefinition.json**. Il nome distingue tra maiuscole e minuscole.

Aggiungere il codice JSON seguente al file.

```json
{
    "handler": "Microsoft.Compute.MultiVm",
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

Salvare il file createUIDefinition.json.

## <a name="package-the-files"></a>Creare il pacchetto dei file

Aggiungere i due file a un file ZIP denominato app.zip. I due file devono essere a livello di radice nel file con estensione zip. Se li si inserisce in una cartella, durante la creazione della definizione di applicazione gestita viene visualizzato un errore che indica che i file necessari non sono presenti. 

Caricare il pacchetto in una posizione accessibile da dove può essere usato. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Creare un'applicazione o un gruppo di utenti Azure Active Directory

Il passaggio successivo consiste nel selezionare un gruppo di utenti o un'applicazione per gestire le risorse per conto del cliente. Questo gruppo di utenti o questa applicazione dispone di autorizzazioni per il gruppo di risorse gestito in base al ruolo assegnato. Il ruolo può essere un ruolo di controllo degli accessi in base al ruolo predefinito, ad esempio Proprietario o Collaboratore. È anche possibile concedere a un singolo utente l'autorizzazione per gestire le risorse, ma in genere si assegna questa autorizzazione a un gruppo utenti. Per creare un nuovo gruppo di utenti di Active Directory, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

È necessario munirsi dell'ID oggetto del gruppo di utenti da usare per la gestione delle risorse. 

![Ottenere l'ID del gruppo](./media/publish-service-catalog-app/get-group-id.png)

### <a name="get-the-role-definition-id"></a>Ottenere l'ID di definizione del ruolo

Ora è necessario l'ID di definizione del ruolo Controllo degli accessi in base al ruolo predefinito a cui si vuole concedere l'accesso all'utente, al gruppo utenti o all'applicazione. In genere si usa il ruolo Proprietario, Collaboratore o Lettore. Il comando seguente illustra come ottenere l'ID di definizione per il ruolo Proprietario:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Se non si dispone già di un gruppo di risorse per archiviare la definizione di applicazione gestita, crearne uno ora:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Creare a questo punto la definizione di applicazione gestita.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "<group-id>:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application-by-using-the-portal"></a>Creare l'applicazione gestita con il portale

A questo punto si usa il portale per distribuire l'applicazione gestita. Viene visualizzata l'interfaccia utente creata nel pacchetto.

1. Accedere al portale di Azure. Selezionare **+ Nuovo** e cercare il **catalogo di servizi**.

   ![Cercare il catalogo di servizi](./media/publish-service-catalog-app/select-new.png)

1. Selezionare **Applicazione gestita del catalogo di servizi**.

   ![Selezionare il catalogo di servizi](./media/publish-service-catalog-app/select-service-catalog.png)

1. Selezionare **Crea**.

   ![Selezionare Crea](./media/publish-service-catalog-app/select-create.png)

1. Trovare l'applicazione gestita che si vuole creare dall'elenco di soluzioni disponibili e selezionarla. Selezionare **Crea**.

   ![Trovare l'applicazione gestita](./media/publish-service-catalog-app/find-application.png)

1. Fornire le informazioni di base necessarie per l'applicazione gestita. Specificare la sottoscrizione e un nuovo gruppo di risorse che contenga l'applicazione gestita. Selezionare **West Central US** (Stati Uniti centro-occidentali) per il percorso. Al termine selezionare **OK**.

   ![Fornire i parametri delle applicazioni gestite](./media/publish-service-catalog-app/provide-basics.png)

1. Fornire i valori specifici per le risorse nell'applicazione gestita. Al termine selezionare **OK**.

   ![Specificare i parametri delle risorse](./media/publish-service-catalog-app/provide-resource-values.png)

1. Il modello convalida i valori specificati. Se la convalida ha esito positivo, selezionare **OK** per iniziare la distribuzione.

   ![Convalidare l'applicazione gestita](./media/publish-service-catalog-app/validate.png)

Al termine della distribuzione, l'applicazione gestita si trova in un gruppo di risorse denominato applicationGroup. L'account di archiviazione si trova in un gruppo di risorse denominato applicationGroup e un valore di stringa hash.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](overview.md).
* Per i progetti di esempio, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).
* Per informazioni sulla pubblicazione di applicazioni gestite in Azure Marketplace, vedere [Applicazioni gestite di Azure nel Marketplace](publish-marketplace-app.md).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).