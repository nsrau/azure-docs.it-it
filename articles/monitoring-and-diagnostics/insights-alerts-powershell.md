---
title: Usare PowerShell per creare avvisi in versione classica per i servizi di Azure | Microsoft Docs
description: Attivare messaggi di posta elettronica o notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni specificate.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286200"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Usare PowerShell per creare avvisi per i servizi di Azure
> [!div class="op_single_selector"]
> * [di Microsoft Azure](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Interfaccia della riga di comando](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [avvisi delle metriche più recenti e migliori](monitoring-near-real-time-metric-alerts.md). Questi avvisi possono monitorare più metriche e consentire le segnalazioni sulle metriche dimensionali. Il supporto di PowerShell per i nuovi avvisi per le metriche sarà presto disponibile.
>
>

Questo articolo descrive come configurare avvisi per le metriche in versione classica tramite PowerShell.  

È possibile ricevere avvisi in base alle metriche per i servizi di Azure oppure per gli eventi che si verificano in Azure.

* **Valori delle metriche**: l'avviso si attiva quando il valore di una metrica specifica supera una soglia assegnata per eccesso o per difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta che quando tale condizione non è più soddisfatta.    
* **Eventi del log attività**: è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato evento. Per altre informazioni sugli avvisi per i log attività, vedere [Creare avvisi del log attività (versione classica)](monitoring-activity-log-alerts.md).

È possibile configurare un avviso per le metriche in versione classica in modo che vengano eseguite le attività seguenti al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica a indirizzi aggiuntivi specificati.
* Chiamare un webhook.
* Avviare l'esecuzione di un runbook di Azure (solo dal portale di Azure).

È possibile configurare le regole di avviso e ottenere informazioni su di esse dalle posizioni seguenti: 

* [Portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaccia della riga di comando di Azure](insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Per altre informazioni, è sempre possibile digitare ```Get-Help``` seguito dal comando di PowerShell per il quale serve aiuto.

## <a name="create-alert-rules-in-powershell"></a>Creare regole di avviso in PowerShell
1. Accedere ad Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Ottenere un elenco delle sottoscrizioni disponibili. Verificare che la sottoscrizione in uso sia quella corretta. In caso contrario, ottenere la sottoscrizione corretta tramite l'output di `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Elencare le regole esistenti in un gruppo di risorse usando il comando seguente:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Per creare una regola, per prima cosa è necessario disporre di alcune informazioni importanti.

    - **ID della risorsa** per la quale si vuole impostare un avviso.
    - **Definizioni delle metriche** disponibili per la risorsa.

     È possibile ottenere l'ID della risorsa tramite il portale di Azure. Se la risorsa è già stata creata, selezionarla nel portale. Nel pannello successivo, nella sezione **Impostazioni**, selezionare quindi **Proprietà**. **ID RISORSA** è un campo del pannello successivo. 
     
     È anche possibile ottenere l'ID della risorsa tramite [Azure Resource Explorer](https://resources.azure.com/).

     Di seguito è illustrato un ID della risorsa di esempio per un'app Web:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Usare `Get-AzureRmMetricDefinition` per visualizzare l'elenco di tutte le definizioni delle metriche per una risorsa specifica:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     L'esempio seguente genera una tabella con il nome della metrica e la relativa unità:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Per un elenco completo delle opzioni disponibili per Get-AzureRmMetricDefinition, eseguire `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. L'esempio seguente configura un avviso relativo a una risorsa di un sito Web. L'avviso viene attivato ogni volta che si riceve il traffico costantemente per 5 minuti e di nuovo quando non si riceve traffico per 5 minuti.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Per creare un webhook o inviare un messaggio di posta elettronica quando viene attivato un avviso, creare prima il messaggio di posta elettronica o il webhook. Subito dopo creare la regola con il tag -Actions, come illustrato nell'esempio seguente. Non è possibile associare webhook o messaggi di posta elettronica a regole già create.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Esaminare le singole regole per verificare che gli avvisi siano stati creati correttamente.

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
* [Leggere una panoramica del monitoraggio di Azure](monitoring-overview.md) che include i tipi di informazioni che è possibile raccogliere e monitorare.
* Leggere informazioni su come [configurare i webhook negli avvisi](insights-webhooks-alerts.md).
* Leggere informazioni sulla [configurazione di avvisi per gli eventi del log attività](monitoring-activity-log-alerts.md).
* Leggere altre informazioni sui [runbook di Automazione di Azure](../automation/automation-starting-a-runbook.md).
* Leggere una [panoramica della raccolta dei log di diagnostica](monitoring-overview-of-diagnostic-logs.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
