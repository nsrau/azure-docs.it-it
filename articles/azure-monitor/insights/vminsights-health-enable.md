---
title: Abilita Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
description: Viene descritto come abilitare l'integrità Guest di Monitoraggio di Azure per le macchine virtuali nella sottoscrizione e come eseguire l'onboarding delle macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.openlocfilehash: 6033ca5b0eaf6845d14407832c776dd8e006226b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686977"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>Abilita Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
Monitoraggio di Azure per le macchine virtuali integrità Guest consente di visualizzare l'integrità di una macchina virtuale in base a quanto definito da un set di misurazioni delle prestazioni campionate a intervalli regolari. Questo articolo descrive come abilitare questa funzionalità nella sottoscrizione e come abilitare il monitoraggio Guest per ogni macchina virtuale.

## <a name="current-limitations"></a>Limitazioni correnti
Monitoraggio di Azure per le macchine virtuali integrità Guest presenta le limitazioni seguenti nell'anteprima pubblica:

- Attualmente sono supportate solo macchine virtuali di Azure. Azure Arc per i server non è attualmente supportato.
- La macchina virtuale deve eseguire uno dei sistemi operativi seguenti: 
  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 o versioni successive
- La macchina virtuale deve trovarsi in una delle aree seguenti:
  - Australia sud-orientale
  - Stati Uniti centrali
  - Stati Uniti orientali
  - Stati Uniti orientali 2
  - Stati Uniti orientali 2 EUAP
  - Europa settentrionale
  - Asia sud-orientale
  - Regno Unito meridionale
  - Europa occidentale
  - Stati Uniti occidentali
  - Stati Uniti occidentali 2
- Log Analytics area di lavoro deve trovarsi in una delle aree seguenti:
  - Stati Uniti orientali
  - Stati Uniti orientali 2 EUAP
  - Area Europa occidentale

## <a name="prerequisites"></a>Prerequisiti

- La macchina virtuale deve essere caricata in Monitoraggio di Azure per le macchine virtuali.
- L'utente che esegue i passaggi di caricamento deve avere un accesso minimo a livello di collaboratore alla sottoscrizione in cui si trovano la regola di raccolta dati e macchina virtuale.
- I provider di risorse di Azure necessari devono essere registrati come descritto nella sezione seguente.


## <a name="register-required-azure-resource-providers"></a>Registrare i provider di risorse di Azure necessari
I provider di risorse di Azure seguenti devono essere registrati per la sottoscrizione per abilitare l'integrità Guest di Monitoraggio di Azure per le macchine virtuali. 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

È possibile usare uno dei metodi disponibili per registrare un provider di risorse come descritto in [tipi e provider di risorse di Azure](../../azure-resource-manager/management/resource-providers-and-types.md). È anche possibile usare il comando di esempio seguente usando armclient, postazione o un altro metodo per effettuare una chiamata autenticata a Azure Resource Manager:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Abilitare una macchina virtuale usando il portale di Azure
Quando si Abilita l'integrità Guest per una macchina virtuale nell'portale di Azure, viene eseguita tutte le operazioni di configurazione necessarie. Ciò include la creazione della regola di raccolta dati richiesta, l'installazione dell'estensione per l'integrità Guest nella macchina virtuale e la creazione di un'associazione con la regola di raccolta dati.

Dalla visualizzazione **iniziale** in monitoraggio di Azure per le macchine virtuali fare clic sul collegamento accanto al messaggio di aggiornamento per una macchina virtuale, quindi fare clic sul pulsante **Aggiorna** . È anche possibile selezionare più macchine virtuali per aggiornarle insieme.

![Abilitare la funzionalità integrità nella macchina virtuale](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Abilitare una macchina virtuale usando il modello di Gestione risorse
Sono necessari tre passaggi per abilitare le macchine virtuali con Azure Resource Manager.

- Creare una regola di raccolta dati.
- Installare l'estensione di integrità Guest in ogni macchina virtuale
- Creare un'associazione tra la macchina virtuale e la regola di raccolta dati.

### <a name="create-data-collection-rule-dcr"></a>Creare una regola di raccolta dati (DCR)

> [!NOTE]
> Se si abilita una macchina virtuale usando il portale di Azure, viene creata automaticamente la regola di raccolta dati descritta di seguito. In questo caso, non è necessario eseguire questo passaggio.

La configurazione per i monitoraggi in Monitoraggio di Azure per le macchine virtuali integrità Guest è archiviata in [Data Collection Rules (DCR)](../platform/data-collection-rule-overview.md). Installare la regola di raccolta dati definita nel modello di Gestione risorse riportato di seguito per abilitare tutti i monitoraggi per le macchine virtuali con l'estensione per l'integrità Guest. Ogni macchina virtuale con l'estensione per l'integrità Guest dovrà avere un'associazione con questa regola.

> [!NOTE]
> È possibile creare regole di raccolta dati aggiuntive per modificare la configurazione predefinita dei monitoraggi, come descritto in [configurare il monitoraggio in monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)](vminsights-health-configure.md).

Il modello richiede i valori per i parametri seguenti:

- **defaultHealthDataCollectionRuleName**: mantiene il nome predefinito definito nel modello.
- **destinationWorkspaceResourceId**: ID risorsa dell'area di lavoro log Analytics usata per la raccolta dei dati della macchina virtuale.
- **dataCollectionRuleLocation**: area della regola di raccolta dati. Deve corrispondere all'area dell'area di lavoro Log Analytics.


Distribuire il modello usando un [metodo di distribuzione per i modelli di gestione risorse](../../azure-resource-manager/templates/deploy-powershell.md). I comandi seguenti distribuiscono il modello e il file dei parametri usando PowerShell o l'interfaccia della riga di comando di Azure

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---



```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data colleciton rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              }
            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>File di parametri di esempio

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



## <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Installare l'estensione di integrità Guest e associarla a una regola di raccolta dati
Usare il modello di Gestione risorse seguente per abilitare una macchina virtuale per l'integrità Guest. In questo modo viene installata l'estensione di integrità Guest e viene creata l'associazione con la regola di raccolta dati. È possibile distribuire questo modello usando un [metodo di distribuzione per i modelli di gestione risorse](../../azure-resource-manager/templates/deploy-powershell.md).


Ad esempio, usare i comandi seguenti per distribuire il modello e il file dei parametri in un gruppo di risorse tramite PowerShell o l'interfaccia della riga di comando di Azure.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>File modello

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>File di parametri di esempio

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      },
      "healthExtensionVersion": {
        "value": "private-preview"
      }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

- [Personalizzare i monitoraggi abilitati da Monitoraggio di Azure per le macchine virtuali](vminsights-health-configure.md)