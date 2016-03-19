<properties
   pageTitle="Modello di Gestione risorse per le assegnazioni di ruolo | Microsoft Azure"
   description="Mostra lo schema di Gestione risorse per la distribuzione di un'assegnazione di ruolo tramite un modello."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# Assegnazioni di ruolo - Schema del modello

Assegna un utente, un gruppo o un'entità servizio a un ruolo in un ambito specifico.

## Formato dello schema

Per creare un'assegnazione di ruolo, aggiungere lo schema seguente alla sezione delle risorse del modello.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
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

## Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sì | **Microsoft.Authorization/roleAssignments** | Il tipo di risorsa da creare. |
| apiVersion | enum | Sì | **01-07-2015** | La versione dell'API da utilizzare per la creazione della risorsa. |  
| name | string | Sì | Identificatore univoco globale | Un identificatore per la nuova assegnazione di ruolo. |
| dependsOn | array | No | Un elenco delimitato da virgole di nomi di risorse o di identificatori univoci di risorse. | La raccolta di risorse da cui dipende questa assegnazione di ruolo. Se l'assegnazione di un ruolo ha come ambito una risorsa e tale risorsa viene distribuita nello stesso modello, includere il nome della risorsa in questo elemento per assicurarsi che la risorsa venga distribuita per prima. | 
| properties | object | Sì | (come illustrato di seguito) | Un oggetto che identifica la definizione del ruolo, l'entità e l'ambito. |  

### oggetto delle proprietà

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | string | Sì | **/subscriptions/{id-sottoscrizione}/providers/Microsoft.Authorization/roleDefinitions/{id-definizione-ruolo}** | L'identificatore di una definizione di ruolo esistente da usare nell'assegnazione di ruolo. |
| principalId | string | Sì | Identificatore univoco globale | Identificatore di un'entità esistente. Esegue il mapping all'id all'interno della directory e può fare riferimento a un utente, all'entità servizio o a un gruppo di sicurezza. |
| scope | string | Sì | Per il gruppo di risorse:<br />**/subscriptions/{id-sottoscrizione}/resourceGroups/{nome-gruppo-risorse}**<br /><br />Per la risorsa:<br />**/subscriptions/{id-sottoscrizione}/resourceGroups/{nome-gruppo-risorse}/providers/{spazio dei nomi-provider}/{tipo-risorsa}/{nome-risorsa}** | L'ambito a cui si applica questa assegnazione di ruolo. |


## Come usare la risorsa di assegnazione di ruolo

Aggiungere un'assegnazione di ruolo al modello quando è necessario aggiungere un utente, un gruppo o un'entità servizio a un ruolo durante la distribuzione. Le assegnazioni di ruolo vengono ereditate dai livelli superiori dell'ambito, pertanto se un'entità è già stata aggiunta a un ruolo a livello di sottoscrizione, non è necessario riassegnarla al gruppo di risorse o alla risorsa.

È possibile generare un nuovo identificatore per **nome** con:

    PS C:\> $name = [System.Guid]::NewGuid().toString()

È possibile recuperare l'identificatore univoco globale per la definizione del ruolo con:

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

È possibile recuperare l'identificatore per l'entità con uno dei comandi seguenti.

Per un gruppo denominato **Auditors**:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Per un utente denominato **exampleperson**:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Per un'entità servizio denominata **exampleapp**:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## esempi

L'esempio seguente assegna un gruppo a un ruolo per il gruppo di risorse.

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

## Modelli di Guida introduttiva

I modelli seguenti illustrano come usare la risorsa di assegnazione di ruolo:

- [Assegnare un ruolo predefinito al gruppo di risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)
- [Assegnare un ruolo predefinito a una macchina virtuale esistente](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)
- [Assegnare un ruolo predefinito a più macchine virtuali esistenti](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)

## Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo di accesso in base al ruolo di Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0107_2016-->