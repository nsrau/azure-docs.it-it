---
title: Abilitare Gestione aggiornamenti con il modello di Azure Resource Manager | Microsoft Docs
description: Questo articolo descrive come usare un modello di Azure Resource Manager per abilitare Gestione aggiornamenti.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: ad9029b44ffb0c98bad58bbf012eb19d084d5446
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185756"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Abilitare Gestione aggiornamenti con il modello di Azure Resource Manager

Per abilitare la funzionalità Gestione aggiornamenti di Automazione di Azure nel gruppo di risorse, è possibile usare un [modello di Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Questo articolo fornisce un modello di esempio che automatizza le operazioni seguenti:

* Creazione di un'area di lavoro Log Analytics di Monitoraggio di Azure.
* Creazione di un account di Automazione di Azure.
* Collegamento dell'account di Automazione all'area di lavoro Log Analytics, se non è già collegato.
* Abilitazione di Gestione aggiornamenti.

Il modello non automatizza l'abilitazione di Gestione aggiornamenti in una o più macchine virtuali di Azure o non Azure.

Se si dispone già di un'area di lavoro Log Analytics e di un account di Automazione distribuiti in un'area supportata della sottoscrizione, questi non sono collegati. Con questo modello viene creato il collegamento e viene distribuito Gestione aggiornamenti.

## <a name="api-versions"></a>Versioni dell'API

La tabella seguente elenca le versioni dell'API per le risorse usate in questo modello.

| Risorsa | Tipo di risorsa | Versione dell'API |
|:---|:---|:---|
| Area di lavoro | aree di lavoro | 2020-03-01-anteprima |
| Account di Automazione | automation | 2018-06-30 | 
| Soluzione | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Prima di usare il modello

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Az di Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) per creare una connessione con Azure. Con Azure PowerShell, la distribuzione usa [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.1.0 o versioni successive dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Con l'interfaccia della riga di comando di Azure, questa distribuzione usa [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). 

Il modello JSON è configurato in modo da richiedere:

* Nome dell'area di lavoro.
* Area in cui creare l'area di lavoro.
* Per abilitare le autorizzazioni per risorse o aree di lavoro.
* Nome dell'account di Automazione.
* Area in cui creare l'account.

Il modello JSON specifica un valore predefinito per gli altri parametri che potrebbero essere usati come configurazione standard nell'ambiente in uso. È possibile archiviare il modello in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).

I parametri seguenti nel modello vengono impostati con un valore predefinito per l'area di lavoro Log Analytics:

* sku: il valore predefinito è il nuovo piano tariffario per GB rilasciato nel modello di prezzi di aprile 2018
* conservazione dei dati: il valore predefinito è trenta giorni

>[!WARNING]
>Se si crea o si configura un'area di lavoro Log Analytics in una sottoscrizione basata sul nuovo modello di prezzi di aprile 2018, l'unico piano tariffario di Log Analytics valido è **PerGB2018**.
>

Il modello JSON specifica un valore predefinito per gli altri parametri che potrebbero essere usati come configurazione standard nell'ambiente in uso. È possibile archiviare il modello in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md).

Se non si ha familiarità con Automazione di Azure e Monitoraggio di Azure, è importante comprendere i dettagli di configurazione seguenti, in modo da evitare errori durante il tentativo di creare, configurare e usare un'area di lavoro Log Analytics collegata al nuovo account di Automazione.

* Vedere [Altri dettagli](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) per comprendere appieno le opzioni di configurazione dell'area di lavoro, ad esempio la modalità di controllo di accesso, il piano tariffario, la conservazione e il livello di prenotazione della capacità.

* Poiché sono supportate solo determinate aree per collegare un'area di lavoro Log Analytics e un account di Automazione nella sottoscrizione, vedere [Mapping dell'area di lavoro](how-to/region-mappings.md) per specificare le aree supportate inline o in un file di parametri.

* Se non si ha familiarità con i log di Monitoraggio di Azure e non è già stata distribuita un'area di lavoro, è consigliabile vedere le linee guida per la [progettazione delle aree di lavoro](../azure-monitor/platform/design-logs-deployment.md) per comprendere i meccanismi di controllo di accesso e le strategie di implementazione della progettazione consigliate per l'organizzazione.

## <a name="deploy-template"></a>Distribuire il modello

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
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
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
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]",
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('location')]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            },
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
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
    ```

2. Modificare il modello in base alle esigenze. Valutare la possibilità di creare un [file di parametri di Resource Manager](../azure-resource-manager/templates/parameter-files.md) anziché passare i parametri come valori inline.

3. Salvare il file in una cartella locale come **deployUMSolutiontemplate.json**.

4. A questo punto è possibile distribuire il modello. È possibile usare PowerShell o l'interfaccia della riga di comando di Azure. Quando vengono richiesti un'area di lavoro e un nome di account di Automazione, fornire un nome univoco a livello globale in tutte le sottoscrizioni di Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Interfaccia della riga di comando di Azure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

    ![Esempio di risultato al termine della distribuzione](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le VM, vedere [Gestire aggiornamenti e patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).

* Se l'area di lavoro Log Analytics non è più necessaria, vedere le istruzioni riportate in [Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti](automation-unlink-workspace-update-management.md).

* Per eliminare macchine virtuali da Gestione aggiornamenti, vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](automation-remove-vms-from-update-management.md).
