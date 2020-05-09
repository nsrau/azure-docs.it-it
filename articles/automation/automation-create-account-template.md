---
title: Usare i modelli di Azure Resource Manager per creare un account di automazione | Microsoft Docs
description: È possibile usare un modello di Azure Resource Manager per creare un account di automazione di Azure.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/24/2020
ms.openlocfilehash: 19aee9d5fdf3f4a3d74484bb7cb2e609bc2807b4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927867"
---
# <a name="create-an-automation-account-by-using-an-azure-resource-manager-template"></a>Creare un account di automazione usando un modello di Azure Resource Manager

È possibile usare i [modelli di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) per creare un account di automazione di Azure nel gruppo di risorse. Questo articolo fornisce un modello di esempio che:

* Automatizza la creazione di un'area di lavoro Log Analytics di monitoraggio di Azure.
* Automatizza la creazione di un account di automazione di Azure.
* Collega l'account di automazione all'area di lavoro Log Analytics.

Il modello non automatizza l'onboarding di macchine virtuali o soluzioni di Azure o non Azure. 

>[!NOTE]
>La creazione dell'account RunAs di automazione non è supportata quando si usa un modello di Azure Resource Manager. Per creare manualmente un account RunAs dal portale o con PowerShell, vedere [gestire gli account RunAs](manage-runas-account.md).

## <a name="api-versions"></a>Versioni dell'API

La tabella seguente elenca la versione dell'API per le risorse usate in questo esempio.

| Risorsa | Tipo di risorsa | Versione dell'API |
|:---|:---|:---|
| Area di lavoro | aree di lavoro | 2017-03-15-preview |
| Account di Automazione | automation | 2015-10-31 | 

## <a name="before-you-use-the-template"></a>Prima di usare il modello

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede il Azure PowerShell AZ Module. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure. Con PowerShell, la distribuzione USA [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, questo articolo richiede la versione 2.1.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Con l'interfaccia della riga di comando di Azure, questa distribuzione USA [AZ Group Deployment create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Il modello JSON è configurato in modo da richiedere:

* Nome dell'area di lavoro.
* Area in cui creare l'area di lavoro.
* Nome dell'account di automazione.
* Area in cui creare l'account.

I seguenti parametri nel modello vengono impostati con un valore predefinito per l'area di lavoro Log Analytics:

* per impostazione predefinita, lo *SKU* è il piano tariffario per GB rilasciato nel modello di determinazione prezzi di aprile 2018.
* *Dataconservazione* viene impostato su 30 giorni.
* il valore predefinito di *capacityReservationLevel* è 100 GB.

>[!WARNING]
>Se si desidera creare o configurare un'area di lavoro Log Analytics in una sottoscrizione che ha scelto il modello di determinazione prezzi di aprile 2018, l'unico piano tariffario Log Analytics valido è *PerGB2018*.
>

Il modello JSON specifica un valore predefinito per gli altri parametri che verrebbero probabilmente usati come configurazione standard nell'ambiente in uso. È possibile archiviare il modello in un account di archiviazione di Azure per l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [distribuire le risorse con i modelli di gestione risorse e l'interfaccia della](../azure-resource-manager/templates/deploy-cli.md)riga di comando di Azure.

Se non si ha familiarità con automazione di Azure e monitoraggio di Azure, è importante comprendere i dettagli di configurazione seguenti. Consentono di evitare errori quando si tenta di creare, configurare e usare un'area di lavoro Log Analytics collegata al nuovo account di automazione. 

* Esaminare [i dettagli aggiuntivi](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) per comprendere completamente le opzioni di configurazione dell'area di lavoro, ad esempio la modalità di controllo di accesso, il piano tariffario, la conservazione e il livello di prenotazione

* Esaminare i [mapping dell'area di lavoro](how-to/region-mappings.md) per specificare le aree supportate inline o in un file di parametri. Sono supportate solo alcune aree per collegare un'area di lavoro Log Analytics e un account di automazione nella sottoscrizione.

* Se non si ha familiarità con i log di monitoraggio di Azure e non è già stata distribuita un'area di lavoro, è consigliabile consultare le [linee guida di progettazione dell'area](../azure-monitor/platform/design-logs-deployment.md) Consente di ottenere informazioni sul controllo di accesso e di comprendere le strategie di implementazione della progettazione consigliate per l'organizzazione.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can have only 7 days."
            }
        },
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This applies only when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        },
        "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "sampleGraphicalRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.graphrunbook"
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorial.ps1"
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookContentUri": {
                "type": "String",
                "defaultValue": "https://eus2oaasibizamarketprod1.blob.core.windows.net/marketplace-runbooks/AzureAutomationTutorialPython2.py"
            }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]",
                    "name": "CapacityReservation",
                    "capacityReservationLevel": 100
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
        "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2015-01-01-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [
                 "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
            ],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
            "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('sampleGraphicalRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePowerShellRunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2015-01-01-preview",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
                        ],
                        "tags": {},
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[parameters('samplePython2RunbookContentUri')]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
        },
        {
            "apiVersion": "2015-11-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
       ]
      }
     ]
    }
    ```

2. Modificare il modello in base alle esigenze. Provare a creare un [file di parametri di gestione risorse](../azure-resource-manager/templates/parameter-files.md) anziché passare parametri come valori inline.

3. Salvare il file come deployAzAutomationAccttemplate. JSON in una cartella locale.

4. A questo punto è possibile distribuire il modello. È possibile usare PowerShell o l'interfaccia della riga di comando di Azure. Quando vengono richieste un'area di lavoro e un nome di account di automazione, fornire un nome univoco a livello globale in tutte le sottoscrizioni di Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployAzAutomationAccttemplate.json
    ```

    **Interfaccia della riga di comando di Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployAzAutomationAccttemplate.json
    ```

    Il completamento della distribuzione potrebbe richiedere alcuni minuti. In tal caso, verrà visualizzato un messaggio simile al seguente che include il risultato.

    ![Esempio di risultato al termine della distribuzione](media/automation-create-account-template/template-output.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un account di automazione, è possibile creare manuali operativi e automatizzare i processi manuali.

* Per iniziare a usare i runbook PowerShell, vedere [Creare un runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Per iniziare a usare manuali operativi del flusso di lavoro PowerShell, vedere [creare un Runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per iniziare a usare Python 2 manuali operativi, vedere [creare un Runbook Python](automation-first-runbook-textual-python2.md).