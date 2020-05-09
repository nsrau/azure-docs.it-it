---
title: Cartelle di lavoro e modelli di Azure Resource Manager di monitoraggio di Azure
description: Semplifica la creazione di report complessi con cartelle di lavoro di monitoraggio di Azure con parametri predefinite e personalizzate distribuite tramite modelli di Azure Resource Manager
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982140"
---
# <a name="programmatically-manage-workbooks"></a>Gestire le cartelle di lavoro a livello di codice

I proprietari delle risorse hanno la possibilità di creare e gestire le cartelle di lavoro a livello di codice tramite Gestione risorse modelli.

Questa operazione può essere utile in scenari come i seguenti:
* Distribuzione di report di analisi specifici del dominio o dell'organizzazione, insieme alle distribuzioni di risorse. Ad esempio, è possibile distribuire cartelle di lavoro di prestazioni e di errore specifiche dell'organizzazione per le nuove app o macchine virtuali.
* Distribuzione di report standard o dashboard tramite cartelle di lavoro per le risorse esistenti.

La cartella di lavoro verrà creata nel gruppo secondario/risorse desiderato e con il contenuto specificato nei modelli di Gestione risorse.

Esistono due tipi di risorse della cartella di lavoro che possono essere gestite a livello di codice:
* [Modelli di cartella di lavoro](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Istanze cartella di lavoro](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Modello Azure Resource Manager per la distribuzione di un modello di cartella di lavoro

1. Aprire una cartella di lavoro che si desidera distribuire a livello di codice.
2. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
3. Aprire il _Editor avanzato_ usando il _</>_ pulsante sulla barra degli strumenti.
4. Assicurarsi di essere nella scheda _modello della raccolta_ .

    ![Scheda modello raccolta](./media/workbooks-automate/gallery-template.png)
1. Copiare negli Appunti il codice JSON nel modello della raccolta.
2. Di seguito è riportato un esempio di Azure Resource Manager modello che consente di distribuire un modello di cartella di lavoro nella raccolta di cartelle di lavoro di monitoraggio Incollare il codice JSON copiato al posto di `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>`. Un modello di Azure Resource Manager di riferimento per la creazione di un modello di cartella di lavoro è disponibile [qui](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

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
1. Nell' `galleries` oggetto compilare le `name` chiavi e `category` con i valori. Ulteriori informazioni sui [parametri](#parameters) sono disponibili nella sezione successiva.
2. Distribuire questo modello di Azure Resource Manager usando il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template), l' [interfaccia della riga di comando](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)e così via.
3. Aprire il portale di Azure e passare alla raccolta di cartelle di lavoro scelta nel modello di Azure Resource Manager. Nel modello di esempio passare alla raccolta di cartelle di lavoro di monitoraggio di Azure:
    1. Aprire il portale di Azure e passare a monitoraggio di Azure
    2. Apri `Workbooks` dal sommario
    3. Trovare il modello nella raccolta in Category `Deployed Templates` (sarà uno degli elementi viola).

### <a name="parameters"></a>Parametri

|Parametri                |Spiegazione                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Nome della risorsa del modello di cartella di lavoro in Azure Resource Manager.                                  |
|`type`                    | Sempre Microsoft. Insights/workbooktemplates                                                            |
| `location`               | Località di Azure in cui verrà creata la cartella di lavoro.                                               |
| `apiVersion`             | Anteprima 2019-10-17                                                                                     |
| `type`                   | Sempre Microsoft. Insights/workbooktemplates                                                            |
| `galleries`              | Set di raccolte in cui visualizzare il modello di cartella di lavoro.                                                |
| `gallery.name`           | Nome descrittivo del modello di cartella di lavoro nella raccolta.                                             |
| `gallery.category`       | Gruppo della raccolta in cui inserire il modello.                                                     |
| `gallery.order`          | Numero che decide l'ordine di visualizzazione del modello all'interno di una categoria della raccolta. L'ordine inferiore implica una priorità più elevata. |
| `gallery.resourceType`   | Tipo di risorsa corrispondente alla raccolta. Si tratta in genere della stringa del tipo di risorsa corrispondente alla risorsa (ad esempio, Microsoft. operationalinsights/Workspaces). |
|`gallery.type`            | Definito come tipo di cartella di lavoro, si tratta di una chiave univoca che differenzia la raccolta all'interno di un tipo di risorsa. Application Insights, ad esempio, disporre di `workbook` tipi `tsg` e corrispondenti a raccolte di cartelle di lavoro diverse. |

### <a name="galleries"></a>Raccolte

| Gallery                                        | Tipo di risorsa                                      | Tipo di cartella di lavoro |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Cartelle di lavoro di monitoraggio di Azure                     | `Azure Monitor`                                    | `workbook`    |
| VM Insights in monitoraggio di Azure                   | `Azure Monitor`                                    | `vm-insights` |
| Cartelle di lavoro nell'area di lavoro di log Analytics           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Cartelle di lavoro in Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Guide alla risoluzione dei problemi in Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Utilizzo in Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Cartelle di lavoro nel servizio Kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Cartelle di lavoro nei gruppi di risorse                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Cartelle di lavoro in Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| Informazioni dettagliate sulle VM in macchine virtuali                | `microsoft.compute/virtualmachines`                | `insights`    |
| Informazioni dettagliate sulle VM nei set di scalabilità di macchine virtuali      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Azure Resource Manager modello per la distribuzione di un'istanza di cartella di lavoro

1. Aprire una cartella di lavoro che si desidera distribuire a livello di codice.
2. Passare alla modalità di modifica della cartella di lavoro facendo clic sull'elemento della barra degli strumenti _modifica_ .
3. Aprire il _Editor avanzato_ usando il _</>_ pulsante sulla barra degli strumenti.
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

### <a name="template-parameters"></a>Parametri di modelli

| Parametro | Spiegazione |
| :------------- |:-------------|
| `workbookDisplayName` | Nome descrittivo per la cartella di lavoro utilizzata nella raccolta o nell'elenco salvato. Deve essere univoco nell'ambito del gruppo di risorse e dell'origine |
| `workbookType` | Raccolta in cui verrà visualizzata la cartella di lavoro. I valori supportati includono cartella `tsg`di lavoro,, monitoraggio di Azure e così via. |
| `workbookSourceId` | ID dell'istanza di risorsa a cui verrà associata la cartella di lavoro. La nuova cartella di lavoro verrà visualizzata in relazione a questa istanza di risorsa, ad esempio nella tabella del contenuto della risorsa nella _cartella di lavoro_. Se si vuole che la cartella di lavoro venga visualizzata nella raccolta di cartelle di lavoro di monitoraggio di Azure, usare la stringa _monitoraggio di Azure_ anziché un ID di risorsa. |
| `workbookId` | GUID univoco per questa istanza della cartella di lavoro. Utilizzare _[newGuid ()]_ per creare automaticamente un nuovo GUID. |
| `kind` | Consente di specificare se la cartella di lavoro creata è condivisa o privata. Usare il valore _Shared_ per le cartelle di lavoro condivise e l' _utente_ per quelli privati. |
| `location` | Località di Azure in cui verrà creata la cartella di lavoro. Usare _[resourceGroup (). Location]_ per crearlo nello stesso percorso del gruppo di risorse |
| `serializedData` | Contiene il contenuto o il payload da utilizzare nella cartella di lavoro. Usare il modello di Gestione risorse dall'interfaccia utente delle cartelle di lavoro per ottenere il valore |

### <a name="workbook-types"></a>Tipi di cartella di lavoro
Tipi di cartella di lavoro specificare il tipo di raccolta di cartelle di lavoro in cui sarà visualizzata la nuova istanza della cartella Le opzioni includono:

| Tipo | Località della raccolta |
| :------------- |:-------------|
| `workbook` | Il valore predefinito usato nella maggior parte dei report, inclusa la raccolta di cartelle di lavoro di Application Insights, monitoraggio di Azure e così via.  |
| `tsg` | Raccolta Guide per la risoluzione dei problemi in Application Insights |
| `usage` | _Più_ raccolta in _uso_ in Application Insights |

### <a name="limitations"></a>Limitazioni
Per motivi tecnici, questo meccanismo non può essere utilizzato per creare istanze di cartella di lavoro nella raccolta di _cartelle di lavoro_ di Application Insights. Stiamo lavorando per risolvere questa limitazione. Nel frattempo, è consigliabile usare la raccolta della Guida alla risoluzione dei problemi (workbookType: `tsg`) per distribuire Application Insights cartelle di lavoro correlate.

## <a name="next-steps"></a>Passaggi successivi

Esplorare il modo in cui vengono usate le cartelle di lavoro per potenziare il nuovo [monitoraggio di Azure per l'esperienza di archiviazione](../insights/storage-insights-overview.md).
