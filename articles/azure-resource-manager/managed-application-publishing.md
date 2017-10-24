---
title: Creare e pubblicare un'applicazione gestita del catalogo dei servizi di Azure | Microsoft Docs
description: Questo articolo descrive come creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/09/2017
ms.author: gauravbh
ms.openlocfilehash: 3ff2108d08bacc4bc5f79a768b9c131aa7e6fceb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Pubblicare un'applicazione gestita per uso interno

È possibile creare e pubblicare [applicazioni gestite](managed-application-overview.md) di Azure studiate per i membri della propria organizzazione. Un reparto IT può, ad esempio, pubblicare applicazioni gestite che garantiscano la conformità agli standard aziendali. Queste applicazioni gestite sono disponibili nel catalogo dei servizi, non in Azure Marketplace.

Per pubblicare un'applicazione gestita per il catalogo dei servizi, è necessario:

* Creare un pacchetto con estensione zip contenente i due file modello necessari.
* Decidere quali utenti, gruppi o applicazioni devono accedere al gruppo di risorse nella sottoscrizione dell'utente.
* Creare la definizione di applicazione gestita che punta al pacchetto con estensione zip e richiede l'accesso per l'identità.

## <a name="create-a-managed-application-package"></a>Creare un pacchetto dell'applicazione gestita

Il primo passaggio consiste nel creare i due file modello necessari. Creare un pacchetto di tutti e due i file in un file con estensione zip e caricarlo in una posizione accessibile, ad esempio un account di archiviazione. Passare un collegamento al file con estensione zip quando si crea la definizione di applicazione gestita.

* **mainTemplate.json**: questo file definisce le risorse di Azure di cui viene effettuato il provisioning nell'ambito dell'applicazione gestita. Il modello non è diverso da un modello standard di Resource Manager. Per creare un account di archiviazione tramite un'applicazione gestita, ad esempio, il file mainTemplate.json contiene:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **createUiDefinition.json**: il portale di Azure usa questo file per generare l'interfaccia utente per gli utenti che creano l'applicazione gestita. È possibile definire la modalità con cui gli utenti inseriscono l'input per ogni parametro. È possibile usare opzioni come un elenco a discesa, una casella di testo, una casella per la password e altri strumenti di input. Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).

  L'esempio seguente illustra un file createUiDefinition.json che consente agli utenti di specificare il prefisso del nome dell'account di archiviazione tramite una casella di testo.

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

Dopo che tutti i file necessari sono pronti, inserirli in un pacchetto con estensione zip. I due file devono essere a livello di radice nel file con estensione zip. Se li si inserisce in una cartella, durante la creazione della definizione di applicazione gestita viene visualizzato un errore che indica che i file necessari non sono presenti. Caricare il pacchetto in una posizione accessibile da dove può essere usato. Nel resto di questo articolo si presuppone che il file con estensione zip sia presente in un contenitore di BLOB di archiviazione accessibile pubblicamente.

Attualmente, per usare un'applicazione gestita è possibile usare l'interfaccia della riga di comando di Azure o il portale di Azure. Entrambi gli approcci sono illustrati in questo argomento.

## <a name="create-managed-application-with-azure-cli"></a>Creare l'applicazione gestita con l'interfaccia della riga di comando di Azure

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Creare un'applicazione o un gruppo di utenti Azure Active Directory

Il passaggio successivo consiste nel selezionare un gruppo di utenti o un'applicazione per gestire le risorse per conto del cliente. Questo gruppo di utenti o questa applicazione dispone di autorizzazioni per il gruppo di risorse gestito in base al ruolo assegnato. Il ruolo può essere un ruolo di controllo degli accessi in base al ruolo predefinito, ad esempio Proprietario o Collaboratore. È anche possibile concedere a un singolo utente l'autorizzazione per gestire le risorse, ma in genere si assegna questa autorizzazione a un gruppo utenti. Per creare un nuovo gruppo di utenti di Active Directory, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

È necessario munirsi dell'ID oggetto del gruppo di utenti da usare per la gestione delle risorse. L'esempio seguente illustra come ottenere l'ID oggetto dal nome visualizzato del gruppo:

```azurecli-interactive
az ad group show --group exampleGroupName
```

Il comando di esempio restituisce il seguente output:

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

Per recuperare solo l'ID oggetto, usare:

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

### <a name="get-the-role-definition-id"></a>Ottenere l'ID di definizione del ruolo

Ora è necessario l'ID di definizione del ruolo Controllo degli accessi in base al ruolo predefinito a cui si vuole concedere l'accesso all'utente, al gruppo utenti o all'applicazione. In genere si usa il ruolo Proprietario, Collaboratore o Lettore. Il comando seguente illustra come ottenere l'ID di definizione per il ruolo Proprietario:

```azurecli-interactive
az role definition list --name owner
```

Questo comando restituisce il seguente output:

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

È necessario il valore della proprietà "name" dell'esempio precedente. È possibile recuperare solo tale proprietà con:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

### <a name="create-the-managed-application-definition"></a>Creare la definizione di applicazione gestita

Se non si dispone già di un gruppo di risorse per archiviare la definizione di applicazione gestita, crearne uno ora:

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

Creare a questo punto la definizione di applicazione gestita.

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

I parametri usati nell'esempio precedente sono i seguenti:

* **resource-group**: nome del gruppo di risorse in cui viene creata la definizione di applicazione gestita.
* **lock-level**: tipo di blocco inserito nel gruppo di risorse gestito. Impedisce al cliente di eseguire operazioni indesiderate su questo gruppo di risorse. ReadOnly è attualmente il solo livello di blocco supportato. Quando ReadOnly è specificato, il cliente può solo leggere le risorse presenti nel gruppo di risorse gestito.
* **authorizations**: indica l'ID dell'entità di sicurezza e l'ID di definizione del ruolo usati per concedere l'autorizzazione al gruppo di risorse gestito. Viene specificato nel formato `<principalId>:<roleDefinitionId>`. Per questa proprietà si possono specificare anche altri valori. Se sono necessari più valori, deve essere specificata nel formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. I valori multipli sono separati da uno spazio.
* **package-file-uri**: posizione del pacchetto dell'applicazione gestita contenente i file modello, che può essere un BLOB del servizio Archiviazione di Azure.

## <a name="create-managed-application-with-portal"></a>Creare l'applicazione gestita con il portale

1. Se necessario, creare un gruppo di utenti di Azure Active Directory per gestire le risorse. Per altre informazioni, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).
1. Nell'angolo in alto a sinistra selezionare **+ Nuovo**.

   ![Nuovo servizio](./media/managed-application-publishing/new.png)

1. Cercare il **catalogo di servizi**.

1. Nei risultati scorrere fino alla **definizione di applicazione gestita del catalogo di servizi**. Selezionarlo.

   ![Ricercare le definizioni di applicazioni gestite](./media/managed-application-publishing/select-managed-apps-definition.png)

1. Selezionare **Crea** per avviare il processo di creazione della definizione di applicazione gestita.

   ![Creare la definizione di applicazione gestita](./media/managed-application-publishing/create-definition.png)

1. Fornire valori per il nome, il nome visualizzato, la descrizione, la località, le sottoscrizioni e il gruppo di risorse. Per l'URI del file di pacchetto specificare il percorso del file con estensione zip creato.

   ![Fornire i valori](./media/managed-application-publishing/fill-application-values.png)

1. Quando si arriva alla sezione Authentication and Lock Level (Autenticazione e livello di blocco) selezionare **Add Authorization** (Aggiungi autorizzazione).

   ![Aggiungere l'autorizzazione](./media/managed-application-publishing/add-authorization.png)

1. Selezionare un gruppo di Azure Active Directory per gestire le risorse e quindi **OK**.

   ![Aggiungere il gruppo di autorizzazioni](./media/managed-application-publishing/add-auth-group.png)

1. Dopo avere fornito tutti i valori, selezionare **Crea**.

   ![Creare l'applicazione gestita](./media/managed-application-publishing/create-app.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](managed-application-overview.md).
* Per gli esempi dei file, vedere [Managed Application samples](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) (Esempi di applicazione gestita).
* Per informazioni sull'uso delle applicazioni gestite del catalogo di servizi, vedere [Utilizzare un'applicazione gestita di Azure](managed-application-consumption.md).
* Per informazioni sulla pubblicazione di applicazioni gestite in Azure Marketplace, vedere [Applicazioni gestite di Azure nel Marketplace](managed-application-author-marketplace.md).
* Per informazioni sull'uso di un'applicazione gestita dal Marketplace, vedere [Consume Azure managed applications in the Marketplace](managed-application-consume-marketplace.md) (Uso delle applicazioni gestite di Azure nel Marketplace).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
