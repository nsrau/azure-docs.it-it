---
title: Usare l'interfaccia della riga di comando di Azure multipiattaforma per creare avvisi in versione classica per i servizi di Azure | Microsoft Docs
description: Attivare messaggi di posta elettronica o notifiche, chiamare URL di siti Web (webhook) o usare l'automazione quando vengono soddisfatte le condizioni specificate.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287107"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Usare l'interfaccia della riga di comando di Azure multipiattaforma per creare avvisi per le metriche in versione classica in Monitoraggio di Azure per i servizi di Azure 

> [!div class="op_single_selector"]
> * [di Microsoft Azure](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Interfaccia della riga di comando](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> Questo articolo descrive come creare avvisi delle metriche classici di tipo precedente. Monitoraggio di Azure supporta ora [avvisi delle metriche più recenti e migliori](monitoring-near-real-time-metric-alerts.md). Questi avvisi possono monitorare più metriche e consentire le segnalazioni sulle metriche dimensionali. Il supporto dell'interfaccia della riga di comando di Azure per i nuovi avvisi per le metriche sarà presto disponibile.
>
>

Questo articolo descrive come configurare avvisi per le metriche di Azure in versione classica tramite l'interfaccia della riga di comando multipiattaforma di Azure.

> [!NOTE]
> Dal 25 settembre 2016 Monitoraggio di Azure è il nuovo nome di "Azure Insights". Gli spazi dei nomi e quindi i comandi descritti in questo articolo contengono tuttavia ancora il termine "insights".

È possibile ricevere un avviso in base alle metriche per i servizi di Azure o in base agli eventi che si verificano in Azure.

* **Valori delle metriche**: l'avviso si attiva quando il valore di una metrica specifica supera una soglia assegnata per eccesso o per difetto. Vale a dire che si attiva sia quando la condizione viene inizialmente soddisfatta che quando tale condizione non è più soddisfatta.    

* **Eventi del log attività**: è possibile attivare un avviso per *ogni* evento o solo quando si verifica un determinato evento. Per altre informazioni sui log attività, vedere [Creare avvisi del log attività (versione classica)](monitoring-activity-log-alerts.md). 

È possibile configurare un avviso classico delle metriche affinché esegua queste operazioni al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio. 
* Inviare un messaggio di posta elettronica agli indirizzi specificati.
* Chiamare un webhook.
* Avviare l'esecuzione di un runbook di Azure (al momento ciò è possibile solo dal portale di Azure).

È possibile configurare le regole di avviso per le metriche in versione classica e ottenere informazioni su di esse tramite: 

* [Il portale di Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Interfaccia della riga di comando di Azure](insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

È anche possibile ottenere informazioni sui comandi digitando un comando seguito da **-help**. Di seguito è illustrato un esempio: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Creare regole di avviso tramite l'interfaccia della riga di comando di Azure
1. Dopo avere installato i prerequisiti, accedere ad Azure. Vedere [Esempi dell'interfaccia della riga di comando per Monitoraggio di Azure](insights-cli-samples.md) per i comandi necessari per iniziare. Questi comandi sono utili per eseguire l'accesso, visualizzare la sottoscrizione in uso e prepararsi all'esecuzione dei comandi di Monitoraggio di Azure.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Per elencare le regole esistenti in un gruppo di risorse, usare il formato seguente: 

   **azure insights alerts rule list** *[opzioni] &lt;GruppoDiRisorse&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Per creare una regola, per prima cosa è necessario disporre di alcune informazioni importanti.
    * **ID della risorsa** per la quale si vuole impostare un avviso.
    * **Definizioni delle metriche** disponibili per la risorsa.

     È possibile ottenere l'ID della risorsa tramite il portale di Azure. Se la risorsa è già stata creata, selezionarla nel portale. Nel pannello successivo, nella sezione **Impostazioni**, selezionare quindi **Proprietà**. **ID RISORSA** è un campo del pannello successivo. 
     
     È anche possibile ottenere l'ID della risorsa tramite [Azure Resource Explorer](https://resources.azure.com/).

     Di seguito è illustrato un ID della risorsa di esempio per un'app Web:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Per ottenere un elenco delle metriche e delle unità per le metriche disponibili per la risorsa di esempio precedente, usare il comando dell'interfaccia della riga di comando di Azure seguente:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* indica la granularità della misura disponibile, ovvero con intervalli di 1 minuto. L'uso di granularità diverse offre opzioni delle metriche diverse.
     
4. Per creare una regola di avviso basata su una metrica, usare un comando nel formato seguente:

    **azure insights alerts rule metric set** *[opzioni] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    L'esempio seguente configura un avviso relativo a una risorsa di un sito Web. L'avviso viene attivato ogni volta che si riceve il traffico costantemente per 5 minuti e di nuovo quando non si riceve traffico per 5 minuti.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Per creare un webhook o inviare un messaggio di posta elettronica all'attivazione di un avviso per le metriche in versione classica, creare prima di tutto il messaggio di posta elettronica o il webhook. Creare la regola immediatamente dopo. Non è possibile associare webhook o messaggi di posta elettronica a regole già create.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. È possibile verificare che gli avvisi siano stati creati correttamente esaminando una singola regola.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Per eliminare le regole, usare un comando nel formato seguente:

    **insights alerts rule delete** [opzioni] &lt;resourceGroup&gt;&lt;ruleName&gt;

    Questi comandi eliminano le regole create in precedenza in questo articolo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Passaggi successivi
* [Leggere una panoramica del monitoraggio di Azure](monitoring-overview.md) che include i tipi di informazioni che è possibile raccogliere e monitorare.
* Leggere altre informazioni sulla [configurazione dei webhook negli avvisi](insights-webhooks-alerts.md).
* Leggere altre informazioni sulla [configurazione di avvisi per gli eventi del log attività](monitoring-activity-log-alerts.md).
* Leggere altre informazioni sui [runbook di Automazione di Azure](../automation/automation-starting-a-runbook.md).
* Leggere una [panoramica della raccolta dei log di diagnostica](monitoring-overview-of-diagnostic-logs.md) per raccogliere metriche dettagliate e ad alta frequenza sul servizio.
* Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
