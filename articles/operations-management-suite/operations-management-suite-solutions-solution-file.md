---
title: Creazione di soluzioni di gestione in Operations Management Suite (OMS) | Documentazione Microsoft
description: "Le soluzioni di gestione estendono la funzionalità di Operations Management Suite (OMS) offrendo scenari di gestione in pacchetto che i clienti possono aggiungere all'area di lavoro OMS.  In questo articolo vengono fornite informazioni dettagliate su come creare soluzioni di gestione da usare nel proprio ambiente o da rendere disponibili per i propri clienti."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: ee3462c13101d18921dc488b08c79e1e4e02ff3a
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---
# <a name="creating-a-management-solution-file-in-operations-management-suite-oms-preview"></a>Creazione di una soluzione di gestione in Operations Management Suite (OMS) (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in OMS attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.  

Le soluzioni di gestione in Operations Management Suite (OMS) vengono implementate come [modelli di Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).  L'attività principale della creazione di soluzioni di gestione consiste nel [creare un modello](../azure-resource-manager/resource-group-authoring-templates.md).  Questo articolo fornisce informazioni dettagliate sui modelli usati per le soluzioni e illustra come configurare le risorse tipiche di una soluzione.


## <a name="tools"></a>Strumenti

È possibile usare qualsiasi editor di testo per interagire con i file della soluzione, ma si consiglia di avvalersi delle funzionalità disponibili in Visual Studio o Visual Studio Code, come descritto negli articoli seguenti.

- [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Utilizzo dei modelli di Azure Resource Manager nel codice di Visual Studio](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>Structure
La struttura di base di un file di una soluzione di gestione corrisponde a quella di un [modello di Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format), come descritto di seguito.  Ognuna delle sezioni seguenti descrive gli elementi principali di una soluzione e i relativi contenuti.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametri
I [parametri](../azure-resource-manager/resource-group-authoring-templates.md#parameters) sono valori richiesti all'utente al momento dell'installazione della soluzione di gestione.  Ci sono parametri standard comuni a tutte le soluzioni ed è possibile aggiungere altri parametri in base a quanto necessario per la soluzione specifica.  Il modo in cui gli utenti forniranno i valori dei parametri quando installano la soluzione dipende dal parametro specifico e dalla modalità di installazione della soluzione.

Quando un utente installa la soluzione di gestione tramite [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-management-solutions) o i [modelli di avvio rapido di Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), viene chiesto di selezionare un'[area di lavoro OMS e un account di Automazione](operations-management-suite-solutions.md#oms-workspace-and-automation-account).  Questi elementi vengono usati per popolare i valori di ognuno dei parametri standard.  All'utente non viene chiesto di fornire direttamente i valori per i parametri standard, ma viene chiesto di fornire i valori per eventuali parametri aggiuntivi.

Quando l'utente installa la soluzione con un [altro metodo](operations-management-suite-solutions.md#finding-and-installing-management-solutions), deve specificare un valore per tutti i parametri standard e tutti i parametri aggiuntivi.

Di seguito è illustrato un parametro di esempio.  

    "startTime": {
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
| solutionName |string |Nome della soluzione.  Se si distribuisce la soluzione tramite modelli di avvio rapido, è necessario definire solutionName come un parametro, in modo da poter definire una stringa anziché richiedere all'utente di specificarne una. |
| workspaceName |string |Nome dell'area di lavoro di Log Analytics. |
| workspaceRegionId |string |Area dell'area di lavoro di Log Analytics. |


Di seguito viene mostrata la struttura dei parametri standard, che è possibile copiare e incollare nel file della soluzione.  

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
        }
    }


Per fare riferimento ai valori di parametro negli altri elementi della soluzione si usa la sintassi **parameters('nome parametro')**.  Per accedere, ad esempio, al nome dell'area di lavoro, usare **parameters('workspaceName')**

## <a name="variables"></a>Variabili
Le [variabili](../azure-resource-manager/resource-group-authoring-templates.md#variables) sono valori che verranno usati nella parte rimanente della soluzione di gestione.  Questi valori non sono esposti all'utente che esegue l'installazione della soluzione.  La loro funzione è quella di offrire all'autore un'unica posizione in cui gestire i valori che possono essere usati più volte all'interno della soluzione. È consigliabile inserire eventuali valori specifici della soluzione in variabili anziché impostarli come hardcoded nell'elemento **resources**.  In questo modo, il codice risulta più leggibile ed è possibile modificare facilmente questi valori nelle versioni successive.

Di seguito è riportato un esempio di elemento **variables** con i parametri tipici usati nelle soluzioni.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Per fare riferimento ai valori di variabile all'interno della soluzione si usa la sintassi **variables('nome variabile')**.  Per accedere, ad esempio, alla variabile SolutionName, usare **variables('SolutionName')**.

È possibile anche definire variabili complesse che moltiplicano set di valori;  risultano particolarmente utili nelle soluzioni di gestione in cui si definiscono più proprietà per diversi tipi di risorse.  È possibile, ad esempio, ristrutturare come indicato di seguito le variabili di soluzione illustrate in precedenza.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

In questo caso, per fare riferimento ai valori di variabile all'interno della soluzione è possibile usare la sintassi **variables('nome variabile').proprietà**.  Per accedere, ad esempio, alla variabile SolutionName, è necessario usare **variables('Solution').Name**.

## <a name="resources"></a>Risorse
Le [risorse](../azure-resource-manager/resource-group-authoring-templates.md#resources) definiscono i vari tipi di risorse che la soluzione di gestione installerà e configurerà.  Si tratta della parte più estesa e complessa del modello.  È possibile ottenere informazioni sulla struttura e una descrizione completa degli elementi di risorsa in [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#resources).  In altri articoli di questa documentazione sono descritti i tipi di risorse definiti più comunemente. 


### <a name="dependencies"></a>Dipendenze
L'elemento **dependsOn** specifica una [dipendenza](../azure-resource-manager/resource-group-define-dependencies.md) da un'altra risorsa.  Quando si installa la soluzione, una risorsa viene creata solo dopo che sono state create tutte le relative dipendenze.  La soluzione potrebbe ad esempio [avviare un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) quando viene installata usando una [risorsa processo](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La risorsa processo dipenderà dalla risorsa runbook, per assicurarsi che il runbook venga creato prima del processo.

### <a name="oms-workspace-and-automation-account"></a>Area di lavoro OMS e account di Automazione
Le soluzioni di gestione richiedono un'[area di lavoro OMS](../log-analytics/log-analytics-manage-access.md) per contenere le viste e un [account di Automazione](../automation/automation-security-overview.md#automation-account-overview) per contenere i runbook e le risorse correlate.  Questi elementi devono essere disponibili prima della creazione delle risorse nella soluzione e non devono essere definiti nella soluzione stessa.  L'utente [specificherà un'area di lavoro e un account](operations-management-suite-solutions.md#oms-workspace-and-automation-account) quando distribuisce la soluzione, ma l'autore della soluzione deve tenere presente quanto segue.

## <a name="solution-resource"></a>Risorse della soluzione
Per ogni soluzione è necessario specificare una risorsa nell'elemento **resources** che definisce la soluzione stessa.  La risorsa sarà di tipo **Microsoft.OperationsManagement/solutions** e avrà la struttura seguente. Sono inclusi i [parametri standard](#parameters) e le [variabili](#variables) generalmente usati per definire le proprietà della soluzione.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspacename'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Dipendenze
La risorsa soluzione deve avere una [dipendenza](../azure-resource-manager/resource-group-define-dependencies.md) da ogni altra risorsa nella soluzione, perché ogni risorsa deve esistere affinché la soluzione possa essere creata.  A tale scopo, aggiungere una voce per ogni risorsa nell'elemento **dependsOn**.

### <a name="properties"></a>Proprietà
La risorsa della soluzione ha le proprietà descritte nella tabella seguente.  Sono incluse le risorse cui viene fatto riferimento dalla soluzione e incluse nella soluzione che definisce come viene gestita la risorsa dopo l'installazione della soluzione.  Ogni risorsa nella soluzione deve essere presente nella proprietà **referencedResources** o **containedResources**.

| Proprietà | Description |
|:--- |:--- |
| workspaceResourceId |ID dell'area di lavoro Log Analytics nel formato *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Nome area di lavoro\>*. |
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



## <a name="sample"></a>Esempio
Esempi di file di soluzioni con una risorsa "soluzione" sono disponibili negli articoli seguenti.

- [Risorse di Automazione](operations-management-suite-solutions-resources-automation.md#sample)
- [Risorse di ricerca e di avviso](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere ricerche salvate e avvisi](operations-management-suite-solutions-resources-searches-alerts.md) alla soluzione di gestione.
* [Aggiungere viste](operations-management-suite-solutions-resources-views.md) alla soluzione di gestione.
* [Aggiungere runbook e altre risorse di automazione](operations-management-suite-solutions-resources-automation.md) alla soluzione di gestione.
* Informazioni dettagliate sulla [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Cercare i [modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates) per esempi di modelli di Resource Manager.

