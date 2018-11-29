---
title: 'Esercitazione: Monitorare i log e le metriche di Firewall di Azure'
description: In questa esercitazione viene descritto come abilitare e gestire i log e le metriche di Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 1354faad4abf9a8a4b56414628d39d9a6f90d721
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426168"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Esercitazione: Monitorare i log e le metriche di Firewall di Azure

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure. Usando le metriche, è possibile visualizzare i contatori delle prestazioni nel portale. 

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati a [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md), Archiviazione e Hub eventi ed essere analizzati in Log Analytics o con strumenti diversi come ad esempio Excel e Power BI.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la registrazione tramite il portale di Azure
> * Abilitare la registrazione con PowerShell
> * Visualizzare e analizzare Log attività
> * Visualizzare e analizzare i log delle regole di rete e di applicazione
> * Visualizzare le metriche

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è consigliabile leggere [Azure Firewall logs and metrics](logs-and-metrics.md) (Log e metriche di Firewall di Azure) per una panoramica dei log di diagnostica e delle metriche disponibili per Firewall di Azure.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Abilitare la registrazione diagnostica tramite il portale di Azure

Dopo aver completato questa procedura per abilitare la registrazione diagnostica possono essere necessari alcuni minuti per la visualizzazione dei dati nei log. Se non viene visualizzato subito alcun risultato, controllare dopo qualche minuto.

1. Nel portale di Azure aprire il gruppo di risorse del firewall e fare clic sul firewall.
2. In **Monitoraggio** fare clic su **Log di diagnostica**.

   Per Firewall di Azure sono disponibili due log specifici del servizio:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Fare clic su **Abilita diagnostica** per avviare la raccolta dei dati.
4. La pagina **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. 
5. In questo esempio Log Analytics archivia i log, quindi digitare **Firewall log analytics** come nome.
6. Fare clic su **Invia a Log Analytics** per configurare l'area di lavoro. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.
7. In **Log Analytics** fare clic su **Configura**.
8. Nella pagina delle aree di lavoro di Log Analytics fare clic su **Crea nuova area di lavoro**.
9. Nella pagina **Area di lavoro di Log Analytics** immettere **firewall-oms** come nome della nuova **Area di lavoro di Log Analytics**.
10. Selezionare la sottoscrizione, usare il gruppo di risorse del firewall esistente (**Test-FW-RG**), selezionare **Stati Uniti orientali** per la località e selezionare il piano tariffario **Gratuito**.
11. Fare clic su **OK**.
   ![Avvio del processo di configurazione][1] Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics.  
12. In **Log** fare clic su **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** per raccogliere i log per le regole di applicazione e di rete.
   ![Salvare le impostazioni di diagnostica][2]
13. Fare clic su **Save**.

## <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

Registrazione attività viene abilitata automaticamente per tutte le risorse di Resource Manager. È necessario abilitare la registrazione diagnostica per iniziare a raccogliere i dati disponibili in tali log.

Per abilitare la registrazione diagnostica, eseguire la procedura seguente:

1. Prendere nota dell'ID risorsa dell'account di archiviazione in cui vengono archiviati i dati dei log. Il valore ha il formato seguente: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Storage/storageAccounts/\<nome account archiviazione\>*.

   È possibile usare qualsiasi account di archiviazione della sottoscrizione. Per trovare queste informazioni è possibile usare il portale di Azure. Le informazioni si trovano nella pagina **Proprietà** della risorsa.

2. Prendere nota dell'ID risorsa del firewall per cui è abilitata la registrazione. Il valore ha il formato seguente: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome gruppo di risorse\>/providers/Microsoft.Network/azureFirewalls/\<nome firewall\>*.

   Per trovare queste informazioni è possibile usare il portale.

3. Abilitare la registrazione diagnostica usando il cmdlet di PowerShell seguente:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>I log di diagnostica non richiedono un account di archiviazione separato. Per l'uso del servizio di archiviazione per la registrazione degli accessi e delle prestazioni è previsto un addebito.

## <a name="view-and-analyze-the-activity-log"></a>Visualizzare e analizzare Log attività

È possibile visualizzare e analizzare i dati del log attività usando uno dei metodi seguenti:

* **Strumenti di Azure**: recuperare le informazioni dal log attività con Azure PowerShell, l'interfaccia della riga di comando (CLI) di Azure, l'API REST di Azure o il portale di Azure. Le istruzioni dettagliate di ciascun metodo sono fornite nell'articolo [Operazioni attività con Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: se non si ha ancora un account [Power BI](https://powerbi.microsoft.com/pricing) , è possibile crearne uno di prova gratuitamente. Usando il [pacchetto di contenuto dei log attività di Azure per Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) è possibile analizzare i dati con i dashboard preconfigurati che possono anche essere personalizzati.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Visualizzare e analizzare i log delle regole di rete e di applicazione

Azure [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md) raccoglie i file di log di contatori ed eventi. Include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

Per query di esempio di Log Analytics di Firewall di Azure, vedere [Azure Firewall Log Analytics samples](log-analytics-samples.md) (Esempi di Log Analytics di Firewall di Azure).

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.

## <a name="view-metrics"></a>Visualizzare le metriche
Passare a un firewall di Azure e in **Monitoraggio** fare clic su **Metriche**. Per visualizzare i valori disponibili, selezionare l'elenco a discesa **METRICA**.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere configurato il firewall per raccogliere i log, è possibile esplorare Log Analytics per visualizzare i dati.

> [!div class="nextstepaction"]
> [Soluzioni di monitoraggio di rete in Log Analytics](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
