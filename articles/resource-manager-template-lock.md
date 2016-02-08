<properties
   pageTitle="Modello di gestione risorse per i blocchi di risorse | Microsoft Azure"
   description="Mostra lo schema di Gestione risorse per la distribuzione di blocchi di risorse tramite un modello."
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
   ms.date="01/21/2016"
   ms.author="tomfitz"/>

# Blocchi di risorse - Schema del modello

Crea un nuovo blocco su una risorsa e le risorse figlio.

## Formato dello schema

Per creare un blocco, aggiungere lo schema seguente alla sezione delle risorse del modello.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sì | Per le risorse: <br />**{namespace}/{type}/providers/locks**<br /><br />per gruppi di risorse:<br />**Microsoft.Authorization/locks** | Il tipo di risorsa da creare. |
| apiVersion | enum | Sì | **01/01/2015** | La versione dell'API da utilizzare per la creazione della risorsa. |  
| name | string | Sì | Per le risorse:<br />**{resouce}/Microsoft.Authorization/{lockname}**<br /><br />Per gruppi di risorse:<br />**{lockname} * ***<br /><br />fino a 64 caratteri<br />non può contenere <>, % &,?, o tutti i caratteri di controllo. | Un valore che specifica la risorsa da bloccare e un nome per il blocco. | | dependsOn | matrice | Non | Un elenco delimitato da virgole di una risorsa di nomi o gli identificatori univoci delle risorse. | La raccolta di risorse da cui dipende tale blocco. Se la risorsa che si sta bloccando viene distribuita nello stesso modello, includere il nome di risorsa in questo elemento per assicurarsi che la risorsa venga distribuita prima. | | proprietà | oggetto | Sì | (come illustrato di seguito) | Un oggetto che identifica il tipo di blocco, e note relative al blocco. | 

### oggetto delle proprietà

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ------- | ---- | ---------------- | -------- | ----------- |
| level | enum | Sì | **CannotDelete** | Il tipo di blocco da applicare all'ambito. CanNotDelete consente la modifica, ma impedisce l'eliminazione. |
| di HDInsight | string | No | 512 caratteri | Descrizione del blocco. |


## Come utilizzare la risorsa di blocco

Si aggiunge questa risorsa al modello per impedire le azioni specificate in una risorsa. Il blocco si applica a tutti gli utenti e gruppi. In genere, si applica un blocco solo per una durata limitata, ad esempio, quando un processo è in esecuzione e ci si desidera assicurare che un utente dell'organizzazione non modifichi o elimini inavvertitamente una risorsa.

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Dei ruoli predefiniti, solo **Owner** e **User Access Administrator** garantiscono tali azioni. Per informazioni sul controllo di accesso basato sul ruolo, vedere [Controllo di accesso in base al ruolo di Azure](./active-directory/role-based-access-control-configure.md).

Il blocco viene applicato alla risorsa specificata e a tutte le risorse figlio.

È possibile rimuovere un blocco con il comando PowerShell **Remove AzureRmResourceLock** o con l’[operazione di eliminazione](https://msdn.microsoft.com/library/azure/mt204562.aspx) dell'API REST.

## esempi

L'esempio seguente applica un blocco di sola lettura a un'app Web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
      			"type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

L'esempio successivo applica un blocco di sola lettura al gruppo di risorse.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## Passaggi successivi

- Per informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
- Per altre informazioni sui blocchi, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0128_2016-->