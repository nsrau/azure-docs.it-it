---
title: Configurare le impostazioni delle regole di rilevamento intelligente di Azure Application Insights con modelli di Azure Resource Manager | Microsoft Docs
description: Automatizzare la gestione e configurazione delle regole di rilevamento intelligente di Azure Application Insights con modelli di Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477870"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gestire le regole di rilevamento intelligente di Application Insights usando modelli di Azure Resource Manager

Le regole di rilevamento intelligente in Application Insights possono essere gestite e configurate mediante [modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Questo metodo può essere usato durante la distribuzione di nuove risorse di Application Insights con automazione di Azure Resource Manager o per modificare le impostazioni delle risorse esistenti.

## <a name="smart-detection-rule-configuration"></a>Configurazione delle regole di rilevamento intelligente

Per una regola di rilevamento intelligente è possibile configurare le impostazioni seguenti:
- Se la regola è abilitata (il valore predefinito è **true**.)
- Se è necessario inviare messaggi di posta elettronica per gli utenti associati alla sottoscrizione [lettore di monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [collaboratore al monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ruoli quando viene trovato un rilevamento (il valore predefinito è **true**.)
- Eventuali destinatari di posta elettronica aggiuntivi che devono ricevere una notifica quando si ha un rilevamento.
    -  Configurazione della posta elettronica non è disponibile per le regole di rilevamento intelligente contrassegnate come _preview_.

Per consentire la configurazione delle impostazioni delle regole tramite Azure Resource Manager, la configurazione delle regole di rilevamento intelligente è ora disponibile come risorsa interna alla risorsa di Application Insights denominata **ProactiveDetectionConfigs**.
Per la massima flessibilità, ogni regola di rilevamento intelligente può essere configurata con impostazioni di notifica univoche.

## 

## <a name="examples"></a>Esempi

Di seguito sono indicati alcuni esempi che illustrano come configurare le impostazioni delle regole di rilevamento intelligente tramite modelli di Azure Resource Manager.
Tutti gli esempi fanno riferimento a una risorsa di Application Insights denominata _"myApplication"_ e alla "regola di rilevamento intelligente dipendenze con durata lunga", che è denominata internamente _"longdependencyduration"_ .
Assicurarsi di sostituire il nome della risorsa di Application Insights e di specificare il nome interno della regola di rilevamento intelligente interessata. Consultare la tabella seguente per l'elenco dei nomi interni di Azure Resource Manager corrispondenti per ogni regola di rilevamento intelligente.

### <a name="disable-a-smart-detection-rule"></a>Disabilitare una regola di rilevamento intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Disabilitare l'invio di notifiche tramite posta elettronica per una regola di rilevamento intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Aggiungere altri destinatari di posta elettronica per una regola di rilevamento intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Regola avviso di errore anomalie v2 (non classico)

Questo modello di Azure Resource Manager illustra la configurazione di una regola di avviso v2 anomalie degli errori con gravità pari a 2. Questa nuova versione della regola di avviso anomalie degli errori fa parte di Azure nuova piattaforma di avviso e sostituisce la versione classica che è in fase di ritiro come parte del [processo di ritiro degli avvisi classici](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Questo modello di Azure Resource Manager è univoco per la regola di avviso v2 anomalie degli errori ed è diversa dalle altre regole di rilevamento intelligente classiche descritta in questo articolo.   

## <a name="smart-detection-rule-names"></a>Nomi delle regole di rilevamento intelligente

Di seguito è riportata una tabella dei nomi delle regole di rilevamento intelligente come appaiono nel portale, insieme ai rispettivi nomi interni che devono essere usati nel modello di Azure Resource Manager.

> [!NOTE]
> Regole di rilevamento intelligente è contrassegnato come _preview_ non supporta le notifiche di posta elettronica. Pertanto, è possibile impostare solo le _abilitato_ proprietà per queste regole. 

| Nome della regola nel portale di Azure | Nome interno
|:---|:---|
| Rallentamento del tempo di caricamento delle pagine | slowpageloadtime |
| Rallentamento del tempo di risposta del server | slowserverresponsetime |
| Dipendenze con durata lunga | longdependencyduration |
| Riduzione delle prestazioni nel tempo di risposta del server | degradationinserverresponsetime |
| Riduzione delle prestazioni nella durata delle dipendenze | degradationindependencyduration |
| Degradazione del rapporto tra i livelli di gravità delle tracce (anteprima) | extension_traceseveritydetector |
| Aumento anomalo nel volume delle eccezioni (anteprima) | extension_exceptionchangeextension |
| Potenziale perdita di memoria rilevata (anteprima) | extension_memoryleakextension |
| Potenziale problema di sicurezza rilevato (anteprima) | extension_securityextensionspackage |
| Aumento anomalo nel volume di dati giornaliero (anteprima) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Fasi successive

Per altre informazioni sul rilevamento automatico:

- [Anomalie degli errori](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Perdite di memoria](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie delle prestazioni](../../azure-monitor/app/proactive-performance-diagnostics.md)
