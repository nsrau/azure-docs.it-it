---
title: Creazione di soluzioni di gestione in Operations Management Suite (OMS) | Documentazione Microsoft
description: "Le soluzioni di gestione estendono la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere all&quot;area di lavoro OMS.  In questo articolo vengono fornite informazioni dettagliate su come creare soluzioni di gestione da usare nel proprio ambiente o da rendere disponibili per i propri clienti."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 63b95e205a48e11a5a0717c07ad49cccc8da91b4


---
# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Creazione di soluzioni di gestione in Operations Management Suite (OMS) (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.  
>
>

Le soluzioni di gestione estendono la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere all'area di lavoro OMS.  Questo articolo fornisce informazioni dettagliate sulla creazione di soluzioni di gestione personalizzate da usare nel proprio ambiente o da rendere disponibili per i propri clienti tramite la community.

## <a name="planning-your-management-solution"></a>Pianificazione della soluzione di gestione
Le soluzioni di gestione in OMS includono più risorse che supportano uno scenario di gestione specifico.  Quando si pianifica una soluzione, è consigliabile concentrarsi sullo scenario che si sta provando a ottenere e su tutte le risorse necessarie per supportarlo.  Ogni soluzione deve essere autonoma e definire ogni risorsa richiesta, anche nel caso in cui una o più risorse siano definite da altre soluzioni.  Quando si installa una soluzione di gestione, ogni risorsa che non esiste già viene creata ed è possibile definire cosa accade alle risorse quando la soluzione viene rimossa.  

Una soluzione di gestione, ad esempio, può includere un [runbook di Automazione di Azure](../automation/automation-intro.md) che raccoglie i dati nel repository di Log Analytics usando una [pianificazione](../automation/automation-schedules.md) e una [vista](../log-analytics/log-analytics-view-designer.md) che offre visualizzazioni diverse dei dati raccolti.  La stessa pianificazione potrebbe essere usata da un'altra soluzione.  Quando si crea la soluzione di gestione, si definiscono tutte e tre le risorse, ma si specifica che il runbook e la vista devono essere rimossi automaticamente in caso di rimozione della soluzione.    Si definisce anche la pianificazione e si specifica che non deve essere rimossa in caso di rimozione della soluzione se ancora in uso in un'altra soluzione.

## <a name="management-solution-files"></a>File delle soluzioni di gestione
Le soluzioni di gestione vengono implementate come [modelli di gestione delle risorse](../resource-manager-template-walkthrough.md).  L'attività principale della creazione di soluzioni di gestione consiste nel [creare un modello](../azure-resource-manager/resource-group-authoring-templates.md).  Questo articolo fornisce informazioni dettagliate sui modelli usati per le soluzioni e illustra come definire le risorse tipiche di una soluzione.

La struttura di base di un file di una soluzione di gestione corrisponde a quella di un [modello di Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format), come descritto di seguito.  Ognuna delle sezioni seguenti descrive gli elementi principali di una soluzione e il loro contenuto.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametri
I [parametri](../azure-resource-manager/resource-group-authoring-templates.md#parameters) sono valori richiesti all'utente al momento dell'installazione della soluzione di gestione.  Ci sono parametri standard comuni a tutte le soluzioni ed è possibile aggiungere altri parametri in base a quanto necessario per la soluzione specifica.  Il modo in cui gli utenti forniranno i valori dei parametri quando installano la soluzione dipende dal parametro specifico e dalla modalità di installazione della soluzione.

Quando un utente installa la soluzione di gestione tramite [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) o i [modelli di avvio rapido di Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), viene chiesto di selezionare un'[area di lavoro OMS e un account di Automazione](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Questi elementi vengono usati per popolare i valori di ognuno dei parametri standard.  All'utente non viene chiesto di fornire direttamente i valori per i parametri standard, ma viene chiesto di fornire i valori per eventuali parametri aggiuntivi.

Quando l'utente installa la soluzione con un [altro metodo](operations-management-suite-solutions.md#finding-and-installing-management-solutions), deve specificare un valore per tutti i parametri standard e tutti i parametri aggiuntivi.

Di seguito è illustrato un parametro di esempio.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

La tabella seguente descrive gli attributi di un parametro.

| Attributo | Descrizione |
|:--- |:--- |
| type |Tipo di dati per il parametro. Il controllo di input visualizzato per l'utente dipende dal tipo di dati.<br><br>bool - Casella di riepilogo a discesa<br>string - Casella di testo<br>int - Casella di testo<br>securestring - Campo della password<br> |
| category |Categoria facoltativa per il parametro.  I parametri della stessa categoria vengono raggruppati insieme. |
| control |Funzionalità aggiuntiva per i parametri di stringa.<br><br>datetime - Viene visualizzato un controllo Datetime.<br>guid - Il valore Guid viene generato automaticamente e il parametro non viene visualizzato. |
| description |Descrizione facoltativa del parametro.  Viene visualizzata in un fumetto di informazioni accanto al parametro. |

### <a name="standard-parameters"></a>Parametri standard
La tabella seguente elenca i parametri standard per tutte le soluzioni di gestione.  Quando la soluzione viene installata da Azure Marketplace o dai modelli di avvio rapido, questi valori vengono popolati automaticamente per l'utente senza che venga chiesto di immetterli.  L'utente deve fornire i valori se la soluzione viene installata con un altro metodo.

> [!NOTE]
> L'interfaccia utente in Azure Marketplace e nei modelli di avvio rapido prevede i nomi di parametro indicati nella tabella.  Se si usano nomi di parametro diversi, all'utente verrà chiesto di specificarli e non verranno popolati automaticamente.
>
>

| Parametro | Tipo | Descrizione |
|:--- |:--- |:--- |
| accountName |string |Nome dell'account di Automazione di Azure. |
| pricingTier |string |Piano tariffario dell'area di lavoro di Log Analytics e dell'account di Automazione di Azure. |
| regionId |string |Area dell'account di Automazione di Azure. |
| solutionName |string |Nome della soluzione. |
| workspaceName |string |Nome dell'area di lavoro di Log Analytics. |
| workspaceRegionId |string |Area dell'area di lavoro di Log Analytics. |

### <a name="sample"></a>Esempio
Di seguito è illustrata un'entità parametro di esempio per una soluzione.  Sono inclusi tutti i parametri standard e due parametri aggiuntivi nella stessa categoria.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Per fare riferimento ai valori di parametro negli altri elementi della soluzione si usa la sintassi **parameters('nome parametro')**.  Per accedere, ad esempio, al nome dell'area di lavoro, usare **parameters('workspaceName')**

## <a name="variables"></a>Variabili
L'elemento **Variables** include i valori che verranno usati nel resto della soluzione di gestione.  Questi valori non sono esposti all'utente che esegue l'installazione della soluzione.  La loro funzione è quella di offrire all'autore un'unica posizione in cui gestire i valori che possono essere usati più volte all'interno della soluzione. È consigliabile inserire i valori specifici per la soluzione in variabili invece che hardcoded nell'elemento **resources**.  In questo modo, il codice risulta più leggibile ed è possibile modificare facilmente questi valori nelle versioni successive.

Di seguito è riportato un esempio di elemento **variables** con i parametri tipici usati nelle soluzioni.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Per fare riferimento ai valori di variabile all'interno della soluzione si usa la sintassi **variables('nome variabile')**.  Per accedere, ad esempio, alla variabile SolutionName, usare **variables('solutionName')**

## <a name="resources"></a>Risorse
L'elemento **resources** definisce le diverse risorse incluse nella soluzione di gestione.  Si tratta della parte più estesa e complessa del modello.  Le risorse vengono definite con la struttura seguente.  

    "resources": [
        {
            "name": "<name-of-the-resource>",            
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",        
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dipendenze
L'elemento **dependsOn** specifica una [dipendenza](../resource-group-define-dependencies.md) da un'altra risorsa.  Quando si installa la soluzione, una risorsa viene creata solo dopo che sono state create tutte le relative dipendenze.  La soluzione potrebbe ad esempio [avviare un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) quando viene installata usando una [risorsa processo](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La risorsa processo dipenderà dalla risorsa runbook, per assicurarsi che il runbook venga creato prima del processo.

### <a name="oms-workspace-and-automation-account"></a>Area di lavoro OMS e account di Automazione
Le soluzioni di gestione richiedono un'[area di lavoro OMS](../log-analytics/log-analytics-manage-access.md) per contenere le viste e un [account di Automazione](../automation/automation-security-overview.md#automation-account-overview) per contenere i runbook e le risorse correlate.  Questi elementi devono essere disponibili prima della creazione delle risorse nella soluzione e non devono essere definiti nella soluzione stessa.  L'utente [specificherà un'area di lavoro e un account](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando distribuisce la soluzione, ma l'autore della soluzione deve tenere presente quanto segue.

## <a name="solution-resource"></a>Risorse della soluzione
Per ogni soluzione è necessario specificare una risorsa nell'elemento **resources** che definisce la soluzione stessa.  La risorsa sarà di tipo **Microsoft.OperationsManagement/solutions**.  Di seguito è riportato un esempio di una risorsa soluzione.  I diversi elementi sono descritti nelle sezioni seguenti.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nome soluzione
Il nome della soluzione deve essere univoco nella sottoscrizione di Azure. Il valore consigliato da usare è il seguente.  Questo valore usa una variabile denominata **SolutionName** per il nome di base e il parametro **workspaceName** per garantire che il nome sia univoco.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Tale valore viene risolto in un nome simile al seguente.

    My Solution Name [MyWorkspace]


### <a name="dependencies"></a>Dipendenze
La risorsa soluzione deve avere una [dipendenza](../resource-group-define-dependencies.md) da ogni altra risorsa nella soluzione, perché ogni risorsa deve esistere affinché la soluzione possa essere creata.  A tale scopo, aggiungere una voce per ogni risorsa nell'elemento **dependsOn**.

### <a name="properties"></a>Proprietà
La risorsa della soluzione ha le proprietà descritte nella tabella seguente.  Sono incluse le risorse cui viene fatto riferimento dalla soluzione e incluse nella soluzione che definisce come viene gestita la risorsa dopo l'installazione della soluzione.  Ogni risorsa nella soluzione deve essere presente nella proprietà **referencedResources** o **containedResources**.

| Proprietà | Description |
|:--- |:--- |
| workspaceResourceId |ID dell'area di lavoro OMS nel formato *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Nome area di lavoro\>*. |
| referencedResources |Elenco delle risorse nella soluzione che non devono essere rimosse quando la soluzione viene rimossa. |
| containedResources |Elenco delle risorse nella soluzione che devono essere rimosse quando la soluzione viene rimossa. |

L'esempio precedente si riferisce a una soluzione con un runbook, una pianificazione e una vista.  Poiché sono presenti *riferimenti* ad essi nell'elemento **properties**, la pianificazione e il runbook non vengono rimossi quando la soluzione viene rimossa.  La vista che è invece *contenuta* viene rimossa quando la soluzione viene rimossa.

### <a name="plan"></a>Pianificazione
L'entità **plan** della risorsa soluzione ha le proprietà descritte nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| name |Nome della soluzione. |
| version |Versione della soluzione determinata dall'autore. |
| product |Stringa univoca che identifica la soluzione. |
| publisher |Autore della soluzione. |

## <a name="other-resources"></a>Altre risorse:
Informazioni dettagliate ed esempi di risorse comuni alle soluzioni di gestione sono disponibili negli articoli seguenti.

* [Viste e dashboard](operations-management-suite-solutions-resources-views.md)
* [Risorse di Automazione](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Test di una soluzione di gestione
Prima di distribuire la soluzione di gestione, è consigliabile testarla usando [Test-AzureRmResourceGroupDeployment](../azure-resource-manager/resource-group-template-deploy.md#deploy).  Il file della soluzione verrà convalidato e sarà possibile identificare eventuali problemi prima di procedere alla distribuzione.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni dettagliate sulla [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Cercare i [modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates) per esempi di modelli di Resource Manager.
* Visualizzare i dettagli per l'[aggiunta di viste a una soluzione di gestione](operations-management-suite-solutions-resources-views.md).
* Visualizzare i dettagli per l'[aggiunta di risorse di Automazione a una soluzione di gestione](operations-management-suite-solutions-resources-automation.md).



<!--HONumber=Dec16_HO1-->


