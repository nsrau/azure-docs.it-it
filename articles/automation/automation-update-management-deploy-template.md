---
title: Usare i modelli di Azure Resource Manager per eseguire l'onboarding di Gestione aggiornamenti. Documenti Microsoft
description: È possibile usare un modello di Azure Resource Manager per eseguire l'onboarding della soluzione di gestione degli aggiornamenti di Automazione di Azure.You can use an Azure Resource Manager template to onboard the Azure Automation Update Management solution.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/27/2020
ms.openlocfilehash: a8b382663b56d7481da876979e33194fb0ac533d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925800"
---
# <a name="onboard-update-management-solution-using-azure-resource-manager-template"></a>Soluzione di gestione degli aggiornamenti in connessione usando il modello di Azure Resource ManagerOnboard Update Management solution using Azure Resource Manager template

È possibile usare i modelli di [Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) per abilitare la soluzione di gestione degli aggiornamenti di Automazione di Azure nel gruppo di risorse. In questo articolo viene fornito un modello di esempio che automatizza gli elementi seguenti:This article provides a sample template that automates the following:

* Creazione di un'area di lavoro di Log Analytics di Monitoraggio di Azure.Creation of a Azure Monitor Log Analytics workspace.
* Creazione di un account di Automazione di Azure.Creation of an Azure Automation account.
* Collega l'account di automazione all'area di lavoro di Log Analytics, se non è già collegato.
* Eseguire l'onboarding della soluzione di gestione degli aggiornamenti di Automazione di AzureOnboard the Azure Automation Update Management solution

The template does not automate the onboarding of one or more Azure or non-Azure VMs.

Se si dispone già di un'area di lavoro di Log Analytics e di un account di automazione distribuiti in un'area supportata nella sottoscrizione, non sono collegati e l'area di lavoro non dispone già della soluzione di gestione degli aggiornamenti distribuita, l'utilizzo di questo modello crea correttamente collegamento e distribuisce la soluzione di gestione degli aggiornamenti. 

## <a name="api-versions"></a>Versioni dell'API

La tabella seguente elenca la versione dell'API per le risorse usate in questo esempio.

| Risorsa | Tipo di risorsa | Versione dell'API |
|:---|:---|:---|
| Area di lavoro | aree di lavoro | 2017-03-15-preview |
| Account di Automazione | automation | 2015-10-31 | 
| Soluzione | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Prima di utilizzare il modello

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede il modulo Azure PowerShell Az.If you choose to install and use PowerShell locally, this article requires the Azure PowerShell Az module. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure. Con Azure PowerShell la distribuzione usa [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.1.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)you need to install or upgrade, see Install Azure CLI. Con l'interfaccia della riga di comando di Azure, questa distribuzione usa la distribuzione di [gruppi di az create.](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) 

Il modello JSON è configurato per richiedere:

* Il nome dell'area di lavoro
* L'area in cui creare l'area di lavoro
* Il nome dell'account di automazione
* L'area in cui creare l'account

Il modello JSON specifica un valore predefinito per gli altri parametri che verrebbero probabilmente utilizzati come configurazione standard nell'ambiente. È possibile archiviare il modello in un account di archiviazione di Azure per l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere Distribuire risorse con i modelli di [Resource Manager e l'interfaccia della riga di comando](../azure-resource-manager/templates/deploy-cli.md)di Azure.For further information about working with templates, see Deploy resources with Resource Manager templates and Azure CLI .

I parametri seguenti nel modello vengono impostati con un valore predefinito per l'area di lavoro di Log Analytics:

* sku: il valore predefinito è il nuovo piano tariffario per GB rilasciato nel modello di prezzi di aprile 2018
* conservazione dei dati - il valore predefinito è trenta giorni

>[!WARNING]
>Se si crea o si configura un'area di lavoro Log Analytics in una sottoscrizione basata sul nuovo modello di prezzi di aprile 2018, l'unico piano tariffario di Log Analytics valido è **PerGB2018**.
>

>[!NOTE]
>Prima di usare questo modello, esaminare [i dettagli aggiuntivi](../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) per comprendere appieno le opzioni di configurazione dell'area di lavoro, ad esempio la modalità di controllo di accesso, il piano tariffario, la conservazione e il livello di prenotazione della capacità. Se non si ha familiarità con i log di Monitoraggio di Azure e non è già stata distribuita un'area di lavoro, è consigliabile esaminare le linee guida di [progettazione dell'area](../azure-monitor/platform/design-logs-deployment.md) di lavoro per informazioni sul controllo di accesso e una conoscenza delle strategie di implementazione della progettazione consigliate per l'organizzazione.

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
        "pricingTier": {
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
        "immediatePurgeDataOn30Days": {
            "type": "bool",
            "defaultValue": "[bool('false')]",
            "metadata": {
                "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "australiacentral",
                "australiaeast",
                "australiasoutheast",
                "brazilsouth",
                "canadacentral",
                "centralindia",
                "centralus",
                "eastasia",
                "eastus",
                "eastus2",
                "francecentral",
                "japaneast",
                "koreacentral",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus",
                "westus2"
            ],
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
            "apiVersion": "2017-03-15-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": { 
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
                    "apiVersion": "2015-11-01-preview",
                    "location": "[resourceGroup().location]",
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
            "apiVersion": "2015-01-01-preview",
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
    ```

2. Modificare il modello in base alle esigenze.

3. Salvare il file come deployUMSolutiontemplate.json in una cartella locale.

4. A questo punto è possibile distribuire il modello. È possibile usare PowerShell o l'interfaccia della riga di comando di Azure.You can use either PowerShell or the Azure CLI. Quando viene richiesto un'area di lavoro e il nome dell'account di automazione, specificare un nome univoco a livello globale in tutte le sottoscrizioni di Azure.When you're prompted for a workspace and Automation account name, provide a globally unique across all Azure subscriptions.

    **Powershell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Interfaccia della riga di comando di AzureAzure**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Per il completamento della distribuzione sarà necessario attendere alcuni minuti. Al termine, viene visualizzato un messaggio simile al seguente che include il risultato:

    ![Esempio di risultato al termine della distribuzione](media/automation-update-management-deploy-template/template-output.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito la soluzione di gestione degli aggiornamenti, è possibile abilitare le macchine virtuali per la gestione, esaminare le valutazioni degli aggiornamenti e distribuire gli aggiornamenti per verificarli.

- [Dall'account di Automazione di Azure](automation-onboard-solutions-from-automation-account.md) per uno o più computer di Azure e manualmente per computer non Azure.From your Azure Automation account for one or more Azure machines and manually for non-Azure machines.

- Per una singola macchina virtuale di Azure dalla pagina della macchina virtuale nel portale di Azure.For a single Azure VM from the virtual machine page in the Azure portal. Questo scenario è disponibile per le macchine virtuali Linux e [Windows.This](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) scenario is available for [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) and Windows VMs.

- Per più macchine virtuali di Azure selezionandole dalla pagina Macchine virtuali nel portale di Azure.For [multiple Azure VMs](manage-update-multi.md) by selecting them from the **Virtual machines** page in the Azure portal. 