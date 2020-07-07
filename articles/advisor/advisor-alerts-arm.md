---
title: Creare avvisi di Azure Advisor per nuove raccomandazioni usando Gestione risorse modello
description: Crea avvisi Azure Advisor per la nuova raccomandazione
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: ef15891cc01d0481c6253023de262f14dce0ec81
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921077"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Guida introduttiva: creare Azure Advisor avvisi sulle nuove raccomandazioni usando un modello ARM

Questo articolo illustra come configurare un avviso per nuove raccomandazioni da Azure Advisor usando un modello di Azure Resource Manager (modello ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ogni volta che Azure Advisor rileva una nuova raccomandazione per una delle risorse, un evento viene archiviato nel [log attività di Azure](/azure/azure-monitor/platform/activity-logs-overview). È possibile configurare avvisi per questi eventi da Azure Advisor usando un'esperienza di creazione di avvisi specifica della raccomandazione. È possibile selezionare una sottoscrizione e, facoltativamente, un gruppo di risorse per specificare le risorse su cui si desidera ricevere gli avvisi.

È anche possibile determinare i tipi di raccomandazioni usando queste proprietà:

- Category
- Livello di effetto
- Tipo di raccomandazione

È anche possibile configurare l'azione che verrà eseguita quando un avviso viene attivato da:  

- Selezione di un gruppo di azioni esistente
- Creazione di un nuovo gruppo di azioni

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> Gli avvisi di Advisor sono attualmente disponibili solo per i consigli relativi a disponibilità elevata, prestazioni e costi. Le raccomandazioni sulla sicurezza non sono supportate.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Per eseguire i comandi dal computer locale, installare l'interfaccia della riga di comando di Azure o i moduli Azure PowerShell. Per altre informazioni, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli) riga di comando di Azure e [installare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Rivedere il modello

Il modello seguente crea un gruppo di azioni con una destinazione di posta elettronica e Abilita tutte le notifiche sull'integrità del servizio per la sottoscrizione di destinazione. Salvare questo modello come *CreateAdvisorAlert.js*.

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

- [Microsoft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello usando un metodo standard per la [distribuzione di un modello ARM](../azure-resource-manager/templates/deploy-portal.md) , ad esempio gli esempi seguenti usando l'interfaccia della riga di comando e PowerShell. Sostituire i valori di esempio per il **gruppo di risorse**e **EmailAddress** con i valori appropriati per l'ambiente in uso. Il nome dell'area di lavoro deve essere univoco tra tutte le sottoscrizioni di Azure.

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

Verificare che l'area di lavoro sia stata creata utilizzando uno dei comandi seguenti. Sostituire i valori di esempio per il **gruppo di risorse** con il valore usato in precedenza.

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

Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create. Quando non è più necessario, eliminare il gruppo di risorse, che elimina la regola di avviso e le risorse correlate. Per eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure o Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

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
