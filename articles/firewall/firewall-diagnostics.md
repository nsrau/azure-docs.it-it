---
title: Monitorare i log e le metriche di Firewall di Azure
description: Questo articolo illustra come abilitare e gestire i log e le metriche del firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 2899121db4b6a3f202be4860e2e4f43027cdef7c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348767"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorare i log e le metriche di Firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure. Usando le metriche, è possibile visualizzare i contatori delle prestazioni nel portale.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati a [log di monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md), archiviazione e hub eventi e analizzati nei log di monitoraggio di Azure o in strumenti diversi, ad esempio Excel e Power bi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è consigliabile leggere i [log e le metriche del firewall di Azure](logs-and-metrics.md) per una panoramica dei log di diagnostica e delle metriche disponibili per il firewall di Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Abilitare la registrazione diagnostica tramite il portale di Azure

Dopo aver completato questa procedura per abilitare la registrazione diagnostica possono essere necessari alcuni minuti per la visualizzazione dei dati nei log. Se non viene visualizzato subito alcun risultato, controllare dopo qualche minuto.

1. Nel portale di Azure aprire il gruppo di risorse firewall e selezionare il firewall.
2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.

   Per il firewall di Azure sono disponibili quattro log specifici del servizio:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Selezionare **Aggiungi impostazioni di diagnostica**. La pagina **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica.
5. In questo esempio i log di Monitoraggio di Azure archiviano i log, quindi digitare **Firewall log analytics** come nome.
6. In **log** selezionare **AzureFirewallApplicationRule** , **AzureFirewallNetworkRule** , **AzureFirewallThreatIntelLog** e **AzureFirewallDnsProxy** per raccogliere i log.
7. Selezionare **Invia a log Analytics** per configurare l'area di lavoro.
8. Selezionare la propria sottoscrizione.
9. Selezionare **Salva**.

## <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione diagnostica per iniziare a raccogliere i dati disponibili in tali log.

Per abilitare la registrazione diagnostica, eseguire la procedura seguente:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il valore è nel formato seguente: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.storage/storageAccounts/ \<storage account name\>*.

   È possibile usare qualsiasi account di archiviazione della sottoscrizione. Per trovare queste informazioni è possibile usare il portale di Azure. Le informazioni si trovano nella pagina **Proprietà** della risorsa.

2. Prendere nota dell'ID risorsa del firewall per cui è abilitata la registrazione. Il valore è nel formato seguente: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\>*.

   Per trovare queste informazioni è possibile usare il portale.

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>I log di diagnostica non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

## <a name="enable-diagnostic-logging-by-using-azure-cli"></a>Abilitare la registrazione diagnostica usando l'interfaccia della riga di comando

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione diagnostica per iniziare a raccogliere i dati disponibili in tali log.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="enable-diagnostic-logging"></a>Abilitare la registrazione diagnostica

Usare i comandi seguenti per abilitare la registrazione diagnostica.

1. Eseguire il comando [AZ monitor Diagnostic-Settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) per abilitare la registrazione diagnostica:

   ```azurecli
   az monitor diagnostic-settings create –name AzureFirewallApplicationRule \
     --resource Firewall07 --storage-account MyStorageAccount
   ```

   Eseguire il comando [AZ monitor Diagnostic-Settings list](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_list) per visualizzare le impostazioni di diagnostica per una risorsa:

   ```azurecli
   az monitor diagnostic-settings list --resource Firewall07
   ```

   Usare [AZ monitor Diagnostic-Settings Show](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_show) per visualizzare le impostazioni di diagnostica attive per una risorsa:

   ```azurecli
   az monitor diagnostic-settings show --name AzureFirewallApplicationRule --resource Firewall07
   ```

1. Eseguire il comando [AZ monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) per aggiornare le impostazioni.

   ```azurecli
   az monitor diagnostic-settings update --name AzureFirewallApplicationRule --resource Firewall07 --set retentionPolicy.days=365
   ```

   Usare il comando [AZ monitor Diagnostic-Settings Delete](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_delete) per eliminare un'impostazione di diagnostica.

   ```azurecli
   az monitor diagnostic-settings delete --name AzureFirewallApplicationRule --resource Firewall07
   ```

> [!TIP]
>I log di diagnostica non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

## <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati del log attività usando uno dei metodi seguenti:

* **Strumenti di Azure** : recuperare le informazioni dal log attività con Azure PowerShell, l'interfaccia della riga di comando (CLI) di Azure, l'API REST di Azure o il portale di Azure. Le istruzioni dettagliate di ciascun metodo sono fornite nell'articolo [Operazioni attività con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power bi** : se non si ha già un account [Power bi](https://powerbi.microsoft.com/pricing) , è possibile provarlo gratuitamente. Usando il [pacchetto di contenuto dei log attività di Azure per Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) è possibile analizzare i dati con i dashboard preconfigurati che possono anche essere personalizzati.
* **Azure Sentinel** : è possibile connettere i log del firewall di Azure ad Azure Sentinel, consentendo di visualizzare i dati di log nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi. Il connettore dati del firewall di Azure in Sentinel di Azure è attualmente disponibile in anteprima pubblica. Per altre informazioni, vedere [connettere i dati dal firewall di Azure](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Visualizzare e analizzare i log delle regole di rete e di applicazione

I [log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md) raccolgono i file di log di contatori ed eventi. Include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

Per query di esempio di Log Analytics di Firewall di Azure, vedere [Esempi di Log Analytics per Firewall di Azure](log-analytics-samples.md).

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="view-metrics"></a>Visualizzare le metriche
Passare a un firewall di Azure, in **monitoraggio** selezionare **metriche**. Per visualizzare i valori disponibili, selezionare l'elenco a discesa **METRICA**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il firewall per raccogliere i log, è possibile esplorare i log di Monitoraggio di Azure per visualizzare i dati.

[Soluzioni di monitoraggio di rete nei log di Monitoraggio di Azure](../azure-monitor/insights/azure-networking-analytics.md)
