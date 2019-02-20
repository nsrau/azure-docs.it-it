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
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962231"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gestire le regole di rilevamento intelligente di Application Insights usando modelli di Azure Resource Manager

Le regole di rilevamento intelligente in Application Insights possono essere gestite e configurate mediante [modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Questo metodo può essere usato durante la distribuzione di nuove risorse di Application Insights con automazione di Azure Resource Manager o per modificare le impostazioni delle risorse esistenti.

## <a name="smart-detection-rule-configuration"></a>Configurazione delle regole di rilevamento intelligente

Per una regola di rilevamento intelligente è possibile configurare le impostazioni seguenti:
- Se la regola è abilitata (il valore predefinito è **true**.)
- Se devono essere inviati messaggi di posta elettronica ai proprietari della sottoscrizione, collaboratori e lettori quando si ha un rilevamento (il valore predefinito è **true**.)
- Eventuali destinatari di posta elettronica aggiuntivi che devono ricevere una notifica quando si ha un rilevamento.
- * La configurazione della posta elettronica non è disponibile per le regole di Rilevamento intelligente contrassegnate come _Anteprima_.

Per consentire la configurazione delle impostazioni delle regole tramite Azure Resource Manager, la configurazione delle regole di rilevamento intelligente è ora disponibile come risorsa interna alla risorsa di Application Insights denominata **ProactiveDetectionConfigs**.
Per la massima flessibilità, ogni regola di rilevamento intelligente può essere configurata con impostazioni di notifica univoche.

## <a name="examples"></a>Esempi

Di seguito sono indicati alcuni esempi che illustrano come configurare le impostazioni delle regole di rilevamento intelligente tramite modelli di Azure Resource Manager.
Tutti gli esempi fanno riferimento a una risorsa di Application Insights denominata _"myApplication"_ e alla "regola di rilevamento intelligente dipendenze con durata lunga", che è denominata internamente _"longdependencyduration"_.
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

## <a name="smart-detection-rule-names"></a>Nomi delle regole di rilevamento intelligente

Di seguito è riportata una tabella dei nomi delle regole di rilevamento intelligente come appaiono nel portale, insieme ai rispettivi nomi interni che devono essere usati nel modello di Azure Resource Manager.

> [!NOTE]
> Le regole di rilevamento intelligente contrassegnate come anteprima non supportano le notifiche via posta elettronica. Pertanto è possibile impostare solo la proprietà abilitata per queste regole. 

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
| Problema di utilizzo delle risorse rilevato (anteprima) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>Chi riceve le notifiche di avviso (classiche)?

Questa sezione si applica esclusivamente agli avvisi classici di rilevamento intelligente e facilita l'ottimizzazione delle notifiche di avviso per garantire che solo i destinatari desiderati ricevano le notifiche. Per comprendere meglio la differenza tra [avvisi classici](../platform/alerts-classic.overview.md) e la nuova esperienza di avvisi, fare riferimento all'[articolo di panoramica sugli avvisi](../platform/alerts-overview.md). Attualmente gli avvisi di rilevamento intelligente supportano solo l'esperienza di avvisi classici. L'unica eccezione sono gli [avvisi di rilevamento intelligente nei servizi cloud di Azure](./proactive-cloud-services.md). Per controllare la notifica di avviso degli avvisi di rilevamento intelligente nei servizi cloud di Azure, usare [gruppi di azioni](../platform/action-groups.md).

* È consigliabile l'uso di destinatari specifici per le notifiche di avviso classiche o di rilevamento intelligente.

* Per gli avvisi di rilevamento intelligente, l'opzione **in blocco/gruppo** della casella di controllo, se abilitata, invia notifiche agli utenti con i ruoli proprietario, collaboratore o lettore nella sottoscrizione. _Tutti_ gli utenti con accesso alla sottoscrizione della risorsa di Application Insights si trovano nell'ambito e riceveranno le notifiche. 

> [!NOTE]
> Se attualmente si usa l'opzione **in blocco/gruppo** della casella di controllo e la si disabilita, sarà impossibile annullare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul rilevamento automatico:

- [Anomalie degli errori](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Perdite di memoria](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalie delle prestazioni](../../azure-monitor/app/proactive-performance-diagnostics.md)