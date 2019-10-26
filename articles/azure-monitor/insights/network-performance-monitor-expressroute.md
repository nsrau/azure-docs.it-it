---
title: Soluzione di monitoraggio delle prestazioni di rete in Azure Log Analytics | Microsoft Docs
description: Usare la funzionalità Monitoraggio di ExpressRoute in Monitoraggio prestazioni rete per monitorare la connettività end-to-end e le prestazioni tra le proprie succursali e Azure tramite Azure ExpressRoute.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 5383402816eddba4c631c240585723b7c7119cef
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898888"
---
# <a name="expressroute-monitor"></a>Monitoraggio di ExpressRoute

È possibile usare la funzionalità Monitoraggio di ExpressRoute di Azure in [Monitoraggio prestazioni rete](network-performance-monitor.md) per monitorare la connettività end-to-end e le prestazioni tra le proprie succursali e Azure tramite Azure ExpressRoute. I vantaggi chiave sono: 

- Rilevamento automatico dei circuiti ExpressRoute associati alla sottoscrizione.
- Rilevamento di utilizzo della larghezza di banda, perdita e latenza nel circuito, peering e livello di rete virtuale di Azure per ExpressRoute.
- Individuazione della topologia di rete dei circuiti ExpressRoute.

![Monitoraggio di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configurazione 
Per aprire la configurazione per Monitoraggio prestazioni rete, aprire la [soluzione Monitoraggio prestazioni rete](network-performance-monitor.md) e selezionare **Configura**.

### <a name="configure-network-security-group-rules"></a>Configurare le regole del gruppo di sicurezza di rete 
Per i server in Azure usati per il monitoraggio tramite Monitoraggio prestazioni rete, configurare regole del gruppo di sicurezza di rete per consentire il traffico TCP sulla porta usata da Monitoraggio prestazioni rete per le transazioni sintetiche. Per impostazione predefinita è la porta 8084. Questa configurazione consente all'agente di Log Analytics installato nelle macchine virtuali di Azure di comunicare con un agente di monitoraggio locale. 

Per altre informazioni sui gruppi di sicurezza di rete, vedere  [Gruppi di sicurezza di rete](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Prima di proseguire con questo passaggio, installare l'agente server locale e l'agente server di Azure ed eseguire lo script di PowerShell EnableRules.ps1. 

 
### <a name="discover-expressroute-peering-connections"></a>Individuare le connessioni peering di ExpressRoute 
 
1. Selezionare la visualizzazione **Peering ExpressRoute**.
2. Selezionare **Individua adesso** per individuare tutti i peering ExpressRoute privati connessi alle reti virtuali nella sottoscrizione di Azure collegata all'area di lavoro Log Analytics.

    >[!NOTE]
    > La soluzione attualmente individua solo peering ExpressRoute privati 

    >[!NOTE]
    > Vengono individuati solo i peering privati connessi alle reti virtuali associate alla sottoscrizione collegata all'area di lavoro Log Analytics. Se ExpressRoute è connesso a reti virtuali esterne alla sottoscrizione collegata a questa area di lavoro, creare un'area di lavoro Log Analytics in queste sottoscrizioni. Usare quindi Monitoraggio prestazioni rete per monitorare i peering. 

    ![Configurazione del monitoraggio di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Al termine dell'individuazione, le connessioni peering private individuate sono elencate in una tabella. Il monitoraggio di questi peering è inizialmente in stato disabilitato. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Abilitare il monitoraggio delle connessioni peering ExpressRoute 

1. Selezionare la connessione peering privata che si vuole monitorare.
2. Nel riquadro a destra selezionare la casella di controllo **Monitora questo peering**. 
3. Se si intende creare eventi di integrità per la connessione, selezionare **Abilita Monitoraggio integrità per questo peering**. 
4. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per la generazione di eventi di integrità immettendo i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la connessione peering, viene generato un evento di integrità. 
5. Selezionare **Aggiungi agenti** per scegliere gli agenti di monitoraggio da usare per il monitoraggio della connessione peering. Assicurarsi di aggiungere agenti a entrambi i lati della connessione. È necessario almeno un agente nella rete virtuale connesso al peering. È anche necessario almeno un agente locale connesso al peering. 
6. Selezionare **Salva** per salvare la configurazione. 

   ![Configurazione del monitoraggio di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Dopo aver abilitato le regole e aver selezionato i valori e gli agenti, attendere da 30 a 60 minuti perché i valori vengano immessi e perché vengano visualizzati i riquadri **Monitoraggio di ExpressRoute**. Quando vengono visualizzati i riquadri di monitoraggio, i circuiti e le risorse di connessione di ExpressRoute vengono monitorati da Monitoraggio prestazioni rete. 

>[!NOTE]
> Questa funzionalità è affidabile nelle aree di lavoro aggiornate al nuovo linguaggio di query.

## <a name="walkthrough"></a>Procedura dettagliata 

Il dashboard Monitoraggio prestazioni rete mostra una panoramica dell'integrità dei circuiti e delle connessioni peering ExpressRoute. 

![Dashboard di monitoraggio delle prestazioni di rete](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Elenco dei circuiti 

Per visualizzare un elenco di tutti i circuiti ExpressRoute monitorati, selezionare il riquadro dei circuiti ExpressRoute. È possibile selezionare un circuito e visualizzare il relativo stato di integrità, i grafici di tendenza per la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza. I grafici sono interattivi. È possibile selezionare un intervallo di tempo personalizzato per tracciare i grafici. Trascinare il puntatore del mouse su un'area del grafico per eseguire lo zoom avanti e visualizzare i punti dati con granularità fine. 

![Elenco dei circuiti ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Tendenze relative a perdita, latenza e velocità effettiva 

I grafici di larghezza di banda, latenza e perdita sono interattivi. Ogni sezione di questi grafici può essere ingrandita tramite i controlli del mouse. È anche possibile visualizzare i dati relativi a larghezza di banda, latenza e perdita per altri intervalli. In alto a destra sotto il pulsante **Azioni** selezionare  **Data/Ora**. 

![Latenza di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Elenco dei peering 

Per visualizzare un elenco di tutte le connessioni alle reti virtuali tramite peering privato, selezionare il riquadro **Peering privati** nel dashboard. È possibile selezionare una connessione di rete virtuale e visualizzare il relativo stato di integrità e i grafici di tendenza riguardanti la perdita di pacchetti, l'utilizzo della larghezza di banda e la latenza. 

![Peering di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia dei circuiti 

Per visualizzare la topologia del circuito, selezionare il riquadro **Topologia**. Questa azione mostra la visualizzazione della topologia del circuito o peering selezionato. Il diagramma della topologia indica la latenza per ogni segmento in rete e ogni hop di livello 3 è rappresentato da un nodo del diagramma. Selezionando un hop, vengono visualizzati altri dettagli sull'hop. Per aumentare il livello di visibilità in modo da includere hop locali, spostare il dispositivo di scorrimento visualizzato sotto **FILTRI**. Spostando il dispositivo di scorrimento verso sinistra o destra, viene aumentato o diminuito il numero di hop nel grafico della topologia. Viene visualizzata la latenza per ogni segmento e in questo modo è possibile isolare più velocemente i segmenti ad alta latenza nella rete.

![Topologia di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Visualizzazione dettagliata della topologia di un circuito 

Questa visualizzazione mostra le connessioni di rete virtuale. 

![Connessioni di rete virtuale di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostica 

Monitoraggio prestazioni rete consente di diagnosticare diversi tipi di problemi di connettività dei circuiti. Di seguito sono elencati alcuni dei problemi che possono essere visualizzati.

È possibile visualizzare i codici di notifica e impostare avvisi corrispondenti tramite **Log Analytics**. Nella pagina **NPM Diagnostics** (Diagnostica NPM) è possibile visualizzare le descrizioni per ogni messaggio di diagnostica attivato.

| Codice di notifica (log) | Description |
| --- | --- |
| 5501 | Non è possibile attraversare la connessione secondaria del circuito ExpressRoute |
| 5502 | Non è possibile attraversare la connessione primaria del circuito ExpressRoute |
| 5503 | Non sono stati trovati circuiti per la sottoscrizione collegata all'area di lavoro | 
| 5508 | Not able to determine whether traffic is passing through any circuit(s) for path (Non è possibile determinare se il traffico transita attraverso alcun circuito per il percorso) |
| 5510 | Il traffico non transita attraverso il circuito previsto | 
| 5511 | Il traffico non transita attraverso la rete virtuale prevista | 

**Il circuito è inattivo.** Monitoraggio prestazioni rete invia una notifica non appena viene persa la connettività tra le risorse locali e le reti virtuali di Azure. Questa notifica permette di intervenire in modo proattivo prima di ricevere comunicazioni dagli utenti e di ridurre il tempo di inattività.

![Circuito ExpressRoute inattivo](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Il traffico non passa attraverso il circuito voluto.** Monitoraggio prestazioni rete invia una notifica ogni volta che il traffico non viene trasmesso attraverso il circuito ExpressRoute previsto. Questo problema può verificarsi se il circuito è inattivo e il traffico viene trasmesso attraverso la route di backup. Questa situazione può anche essere dovuta a un problema di routing. Queste informazioni permettono di gestire in modo proattivo tutti i problemi di configurazione dei criteri di routing e di assicurarsi che venga usata la route più ottimale e sicura. 

 

**Il traffico non passa attraverso il circuito primario.** Monitoraggio prestazioni rete invia una notifica quando il traffico viene trasmesso attraverso il circuito ExpressRoute secondario. Anche se in questo caso non si verificano problemi di connettività, un intervento proattivo per la risoluzione dei problemi del circuito permette di prepararsi al meglio. 

 
![Flusso del traffico di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Riduzione delle prestazioni a causa di picchi di utilizzo.** È possibile correlare la tendenza di utilizzo della larghezza di banda alla tendenza di latenza per determinare se la riduzione delle prestazioni del carico di lavoro di Azure sia dovuta o meno a un picco di utilizzo della larghezza di banda. Sarà quindi possibile intervenire di conseguenza.

![Utilizzo della larghezza di banda di ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Passaggi successivi
[Effettuare una ricerca nei log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
