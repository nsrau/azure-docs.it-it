---
title: Modello di Resource Manager per i blocchi di risorse | Microsoft Docs
description: Mostra lo schema di Gestione risorse per la distribuzione di blocchi di risorse tramite un modello.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>Blocchi di risorse - Schema del modello
Crea un blocco in una risorsa e nelle risorse figlio.

## <a name="schema-format"></a>Formato dello schema
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



## <a name="values"></a>Valori
Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Obbligatorio | Descrizione |
| --- | --- | --- |
| type |Sì |Tipo di risorsa da creare.<br /><br />Per le risorse:<br />**{spazio dei nomi}/{tipo}/providers/locks**<br /><br/>Per i gruppi di risorse:<br />**Microsoft.Authorization/locks** |
| apiVersion |Sì |Versione dell'API da usare per creare la risorsa.<br /><br />Usare:<br />**01-01-2015**<br /><br /> |
| name |Sì |Un valore che specifica sia la risorsa da bloccare che un nome per il blocco. Può essere di 64 caratteri al massimo e non può contenere <, > %, &, ? o qualsiasi altro carattere di controllo.<br /><br />Per le risorse:<br />**{risorsa}/Microsoft.Authorization/{nome blocco}**<br /><br />Per i gruppi di risorse:<br />**{nome blocco}** |
| dependsOn |No |Elenco di valori separati da virgole di nomi di risorsa o di identificatori univoci di risorse.<br /><br />Raccolta di risorse da cui dipende questo blocco. Se la risorsa che si sta bloccando viene distribuita nello stesso modello, includere il nome della risorsa in questo elemento per assicurarsi che la risorsa venga distribuita per prima. |
| properties |Sì |Oggetto che identifica il tipo di blocco e note relative al blocco.<br /><br />Vedere [oggetto delle proprietà](#properties-object). |

### <a name="properties-object"></a>oggetto delle proprietà
| Nome | Obbligatorio | Descrizione |
| --- | --- | --- |
| level |Sì |Tipo di blocco da applicare all'ambito.<br /><br />**CannotDelete**: gli utenti possono modificare una risorsa ma non eliminarla.<br />**ReadOnly**: gli utenti possono leggere una risorsa, ma non eliminarla o eseguire azioni su di essa. |
| di HDInsight |No |Descrizione del blocco. Può contenere fino a 512 caratteri. |

## <a name="how-to-use-the-lock-resource"></a>Come utilizzare la risorsa di blocco
Si aggiunge questa risorsa al modello per impedire le azioni specificate in una risorsa. Il blocco si applica a tutti gli utenti e gruppi.

Per creare o eliminare i blocchi di gestione, è necessario avere accesso alle azioni **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Fra i ruoli predefiniti, solo a **Proprietario** e **Amministratore Accesso utenti** sono concesse tali azioni. Per informazioni sul controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-configure.md).

Il blocco viene applicato alla risorsa specificata e a tutte le risorse figlio.

È possibile rimuovere un blocco con il comando PowerShell **Remove AzureRmResourceLock** o con l’ [operazione di eliminazione](https://msdn.microsoft.com/library/azure/mt204562.aspx) dell'API REST.

## <a name="examples"></a>esempi
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

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).
* Per altre informazioni sui blocchi, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).




<!--HONumber=Nov16_HO3-->


