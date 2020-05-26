---
title: Cartelle di lavoro di Monitoraggio di Azure e modelli di Azure Resource Manager
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri di Monitoraggio di Azure predefinite e personalizzate distribuite tramite modelli di Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mbullwin
ms.openlocfilehash: 76ecc3ee17353ebd0bbead1bba959f85d521d0df
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982140"
---
# <a name="programmatically-manage-workbooks"></a>Gestire le cartelle di lavoro a livello di codice

I proprietari delle risorse possono creare e gestire le cartelle di lavoro a livello di codice tramite i modelli di Resource Manager.

Ciò può essere utile in scenari come i seguenti:
* Distribuzione di report di analisi specifici del dominio o dell'organizzazione, insieme alle distribuzioni di risorse. È ad esempio possibile distribuire cartelle di lavoro relative a prestazioni ed errori specifiche dell'organizzazione per le nuove app o macchine virtuali.
* Distribuzione di report o dashboard standard tramite cartelle di lavoro per le risorse esistenti.

La cartella di lavoro verrà creata nel gruppo di risorse o secondario desiderato e con il contenuto specificato nei modelli di Resource Manager.

Ci sono due tipi di risorse di cartelle di lavoro che è possibile gestire a livello di codice:
* [Modelli di cartella di lavoro](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Istanze di cartella di lavoro](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Modello di Azure Resource Manager per la distribuzione di un modello di cartella di lavoro

1. Aprire una cartella di lavoro che si vuole distribuire a livello di codice.
2. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
3. Aprire l'_Editor avanzato_ usando il pulsante _</>_ sulla barra degli strumenti.
4. Verificare che sia visualizzata la scheda _Modello raccolta_.

    ![Scheda Modello raccolta](./media/workbooks-automate/gallery-template.png)
1. Copiare negli Appunti il codice JSON del modello di raccolta.
2. Di seguito è illustrato un modello di Azure Resource Manager di esempio che consente di distribuire un modello di cartella di lavoro nella raccolta di cartelle di lavoro di Monitoraggio di Azure. Incollare il codice JSON copiato al posto di `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>`. È possibile trovare un modello di Azure Resource Manager di riferimento per la creazione di un modello di cartella di lavoro [qui](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. Nell'oggetto `galleries` inserire i propri valori per le chiavi `name` e `category`. Per altre informazioni sui [parametri](#parameters) vedere la sezione successiva.
2. Distribuire il modello di Azure Resource Manager usando il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template), l'[interfaccia della riga di comando](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell) e così via.
3. Aprire il portale di Azure e passare alla raccolta di cartelle di lavoro scelta nel modello di Azure Resource Manager. Nel modello di esempio passare alla raccolta di cartelle di lavoro di Monitoraggio di Azure:
    1. Aprire il portale di Azure e passare a Monitoraggio di Azure
    2. Aprire `Workbooks` dal sommario
    3. Trovare il modello nella raccolta nella categoria `Deployed Templates` (sarà uno degli elementi viola).

### <a name="parameters"></a>Parametri

|Parametri                |Spiegazione                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Nome della risorsa modello di cartella di lavoro in Azure Resource Manager.                                  |
|`type`                    | Sempre microsoft.insights/workbooktemplates                                                            |
| `location`               | Posizione di Azure in cui verrà creata la cartella di lavoro.                                               |
| `apiVersion`             | Anteprima 2019-10-17                                                                                     |
| `type`                   | Sempre microsoft.insights/workbooktemplates                                                            |
| `galleries`              | Set di raccolte in cui visualizzare il modello di cartella di lavoro.                                                |
| `gallery.name`           | Nome descrittivo del modello di cartella di lavoro nella raccolta.                                             |
| `gallery.category`       | Gruppo nella raccolta in cui inserire il modello.                                                     |
| `gallery.order`          | Numero che determina l'ordine di visualizzazione del modello all'interno di una categoria nella raccolta. Un valore inferiore dell'ordine implica una priorità più elevata. |
| `gallery.resourceType`   | Tipo di risorsa corrispondente alla raccolta. Si tratta in genere della stringa del tipo di risorsa corrispondente alla risorsa (ad esempio, microsoft.operationalinsights/workspaces). |
|`gallery.type`            | Corrisponde al tipo di cartella di lavoro ed è una chiave univoca che differenzia la raccolta all'interno di un tipo di risorsa. Application Insights, ad esempio, ha i tipi `workbook` e `tsg` corrispondenti a raccolte di cartelle di lavoro diverse. |

### <a name="galleries"></a>Raccolte

| Raccolta                                        | Tipo di risorsa                                      | Tipo di cartella di lavoro |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Cartelle di lavoro in Monitoraggio di Azure                     | `Azure Monitor`                                    | `workbook`    |
| Informazioni dettagliate macchina virtuale in Monitoraggio di Azure                   | `Azure Monitor`                                    | `vm-insights` |
| Cartelle di lavoro in Area di lavoro Log Analytics           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Cartelle di lavoro in Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Guide alla risoluzione dei problemi in Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Utilizzo in Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Cartelle di lavoro in Servizio Kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Cartelle di lavoro in Gruppi di risorse                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Cartelle di lavoro in Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| Informazioni dettagliate macchina virtuale in Macchine virtuali                | `microsoft.compute/virtualmachines`                | `insights`    |
| Informazioni dettagliate macchina virtuale in Set di scalabilità di macchine virtuali      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Modello di Azure Resource Manager per la distribuzione di un'istanza di cartella di lavoro

1. Aprire una cartella di lavoro che si vuole distribuire a livello di codice.
2. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
3. Aprire l'_Editor avanzato_ usando il pulsante _</>_ sulla barra degli strumenti.
4. Nell'editor impostare _Tipo di modello_ su _Modello di Resource Manager_.
5. Il modello di Resource Manager per la creazione verrà visualizzato nell'editor. Copiare il contenuto e usarlo così com'è o unirlo con un modello più ampio che consente anche la distribuzione della risorsa di destinazione.

    ![Immagine che mostra come ottenere il modello di Resource Manager dall'interfaccia utente della cartella di lavoro](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modello di Azure Resource Manager di esempio
Questo modello mostra come distribuire una cartella di lavoro semplice in cui è visualizzato "Hello World!"
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
| `workbookDisplayName` | Nome descrittivo per la cartella di lavoro usato nella raccolta o nell'elenco elementi salvati. Il nome deve essere univoco nell'ambito del gruppo di risorse e dell'origine. |
| `workbookType` | Raccolta in cui verrà visualizzata la cartella di lavoro. I valori supportati includono workbook, `tsg`, Azure Monitor e così via. |
| `workbookSourceId` | ID dell'istanza della risorsa a cui verrà associata la cartella di lavoro. La nuova cartella di lavoro verrà visualizzata in relazione a questa istanza di risorsa, ad esempio nel sommario della risorsa in _Cartella di lavoro_. Se si vuole che la cartella di lavoro venga visualizzata nella raccolta di cartelle di lavoro in Monitoraggio di Azure, usare la stringa _Azure Monitor_ invece di un ID della risorsa. |
| `workbookId` | GUID univoco per l'istanza della cartella di lavoro. Usare _[newGuid()]_ per creare automaticamente un nuovo GUID. |
| `kind` | Usato per specificare se la cartella di lavoro creata è condivisa o privata. Usare il valore _shared_ per le cartelle di lavoro condivise e _user_ per quelle private. |
| `location` | Posizione di Azure in cui verrà creata la cartella di lavoro. Usare _[resourceGroup().location]_ per eseguire la creazione nella stessa posizione del gruppo di risorse. |
| `serializedData` | Contiene il contenuto o il payload da usare nella cartella di lavoro. Usare il modello di Resource Manager dall'interfaccia utente delle cartelle di lavoro per ottenere il valore |

### <a name="workbook-types"></a>Tipi di cartella di lavoro
I tipi di cartella di lavoro specificano il tipo di raccolta di cartelle di lavoro in cui sarà visualizzata la nuova istanza di cartella di lavoro. Le opzioni includono:

| Tipo | Posizione della raccolta |
| :------------- |:-------------|
| `workbook` | Posizione predefinita usata nella maggior parte dei report, tra cui la raccolta di cartelle di lavoro di Application Insights, Monitoraggio di Azure e così via.  |
| `tsg` | Raccolta Guide alla risoluzione dei problemi in Application Insights |
| `usage` | Raccolta _Altro_ in _Utilizzo_ in Application Insights |

### <a name="limitations"></a>Limitazioni
Per motivi tecnici, questo meccanismo non può essere usato per creare istanze di cartelle di lavoro nella raccolta _Cartelle di lavoro_ di Application Insights. Microsoft sta lavorando per ovviare a questa limitazione. Nel frattempo, è consigliabile usare la raccolta Guida alla risoluzione dei problemi (tipo di cartella di lavoro: `tsg`) per distribuire le cartelle di lavoro correlate ad Application Insights.

## <a name="next-steps"></a>Passaggi successivi

Esplorare il modo in cui vengono usate le cartelle di lavoro per potenziare la nuova [esperienza di Monitoraggio di Azure per l'archiviazione](../insights/storage-insights-overview.md).
