---
title: Abilitare Gestione aggiornamenti con il modello di Azure Resource Manager | Microsoft Docs
description: Questo articolo descrive come usare un modello di Azure Resource Manager per abilitare Gestione aggiornamenti.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/18/2020
ms.openlocfilehash: 312457a6220920d550f05e3a1cd1dc5ec2f4449a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327851"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Abilitare Gestione aggiornamenti con il modello di Azure Resource Manager

Per abilitare la funzionalità Gestione aggiornamenti di Automazione di Azure nel gruppo di risorse, è possibile usare un [modello di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Questo articolo fornisce un modello di esempio che automatizza le operazioni seguenti:

* Automatizza la creazione di un'area di lavoro Log Analytics di Monitoraggio di Azure.
* Automatizza la creazione di un account di Automazione di Azure.
* Collega l'account di Automazione all'area di lavoro Log Analytics.
* Aggiunge runbook di automazione di esempio all'account.
* Abilita la funzionalità Gestione aggiornamenti.

Il modello non automatizza l'abilitazione di Gestione aggiornamenti in una o più macchine virtuali di Azure o non Azure.

Se si dispone già di un'area di lavoro Log Analytics e di un account di Automazione distribuiti in un'area supportata della sottoscrizione, questi non sono collegati. Con questo modello viene creato il collegamento e viene distribuito Gestione aggiornamenti.

>[!NOTE]
>La creazione dell'account RunAs di Automazione non è supportata quando si usa un modello di Resource Manager. Per creare manualmente un account RunAs dal portale o con PowerShell, vedere [Gestire gli account RunAs](../manage-runas-account.md).

Dopo aver completato questi passaggi, è necessario [configurare le impostazioni di diagnostica](../automation-manage-send-joblogs-log-analytics.md) per l'account di Automazione per inviare lo stato del processo del runbook e i flussi di processo all'area di lavoro Log Analytics collegata.

## <a name="api-versions"></a>Versioni dell'API

La tabella seguente elenca la versione dell'API per le risorse usate in questo esempio.

| Risorsa | Tipo di risorsa | Versione dell'API |
|:---|:---|:---|
| [Area di lavoro](/azure/templates/microsoft.operationalinsights/workspaces) | aree di lavoro | 2020-03-01-preview |
| [Account di Automazione](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Servizi collegati dell'area di lavoro](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | aree di lavoro | 2020-03-01-preview |
| [Soluzioni](/azure/templates/microsoft.operationsmanagement/solutions) | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Prima di usare il modello

Il modello JSON è configurato in modo da richiedere:

* Nome dell'area di lavoro.
* Area in cui creare l'area di lavoro.
* Nome dell'account di Automazione.
* Area in cui creare l'account di automazione.

I parametri seguenti nel modello vengono impostati con un valore predefinito per l'area di lavoro Log Analytics:

* Il valore predefinito per *sku* è il nuovo piano tariffario per GB rilasciato nel modello di prezzi di aprile 2018.
* Il valore predefinito per *dataRetention* è 30 giorni.

>[!WARNING]
>Se si vuole creare o configurare un'area di lavoro Log Analytics in una sottoscrizione basata sul modello di prezzi di aprile 2018, l'unico piano tariffario di Log Analytics valido è *PerGB2018*.
>

Il modello JSON specifica un valore predefinito per gli altri parametri che potrebbero essere usati come configurazione standard nell'ambiente in uso. È possibile archiviare il modello in un account di archiviazione di Azure per consentire l'accesso condiviso nell'organizzazione. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md).

Se non si ha familiarità con Automazione di Azure e Monitoraggio di Azure, è importante comprendere i dettagli di configurazione seguenti. Consentono di evitare errori quando si tenta di creare, configurare e usare un'area di lavoro Log Analytics collegata al nuovo account di Automazione.

* Vedere [altri dettagli](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace) per comprendere appieno le opzioni di configurazione dell'area di lavoro, ad esempio la modalità di controllo di accesso, il piano tariffario, la conservazione e il livello di prenotazione della capacità.

* Vedere i [mapping dell'area di lavoro](../how-to/region-mappings.md) per specificare le aree supportate inline o in un file di parametri. Sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione nella sottoscrizione.

* Se non si ha familiarità con i log di Monitoraggio di Azure e non è già stata distribuita un'area di lavoro, è consigliabile vedere le [linee guida per la progettazione delle aree di lavoro](../../azure-monitor/platform/design-logs-deployment.md). Queste informazioni saranno utili per comprendere i meccanismi di controllo di accesso e le strategie di implementazione della progettazione consigliate per l'organizzazione.

## <a name="deploy-template"></a>Distribuire il modello

1. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
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
                    "description": "Specifies the location in which to create the Automation account."
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
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
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
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
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
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Modificare il modello in base alle esigenze. Valutare la possibilità di creare un [file di parametri di Resource Manager](../../azure-resource-manager/templates/parameter-files.md) anziché passare i parametri come valori inline.

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

    ![Esempio di risultato al termine della distribuzione](media/update-mgmt-enable-template/template-output.png)

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nella portale di Azure aprire l'account di automazione creato.

3. Nel riquadro sinistro selezionare **Runbook**. Nella pagina **Runbook** sono elencati tre runbook di esercitazione creati con l'account di automazione.

    ![Runbook di esercitazione creati con l'account di automazione](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Nel riquadro sinistro selezionare **Area di lavoro collegata**. Nella pagina **Area di lavoro collegata** viene visualizzata l'area di lavoro Log Analytics specificata in precedenza collegata all'account di automazione.

    ![Account di automazione collegato all'area di lavoro Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. Dal riquadro sinistro selezionare **Gestione aggiornamenti**. Nella pagina **Gestione aggiornamenti** viene visualizzata la pagina valutazione senza informazioni come risultato della semplice abilitazione e le macchine virtuali non sono configurate per la gestione.

    ![Visualizzazione della valutazione della funzionalità Gestione aggiornamenti](./media/update-mgmt-enable-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare la soluzione **aggiornamenti** nell'area di lavoro log Analytics, scollegare l'account di automazione dall'area di lavoro e quindi eliminare l'account di automazione e l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le macchine virtuali, vedere [gestire gli aggiornamenti e le patch per le macchine virtuali](update-mgmt-manage-updates-for-vm.md).

* Se non si vuole più usare Gestione aggiornamenti e si desidera rimuoverlo, vedere le istruzioni riportate in [rimuovere Gestione aggiornamenti funzionalità](update-mgmt-remove-feature.md).

* Per eliminare macchine virtuali da Gestione aggiornamenti, vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](update-mgmt-remove-vms.md).
