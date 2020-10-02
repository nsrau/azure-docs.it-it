---
title: Creare avvisi di Azure Advisor per nuove raccomandazioni usando un modello di Resource Manager
description: Informazioni su come configurare un avviso per nuove raccomandazioni di Azure Advisor usando un modello di Azure Resource Manager.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: a10ca5f0b4119fb65d6b0f717f5c212acb20f9cd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973682"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Avvio rapido: Creare avvisi di Azure Advisor per nuove raccomandazioni usando un modello di Azure Resource Manager

Questo articolo illustra come configurare un avviso per nuove raccomandazioni di Azure Advisor usando un modello di Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ogni volta che Azure Advisor rileva una nuova raccomandazione per una delle risorse in uso, viene archiviato un evento nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md). È possibile configurare avvisi per questi eventi di Azure Advisor usando un'esperienza di creazione di avvisi specifici delle raccomandazioni. È possibile selezionare una sottoscrizione e, facoltativamente, un gruppo di risorse per specificare le risorse per le quali si vogliono ricevere gli avvisi.

Si possono anche determinare i tipi di raccomandazioni usando queste proprietà:

- Categoria
- Livello di impatto
- Tipo di raccomandazione

È anche possibile configurare l'azione che verrà eseguita quando un avviso viene attivato in uno dei due modi seguenti:  

- Selezionando un gruppo di azioni esistente
- Creando un nuovo gruppo di azioni

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Gli avvisi di Azure Advisor sono attualmente disponibili solo per le raccomandazioni di tipo Disponibilità elevata, Prestazioni e Costo. Le raccomandazioni sulla sicurezza non sono supportate.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Per eseguire i comandi dal computer locale, installare l'interfaccia della riga di comando di Azure o i moduli di Azure PowerShell. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Installare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Rivedere il modello

Il modello seguente crea un gruppo di azioni con una destinazione del messaggio di posta elettronica e abilita tutte le notifiche sull'integrità dei servizi per la sottoscrizione di destinazione. Salvare questo modello con il nome *CreateAdvisorAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2019-06-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

Il modello definisce due risorse:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello usando un metodo standard per la [distribuzione di un modello di Resource Manager](../azure-resource-manager/templates/deploy-portal.md), come gli esempi seguenti in cui si usano l'interfaccia della riga di comando e PowerShell. Sostituire i valori di esempio di **Resource Group** e **emailAddress** con quelli appropriati per l'ambiente corrente. Il nome dell'area di lavoro deve essere univoco tra tutte le sottoscrizioni di Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Per verificare che l'area di lavoro sia stata creata, usare uno dei comandi seguenti. Sostituire il valore di esempio di **Resource Group** con quello usato in precedenza.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create. Quando non è più necessario, eliminare il gruppo di risorse per eliminare la regola di avviso e le risorse correlate. Per eliminare il gruppo di risorse con l'interfaccia della riga di comando di Azure oppure con Azure PowerShell

# <a name="cli"></a>[Interfaccia della riga di comando](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Passaggi successivi

- Leggere una [panoramica degli avvisi del log attività](../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).
