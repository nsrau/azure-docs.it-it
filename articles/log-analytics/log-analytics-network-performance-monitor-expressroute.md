---
title: Soluzione di monitoraggio delle prestazioni di rete in Azure Log Analytics | Microsoft Docs
description: "La funzionalità ExpressRoute Manager in Monitoraggio prestazioni rete consente di monitorare la connettività e le prestazioni end-to-end tra le filiali e Azure tramite Azure ExpressRoute."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

La funzionalità ExpressRoute Manager in [Monitoraggio prestazioni rete](log-analytics-network-performance-monitor.md) consente di monitorare la connettività e le prestazioni end-to-end tra le filiali e Azure tramite Azure ExpressRoute. I vantaggi chiave sono: 

- Rilevamento automatico dei circuiti ExpressRoute associati alla sottoscrizione 
- Rilevamento dell'utilizzo della larghezza di banda, della perdita e della latenza a livello di circuito, peering e rete virtuale per ExpressRoute 
- Individuazione della topologia di rete dei circuiti ExpressRoute 

![Monitoraggio di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Configurazione 
Per aprire la configurazione per Monitoraggio prestazioni rete, aprire la [soluzione Monitoraggio prestazioni rete](log-analytics-network-performance-monitor.md) e fare clic sul pulsante **Configura**.

### <a name="configure-nsg-rules"></a>Configurare regole per i gruppi di sicurezza di rete 
Per i server in Azure usati per il monitoraggio tramite Monitoraggio prestazioni rete, è necessario configurare regole del gruppo di sicurezza di rete per consentire il traffico TCP sulla porta usata da Monitoraggio prestazioni rete per le transazioni sintetiche. Per impostazione predefinita è la porta 8084. Questo consente all'agente OMS installato nella VM di Azure di comunicare con un agente di monitoraggio locale. 

Per altre informazioni sui gruppi di sicurezza di rete, vedere  [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Assicurarsi di aver installato gli agenti (sia l'agente server locale che l'agente server di Azure) e di aver eseguito lo script di PowerShell EnableRules.ps1 prima di procedere con questo passaggio. 

 
### <a name="discover-expressroute-peering-connections"></a>Individuare le connessioni peering di ExpressRoute 
 
1. Fare clic sulla visualizzazione **Monitoraggio di ExpressRoute**.  
2. Fare clic sul pulsante **Individua adesso** per individuare tutti i peering ExpressRoute privati connessi alle reti virtuali nella sottoscrizione di Azure collegata all'area di lavoro di Log Analytics.  

>[!NOTE]  
> La soluzione attualmente individua solo peering ExpressRoute privati 

>[!NOTE]  
> connessi alle reti virtuali associate alla sottoscrizione collegata all'area di lavoro di Log Analytics. Se ExpressRoute è connesso a reti virtuali all'esterno della sottoscrizione collegata a questa area di lavoro, è necessario creare un'area di lavoro di Log Analytics nelle sottoscrizioni esterne e monitorare i peering corrispondenti tramite Monitoraggio prestazioni rete. 

 ![Configurazione di Monitoraggio di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Al termine dell'individuazione, le connessioni peering private individuate sono elencate in una tabella. Il monitoraggio di questi peering è inizialmente disabilitato. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Abilitare il monitoraggio delle connessioni peering ExpressRoute 

1. Fare clic sulla connessione peering privata che si vuole monitorare.  
2. Nel riquadro RHS fare clic sulla casella di controllo **Monitora questo peering**. 
3. Se si prevede di creare eventi di integrità per la connessione, selezionare **Abilita Monitoraggio integrità per questo peering**. 
4. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per la generazione di eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la connessione peering, viene generato un evento di integrità. 
5. Fare clic su **Aggiungi agenti** per scegliere gli agenti di monitoraggio da usare per il monitoraggio della connessione peering. È necessario assicurarsi di aggiungere agenti a entrambe le estremità della connessione, almeno un agente nella rete virtuale di Azure connessa a questo peering e almeno un agente locale connesso a questo peering. 
6. Fare clic su **Salva** per salvare la configurazione. 

![Configurazione di Monitoraggio di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Dopo aver attivato le regole e selezionato i valori e gli agenti che si desidera monitorare, c'è un'attesa di circa 30-60 prima che i valori inizino a popolarsi e i riquadri **Monitoraggio di ExpressRoute** diventino disponibili. Dopo aver visualizzato i riquadri di monitoraggio, i circuiti ExpressRoute e le risorse di connessione vengono monitorate da NPM. 

>[!NOTE]
> Questa funzionalità è affidabile nelle aree di lavoro aggiornate al nuovo linguaggio di query.  

## <a name="walkthrough"></a>Procedura dettagliata 

Il dashboard di Monitoraggio prestazioni rete visualizza una panoramica dell'integrità dei circuiti ExpressRoute e delle connessioni peering. 

![ExpressRoute nel dashboard di Monitoraggio prestazioni rete](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Elenco dei circuiti 

Per visualizzare l'elenco di tutti i circuiti ExpressRoute monitorati, fare clic sul riquadro dei circuiti ExpressRoute. È possibile selezionare un circuito e visualizzare il relativo stato di integrità, i grafici di tendenza per la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza. I grafici sono interattivi. È possibile selezionare un intervallo di tempo personalizzato per tracciare i grafici. Si può trascinare il puntatore del mouse su un'area del grafico per eseguire lo zoom avanti e visualizzare i punti dati con granularità fine. 

![Elenco dei circuiti ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>Tendenze relative a perdita, latenza e velocità effettiva 

I grafici della larghezza di banda, la latenza e la perdita sono interattivi. Ogni sezione di questi grafici può essere ingrandita mediante i controlli del mouse. È anche possibile visualizzare i dati relativi alla larghezza di banda, alla latenza e alla perdita per altri intervalli facendo clic su Data/Ora, sotto il pulsante Azioni in alto a sinistra. 

![Latenza di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Elenco dei peering 

Facendo clic sul riquadro **Peering privati** nel dashboard viene visualizzato un elenco di tutte le connessioni alle reti virtuali su peering privato. È possibile selezionare una connessione di rete virtuale e visualizzare il relativo stato di integrità e i grafici di tendenza riguardanti la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza. 

![Peering di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia dei circuiti 

Per visualizzare la topologia del circuito, fare clic sul riquadro **Topologia**. Verrà visualizzata la vista della topologia del circuito o peering. Il diagramma della topologia fornisce la latenza per ogni segmento della rete, e ogni hop di livello 3 è rappresentato da un nodo del diagramma. Facendo clic su un hop, vengono visualizzate altre informazioni sull'hop. È possibile aumentare il livello di visibilità per includere hop locali spostando il dispositivo di scorrimento sotto a **Filtri**. Spostare il dispositivo di scorrimento a sinistra o a destra, aumenta o diminuisce il numero di hop nel grafico della topologia. È visibile la latenza per ogni segmento e questo consente di isolare più velocemente i segmenti ad alta latenza nella rete. 

![Topologia di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>Visualizzazione dettagliata della Topologia di un circuito 

Questa visualizzazione mostra le connessioni della rete virtuale. 

![Rete virtuale di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostica 

Monitoraggio prestazioni rete consente di diagnosticare diversi tipi di problemi di connettività dei circuiti. Di seguito sono elencati alcuni problemi 

**Il circuito è inattivo.** Monitoraggio prestazioni rete invia una notifica non appena viene persa la connettività tra le risorse locali e le reti virtuali di Azure. Questo consente di intervenire attivamente prima di ricevere comunicazioni dagli utenti e di ridurre i tempi di inattività 

![Circuito ExpressRoute inattivo](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**Il traffico non passa attraverso il circuito voluto.** Monitoraggio prestazioni invia una notifica ogni volta che il traffico non passa attraverso il circuito ExpressRoute voluto e ciò non è previsto. Questa situazione può verificarsi se il circuito è inattivo e il traffico passa attraverso la route di backup o se si verifica un problema di routing. Queste informazioni consentono di gestire in modo proattivo tutti i problemi di configurazione dei criteri di routing e di assicurarsi che venga usata la route più efficiente e sicura. 

 

**Il traffico non passa attraverso il circuito primario.** La funzionalità invia una notifica quando il traffico passa attraverso il circuito ExpressRoute secondario. Anche se in questo caso non si verificano problemi di connettività, un intervento proattivo per la risoluzione dei problemi del circuito consente una preparazione migliore. 

 
![Flusso del traffico di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Riduzione delle prestazioni a causa di picchi di utilizzo.** È possibile correlare la tendenza dell'utilizzo della larghezza di banda con la tendenza della latenza per comprendere se la riduzione delle prestazioni del carico di lavoro di Azure sia dovuta a un picco di utilizzo della larghezza di banda o meno, intervenendo di conseguenza.  

![Monitoraggio di ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare una ricerca nei log](log-analytics-log-searches.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
