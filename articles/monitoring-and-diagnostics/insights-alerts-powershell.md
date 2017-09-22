---
title: Creare avvisi per i servizi di Azure - PowerShell | Microsoft Docs
description: Attivare messaggi di posta elettronica o notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni specificate.
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: d3fca8675c1f15b8fd0f952cfbf520f5c68478b3
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Creare avvisi sulle metriche in Monitoraggio di Azure per i servizi di Azure: PowerShell
> [!div class="op_single_selector"]
> * [Portale](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi sulle metriche tramite PowerShell.  

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.    
* **Eventi del log attività**: è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato evento. Per altre informazioni sugli avvisi sui log attività [fare clic qui](monitoring-activity-log-alerts.md)

È possibile configurare un avviso sulle metriche affinché esegua queste operazioni al momento dell'attivazione:

* inviare un messaggio di posta elettronica all'amministratore e ai coamministratori del servizio
* inviare un messaggio di posta elettronica ad altri indirizzi specificati
* chiamare un webhook
* avviare l'esecuzione di un runbook di Azure (solo dal portale di Azure)

È possibile configurare e ottenere informazioni sulle regole degli avvisi tramite

* [Portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interfaccia della riga di comando](insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Per altre informazioni è sempre possibile digitare ```Get-Help``` e quindi il comando di PowerShell da approfondire.

## <a name="create-alert-rules-in-powershell"></a>Creare regole di avviso in PowerShell
1. Accedere ad Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Visualizzare l'elenco delle sottoscrizioni disponibili. Assicurarsi di lavorare con la giusta sottoscrizione. In caso contrario, impostare la sottoscrizione giusta usando l'output di `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Per elencare le regole esistenti in un gruppo di risorse, usare il comando seguente:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Per creare una regola, per prima cosa è necessario disporre di alcune informazioni importanti.

  * L' **ID risorsa** della risorsa per la quale si intende impostare un avviso
  * Le **definizioni delle metriche** disponibili per tale risorsa

     È possibile ottenere l'ID della risorsa tramite il portale di Azure. Se la risorsa è già stata creata, selezionarla nel portale. Nel pannello successivo, nella sezione *Impostazioni*, selezionare *Proprietà*. **ID RISORSA** è un campo del pannello successivo. È anche possibile usare [Esplora risorse di Azure](https://resources.azure.com/).

     Un esempio di ID risorsa per un'app web è

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Usare `Get-AzureRmMetricDefinition` per visualizzare l'elenco di tutte le definizioni delle metriche per una specifica risorsa.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     L'esempio seguente genera una tabella con la metrica Name e il relativo valore Unit.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Un elenco completo delle opzioni disponibili per Get-AzureRmMetricDefinition è visualizzabile eseguendo `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. L'esempio seguente imposta un avviso relativo a una risorsa del sito Web. L'avviso viene attivato ogni volta che si riceve il traffico costantemente per 5 minuti e di nuovo quando non si riceve traffico per 5 minuti.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Per creare il webhook o inviare un messaggio di posta elettronica quando viene attivato l'avviso, creare prima il messaggio di posta elettronica e/o i webhook. Subito dopo creare la regola con il tag -Actions, come illustrato nell'esempio seguente. Non è possibile associare webhook o messaggi di posta elettronica a regole già create tramite PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Verificare che gli avvisi siano stati creati correttamente esaminando le singole regole.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Eliminare gli avvisi. Questi comandi eliminano le regole create in precedenza in questo articolo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Leggere una panoramica del monitoraggio di Azure](monitoring-overview.md) che include anche i tipi di informazioni che è possibile raccogliere e monitorare.
* Altre informazioni sulla [configurazione dei webhook negli avvisi](insights-webhooks-alerts.md).
* Altre informazioni sulla [configurazione di avvisi sugli eventi del log attività](monitoring-activity-log-alerts.md).
* Altre informazioni sui [runbook di automazione di Azure](../automation/automation-starting-a-runbook.md).
* Leggere una [panoramica della raccolta dei log di diagnostica](monitoring-overview-of-diagnostic-logs.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.

