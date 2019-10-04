---
title: Creare un'applicazione gestita di Azure con azioni e risorse personalizzate
description: Questa esercitazione descrive come creare un'applicazione gestita di Azure con un provider personalizzato di Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: 3dd0887114156956b55f554d0265e3ca2b9b10ab
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336106"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Esercitazione: Creare un'applicazione gestita con azioni e risorse personalizzate

Questa esercitazione illustra come creare un'applicazione gestita con azioni e risorse personalizzate. L'applicazione gestita conterrà un'azione personalizzata nella pagina `Overview`, un tipo di risorsa personalizzata visualizzato come voce di menu separata in `Table of Content` e un'azione contestuale personalizzata nella pagina di risorse personalizzate.

Questa esercitazione include i passaggi seguenti:

> [!div class="checklist"]
> * Creare un file di definizione dell'interfaccia utente per la creazione di un'istanza di applicazione gestita
> * Creare un modello di distribuzione con un provider personalizzato di Azure, un account di archiviazione di Azure e una funzione di Azure
> * Creare l'artefatto di definizione delle visualizzazioni con azioni e risorse personalizzate
> * Distribuire una definizione di applicazione gestita
> * Distribuire un'istanza di applicazione gestita
> * Eseguire azioni personalizzate e creare risorse personalizzate

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario sapere come:

* [Creare e pubblicare una definizione di applicazione gestita](publish-service-catalog-app.md).
* [Distribuire un'app del catalogo di servizi tramite il portale di Azure](deploy-service-catalog-quickstart.md).
* [Creare l'interfaccia utente del portale di Azure per l'applicazione gestita](create-uidefinition-overview.md).
* Usare le funzionalità dell'[artefatto di definizione delle visualizzazioni](concepts-view-definition.md).
* Usare le funzionalità del [provider personalizzato di Azure](custom-providers-overview.md).

## <a name="user-interface-definition"></a>Definizione di interfaccia utente

In questa esercitazione verrà creata un'applicazione gestita e il relativo gruppo di risorse gestite conterrà un'istanza del provider personalizzato, un account di archiviazione e una funzione. La funzione di Azure usata in questo esempio implementa un'API che gestisce le operazioni del provider personalizzato per azioni e risorse. L'account di archiviazione di Azure viene usato come risorsa di archiviazione di base per le risorse del provider personalizzato.

La definizione dell'interfaccia utente per la creazione di un'istanza di applicazione gestita include gli elementi di input `funcname` e `storagename`. Il nome dell'account di archiviazione e il nome della funzione devono essere globalmente univoci. Per impostazione predefinita, i file della funzione verranno distribuiti dal [pacchetto di funzioni di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), che è tuttavia possibile cambiare aggiungendo un elemento di input per il collegamento a un pacchetto in *createUIDefinition.json*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

Con l'output in *createUiDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

L'esempio *createUIDefinition.json* completo è disponibile in [Informazioni di riferimento: Artefatti degli elementi dell'interfaccia utente](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Modello con provider personalizzato

Per creare un'istanza di applicazione gestita con un provider personalizzato, è necessario definire una risorsa provider personalizzato con il nome **public** e il tipo **Microsoft.CustomProviders/resourceProviders** nel file **mainTemplate.json**. In tale risorsa si definiscono i tipi di risorse e le azioni per il servizio. Per distribuire le istanze della funzione di Azure e dell'account di archiviazione di Azure, definire rispettivamente le risorse di tipo `Microsoft.Web/sites` e `Microsoft.Storage/storageAccounts`.

In questa esercitazione verranno creati un tipo di risorsa `users`, un'azione personalizzata `ping`e un'azione personalizzata `users/contextAction` da eseguire nel contesto di una risorsa personalizzata `users`. Per ogni tipo di risorsa e azione fornire un endpoint che punta alla funzione con il nome specificato in [createUIDefinition.json](#user-interface-definition). Specificare **routingType** come `Proxy,Cache` per i tipi di risorsa e `Proxy` per le azioni:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

L'esempio *mainTemplate.json* completo è disponibile in [Informazioni di riferimento: Distribuzione dell'artefatto del modello](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Artefatto di definizione delle visualizzazioni

Per definire l'interfaccia utente che include azioni personalizzate e risorse personalizzate nell'applicazione gestita, è necessario creare l'artefatto **viewDefinition.json**. Per altre informazioni sull'artefatto di definizione delle visualizzazioni, vedere [Artefatto di definizione delle visualizzazioni nelle applicazioni gestite di Azure](concepts-view-definition.md).

In questa esercitazione definire:
* Una pagina *Overview* con un pulsante della barra degli strumenti che rappresenta un'azione personalizzata `TestAction` con un input di testo di base.
* Una pagina *Users* che rappresenta un tipo di risorsa personalizzato `users`.
* Un'azione di risorsa personalizzata `users/contextAction` nella pagina *Users* che verrà eseguita nel contesto di una risorsa personalizzata di tipo `users`.

L'esempio seguente illustra la configurazione delle visualizzazioni per una pagina "Overview":

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

L'esempio seguente include la configurazione della pagina di risorse "Users" con l'azione di risorsa personalizzata:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

L'esempio *viewDefinition.json* completo è disponibile in [Informazioni di riferimento: Artefatto di definizione delle visualizzazioni](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definizione dell'applicazione gestita

Inserire i seguenti artefatti dell'applicazione gestita in un archivio ZIP e caricarlo nella risorsa di archiviazione:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Tutti i file devono trovarsi al livello radice. Il pacchetto con gli artefatti può essere archiviato in qualsiasi risorsa di archiviazione, ad esempio BLOB di GitHub o BLOB dell'account di archiviazione di Azure. Ecco uno script per caricare il pacchetto dell'applicazione nell'account di archiviazione: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Eseguire il seguente script dell'interfaccia della riga di comando di Azure oppure seguire i passaggi descritti nel portale di Azure per distribuire una definizione di applicazione gestita del catalogo di servizi:

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco di risorse digitare e selezionare **Centro applicazioni gestite**.
2. Nel **Centro applicazioni gestite** scegliere **Definizione di applicazione del catalogo di servizi** e fare clic su **Aggiungi**. 
    
    ![Aggiungere il catalogo di servizi](./media/managed-application-with-custom-providers/service-catalog-managed-application.png)

3. Specificare i valori per la creazione di una definizione del catalogo di servizi:

    * Specificare un valore univoco per **Nome** per la definizione del catalogo di servizi e compilare i campi **Nome visualizzato** e *Descrizione* (facoltativo).
    * Selezionare i valori per **Sottoscrizione**, **Gruppo di risorse** e **Località** in cui verrà creata la definizione di applicazione. È possibile usare lo stesso gruppo di risorse specificato per il pacchetto ZIP oppure crearne uno nuovo.
    * Per **URI del file del pacchetto** specificare il percorso del file ZIP creato nel passaggio precedente.

    ![Fornire i valori](./media/managed-application-with-custom-providers/add-service-catalog-managed-application.png)

4. Quando si arriva alla sezione Authentication and Lock Level (Autenticazione e livello di blocco) selezionare **Add Authorization** (Aggiungi autorizzazione).

    ![Aggiungere l'autorizzazione](./media/managed-application-with-custom-providers/add-authorization.png)

5. Selezionare un gruppo di Azure Active Directory per gestire le risorse e quindi **OK**.

   ![Aggiungere il gruppo di autorizzazioni](./media/managed-application-with-custom-providers/add-auth-group.png)

6. Dopo avere fornito tutti i valori, selezionare **Crea**.

   ![Creare la definizione di applicazione gestita](./media/managed-application-with-custom-providers/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Istanza di applicazione gestita

Quando viene distribuita la definizione di applicazione gestita, eseguire lo script seguente o seguire i passaggi descritti nel portale di Azure per distribuire l'istanza di applicazione gestita con il provider personalizzato:

# <a name="azure-clitabazurecli-interactive"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco di risorse digitare e selezionare **Centro applicazioni gestite**.
2. Nel **Centro applicazioni gestite** scegliere **Applicazioni del catalogo di servizi** e fare clic su **Aggiungi**. 

    ![Aggiungere l'applicazione gestita](./media/managed-application-with-custom-providers/add-managed-application.png)

3. Nella casella di ricerca della pagina **Applicazioni del catalogo di servizi** digitare il nome visualizzato della definizione del catalogo di servizi. Selezionare la definizione creata nel passaggio precedente e fare clic su **Crea**.

    ![Selezionare il catalogo di servizi](./media/managed-application-with-custom-providers/select-service-catalog-definition.png)

4. Specificare i valori per la creazione di un'istanza di applicazione gestita dalla definizione del catalogo di servizi:

    * Selezionare i valori per **Sottoscrizione**, **Gruppo di risorse** e **Località** in cui verrà creata l'istanza.
    * Specificare un nome di funzione di Azure univoco e il nome dell'account di archiviazione di Azure.

    ![Impostazioni dell'applicazione](./media/managed-application-with-custom-providers/application-settings.png)

5. Quando la convalida viene superata, fare clic su **OK** per distribuire un'istanza di un'applicazione gestita. 
    
    ![Distribuire l'applicazione gestita](./media/managed-application-with-custom-providers/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Azioni e risorse personalizzate

Dopo la distribuzione dell'istanza di applicazione del catalogo di servizi, sono disponibili due nuovi gruppi di risorse. Il primo gruppo di risorse, `applicationGroup`, contiene un'istanza dell'applicazione gestita e il secondo, `managedResourceGroup`, contiene le risorse per l'applicazione gestita, incluso il **provider personalizzato**.

![Gruppi di risorse dell'applicazione](./media/managed-application-with-custom-providers/application-resource-groups.png)

È possibile passare all'istanza di applicazione gestita ed eseguire un'**azione personalizzata** nella pagina "Overview", creare la risorsa personalizzata **users** nella pagina "Users" ed eseguire un'**azione contestuale personalizzata** nella risorsa personalizzata.

* Passare alla pagina "Overview" e fare clic sul pulsante "Ping Action":

![Eseguire l'azione personalizzata](./media/managed-application-with-custom-providers/perform-custom-action.png)

* Passare alla pagina "Users" e fare clic sul pulsante "Add". Specificare gli input per la creazione di una risorsa e inviare il modulo:

![Creare una risorsa personalizzata](./media/managed-application-with-custom-providers/create-custom-resource.png)

* Passare alla pagina "Users", selezionare una risorsa "users" e fare clic su "Custom Context Action":

![Creare una risorsa personalizzata](./media/managed-application-with-custom-providers/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Assistenza

In caso di domande sulle applicazioni gestite di Azure, provare a inviarle a [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-managedapps). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di pubblicarle. Aggiungere il tag `azure-managedapps` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

Per pubblicare l'applicazione gestita in Azure Marketplace, vedere [Applicazioni gestite di Azure nel Marketplace](publish-marketplace-app.md).
