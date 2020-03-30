---
title: Azure Monitor Workbooks and Azure Resource Manager Templates
description: Semplifica la creazione di report complessi con cartelle di lavoro di Monitoraggio di Azure predefinite e personalizzate distribuite tramite i modelli di Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658405"
---
# <a name="programmatically-manage-workbooks"></a>Gestire le cartelle di lavoro a livello di codiceProgrammatically manage workbooks

I proprietari delle risorse hanno la possibilità di creare e gestire le cartelle di lavoro a livello di programmazione tramite i modelli di Resource Manager.Resource owners have the option to create and manage their workbooks programmatically via Resource Manager templates. 

Ciò può essere utile in scenari come:This can be useful in scenarios like:
* Distribuzione di report di analisi specifici dell'organizzazione o del dominio insieme alle distribuzioni delle risorse. Ad esempio, è possibile distribuire cartelle di lavoro di prestazioni e errori specifiche dell'organizzazione per le nuove app o macchine virtuali.
* Distribuzione di report o dashboard standard utilizzando cartelle di lavoro per le risorse esistenti.

La cartella di lavoro verrà creata nel gruppo secondario/risorsa desiderato e con il contenuto specificato nei modelli di Resource Manager.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Modello di Azure Resource Manager per la distribuzione di cartelle di lavoroAzure Resource Manager template for deploying workbooks
1. Aprire una cartella di lavoro che si desidera distribuire a livello di codice.
2. Impostare la modalità di modifica della cartella di lavoro facendo clic sull'elemento _modifica_ della barra degli strumenti.
3. Aprire _l'Editor avanzato_ utilizzando il _</>_ pulsante sulla barra degli strumenti.
4. Nell'editor impostare _Tipo modello_ su _modello Gestione risorse_.
5. Il modello Resource Manager per la creazione viene visualizzato nell'editor. Copiare il contenuto e usarlo così com'è o unirlo a un modello più grande che distribuisce anche la risorsa di destinazione.

    ![Immagine che mostra come ottenere il modello di Resource Manager dall'interfaccia utente della cartella di lavoro](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modello di Azure Resource Manager di esempioSample Azure Resource Manager template
Questo modello mostra come distribuire una cartella di lavoro semplice che visualizza un 'Hello World!'
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
| `workbookDisplayName` | Nome descrittivo della cartella di lavoro utilizzata nella raccolta o nell'elenco salvato. Deve essere univoco nell'ambito del gruppo di risorse e dell'origine |
| `workbookType` | Raccolta in cui verrà visualizzata la cartella di lavoro. I valori supportati `tsg`includono cartella di lavoro, Monitoraggio di Azure e così via. |
| `workbookSourceId` | ID dell'istanza della risorsa a cui verrà associata la cartella di lavoro. La nuova cartella di lavoro verrà visualizzata in relazione a questa istanza di risorsa, ad esempio nell'indice di contenuto della risorsa in _Cartella di lavoro_. Se si vuole che la cartella di lavoro venga visualizzata nella raccolta di cartelle di lavoro in Monitoraggio di Azure, usare la stringa _Monitoraggio di Azure_ anziché un ID risorsa. |
| `workbookId` | Guid univoco per questa istanza della cartella di lavoro. Utilizzare _[newGuid()]_ per creare automaticamente un nuovo GUID. |
| `kind` | Consente di specificare se la cartella di lavoro creata è condivisa o privata. Utilizzare il valore _condiviso_ per le cartelle di lavoro condivise e _l'utente_ per quelle private. |
| `location` | Percorso di Azure in cui verrà creata la cartella di lavoro. Usare [resourceGroup().location] per crearlo nello stesso percorso del gruppo di risorseUse _[resourceGroup().location]_ to create it in the same location as the resource group |
| `serializedData` | Contiene il contenuto o il payload da utilizzare nella cartella di lavoro. Usare il modello Resource Manager dall'interfaccia utente delle cartelle di lavoro per ottenere il valoreUse the Resource Manager template from the workbooks UI to get the value |

### <a name="workbook-types"></a>Tipi di cartella di lavoro
Tipi di cartella di lavoro specificano il tipo di raccolta cartelle di lavoro in cui verrà visualizzata la nuova istanza della cartella di lavoro. Le opzioni includono:

| Type | Posizione della galleria |
| :------------- |:-------------|
| `workbook` | Il valore predefinito usato nella maggior parte dei report, inclusa la raccolta Di cartelle di lavoro di Application Insights, Monitoraggio di Azure e così via.  |
| `tsg` | Raccolta Guide per la risoluzione dei problemi in Application Insights |
| `usage` | Raccolta Altre in Utilizzo in Application InsightsThe _More_ gallery under _Usage_ in Application Insights |

### <a name="limitations"></a>Limitazioni
Per un motivo tecnico, questo meccanismo non può essere utilizzato per creare istanze della cartella di lavoro nella raccolta Di lavoro di Application Insights.For a technical reason, this mechanism cannot be used to create workbook instances in the _Workbooks_ gallery of Application Insights. Stiamo lavorando per affrontare questa limitazione. Nel frattempo, è consigliabile utilizzare la raccolta Guida `tsg`alla risoluzione dei problemi (workbookType: ) per distribuire le cartelle di lavoro correlate ad Application Insights.

## <a name="next-steps"></a>Passaggi successivi

Scopri come vengono usate le cartelle di lavoro per potenziare la nuova esperienza di [Monitoraggio di Azure per archiviazione.](../insights/storage-insights-overview.md)

