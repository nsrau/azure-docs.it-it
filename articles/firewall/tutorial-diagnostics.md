---
title: 'Esercitazione: Monitorare i log di Firewall di Azure'
description: In questa esercitazione viene descritto come abilitare e gestire i log di Firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991904"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Esercitazione: Monitorare i log di Firewall di Azure

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Gli esempi negli articoli di Firewall di Azure presuppongono che sia già stata abilitata l'anteprima pubblica di Firewall di Azure. Per altre informazioni, vedere [Abilitare l'anteprima pubblica di Firewall di Azure](public-preview.md).

È possibile monitorare Firewall di Azure con i log del firewall. È possibile usare anche i log attività per controllare le operazioni eseguite sulle risorse di Firewall di Azure.

Alcuni di questi log sono accessibili tramite il portale. I log possono essere inviati a [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Archiviazione e Hub eventi ed essere analizzati in Log Analytics o con strumenti diversi come ad esempio Excel e Power BI.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la registrazione tramite il portale di Azure
> * Abilitare la registrazione con PowerShell
> * Visualizzare e analizzare Log attività
> * Visualizzare e analizzare i log delle regole di rete e di applicazione

## <a name="diagnostic-logs"></a>Log di diagnostica

 I log di diagnostica seguenti sono disponibili per Firewall di Azure:

* **Log delle regole di applicazione**

   Il log delle regole di applicazione viene salvato in un account di archiviazione, trasmesso all'Hub eventi e/o inviato a Log Analytics solo se è stato abilitato per ogni Firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di applicazione configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Log delle regole di rete**

   Il log delle regole di rete viene salvato in un account di archiviazione, trasmesso all'Hub eventi e/o inviato a Log Analytics solo se è stato abilitato per ogni Firewall di Azure. Ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata. I dati vengono registrati in formato JSON, come illustrato nell'esempio seguente:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Sono disponibili tre opzioni di archiviazione dei log:

* **Account di archiviazione**: ideali quando i log vengono archiviati per un periodo più lungo ed esaminati quando necessario.
* **Hub eventi**: ottima opzione per l'integrazione con altri strumenti di gestione delle informazioni di sicurezza e degli eventi (SEIM) per ricevere avvisi sulle risorse.
* **Log Analytics**: soluzione adatta al monitoraggio generale in tempo reale dell'applicazione o all'analisi delle tendenze.

## <a name="activity-logs"></a>Log attività

   Le voci dei log attività vengono raccolte per impostazione predefinita e possono essere visualizzate nel portale di Azure.

   È possibile usare i [log attività di Azure](../azure-resource-manager/resource-group-audit.md), chiamati in precedenza log operativi e log di controllo, per visualizzare tutte le operazioni inviate alla sottoscrizione di Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Abilitare la registrazione diagnostica tramite il portale di Azure

Dopo aver completato questa procedura per abilitare la registrazione diagnostica possono essere necessari alcuni minuti per la visualizzazione dei dati nei log. Se non viene visualizzato subito alcun risultato, controllare dopo qualche minuto.

1. Nel portale di Azure aprire il gruppo di risorse del firewall e fare clic sul firewall.
2. In **Monitoraggio** fare clic su **Log di diagnostica**.

   Per Firewall di Azure sono disponibili due log specifici del servizio:

   * Log delle regole di applicazione
   * Log delle regole di rete

3. Fare clic su **Abilita diagnostica** per avviare la raccolta dei dati.
4. La pagina **Impostazioni di diagnostica** include le impostazioni per i log di diagnostica. 
5. In questo esempio Log Analytics archivia i log, quindi digitare **Firewall log analytics** come nome.
6. Fare clic su **Invia a Log Analytics** per configurare l'area di lavoro. Per salvare i log di diagnostica è possibile anche usare l'hub eventi e un account di archiviazione.
7. In **Log Analytics** fare clic su **Configura**.
8. Nella pagina aree di lavoro OMS fare clic su **Crea nuova area di lavoro**.
9. Nella pagina **Area di lavoro di Log Analytics** immettere **firewall-oms** come nome della nuova **Area di lavoro OMS**.
10. Selezionare la sottoscrizione, usare il gruppo di risorse del firewall esistente (**Test-FW-RG**), selezionare **Stati Uniti orientali** per la località e selezionare il piano tariffario **Gratuito**.
11. Fare clic su **OK**.
   ![Avvio del processo di configurazione][1]
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

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) raccoglie i file di log di contatori ed eventi. Include visualizzazioni e funzionalità di ricerca avanzate per analizzare i log.

È anche possibile connettersi all'account di archiviazione e recuperare le voci del log JSON per i log di accesso e delle prestazioni. Dopo avere scaricato i file JSON, è possibile convertirli in CSV e visualizzarli in Excel, PowerBI o un altro strumento di visualizzazione dei dati.

> [!TIP]
> Se si ha familiarità con Visual Studio e i concetti di base della modifica dei valori di costanti e variabili in C#, è possibile usare i [convertitori di log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibili in GitHub.


## <a name="next-steps"></a>Passaggi successivi

Dopo avere configurato il firewall per raccogliere i log, è possibile esplorare Log Analytics per visualizzare i dati.

> [!div class="nextstepaction"]
> [Soluzioni di monitoraggio di rete in Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
