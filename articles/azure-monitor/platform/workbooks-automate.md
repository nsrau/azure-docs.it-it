---
title: A livello gestire le cartelle di lavoro di monitoraggio di Azure con modelli di Azure Resource Manager | Microsoft docs
description: Semplifica la creazione di report complessi con cartelle di lavoro di monitoraggio di Azure con parametri predefinite e personalizzate distribuite tramite modelli di Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d5e22093fa796a9fbd60dc2bc242f37a6cac7cf0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166109"
---
# <a name="programmatically-manage-workbooks"></a>Gestire le cartelle di lavoro a livello di codice

I proprietari delle risorse hanno la possibilità di creare e gestire le cartelle di lavoro a livello di codice tramite Gestione risorse modelli. 

Questa operazione può essere utile in scenari come i seguenti:
* Distribuzione di report di analisi specifici del dominio o dell'organizzazione, insieme alle distribuzioni di risorse. Ad esempio, è possibile distribuire cartelle di lavoro di prestazioni e di errore specifiche dell'organizzazione per le nuove app o macchine virtuali.
* Distribuzione di report standard o dashboard tramite cartelle di lavoro per le risorse esistenti.

La cartella di lavoro verrà creata nel gruppo secondario/risorse desiderato e con il contenuto specificato nei modelli di Gestione risorse.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Modello di Azure Resource Manager per la distribuzione di cartelle di lavoro
1. Aprire una cartella di lavoro che si desidera distribuire a livello di codice.
2. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
3. Aprire il _Editor avanzato_ usando il pulsante _</>_ sulla barra degli strumenti.
4. Nell'editor modificare il _tipo di modello_ in _Gestione risorse modello_.
5. Il modello di Gestione risorse per la creazione viene visualizzato nell'editor. Copiare il contenuto e usare così com'è o unirlo con un modello più ampio che distribuisce anche la risorsa di destinazione.

    ![Immagine che illustra come ottenere il modello di Gestione risorse dall'interfaccia utente della cartella di lavoro](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modello di Azure Resource Manager di esempio
Questo modello Mostra come distribuire una semplice cartella di lavoro in cui viene visualizzato un'Hello World!'
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parametri del modello

| Parametro | Spiegazione |
| :------------- |:-------------|
| `workbookDisplayName` | Nome descrittivo per la cartella di lavoro utilizzata nella raccolta o nell'elenco salvato. Deve essere univoco nell'ambito del gruppo di risorse e dell'origine |
| `workbookType` | Raccolta in cui verrà visualizzata la cartella di lavoro. I valori supportati includono cartella di lavoro, `tsg`, monitoraggio di Azure e così via. |
| `workbookSourceId` | ID dell'istanza di risorsa a cui verrà associata la cartella di lavoro. La nuova cartella di lavoro verrà visualizzata in relazione a questa istanza di risorsa, ad esempio nella tabella del contenuto della risorsa nella _cartella di lavoro_. Se si vuole che la cartella di lavoro venga visualizzata nella raccolta di cartelle di lavoro di monitoraggio di Azure, usare la stringa _monitoraggio di Azure_ anziché un ID di risorsa. |
| `workbookId` | GUID univoco per questa istanza della cartella di lavoro. Utilizzare _[newGuid ()]_ per creare automaticamente un nuovo GUID. |
| `kind` | Consente di specificare se la cartella di lavoro creata è condivisa o privata. Usare il valore _Shared_ per le cartelle di lavoro condivise e l' _utente_ per quelli privati. |
| `location` | Località di Azure in cui verrà creata la cartella di lavoro. Usare _[resourceGroup (). Location]_ per crearlo nello stesso percorso del gruppo di risorse |
| `serializedData` | Contiene il contenuto o il payload da utilizzare nella cartella di lavoro. Usare il modello di Gestione risorse dall'interfaccia utente delle cartelle di lavoro per ottenere il valore |

### <a name="workbook-types"></a>Tipi di cartella di lavoro
Tipi di cartella di lavoro specificare il tipo di raccolta di cartelle di lavoro in cui sarà visualizzata la nuova istanza della cartella Le opzioni includono:

| Type | Località della raccolta |
| :------------- |:-------------|
| `workbook` | Il valore predefinito usato nella maggior parte dei report, inclusa la raccolta di cartelle di lavoro di Application Insights, monitoraggio di Azure e così via.  |
| `tsg` | Raccolta Guide per la risoluzione dei problemi in Application Insights |
| `usage` | _Più_ raccolta in _uso_ in Application Insights |

### <a name="limitations"></a>Limitazioni
Per motivi tecnici, questo meccanismo non può essere utilizzato per creare istanze di cartella di lavoro nella raccolta di _cartelle di lavoro_ di Application Insights. Stiamo lavorando per risolvere questa limitazione. Nel frattempo, è consigliabile usare la raccolta della Guida alla risoluzione dei problemi (workbookType: `tsg`) per distribuire Application Insights cartelle di lavoro correlate.

## <a name="next-steps"></a>Passaggi successivi

Esplorare il modo in cui vengono usate le cartelle di lavoro per potenziare il nuovo [monitoraggio di Azure per l'esperienza di archiviazione](../insights/storage-insights-overview.md).

