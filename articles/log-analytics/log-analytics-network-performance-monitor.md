---
title: Soluzione Monitoraggio prestazioni rete in Azure | Microsoft Docs
description: Monitoraggio prestazioni rete in Azure consente di monitorare le prestazioni delle reti quasi in tempo reale per rilevare e trovare i colli di bottiglia delle prestazioni di rete.
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
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Soluzione Monitoraggio prestazioni rete in Azure

![Simbolo di Monitoraggio prestazioni rete](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Monitoraggio prestazioni rete (NPM, Network Performance Monitor) è una soluzione di monitoraggio di rete ibrida basata sul cloud che consente di monitorare le prestazioni della rete tra diversi punti dell'infrastruttura di rete, di monitorare la connettività della rete agli endpoint di servizio/applicazione e di monitorare le prestazioni di Azure ExpressRoute.  

NPM rileva problemi di rete come i buchi neri del traffico, gli errori di routing e i problemi che i metodi di monitoraggio della rete tradizionali non riescono a rilevare. La soluzione genera avvisi, notifica quando viene superata una soglia per un collegamento di rete, garantisce una tempestiva individuazione dei problemi legati alle prestazioni di rete e localizza l'origine del problema in un particolare segmento di rete o un dispositivo. 

NPM offre tre funzionalità generali: 

[Performance Monitor](log-analytics-network-performance-monitor-performance-monitor.md): monitora la connettività di rete tra distribuzioni cloud e percorsi locali, più data center e succursali, applicazioni/microservizi multilivello cruciali. La funzionalità Performance Monitor consente di rilevare tempestivamente i problemi a livello di rete.  

[Monitoraggio endpoint di servizio](log-analytics-network-performance-monitor-service-endpoint.md): è possibile monitorare la connettività dagli utenti ai servizi più importanti, determinare il tipo di infrastruttura nel percorso e i punti in cui si verificano i colli di bottiglia. Consente di essere avvisati delle interruzioni di servizio prima degli utenti e fornisce la posizione esatta dei problemi lungo il percorso di rete. 

Questa funzionalità consente di eseguire test basati su HTTP, HTTPS, TCP e ICMP per monitorare la disponibilità e il tempo di risposta del servizio quasi in tempo reale o su base cronologica, nonché il contributo della rete rispetto a latenza e perdita di pacchetti. Grazie alla mappa della topologia di rete, è possibile isolare i rallentamenti della rete identificando le aree problematiche che si verificano lungo il percorso di rete dal nodo al servizio, con i dati di latenza per ogni hop. I test predefiniti consentono di monitorare la connettività a Office 365 e a Dynamics CRM senza alcuna preconfigurazione. Questa funzionalità consente di monitorare la connettività della rete a qualsiasi endpoint idoneo per TCP, ad esempio siti Web, applicazioni SaaS e PaaS, database SQL e così via.  

[Monitoraggio di ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): monitora la connettività e le prestazioni end-to-end tra succursali e Azure tramite Azure ExpressRoute.  
 

## <a name="set-up-and-configure"></a>Installare e configurare

### <a name="install-and-configure-agents"></a>Installare e configurare agenti 

Usare i processi di base per installare gli agenti descritti in [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md) e [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Dove installare gli agenti 

**Performance Monitor:** installare gli agenti di OMS in almeno un nodo connesso a ogni subnet da cui si vuole monitorare la connettività ad altre subnet.  

Per monitorare un collegamento di rete, è necessario installare gli agenti in entrambi gli endpoint del collegamento.  Se non si conosce la topologia della propria rete, installare gli agenti nei server con carichi di lavoro critici tra cui si vogliono monitorare le prestazioni di rete. Se ad esempio si vuole monitorare la connessione di rete tra un server Web e un server che esegue SQL, installare un agente in entrambi i server. Gli agenti monitorano la connettività di rete (collegamenti) tra gli host, non gli host stessi. 

**Monitoraggio endpoint di servizio:** installare l'agente di OMS in ogni nodo da cui si vuole monitorare la connettività della rete all'endpoint di servizio. Se ad esempio si intende monitorare la connettività della rete a Office365 dall'ufficio O1, O2 e O3, installare l'agente di OMS in almeno un nodo ognuno in O1, O2 e O3. 

**Monitoraggio di ExpressRoute:** installare almeno un agente di OMS nella rete virtuale di Azure e almeno un agente nella subnet locale, connessa tramite il peering privato ExpressRoute.  

### <a name="configure-oms-agents-for-monitoring"></a>Configurare gli agenti di OMS per il monitoraggio  

NPM usa le transazioni sintetiche per monitorare le prestazioni della rete tra gli agenti di origine e destinazione. La soluzione consente di scegliere tra TCP e ICMP come protocollo per il monitoraggio delle funzionalità di Performance Monitor e Monitoraggio endpoint di servizio, mentre per Monitoraggio di ExpressRoute viene usato TCP. Assicurarsi che il firewall consenta la comunicazione tra gli agenti di OMS usati per il monitoraggio del protocollo scelto per il monitoraggio.  

**Protocollo TCP:** se si è scelto TCP come protocollo per il monitoraggio, aprire la porta del firewall negli agenti usati per le funzionalità di Performance Monitor e Monitoraggio di ExpressRoute, per assicurare la connessione tra gli agenti. A questo scopo, eseguire lo script di PowerShell EnableRules.ps1 senza parametri in una finestra di PowerShell con privilegi amministrativi.  

Lo script crea le chiavi del Registro di sistema richieste dalla soluzione e crea regole del firewall Windows per consentire agli agenti di creare connessioni TCP tra loro. Le chiavi del Registro di sistema create dallo script inoltre specificano se registrare i log di debug e il percorso al file di log. Inoltre definiscono la porta TCP agente usata per la comunicazione. I valori per queste chiavi vengono impostati automaticamente dallo script, quindi non bisogna modificare manualmente le chiavi. La porta aperta per impostazione predefinita è 8084. È possibile usare una porta personalizzata fornendo il parametro portNumber allo script. Tuttavia, è necessario usare la stessa porta su tutti i computer in cui viene eseguito lo script. 

>[!NOTE]
> Lo script configura solo il firewall Windows in locale. Se si ha un firewall di rete, è necessario assicurarsi che consenta il traffico destinato alla porta TCP usata da NPM 

>[!NOTE]
> Non è necessario eseguire lo script di PowerShell EnableRules.ps1 per Monitoraggio endpoint di servizio 

 

**Protocollo ICMP**: se si è scelto ICMP come protocollo per il monitoraggio, abilitare le regole del firewall seguenti per usare ICMP in modo affidabile: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Configurare la soluzione 

1. Aggiungere la soluzione di monitoraggio delle prestazioni di rete all'area di lavoro da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) o usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). 
2. Aprire l'area di lavoro di Log Analytics e fare clic sul riquadro **Panoramica**.  
3. Fare clic sul riquadro chiamato **Monitoraggio prestazioni rete** con il messaggio  *La soluzione richiede configurazione aggiuntiva*.

    ![Riquadro Monitoraggio prestazioni rete](media/log-analytics-network-performance-monitor/npm-config.png)

3. Nella pagina **Installazione** viene visualizzata l'opzione per installare gli agenti di OMS e configurare gli agenti per il monitoraggio nella visualizzazione **Impostazioni comuni**. Come illustrato sopra, se gli agenti di OMS sono già stati installati e configurati, fare clic sulla visualizzazione **Installazione** per configurare la funzionalità che si è interessati a usare.  

    **Visualizzazione Performance Monitor**: scegliere il protocollo da usare per le transazioni sintetiche nella regola di monitoraggio delle prestazioni predefinita e fare clic su Salva e continua. Questa selezione del protocollo è valida solo per la regola predefinita generata dal sistema ed è necessario scegliere il protocollo ogni volta che si crea in modo esplicito una regola di Performance Monitor. È sempre possibile passare alle impostazioni della regola predefinita nella scheda Performance Monitor (visualizzata dopo avere completato la configurazione del giorno 0) e modificare il protocollo in seguito. Se non si è interessati alla funzionalità Perfomance Monitor, è possibile disabilitare la regola predefinita dalle impostazioni della regola predefinita nella scheda Performance Monitor. 

    ![Configurazione NPM](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Visualizzazione Monitoraggio endpoint di servizio**: la funzionalità fornisce test preconfigurati predefiniti per monitorare la connettività della rete a Office365 e Dynamcis365 dagli agenti. Scegliere i servizi di Office365 e Dynamcis365 che si è interessati a monitorare scegliendo la relativa casella di controllo. Scegliere gli agenti da cui si vuole eseguire il monitoraggio, facendo clic sul pulsante Aggiungi agenti. Se non si vuole usare questa funzionalità o si preferisce configurarla più avanti, è possibile ignorarla e fare direttamente clic su **Salva** e **Continua** senza scegliere nulla.  

    ![Configurazione NPM](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Visualizzazione Monitoraggio di ExpressRoute**: fare clic sul pulsante **Individua adesso** per individuare tutti i peering ExpressRoute privati connessi alle reti virtuali nella sottoscrizione di Azure collegata all'area di lavoro di Log Analytics.  


    >[!NOTE] 
    > La soluzione attualmente individua solo peering ExpressRoute privati 

    >[!NOTE] 
    > connessi alle reti virtuali associate alla sottoscrizione collegata all'area di lavoro di Log Analytics. Se ExpressRoute è connesso a reti virtuali all'esterno della sottoscrizione collegata a questa area di lavoro, è necessario creare un'area di lavoro di Log Analytics nelle sottoscrizioni esterne e monitorare i peering corrispondenti tramite Monitoraggio prestazioni rete. 

    ![Configurazione NPM](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Al termine dell'individuazione, i peering privati individuati sono elencati in una tabella.  

    ![Configurazione NPM](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    Il monitoraggio di questi peering è inizialmente disabilitato. Fare clic su ogni peering che si è interessati a monitorare e configurare il monitoraggio nella visualizzazione dei dettagli sul lato destro.  Fare clic sul pulsante Salva per salvare la configurazione. Per altre informazioni, vedere [Configurare Monitoraggio di ExpressRoute]().  

    Dopo che la configurazione è stata completata, il popolamento dei dati richiede da 30 minuti a un'ora. Mentre la soluzione aggrega i dati dalla rete, nel riquadro della panoramica NPM viene visualizzato *La soluzione richiede una configurazione aggiuntiva*. Dopo che i dati sono stati raccolti e indicizzati, il riquadro della panoramica cambia e mostra il riepilogo dell'integrità della rete. È quindi possibile scegliere di modificare il monitoraggio dei nodi in cui sono installati gli agenti di OMS e delle subnet individuate dall'ambiente 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Modificare le impostazioni di monitoraggio per le subnet e i nodi 

Tutte le subnet con almeno un agente installato sono elencate nella scheda Subnet della pagina di configurazione. 


Per abilitare o disabilitare il monitoraggio di determinate subnet 

1. Selezionare o deselezionare la casella accanto all' **ID subnet** e quindi verificare che sia selezionata o deselezionata l'opzione  **Usa per il monitoraggio** in base alle esigenze. È possibile selezionare o deselezionare più subnet. Se disabilitate, le subnet non vengono monitorate perché gli agenti vengono aggiornati in modo da interrompere l'esecuzione di ping di altri agenti. 
2. Scegliere i nodi da monitorare in una determinata subnet selezionando la subnet dall'elenco e spostando i nodi necessari tra gli elenchi che contengono nodi monitorati e non monitorati. È possibile aggiungere una **descrizione personalizzata** alla subnet. 
3. Fare clic su **Salva** per salvare la configurazione. 

#### <a name="choose-nodes-to-monitor"></a>Selezionare i nodi da monitorare

Tutti i nodi su cui è installato un agente sono elencati nella scheda **Nodi**. 

1. Selezionare o deselezionare i nodi per cui si desidera abilitare o interrompere il monitoraggio. 
2. Fare clic su  **Usa per il monitoraggio** oppure deselezionare l'opzione in base alle esigenze. 
3. Fare clic su **Save**. 


Configurare le funzionalità a cui si è interessati: 
- Configurare [Performance Monitor](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configurare [Monitoraggio endpoint di servizio](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configurare [Monitoraggio di ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati
Monitoraggio delle prestazioni di rete usa pacchetti di handshake TCP SYN-SYNACK-ACK quando viene scelto TCP e ICMP ECHO ICMP ECHO REPLY quando viene scelto ICMP come protocollo per raccogliere informazioni sulle perdite e sulla latenza. Viene anche usato Traceroute per ottenere informazioni sulla topologia.

Nella tabella seguente vengono illustrati i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per il monitoraggio delle prestazioni di rete.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Handshake TCP/messaggi ICMP ECHO ogni 5 secondi, dati inviati ogni 3 minuti |
 

 
La soluzione usa le transazioni sintetiche per valutare l'integrità della rete. Gli agenti OMS installati in vari punti della rete si scambiano pacchetti TCP o ICMP Echo (a seconda del protocollo selezionato per il monitoraggio). Durante il processo, gli agenti vengono informati del tempo di round trip e di eventuali perdite di pacchetti. Periodicamente, ogni agente esegue anche un comando di tracciamento delle route agli altri agenti per trovare tutte le varie route nella rete che devono essere verificate. Usando questi dati, gli agenti possono dedurre i valori relativi a latenza di rete e perdita di pacchetti. I test vengono ripetuti ogni cinque secondi e i dati vengono aggregati per tre minuti dagli agenti prima del caricamento nel servizio Log Analytics. 



>[!NOTE]
> Anche se gli agenti comunicano spesso tra loro, non generano un traffico di rete intenso durante l'esecuzione dei test. Gli agenti si basano solo sui pacchetti di handshake TCP SYN-SYNACK-ACK per determinare la perdita e la latenza, senza scambio di pacchetti di dati. Durante questo processo, gli agenti comunicano tra loro solo quando è necessario e la topologia di comunicazione degli agenti è ottimizzata per ridurre il traffico di rete.

## <a name="using-the-solution"></a>Uso della soluzione 

### <a name="npm-overview-tile"></a>Riquadro Panoramica di NPM 

Dopo aver abilitato la soluzione Monitoraggio prestazioni rete, il riquadro della soluzione nella pagina Panoramica offre una rapida panoramica dell'integrità della rete. 

 ![Riquadro Panoramica di NPM](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Dashboard di monitoraggio delle prestazioni di rete 

La pagina **Principali eventi di integrità della rete** fornisce un elenco dei più recenti avvisi ed eventi di integrità nel sistema e specifica da quanto tempo è attivo l'evento. Un evento o avviso di integrità viene generato quando il valore della metrica scelta (perdita, latenza, tempo di risposta o utilizzo della larghezza di banda) per la regola di monitoraggio supera la soglia. 

La pagina  **Performance Monitor** offre un riepilogo dell'integrità dei collegamenti di rete e dei collegamenti di subnet monitorati dalla soluzione. Il riquadro Topologia indica il numero dei percorsi di rete monitorati nella rete. Facendo clic su questo riquadro, si passa direttamente alla visualizzazione Topologia. 

La pagina  **Monitoraggio endpoint di servizio** offre un riepilogo dell'integrità dei diversi test creati. Il riquadro Topologia indica il numero di endpoint monitorati. Facendo clic su questo riquadro, si passa direttamente alla visualizzazione Topologia.

La pagina  **Monitoraggio di ExpressRoute** offre un riepilogo dell'integrità delle diverse connessioni peering ExpressRoute monitorate dalla soluzione. Il riquadro Topologia indica il numero dei percorsi di rete attraverso i circuiti di ExpressRoute monitorati nella rete. Facendo clic su questo riquadro, si passa direttamente alla visualizzazione Topologia.

La pagina  **Query comuni** contiene una serie di query di ricerca che recuperano direttamente i dati di monitoraggio di rete non elaborati. È possibile usare queste query come punto di partenza per creare le proprie query per l'elaborazione di report personalizzati. 

![Dashboard NPM](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Drill-down in profondità 

È possibile fare clic su diversi collegamenti nel dashboard della soluzione per eseguire il drill-down più approfondito in qualsiasi area di interesse. Ad esempio, quando viene visualizzato un avviso o un collegamento di rete non integro del dashboard, è possibile fare clic per indagare più a fondo. Viene visualizzata una pagina con un elenco di tutti i collegamenti di subnet per un particolare collegamento di rete. È quindi possibile visualizzare lo stato di perdita, latenza e integrità di ogni collegamento di subnet e trovare rapidamente i collegamenti di subnet che causano il problema. È quindi possibile fare clic su  **Visualizza collegamenti nodo** per visualizzare tutti i collegamenti del nodo per il collegamento di subnet non integro. È possibile vedere i singoli collegamenti tra nodi e trovare i collegamenti del nodo non integri. 

È possibile fare clic su  **Visualizza topologia** per vedere la topologia hop per hop delle route tra i nodi di origine e di destinazione. Le route non integre vengono mostrate in rosso ed è possibile visualizzare la latenza generata da ogni hop per consentire di associare rapidamente il problema a una parte specifica della rete. 

 

### <a name="network-state-recorder"></a>Utilità di registrazione dello stato di rete 

Ogni visualizzazione mostra uno snapshot dell'integrità della rete in un determinato momento. Per impostazione predefinita, viene visualizzato lo stato più recente. La barra nella parte superiore della pagina indica il momento per cui viene visualizzato lo stato. È possibile scegliere di tornare indietro nel tempo e visualizzare lo snapshot dell'integrità della rete facendo clic su Azioni sulla barra. È anche possibile scegliere di abilitare o disabilitare l'aggiornamento automatico per qualsiasi pagina mentre si visualizza lo stato più recente. 

 ![Utilità di registrazione dello stato di rete](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafici delle tendenze 

In ogni livello del drill-down, è possibile vedere la tendenza della metrica applicabile: perdita, latenza, tempo di risposta, utilizzo della larghezza di banda. È possibile modificare l'intervallo di tempo per la tendenza da tracciare usando il controllo del tempo nella parte superiore del grafico. 

I grafici delle tendenze mostrano una prospettiva cronologica delle prestazioni di una metrica delle prestazioni. Alcuni problemi di rete sono di natura temporanea e sarebbero difficili da rilevare solo esaminando lo stato corrente della rete. Infatti i problemi possono venire alla luce rapidamente e sparire prima che qualcuno li noti, per poi riapparire in un secondo momento. Tali problemi temporanei inoltre possono essere difficili per gli amministratori delle applicazioni, poiché spesso si presentano come aumenti inspiegabili nel tempo di risposta delle applicazioni, anche quando tutti i componenti delle applicazioni in apparenza vengono eseguiti correttamente. 

È possibile rilevare facilmente questi tipi di problemi esaminando un grafico delle tendenze in cui il problema viene visualizzato come un picco improvviso di latenza di rete o di perdita dei pacchetti. È quindi possibile esaminare il problema usando il controllo di registrazione dello stato di rete per visualizzare lo snapshot della rete e la topologia del momento in cui il problema si è verificato. 

 
![Grafici delle tendenze](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mappa della topologia 

NPM mostra la topologia hop per hop delle route tra l'endpoint di origine e di destinazione, in una mappa della topologia interattiva. È possibile visualizzare la mappa della topologia facendo clic sul riquadro **Topologia** nel dashboard della soluzione o facendo clic sul collegamento **Visualizza topologia** nelle pagine di drill-down.  

La mappa della topologia visualizza la quantità di route presenti tra l'origine e la destinazione e i percorsi intrapresi dai pacchetti di dati. È visibile anche la latenza generata da ogni hop di rete. Tutti i percorsi per cui la latenza totale dei percorsi è superiore alla soglia (impostata nella regola di monitoraggio corrispondente) vengono visualizzati in rosso.  

Quando si fa clic su un nodo o si passa il puntatore del mouse su tale nodo nella mappa topologica, vengono visualizzate le proprietà del nodo, ad esempio FQDN e indirizzo IP. Fare clic su un hop per visualizzarne l'indirizzo IP. È possibile identificare l'hop di rete in cui si è verificato il problema osservando la latenza generata dall'hop. È possibile scegliere di filtrare determinate route usando i filtri nel riquadro azioni comprimibile. È anche possibile semplificare le topologie di rete nascondendo gli hop intermedi con il dispositivo di scorrimento nel riquadro delle azioni. È possibile ingrandire o ridurre la mappa topologica usando la rotellina del mouse. 

Si noti che la topologia illustrata sulla mappa è di livello 3 e non contiene connessioni e dispositivi di livello 2. 

 
![Mappa della topologia](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Ricerca di Log Analytics 

Tutti i dati esposti graficamente attraverso il dashboard NPM e le pagine di drill-down sono disponibili in modalità nativa nella [ricerca di Log Analytics](log-analytics-log-search-new.md). È possibile eseguire l'analisi interattiva dei dati nel repository, correlare dati da origini diverse, creare avvisi personalizzati, creare visualizzazioni personalizzate ed esportare i dati in Excel, PowerBI o un collegamento condivisibile. Nell'area Query comuni del dashboard sono riportate alcune query utili che è possibile usare come punto di partenza per creare query e report. 

 

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti 

**UserVoice**: è possibile pubblicare le proprie idee sulle funzionalità di monitoraggio delle prestazioni di rete che si ritiene possano essere migliorate. Visitare la  [pagina UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Partecipa alla coorte** : l'aggiunta di nuovi clienti alla coorte è sempre motivo di grande interesse. In questo contesto si ottiene l'accesso in anteprima alle nuove funzionalità e si può contribuire a migliorare Monitoraggio prestazioni rete. Per partecipare, compilare questo  [sondaggio rapido](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Passaggi successivi 
- Per altre informazioni, vedere [Performance Monitor](log-analytics-network-performance-monitor-performance-monitor.md), [Monitoraggio endpoint di servizio](log-analytics-network-performance-monitor-performance-monitor.md) e [Monitoraggio di ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 
