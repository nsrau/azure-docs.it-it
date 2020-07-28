---
title: Ricevere gli avvisi del log attività nelle notifiche del servizio di Azure usando il modello di Gestione risorse
description: Ricevere le notifiche tramite SMS, posta elettronica o webhook nel servizio di Azure.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 84c888195ab7e2f3288691948706d31160393d25
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918916"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Guida introduttiva: creare avvisi del log attività per le notifiche del servizio usando un modello ARM

Questo articolo illustra come configurare gli avvisi del log attività per le notifiche sull'integrità del servizio usando un modello di Azure Resource Manager (modello ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Le notifiche sull'integrità del servizio vengono archiviate nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md). Dato il notevole volume di informazioni archiviate nel log attività, è disponibile un'interfaccia utente separata che semplifica la visualizzazione e la configurazione degli avvisi per le notifiche sull'integrità del servizio.

È possibile ricevere un avviso quando Azure invia le notifiche sull'integrità del servizio alla sottoscrizione di Azure. È possibile configurare l'avviso in base a:

- La classe di notifica dell'integrità del servizio (problemi del servizio, manutenzione pianificata, avvisi di integrità).
- La sottoscrizione interessata.
- I servizi interessati.
- Le aree interessate.

> [!NOTE]
> Le notifiche sull'integrità del servizio non inviano un avviso relativo a eventi di integrità delle risorse.

È anche possibile configurare l'utente a cui deve essere inviato l'avviso:

- Selezionare un gruppo di azione esistente.
- Creare un nuovo gruppo di azione che può essere usato per avvisi futuri.

Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Per eseguire i comandi dal computer locale, installare l'interfaccia della riga di comando di Azure o i moduli Azure PowerShell. Per altre informazioni, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli) riga di comando di Azure e [installare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Rivedere il modello

Il modello seguente crea un gruppo di azioni con una destinazione di posta elettronica e Abilita tutte le notifiche sull'integrità del servizio per la sottoscrizione di destinazione. Salvare questo modello come *CreateServiceHealthAlert.js*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "SubHealth",
      "type": "String"
    },
    "activityLogAlerts_name": {
      "defaultValue": "ServiceHealthActivityLogAlert",
      "type": "String"
    },
    "emailAddress":{
      "type":"string"
    }
  },
  "variables": {
    "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
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
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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

Distribuire il modello usando un metodo standard per la [distribuzione di un modello ARM](../azure-resource-manager/templates/deploy-portal.md) , ad esempio gli esempi seguenti usando l'interfaccia della riga di comando e PowerShell. Sostituire i valori di esempio per il **gruppo di risorse** e **EmailAddress** con i valori appropriati per l'ambiente in uso.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Verificare che l'area di lavoro sia stata creata utilizzando uno dei comandi seguenti. Sostituire i valori di esempio per il **gruppo di risorse** con il valore usato in precedenza.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- Informazioni sulle [procedure consigliate per la configurazione degli avvisi di integrità dei servizi di Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Informazioni su come [configurare le notifiche push per dispositivi mobili per l'integrità dei servizi di Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Informazioni su come [configurare le notifiche webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Informazioni sulle [notifiche per l'integrità del servizio](service-notifications.md).
- Informazioni sulla [limitazione della frequenza delle notifiche](../azure-monitor/platform/alerts-rate-limiting.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leggere una [panoramica degli avvisi del log attività](../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).
