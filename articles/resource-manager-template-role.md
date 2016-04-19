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
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Assegnazioni di ruolo - Schema del modello

Assegna un utente, un gruppo o un'entità servizio a un ruolo in un ambito specifico.

## Formato dello schema

Per creare un'assegnazione di ruolo, aggiungere lo schema seguente alla sezione delle risorse del modello.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2014-10-01-preview",
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

| Nome | Valore |
| ---- | ---- |
| type | Enum<br />Obbligatorio<br />**Microsoft.Authorization/roleAssignments**<br /><br />Tipo della risorsa da creare. |
| apiVersion | Enum<br />Obbligatorio<br />**2014-10-01-preview**<br /><br />Versione dell'API da usare per creare la risorsa. |  
| name | String<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore della nuova assegnazione di ruolo. |
| dependsOn | Array<br />Facoltativo<br />Elenco di valori separati da virgole di nomi di risorsa o di identificatori univoci di risorse.<br /><br />Raccolta di risorse da cui dipende questa assegnazione di ruolo. Se l'assegnazione di un ruolo ha come ambito una risorsa e tale risorsa viene distribuita nello stesso modello, includere il nome della risorsa in questo elemento per assicurarsi che la risorsa venga distribuita per prima. | 
| properties | Oggetto<br />Obbligatorio<br />[oggetto properties](#properties)<br /><br />Oggetto che identifica la definizione del ruolo, l'entità e l'ambito. |  

<a id="properties" />
### oggetto delle proprietà

| Nome | Valore |
| ------- | ---- |
| roleDefinitionId | String<br />Obbligatorio<br /> **/subscriptions/{ID sottoscrizione}/providers/Microsoft.Authorization/roleDefinitions/{ID definizione ruolo}**<br /><br />Identificatore di una definizione di ruolo esistente da usare nell'assegnazione di ruolo. |
| principalId | String<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore di un'entità esistente. Esegue il mapping all'id all'interno della directory e può fare riferimento a un utente, all'entità servizio o a un gruppo di sicurezza. |
| scope | String<br />Obbligatorio<br />**/subscriptions/{ID sottoscrizione}/resourceGroups/{nome gruppo risorse}** (per i gruppi di risorse) o<br />**/subscriptions/{ID sottoscrizione}/resourceGroups/{nome gruppo risorse}/providers/{spazio dei nomi provider}/{tipo risorsa}/{nome risorsa}** (per le risorse)<br /><br />Ambito a cui si applica l'assegnazione di ruolo. |


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

- [Assegnare un ruolo predefinito al gruppo di risorse](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Assegnare un ruolo predefinito a una macchina virtuale esistente](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Assegnare un ruolo predefinito a più macchine virtuali esistenti](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo di accesso in base al ruolo di Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0406_2016-->