---
title: Soluzione Azure Networking Analytics in Log Analytics | Microsoft Docs
description: "È possibile usare la soluzione Azure Networking Analytics in Log Analytics per esaminare i log dei gruppi di sicurezza di rete di Azure e i log dei gateway applicazione di Azure Application Gateway."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/1/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: a86819102797b0e243d28cd9ddb3d2c88c74bfca
ms.openlocfilehash: 7ea593885c1b380236a49ec030c00ad19097e2fa


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Soluzione Azure Networking Analytics (anteprima) in Log Analytics

È possibile usare la soluzione di analisi di rete di Azure in Log Analytics per esaminare gli elementi seguenti:

* Log di gateway applicazione di Azure
* Metriche di gateway applicazione di Azure 
* Log dei gruppi di sicurezza di rete di Azure

> [!NOTE]
> Analisi di rete di Azure è una [soluzione di anteprima](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Per usare la soluzione, abilitare la diagnostica per i log di gateway applicazione di Azure e dei gruppi di sicurezza di rete di Azure e indirizzare la diagnostica a un'area di lavoro di Log Analytics. Non è necessario inserire i log nell'Archiviazione BLOB di Azure.

I log seguenti sono supportati per i gateway applicazione:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Le metriche seguenti sono supportate per i gateway applicazione:

* Velocità effettiva di 5 minuti

I log seguenti sono supportati per i gruppi di sicurezza di rete:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter
* NetworkSecurityGroupFlowEvent

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Azure Networking Analytics di Azure:

1. Abilitare la registrazione diagnostica per le risorse da monitorare:
   * [Gateway applicazione](../application-gateway/application-gateway-diagnostics.md)
   * [Gruppo di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md)
2. Abilitare la soluzione Azure Networking Analytics seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  

Lo script PowerShell consente di abilitare la registrazione diagnostica per gateway applicazione e gruppi di sicurezza di rete 
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```


Se non si abilita la registrazione diagnostica per un tipo specifico di risorsa, i pannelli del dashboard per quella risorsa sono vuoti.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Esaminare i dettagli della raccolta di dati di Azure Networking Analytics
La soluzione di analisi di rete di Azure raccoglie i log di diagnostica direttamente dal gateway applicazione e dai gruppi di sicurezza di rete di Azure. Non è necessario inserire i log in Archiviazione BLOB di Azure né è necessario alcun agente per la raccolta dati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per Azure Networking Analytics.

| Piattaforma | Agente diretto | Agente di Systems Center Operations Manager | Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Sì](./media/log-analytics-azure-networking/oms-bullet-green.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 minuti |

## <a name="use-azure-networking-analytics"></a>Usare Azure Networking Analytics
Dopo l'installazione della soluzione, è possibile visualizzare il riepilogo degli errori di client e server per i gateway applicazione monitorati usando il riquadro **Azure Networking Analytics** nella pagina **Panoramica** in Log Analytics.

![Immagine del riquadro Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Dopo avere selezionato il riquadro **Panoramica**, è possibile visualizzare i riepiloghi dei log e quindi analizzare i dettagli per le categorie seguenti:

* Log di accesso del gateway applicazione
  * Errori di client e server per i log di accesso del gateway applicazione
  * Richieste all'ora per ogni gateway applicazione
  * Richieste non riuscite all'ora per ogni gateway applicazione
  * Errori per agente utente per gateway applicazione
* Prestazioni del gateway applicazione
  * Integrità dell'host per il gateway applicazione
  * Valore massimo e 95° percentile per le richieste non riuscite del gateway applicazione
* Flussi bloccati dei gruppi di sicurezza di rete
  * Regole dei gruppi di sicurezza di rete con flussi bloccati
  * Indirizzi MAC con flussi bloccati
* Flussi consentiti dei gruppi di sicurezza di rete
  * Regole dei gruppi di sicurezza di rete con flussi consentiti
  * Indirizzi MAC con flussi consentiti

![Immagine del dashboard di Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Immagine del dashboard di Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Immagine del dashboard di Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Immagine del dashboard di Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Per visualizzare i dettagli per un riepilogo di log
1. Nella pagina **Panoramica** fare clic sul riquadro **Azure Networking Analytics**.
2. Nel dashboard **Azure Networking Analytics** esaminare le informazioni di riepilogo in uno dei pannelli, quindi fare clic su un pannello per visualizzare le informazioni dettagliate corrispondenti nella pagina di ricerca di log.
   
    In una pagina di ricerca di log qualsiasi è possibile visualizzare i risultati in base all'ora, ai dettagli e alla cronologia di ricerca. È anche possibile filtrare per facet in modo da limitare i risultati.

## <a name="next-steps"></a>Passaggi successivi
* Usare le [Ricerche nei log in Log Analytics](log-analytics-log-searches.md) per visualizzare i dati dettagliati per Azure Networking Analytics.




<!--HONumber=Dec16_HO1-->


