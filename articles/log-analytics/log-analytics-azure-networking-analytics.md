---
title: Soluzione Azure Networking Analytics in Log Analytics | Microsoft Docs
description: È possibile usare la soluzione Azure Networking Analytics in Log Analytics per esaminare i log dei gruppi di sicurezza di rete di Azure e i log dei gateway applicazione di Azure Application Gateway.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: richrund

---
# <a name="azure-networking-analytics-(preview)-solution-in-log-analytics"></a>Soluzione Azure Networking Analytics (anteprima) in Log Analytics
> [!NOTE]
> Si tratta di una [soluzione disponibile in anteprima](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).
> 
> 

È possibile usare la soluzione Azure Networking Analytics in Log Analytics per esaminare i log dei gateway applicazione di Azure e i log dei gruppi di sicurezza di rete di Azure.

È possibile abilitare la registrazione per i log dei gateway applicazione di Azure e dei gruppi di sicurezza di rete di Azure. Questi log vengono scritti nell'archivio BLOB, in cui possono essere quindi indicizzati da Log Analytics per la ricerca e l'analisi.

I log seguenti sono supportati per i gateway applicazione:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog

I log seguenti sono supportati per i gruppi di sicurezza di rete:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le istruzioni seguenti per installare e configurare la soluzione Azure Networking Analytics di Azure:

1. Abilitare la registrazione diagnostica per le risorse da monitorare:
   * [Gateway applicazione](../application-gateway/application-gateway-diagnostics.md)
   * [Gruppo di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md)
2. Configurare Log Analytics per la lettura dei log dall'archivio BLOB usando il processo illustrato in [File JSON nell'archivio BLOB](log-analytics-azure-storage-json.md).
3. Abilitare la soluzione Azure Networking Analytics seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  

Se non si abilita la registrazione diagnostica per un tipo specifico di risorsa, i pannelli del dashboard per quella risorsa saranno vuoti.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Esaminare i dettagli della raccolta di dati di Azure Networking Analytics
La soluzione Azure Networking Analytics raccoglie log di diagnostica dall'archivio BLOB di Azure per i gateway applicazione di Azure e per i gruppi di sicurezza di rete.
Non sono necessari agenti per la raccolta di dati.

La tabella seguente illustra i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per Azure Networking Analytics.

| Piattaforma | Agente diretto | Agente di Systems Center Operations Manager (SCOM) | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
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

<!--HONumber=Oct16_HO2-->


