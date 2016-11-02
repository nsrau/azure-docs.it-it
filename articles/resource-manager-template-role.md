<properties
   pageTitle="Modello di Gestione risorse per le assegnazioni di ruolo | Microsoft Azure"
   description="Mostra lo schema di Gestione risorse per la distribuzione di un'assegnazione di ruolo tramite un modello."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="tomfitz"/>


# <a name="role-assignments-template-schema"></a>Assegnazioni di ruolo - Schema del modello

Assegna un utente, un gruppo o un'entità servizio a un ruolo in un ambito specifico.

## <a name="resource-format"></a>Formato di risorsa

Per creare un'assegnazione di ruolo, aggiungere lo schema seguente alla sezione delle risorse del modello.
    
    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Obbligatorio | Descrizione |
| ---- | -------- | ----------- |
| type | Sì    | Tipo di risorsa da creare.<br /><br /> Per il gruppo di risorse:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Per la risorsa:<br />**{spazio dei nomi-provider}/{tipo-risorsa}/providers/roleAssignments** |
| apiVersion |Sì | Versione dell'API da usare per creare la risorsa.<br /><br /> Usare **2015-07-01**. | 
| name | Sì | Un identificatore univoco globale per la nuova assegnazione di ruolo. |
| dependsOn | No | Una matrice di valori separati da virgola di nomi di risorsa o identificatori univoci di risorsa.<br /><br />La raccolta di risorse da cui dipende questa assegnazione di ruolo. Se l'assegnazione di un ruolo ha come ambito una risorsa e tale risorsa viene distribuita nello stesso modello, includere il nome della risorsa in questo elemento per assicurarsi che la risorsa venga distribuita per prima. |
| properties | Sì | L'oggetto proprietà che identifica definizione di ruolo, entità e ambito. |

### <a name="properties-object"></a>oggetto delle proprietà

| Nome | Obbligatorio | Descrizione |
| ---- | -------- | ----------- |
| roleDefinitionId | Sì |  L'identificatore di una definizione di ruolo esistente da usare nell'assegnazione di ruolo.<br /><br /> Utilizzare il seguente formato:<br /> **/subscriptions/{ID-sottoscrizione}/providers/Microsoft.Authorization/roleDefinitions/{ID-definizione-ruolo}** |
| principalId | Sì | L'identificatore univoco globale per un'entità esistente. Questo valore è associato all'id all'interno della directory e può fare riferimento a un utente, entità servizio o gruppo di sicurezza. |
| scope | No | L'ambito a cui si applica questa assegnazione di ruolo.<br /><br />Per i gruppi di risorse usare:<br />**/subscriptions/{id-sottoscrizione}/resourceGroups/{nome-gruppo-risorse}**  <br /><br />Per le risorse usare:<br />**/subscriptions/{id-sottoscrizione}/resourceGroups/{nome-gruppo-risorse}/providers/{spazio dei nomi-provider}/{tipo-risorsa}/{nome-risorsa}** |  |


## <a name="how-to-use-the-role-assignment-resource"></a>Come usare la risorsa di assegnazione di ruolo

Aggiungere un'assegnazione di ruolo al modello quando è necessario aggiungere un utente, un gruppo o un'entità servizio a un ruolo durante la distribuzione. Le assegnazioni di ruolo vengono ereditate dai livelli superiori dell'ambito, pertanto se un'entità è già stata aggiunta a un ruolo a livello di sottoscrizione, non è necessario riassegnarla al gruppo di risorse o alla risorsa.

Esistono molti valori identificatore che devono essere specificati quando si lavora con le assegnazioni di ruolo. È possibile recuperare i valori tramite PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="powershell"></a>PowerShell

Il nome dell'assegnazione di ruolo richiede un identificatore univoco globale. È possibile generare un nuovo identificatore per **nome** con:

    $name = [System.Guid]::NewGuid().toString()

È possibile recuperare l'identificatore per la definizione del ruolo con:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

È possibile recuperare l'identificatore per l'entità con uno dei comandi seguenti.

Per un gruppo denominato **Auditors**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Per un utente denominato **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Per un'entità servizio denominata **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile recuperare l'identificatore per la definizione del ruolo con:

    azure role show Reader --json | jq .[].Id -r

È possibile recuperare l'identificatore per l'entità con uno dei comandi seguenti.

Per un gruppo denominato **Auditors**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Per un utente denominato **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Per un'entità servizio denominata **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>esempi

Il modello seguente riceve un identificatore per un ruolo e un identificatore per un utente, gruppo o entità servizio. Assegna il ruolo a livello di gruppo di risorse.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



Il modello seguente crea un account di archiviazione e assegna il ruolo lettura all'account di archiviazione. Gli identificatori per i due gruppi e il ruolo lettura sono stati inclusi nel modello per semplificare la distribuzione. Questi valori possono essere recuperati in fase di distribuzione tramite script e passati come parametri.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Modelli di Guida introduttiva

I modelli seguenti illustrano come usare la risorsa di assegnazione di ruolo:

- [Assegnare un ruolo predefinito al gruppo di risorse](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Assegnare un ruolo predefinito a una macchina virtuale esistente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Assegnare un ruolo predefinito a più macchine virtuali esistenti](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](./active-directory/role-based-access-control-configure.md).



<!--HONumber=Oct16_HO2-->


