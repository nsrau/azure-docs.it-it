---
title: Soluzione Azure Networking Analytics in Log Analytics | Microsoft Docs
description: "È possibile usare la soluzione Azure Networking Analytics in Log Analytics per esaminare i log dei gruppi di sicurezza di rete di Azure e i log dei gateway applicazione di Azure Application Gateway."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: ewinner
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 10ca10b2f644c29aad244abab720d2ce5586714f
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Soluzioni di monitoraggio di rete di Azure in Log Analytics

Log Analytics offre le seguenti soluzioni per il monitoraggio delle reti:
* Monitoraggio delle prestazioni di rete per
 * Verificare l'integrità della rete
* Azure Application Gateway Analytics per analizzare
 * Log di gateway applicazione di Azure
 * Metriche di gateway applicazione di Azure
* Azure Network Security Group Analytics per analizzare
 * Log dei gruppi di sicurezza di rete di Azure

## <a name="network-performance-monitor-npm"></a>Monitoraggio delle prestazioni di rete

La soluzione di gestione [Monitoraggio delle prestazioni di rete](log-analytics-network-performance-monitor.md) consente di monitorare l'integrità, la disponibilità e la raggiungibilità delle reti.  Viene usata per monitorare la connettività tra:

* Cloud pubblico e risorse locali
* Data center e percorsi utente (succursali)
* Subnet che ospitano vari livelli di un'applicazione multilivello.

Per altre informazioni, vedere [Monitoraggio delle prestazioni di rete](log-analytics-network-performance-monitor.md).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway Analytics e Azure Network Security Group Analytics
Per usare le soluzioni:
1. Aggiungere la soluzione di gestione a Log Analytics e
2. Abilitare la funzionalità diagnostica per indirizzare la diagnostica a un'area di lavoro di Log Analytics. Non è necessario inserire i log nell'Archiviazione BLOB di Azure.

È possibile abilitare la diagnostica e la soluzione corrispondente per uno o per entrambe le soluzioni: gateway applicazione e gruppi di sicurezza di rete.

Se non si abilita la registrazione diagnostica per un tipo specifico di risorsa ma si installa la soluzione, i pannelli del dashboard per quella risorsa sono vuoti e visualizzano un messaggio di errore.

> [!NOTE]
> A gennaio 2017 è stato modificato il metodo supportato per inviare i log dai gateway applicazione e dai gruppi di sicurezza di rete a Log Analytics. Se viene visualizzata la soluzione **Azure Networking Analytics (deprecata)**, fare riferimento ai passaggi di [migrazione dalla vecchia soluzione Networking Analytics](#migrating-from-the-old-networking-analytics-solution) per istruzioni su come procedere.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Esaminare i dettagli della raccolta di dati di rete di Azure
Le soluzioni di gestione delle analisi del gateway applicazione e del gruppo di sicurezza di rete di Azure raccolgono i log di diagnostica direttamente dalle due risorse appena citate. Non è necessario inserire i log in Archiviazione BLOB di Azure né è necessario alcun agente per la raccolta dati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per l'analisi del gateway applicazione e il gruppo di sicurezza di rete di Azure.

| Piattaforma | Agente diretto | Agente di Systems Center Operations Manager | Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Sì](./media/log-analytics-azure-networking/oms-bullet-green.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |Quando connesso |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Soluzione di analisi del gateway applicazione di Azure in Log Analytics

![Simbolo di Analisi gateway applicazione di Azure](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

I log seguenti sono supportati per i gateway applicazione:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Le metriche seguenti sono supportate per i gateway applicazione:

* Velocità effettiva di 5 minuti

### <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione di analisi del gateway applicazione di Azure:

1. Abilitare la soluzione Azure Application Gateway Analytics da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
2. Abilitare la registrazione diagnostica per i [gateway applicazione](../application-gateway/application-gateway-diagnostics.md) da monitorare.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Abilitare la diagnostica del gateway applicazione di Azure nel portale

1. Nel portale di Azure passare alla risorsa gateway applicazione da monitorare
2. Selezionare *Log di diagnostica* per aprire la pagina seguente

   ![Immagine della risorsa gateway applicazione di Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Fare clic su *Attiva diagnostica* per aprire la pagina seguente

   ![Immagine della risorsa gateway applicazione di Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Per attivare la diagnostica, fare clic su *Attivato* in *Stato*
5. Selezionare la casella di controllo *Send to Log Analytics* (Invia a Log Analytics)
6. Selezionare un'area di lavoro Log Analytics esistente o crearne una
7. Selezionare la casella di controllo in **Log** per ciascuno dei tipi di log da raccogliere
8. Fare clic su *Salva* per abilitare la registrazione della diagnostica per Log Analytics

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Abilitare la diagnostica di rete di Azure tramite PowerShell

Lo script PowerShell seguente contiene un esempio su come abilitare la registrazione diagnostica per i gateway applicazione.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Usare l'analisi dei gateway applicazione di Azure
![Immagine del riquadro di analisi dei gateway applicazione di Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Dopo aver selezionato il riquadro **di analisi del gateway applicazione di Azure** nella panoramica, è possibile visualizzare i riepiloghi dei log e quindi analizzare i dettagli per le categorie seguenti:

* Log di accesso del gateway applicazione
  * Errori di client e server per i log di accesso del gateway applicazione
  * Richieste all'ora per ogni gateway applicazione
  * Richieste non riuscite all'ora per ogni gateway applicazione
  * Errori per agente utente per gateway applicazione
* Prestazioni del gateway applicazione
  * Integrità dell'host per il gateway applicazione
  * Valore massimo e 95° percentile per le richieste non riuscite del gateway applicazione

![Immagine del dashboard di analisi del gateway applicazione di Azure](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Immagine del dashboard di analisi del gateway applicazione di Azure](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

Nel dashboard **di analisi del gateway applicazione di Azure** esaminare le informazioni di riepilogo in uno dei pannelli, quindi fare clic su un pannello per visualizzare le informazioni dettagliate nella pagina di ricerca di log.

In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Soluzione di analisi del gruppo di sicurezza di rete di Azure in Log Analytics

![Simbolo di Analisi gruppo di sicurezza di rete di Azure](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

I log seguenti sono supportati per i gruppi di sicurezza di rete:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Azure Networking Analytics di Azure:

1. Abilitare la soluzione Azure Network Security Group Analytics da [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
2. Abilitare la registrazione diagnostica per le risorse [gruppo di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md) da monitorare.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Abilitare la diagnostica del gruppo di sicurezza di rete di Azure nel portale

1. Nel portale di Azure passare alla risorsa gruppo di sicurezza di rete da monitorare
2. Selezionare *Log di diagnostica* per aprire la pagina seguente

   ![Immagine della risorsa gruppo di sicurezza di rete di Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Fare clic su *Attiva diagnostica* per aprire la pagina seguente

   ![Immagine della risorsa gruppo di sicurezza di rete di Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Per attivare la diagnostica, fare clic su *Attivato* in *Stato*
5. Selezionare la casella di controllo *Send to Log Analytics* (Invia a Log Analytics)
6. Selezionare un'area di lavoro Log Analytics esistente o crearne una
7. Selezionare la casella di controllo in **Log** per ciascuno dei tipi di log da raccogliere
8. Fare clic su *Salva* per abilitare la registrazione della diagnostica per Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Abilitare la diagnostica di rete di Azure tramite PowerShell

Lo script PowerShell seguente contiene un esempio su come abilitare la registrazione diagnostica per i gruppi di sicurezza di rete
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Usare l'analisi del gruppo di sicurezza di rete di Azure
Dopo aver selezionato il riquadro **di analisi del gruppo di sicurezza di rete di Azure** nella panoramica, è possibile visualizzare i riepiloghi dei log e quindi analizzare i dettagli per le categorie seguenti:

* Flussi bloccati dei gruppi di sicurezza di rete
  * Regole dei gruppi di sicurezza di rete con flussi bloccati
  * Indirizzi MAC con flussi bloccati
* Flussi consentiti dei gruppi di sicurezza di rete
  * Regole dei gruppi di sicurezza di rete con flussi consentiti
  * Indirizzi MAC con flussi consentiti

![Immagine del dashboard di analisi del gruppo di sicurezza di rete di Azure](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Immagine del dashboard di analisi del gruppo di sicurezza di rete di Azure](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

Nel dashboard **di analisi del gruppo di sicurezza di rete di Azure** esaminare le informazioni di riepilogo in uno dei pannelli, quindi fare clic su un pannello per visualizzare le informazioni dettagliate nella pagina di ricerca di log.

In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrazione dalla vecchia soluzione Networking Analytics
A gennaio 2017 è stato modificato il metodo supportato per inviare i log dai gateway applicazione e dai gruppi di sicurezza di rete di Azure a Log Analytics. In questo modo si otterranno i vantaggi seguenti:
+ I log vengono scritti direttamente in Log Analytics senza la necessità di utilizzare un account di archiviazione
+ Minore latenza dal momento in cui i log vengono generati essendo immediatamente disponibili in Log Analytics
+ Meno passaggi di configurazione
+ Un formato comune per tutti i tipi di diagnostica di Azure

Per usare le soluzioni aggiornate:

1. [Configurare la diagnostica in modo che venga inviata direttamente a Log Analytics dai gateway applicazione di Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configurare la diagnostica in modo che venga inviata direttamente a Log Analytics dai gruppi di sicurezza di rete di Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Abilitare la *soluzione di analisi del gateway applicazione* e del *gruppo di sicurezza di rete di Azure* seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md)
3. Aggiornare tutte le query salvate, i dashboard o gli avvisi per utilizzare il nuovo tipo di dati
  + Il tipo è AzureDiagnostics. È possibile usare ResourceType per filtrare i log di rete di Azure.

    | Invece di: | Usare: |
    | --- | --- |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayAccess`| `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayAccess` |
    |`Type=NetworkApplicationgateways OperationName=ApplicationGatewayPerformance` | `Type=AzureDiagnostics ResourceType=APPLICATIONGATEWAYS OperationName=ApplicationGatewayPerformance` |
    | `Type=NetworkSecuritygroups` | `Type=AzureDiagnostics ResourceType=NETWORKSECURITYGROUPS` |

   + Per ogni campo con suffisso di \_s, \_d o \_g nel nome, modificare il primo carattere in lettere minuscole
   + Per ogni campo con suffisso di \_o nel nome, i dati sono suddivisi in singoli campi in base ai nomi dei campi nidificati.
4. Rimuovere la soluzione *Azure Networking Analytics (deprecata)*.
  + Se si usa PowerShell, usare `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

I dati raccolti prima della modifica non sono visibili nella nuova soluzione. È possibile continuare a eseguire query per questi dati utilizzando i nomi di campo e il tipo vecchi.

## <a name="troubleshooting"></a>Risoluzione dei problemi
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passaggi successivi
* Usare le [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati per la diagnostica di Azure.

