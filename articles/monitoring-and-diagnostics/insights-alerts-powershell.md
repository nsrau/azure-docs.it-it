---
title: Creare avvisi per i servizi di Azure - PowerShell | Microsoft Docs
description: Attivare email, notifiche, chiamare gli URL di siti Web (webhook) o eseguire le azioni autoticamente quando vengono soddisfatte le condizioni specificate.
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
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 0a6a6e5b76e03eba113600b5be558daa8c924f0f


---
# <a name="create-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Creare avvisi in Monitoraggio di Azure per i servizi di Azure - Interfaccia della riga di comando multipiattaforma 
> [!div class="op_single_selector"]
> * [Portale](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Panoramica
Questo articolo descrive come impostare gli avvisi di Azure tramite PowerShell.  

È possibile ricevere avvisi basati su metriche di monitoraggio o eventi nei servizi Azure.

* **Valori metrici** : l'avviso si attiva quando il valore di una specifica metrica supera una soglia assegnata per eccesso o difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta e successivamente quando tale condizione non è più soddisfatta.    
* **Eventi del log attività** : è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato numero di eventi.

È possibile configurare un avviso affinché esegua le operazioni seguenti al momento dell'attivazione:

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
     Un elenco completo delle opzioni disponibili per Get-AzureRmMetricDefinition è visualizzabile eseguendo Get-MetricDefinitions.
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


1. Per creare un avviso che viene attivato in seguito al verificarsi di una condizione specifica nel log attività, usare i comandi come indicato di seguito.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName corrisponde a un tipo di evento nel log attività. Alcuni esempi sono *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    È possibile usare il comando PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) per ottenere un elenco di possibili operationNames. In alternativa, è possibile usare il portale di Azure per eseguire una query sul log attività e individuare operazioni passate specifiche per le quali creare un avviso. Le operazioni vengono mostrate nella visualizzazione del log di grafica dei nomi descrittivi. Individuare la voce nel codice JSON ed estrarre il valore di OperationName.   
2. Verificare che gli avvisi siano stati creati correttamente esaminando la singola regola.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
3. Eliminare gli avvisi. Questi comandi eliminano le regole create in precedenza in questo articolo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Leggere una panoramica del monitoraggio di Azure](monitoring-overview.md) che include anche i tipi di informazioni che è possibile raccogliere e monitorare.
* Altre informazioni sulla [configurazione dei webhook negli avvisi](insights-webhooks-alerts.md).
* Altre informazioni sui [runbook di automazione di Azure](../automation/automation-starting-a-runbook.md).
* Leggere una [panoramica della raccolta dei log di diagnostica](monitoring-overview-of-diagnostic-logs.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.



<!--HONumber=Jan17_HO5-->


