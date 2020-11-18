---
title: Monitoraggio connessione | Microsoft Docs
description: Informazioni su come usare il monitoraggio della connessione per monitorare la comunicazione di rete in un ambiente distribuito.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dbb8d508fe824d0264043625c988f43092f3f78
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699237"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Monitoraggio della connettività di rete con monitoraggio connessione

Il monitoraggio della connessione fornisce il monitoraggio della connessione end-to-end unificato in Azure Network Watcher. La funzionalità di monitoraggio della connessione supporta le distribuzioni ibride e cloud di Azure. Network Watcher offre strumenti per monitorare, diagnosticare e visualizzare le metriche relative alla connettività per le distribuzioni di Azure.

Di seguito sono riportati alcuni casi d'uso per il monitoraggio della connessione:

- La VM del server Web front-end comunica con una macchina virtuale del server di database in un'applicazione multilivello. Si vuole controllare la connettività di rete tra le due VM.
- Si vuole che le macchine virtuali nell'area Stati Uniti orientali per il ping delle macchine virtuali nell'area Stati Uniti centrali e si desideri confrontare le latenze di rete tra aree.
- Sono disponibili più siti di Office locali a Seattle, Washington e in Ashburn, Virginia. I siti di Office si connettono a Microsoft 365 URL. Per gli utenti di Microsoft 365 URL, confrontare le latenze tra Seattle e Ashburn.
- Per l'applicazione ibrida è necessaria la connettività a un endpoint di archiviazione di Azure. Il sito locale e l'applicazione Azure si connettono allo stesso endpoint di archiviazione di Azure. Si desidera confrontare le latenze del sito locale con le latenze dell'applicazione Azure.
- Si vuole controllare la connettività tra le installazioni locali e le macchine virtuali di Azure che ospitano l'applicazione cloud.

Il monitoraggio della connessione combina il meglio di due funzionalità: la funzionalità di [monitoraggio della connessione Network Watcher (classica)](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) e il monitoraggio della [connettività del servizio](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)di monitoraggio prestazioni rete (NPM), il [monitoraggio di ExpressRoute](https://docs.microsoft.com/azure/expressroute/how-to-npm)e le funzionalità di [monitoraggio delle prestazioni](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-performance-monitor) .

Di seguito sono riportati alcuni vantaggi del monitoraggio della connessione:

* Esperienza unificata e intuitiva per le esigenze di Azure e di monitoraggio ibrido
* Monitoraggio della connettività tra aree di lavoro
* Frequenze di probe più elevate e visibilità migliore sulle prestazioni della rete
* Avvisi più veloci per le distribuzioni ibride
* Supporto per i controlli di connettività basati su HTTP, TCP e ICMP 
* Metriche e supporto Log Analytics per le configurazioni di test di Azure e non Azure

![Diagramma che illustra il modo in cui il monitoraggio delle connessioni interagisce con le VM di Azure, gli host non Azure, gli endpoint e le posizioni di archiviazione dei dati](./media/connection-monitor-2-preview/hero-graphic.png)

Per iniziare a utilizzare monitoraggio connessione per il monitoraggio, attenersi alla seguente procedura: 

1. Installare gli agenti di monitoraggio.
1. Abilitare Network Watcher sulla sottoscrizione.
1. Creazione di un monitoraggio della connessione.
1. Configurare gli avvisi e l'analisi dei dati.
1. Diagnosticare i problemi nella rete.

Le sezioni seguenti forniscono informazioni dettagliate su questi passaggi.

## <a name="install-monitoring-agents"></a>Installare gli agenti di monitoraggio

Il monitoraggio della connessione si basa sui file eseguibili leggeri per eseguire i controlli di connettività. Supporta i controlli di connettività da ambienti Azure e ambienti locali. Il file eseguibile usato varia a seconda che la macchina virtuale sia ospitata in Azure o in locale.

### <a name="agents-for-azure-virtual-machines"></a>Agenti per macchine virtuali di Azure

Per fare in modo che il monitoraggio delle connessioni riconosca le VM di Azure come origini di monitoraggio, installare l'estensione della macchina virtuale di Network Watcher Agent. Questa estensione è nota anche come *estensione Network Watcher*. Le macchine virtuali di Azure richiedono l'estensione per attivare il monitoraggio end-to-end e altre funzionalità avanzate. 

È possibile installare l'estensione Network Watcher quando si [Crea una macchina virtuale](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). È anche possibile installare, configurare e risolvere i problemi relativi all'estensione Network Watcher per [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Le regole per un gruppo di sicurezza di rete (NSG) o un firewall possono bloccare la comunicazione tra l'origine e la destinazione. Il monitoraggio della connessione rileva questo problema e lo Mostra come un messaggio di diagnostica nella topologia. Per abilitare il monitoraggio della connessione, assicurarsi che le regole NSG e firewall consentano i pacchetti su TCP o ICMP tra l'origine e la destinazione.

### <a name="agents-for-on-premises-machines"></a>Agenti per computer locali

Per fare in modo che il monitoraggio della connessione riconosca i computer locali come origini per il monitoraggio, installare l'agente Log Analytics nei computer. Abilitare quindi la soluzione Monitoraggio prestazioni rete. Questi agenti sono collegati alle aree di lavoro Log Analytics, quindi è necessario configurare l'ID dell'area di lavoro e la chiave primaria prima che gli agenti possano avviare il monitoraggio.

Per installare l'agente di Log Analytics per i computer Windows, vedere [estensione macchina virtuale di monitoraggio di Azure per Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Se il percorso include firewall o appliance virtuali di rete (appliance virtuali), assicurarsi che la destinazione sia raggiungibile.

## <a name="enable-network-watcher-on-your-subscription"></a>Abilitare Network Watcher sulla sottoscrizione

Tutte le sottoscrizioni con una rete virtuale sono abilitate con Network Watcher. Quando si crea una rete virtuale nella sottoscrizione, Network Watcher viene abilitata automaticamente nell'area e nella sottoscrizione della rete virtuale. Questa abilitazione automatica non influisce sulle risorse o è soggetta a un addebito. Verificare che Network Watcher non sia disabilitato in modo esplicito nella sottoscrizione. 

Per ulteriori informazioni, vedere [Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione 

Il monitoraggio della connessione monitora la comunicazione a intervalli regolari. Indica le modifiche apportate alla raggiungibilità e alla latenza. È anche possibile controllare la topologia di rete corrente e cronologica tra gli agenti di origine e gli endpoint di destinazione.

Le origini possono essere macchine virtuali di Azure o computer locali in cui è installato un agente di monitoraggio. Gli endpoint di destinazione possono essere Microsoft 365 URL, URL Dynamics 365, URL personalizzati, ID risorsa VM di Azure, IPv4, IPv6, FQDN o qualsiasi nome di dominio.

### <a name="access-connection-monitor"></a>Monitoraggio connessione di accesso

1. Nella home page portale di Azure passare a **Network Watcher**.
1. Nella sezione **monitoraggio** a sinistra selezionare **monitoraggio connessione**.
1. Vengono visualizzati tutti i monitoraggi connessione creati in monitoraggio connessione. Per visualizzare i monitoraggi connessioni creati nell'esperienza classica di monitoraggio connessione, passare alla scheda **monitoraggio connessione** .
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot che mostra i monitoraggi connessione creati in monitoraggio connessione" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

Nei monitoraggi connessione creati in monitoraggio connessione è possibile aggiungere sia i computer locali sia le VM di Azure come origini. Questi monitoraggi di connessione possono inoltre monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o indirizzo IP.

Il monitoraggio della connessione include le seguenti entità:

* **Risorsa di monitoraggio della connessione** : una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà di una risorsa di monitoraggio della connessione.
* **Endpoint** : un'origine o una destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono macchine virtuali di Azure, agenti locali, URL e indirizzi IP.
* **Configurazione di test** : una configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test** : il gruppo che contiene gli endpoint di origine, gli endpoint di destinazione e le configurazioni di test. Un monitoraggio connessione può contenere più di un gruppo di test.
* **Test** : la combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di round trip (RTT).

 ![Diagramma che mostra un monitoraggio della connessione, che definisce la relazione tra i gruppi di test e i test](./media/connection-monitor-2-preview/cm-tg-2.png)

È possibile creare un monitoraggio della connessione utilizzando [portale di Azure](connection-monitor-preview-create-using-portal.md) o [ARMClient](connection-monitor-preview-create-using-arm-client.md)

Tutte le origini, le destinazioni e le configurazioni di test aggiunte a un gruppo di test vengono suddivise in singoli test. Ecco un esempio di come vengono suddivise le origini e le destinazioni:

* Gruppo di test: TG1
* Origini: 3 (A, B, C)
* Destinazioni: 2 (D, E)
* Configurazioni di test: 2 (config 1, config 2)
* Totale test creati: 12

| Numero di test | Source (Sorgente) | Destination | Configurazione di test |
| --- | --- | --- | --- |
| 1 | A | D | Configurazione 1 |
| 2 | A | D | Configurazione 2 |
| 3 | A | E | Configurazione 1 |
| 4 | A | E | Configurazione 2 |
| 5 | B | D | Configurazione 1 |
| 6 | B | D | Configurazione 2 |
| 7 | B | E | Configurazione 1 |
| 8 | B | E | Configurazione 2 |
| 9 | C | D | Configurazione 1 |
| 10 | C | D | Configurazione 2 |
| 11 | C | E | Configurazione 1 |
| 12 | C | E | Configurazione 2 |

### <a name="scale-limits"></a> Limiti di scalabilità

I monitoraggi connessione presentano i limiti di scalabilità seguenti:

* Numero massimo di monitoraggi di connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per connessione: 20
* Numero massimo di origini e destinazioni per monitoraggio connessione: 100
* Numero massimo di configurazioni di test per ogni monitoraggio connessione: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analizzare i dati di monitoraggio e impostare gli avvisi

Dopo aver creato un monitoraggio della connessione, le origini controllano la connettività alle destinazioni in base alla configurazione di test.

### <a name="checks-in-a-test"></a>Verifica in un test

In base al protocollo scelto nella configurazione di test, il monitoraggio della connessione esegue una serie di controlli per la coppia di origine-destinazione. I controlli vengono eseguiti in base alla frequenza di test scelta.

Se si usa HTTP, il servizio calcola il numero di risposte HTTP che hanno restituito un codice di risposta valido. I codici di risposta validi possono essere impostati tramite PowerShell e l'interfaccia della riga di comando. Il risultato determina la percentuale di controlli non riusciti. Per calcolare RTT, il servizio misura il tempo tra una chiamata HTTP e la risposta.

Se si usa TCP o ICMP, il servizio calcola la percentuale di perdita dei pacchetti per determinare la percentuale di verifiche non riuscite. Per calcolare RTT, il servizio misura il tempo impiegato per ricevere il riconoscimento (ACK) per i pacchetti inviati. Se sono stati abilitati i dati traceroute per i test di rete, è possibile visualizzare la perdita e la latenza hop-by-hop per la rete locale.

### <a name="states-of-a-test"></a>Stati di un test

In base ai dati restituiti dai controlli, i test possono avere gli Stati seguenti:

* **Pass** : i valori effettivi per la percentuale di controlli non riusciti e RTT sono compresi entro le soglie specificate.
* **Errore** : i valori effettivi per la percentuale di controlli non riusciti o RTT hanno superato le soglie specificate. Se non viene specificata alcuna soglia, un test raggiunge lo stato di errore quando la percentuale di verifiche non riuscite è 100.
* **Avviso** : 
     * Se viene specificata la soglia e il monitoraggio della connessione osserva i controlli non riusciti percentuale più del 80% della soglia, il test viene contrassegnato come avviso.
     * In assenza di soglie specificate, il monitoraggio della connessione assegna automaticamente una soglia. Quando viene superata tale soglia, lo stato del test passa ad avviso.Per round trip tempo nei test TCP o ICMP, la soglia è 750msec. Per i controlli non riusciti percentuale, la soglia è 10%. 
* **Indeterminato**   -Nessun dato nell'area di lavoro Log Analytics.Controllare la metrica. 
* **Non in esecuzione**   -Disabilitato disabilitando il gruppo di test  

### <a name="data-collection-analysis-and-alerts"></a>Raccolta dati, analisi e avvisi

I dati raccolti da monitoraggio connessione vengono archiviati nell'area di lavoro Log Analytics. Questa area di lavoro viene configurata durante la creazione del monitoraggio connessione. 

I dati di monitoraggio sono disponibili anche nelle metriche di monitoraggio di Azure. È possibile utilizzare Log Analytics per proteggere i dati di monitoraggio fino a quando si desidera. Monitoraggio di Azure archivia le metriche solo per 30 giorni per impostazione predefinita. 

È possibile [impostare avvisi basati sulle metriche per i dati](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Dashboard di monitoraggio

Nei dashboard di monitoraggio viene visualizzato un elenco dei monitoraggi connessione a cui è possibile accedere per sottoscrizioni, aree, timestamp, origini e tipi di destinazione.

Quando si passa a monitoraggio connessione da Network Watcher, è possibile visualizzare i dati in base a:

* **Connection Monitor** : elenco di tutti i monitoraggi connessioni creati per sottoscrizioni, aree, timestamp, origini e tipi di destinazione. Questa vista è quella predefinita.
* **Gruppi di test** : elenco di tutti i gruppi di test creati per le sottoscrizioni, le aree, i timestamp, le origini e i tipi di destinazione. Questi gruppi di test non vengono filtrati in base ai monitoraggi connessione.
* **Test** : elenco di tutti i test eseguiti per sottoscrizioni, aree, timestamp, origini e tipi di destinazione. Questi test non vengono filtrati in base ai monitoraggi connessione o ai gruppi di test.

Nella figura seguente, le tre visualizzazioni dati sono indicate dalla freccia 1.

Nel dashboard è possibile espandere ogni monitoraggio della connessione per visualizzare i gruppi di test. È quindi possibile espandere ogni gruppo di test per visualizzare i test in esecuzione. 

È possibile filtrare un elenco in base a:

* **Filtri di primo livello** : ricerca nell'elenco per testo, tipo di entità (monitoraggio connessione, gruppo di test o test) timestamp e ambito. L'ambito include sottoscrizioni, aree, origini e tipi di destinazione. Vedere la casella 1 nell'immagine seguente.
* **Filtri basati sullo stato** : filtrare in base allo stato del monitoraggio della connessione, del gruppo di test o del test. Vedere la casella 2 nell'immagine seguente.
* **Filtro basato su avviso** : consente di filtrare in base agli avvisi generati sulla risorsa di monitoraggio della connessione. Vedere la casella 3 nell'immagine seguente.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Screenshot che illustra come filtrare le visualizzazioni dei monitoraggi connessione, i gruppi di test e i test nel monitoraggio della connessione " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Ad esempio, per esaminare tutti i test nel monitoraggio della connessione in cui l'indirizzo IP di origine è 10.192.64.56:
1. Modificare la visualizzazione in **test**.
1. Nel campo di ricerca digitare *10.192.64.56*
1. In **ambito** nel filtro di primo livello selezionare **origini**.

Per visualizzare solo i test non superati nel monitoraggio della connessione in cui l'indirizzo IP di origine è 10.192.64.56:
1. Modificare la visualizzazione in **test**.
1. Per il filtro basato sullo stato, selezionare **esito negativo**.
1. Nel campo di ricerca digitare *10.192.64.56*
1. In **ambito** nel filtro di primo livello selezionare **origini**.

Per visualizzare solo i test non superati nel monitoraggio della connessione in cui la destinazione è outlook.office365.com:
1. Modificare la visualizzazione da **testare**.
1. Per il filtro basato sullo stato, selezionare **esito negativo**.
1. Nel campo di ricerca immettere *Outlook.office365.com*
1. In **ambito** nel filtro di primo livello selezionare **destinazioni**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Screenshot che mostra una visualizzazione filtrata per mostrare solo i test non superati per la destinazione Outlook.Office365.com" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Per individuare la causa dell'errore di un monitoraggio della connessione o di un gruppo di test o di un test, fare clic sulla colonna motivo.  Indica quale soglia (i controlli non riusciti% o RTT) è stata violata e i messaggi di diagnostica correlati
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Screenshot che illustra il motivo dell'errore per un monitoraggio della connessione, un gruppo di test o di test" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Per visualizzare le tendenze in RTT e la percentuale di verifiche non riuscite per un monitoraggio della connessione:
1. Selezionare il monitoraggio della connessione che si desidera analizzare.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Screenshot che mostra le metriche per un monitoraggio della connessione, visualizzate dal gruppo di test" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Vengono visualizzate le sezioni seguenti  
    1. Essentials-proprietà specifiche delle risorse del monitoraggio della connessione selezionato 
    1. Riepilogo 
        1. Linee di tendenza aggregate per RTT e percentuale di verifiche non riuscite per tutti i test nel monitoraggio della connessione. È possibile impostare un periodo di tempo specifico per visualizzare i dettagli.
        1. Primi 5 in gruppi di test, origini e destinazioni in base al RTT o alla percentuale di verifiche non riuscite. 
    1. Schede per gruppi di test, origini, destinazioni e configurazioni di test: elenca gruppi di test, origini o destinazioni nel monitoraggio della connessione. Test di controllo non riusciti, RTT aggregati e controlli non riusciti% valori.  È anche possibile tornare indietro nel tempo per visualizzare i dati. 
    1. Problemi a livello di hop per ogni test nel monitoraggio della connessione. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Screenshot che mostra le metriche per un monitoraggio della connessione, visualizzate dalla parte 2 del gruppo di test" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. È possibile
    * Fare clic su Visualizza tutti i test per visualizzare tutti i test nel monitoraggio della connessione
    * Fare clic su Visualizza tutti i gruppi di test, le configurazioni di test, le origini e le destinazioni per visualizzare i dettagli specifici di ognuno di essi. 
    * Scegliere un gruppo di test, configurazione di test, origine o destinazione per visualizzare tutti i test nell'entità.

1. Dalla vista Visualizza tutti i test è possibile:
    * Selezionare test e fare clic su Confronta.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Screenshot che mostra il confronto tra due test" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Usare il cluster per espandere risorse composte come VNET, subnet per le risorse figlio
    * Consente di visualizzare la topologia di tutti i test facendo clic su topologia.

Per visualizzare le tendenze in RTT e la percentuale di verifiche non riuscite per un gruppo di test:
1. Selezionare il gruppo di test che si desidera analizzare. 
1. Si otterrà una visualizzazione simile a monitoraggio connessione-Essentials, riepilogo, tabella per gruppi di test, origini, destinazioni e configurazioni di test. Esplorarli come per un monitoraggio della connessione

Per visualizzare le tendenze in RTT e la percentuale di verifiche non riuscite per un test:
1. Selezionare il test che si desidera analizzare. Si noterà che la topologia di rete e i grafici di tendenza end-to-end per i controlli non sono riusciti% e round trip tempo. Per visualizzare i problemi identificati, nella topologia selezionare qualsiasi hop nel percorso. Questi hop sono risorse di Azure. Questa funzionalità non è attualmente disponibile per le reti locali

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Screenshot che mostra la visualizzazione della topologia di un test" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Query di log in Log Analytics

Usare Log Analytics per creare visualizzazioni personalizzate dei dati di monitoraggio. Tutti i dati visualizzati dall'interfaccia utente sono Log Analytics. È possibile analizzare in modo interattivo i dati nel repository. Correlare i dati da Integrità agente o da altre soluzioni basate su Log Analytics. Esportare i dati in Excel o Power BI oppure creare un collegamento condivisibile.

#### <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

Nei monitoraggi connessioni creati prima dell'esperienza di monitoraggio della connessione sono disponibili tutte e quattro le metriche:% probe non riusciti, AverageRoundtripMs, ChecksFailedPercent (anteprima) e RoundTripTimeMs (anteprima). Nei monitoraggi connessioni creati nell'esperienza di monitoraggio della connessione, i dati sono disponibili solo per le metriche contrassegnate con *(anteprima)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Screenshot che mostra le metriche nel monitoraggio della connessione" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Quando si usano le metriche, impostare il tipo di risorsa come Microsoft. Network/networkWatchers/connectionMonitors

| Metrica | Nome visualizzato | Unità | Tipo di aggregazione | Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % di probe non riusciti | Percentuale | Media | Percentuale di probe di monitoraggio della connettività non riuscita. | Nessuna dimensione |
| AverageRoundtripMs | Avg. Tempo di round trip (ms) | Millisecondi | Media | RTT di rete medio per i probe di monitoraggio della connettività inviati tra l'origine e la destinazione. |             Nessuna dimensione |
| ChecksFailedPercent (anteprima) | % Controlli non riusciti (anteprima) | Percentuale | Media | Percentuale di verifiche non riuscite per un test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocollo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Area |
| RoundTripTimeMs (anteprima) | Tempo di round trip (MS) (anteprima) | Millisecondi | Media | RTT per i controlli inviati tra l'origine e la destinazione. Questo valore non è medio. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocollo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Area |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Avvisi basati su metriche per il monitoraggio della connessione

È possibile creare avvisi sulle metriche nei monitoraggi connessione utilizzando i metodi seguenti 

1. Da monitoraggio connessione, durante la creazione del monitoraggio connessione [utilizzando portale di Azure](connection-monitor-preview-create-using-portal.md#) 
1. Dal monitoraggio della connessione, usando "Configura avvisi" nel dashboard 
1. Da monitoraggio di Azure: per creare un avviso in monitoraggio di Azure: 
    1. Scegliere la risorsa di monitoraggio della connessione creata in monitoraggio connessione.
    1. Assicurarsi che la **metrica** venga visualizzata come tipo di segnale per il monitoraggio della connessione.
    1. In **Aggiungi condizione** per il **nome del segnale** selezionare **ChecksFailedPercent (anteprima)** o **RoundTripTimeMs (anteprima)**.
    1. Per **tipo di segnale** scegliere **metrica**. Ad esempio, selezionare **ChecksFailedPercent (anteprima)**.
    1. Vengono elencate tutte le dimensioni della metrica. Scegliere il nome della dimensione e il valore della dimensione. Selezionare, ad esempio, **indirizzo di origine** e quindi immettere l'indirizzo IP di qualsiasi origine nel monitoraggio della connessione.
    1. In **logica avvisi** compilare i dettagli seguenti:
        * **Tipo di condizione**: **static**.
        * **Condizione** e **soglia**.
        * **Granularità e frequenza di valutazione dell'aggregazione**: il monitoraggio della connessione aggiorna i dati ogni minuto.
    1. In **azioni** scegliere il gruppo di azioni.
    1. Fornire i dettagli dell'avviso.
    1. Creare la regola di avviso.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Screenshot che illustra l'area Crea regola in monitoraggio di Azure. L'indirizzo di origine e il nome dell'endpoint di origine sono evidenziati" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnosticare i problemi nella rete

Il monitoraggio delle connessioni consente di diagnosticare i problemi nel monitoraggio della connessione e nella rete. I problemi nella rete ibrida vengono rilevati dagli agenti Log Analytics installati in precedenza. I problemi in Azure vengono rilevati dall'estensione Network Watcher. 

È possibile visualizzare i problemi nella rete di Azure nella topologia di rete.

Per le reti le cui origini sono macchine virtuali locali, è possibile rilevare i problemi seguenti:

* Timeout della richiesta.
* Endpoint non risolto da DNS: temporaneo o persistente. URL non valido.
* Non sono stati trovati host.
* L'origine non è in grado di connettersi alla destinazione. Destinazione non raggiungibile tramite ICMP.
* Problemi relativi ai certificati: 
    * Certificato client necessario per autenticare l'agente. 
    * L'elenco di rilocazione del certificato non è accessibile. 
    * Il nome host dell'endpoint non corrisponde al soggetto o al nome alternativo del soggetto del certificato. 
    * Il certificato radice non è presente nell'archivio Autorità di certificazione attendibili del computer locale dell'origine. 
    * Il certificato SSL è scaduto, non valido, revocato o incompatibile.

Per le reti le cui origini sono macchine virtuali di Azure, è possibile rilevare i problemi seguenti:

* Problemi dell'agente:
    * Agente arrestato.
    * Risoluzione DNS non riuscita.
    * Nessuna applicazione o listener in ascolto sulla porta di destinazione.
    * Non è stato possibile aprire il socket.
* Problemi relativi allo stato della macchina virtuale: 
    * Avvio in corso
    * Stopping
    * Arrestato
    * Deallocazione
    * Deallocato
    * Riavvio
    * Non allocato
* Voce della tabella ARP mancante.
* Il traffico è stato bloccato a causa di problemi del firewall locale o di regole NSG.
* Problemi relativi al gateway di rete virtuale: 
    * Route mancanti.
    * Il tunnel tra due gateway è disconnesso o mancante.
    * Il secondo gateway non è stato trovato dal tunnel.
    * Non sono state trovate informazioni sul peering.
* Route mancante in Microsoft Edge.
* Il traffico è stato interrotto a causa di route di sistema o UDR.
* BGP non è abilitato nella connessione gateway.
* Il probe DIP è inattivo nel servizio di bilanciamento del carico.

## <a name="next-steps"></a>Passaggi successivi
    
   * Informazioni [su come creare un monitoraggio connessione utilizzando portale di Azure](connection-monitor-preview-create-using-portal.md)  
   * Informazioni [su come creare un monitoraggio della connessione tramite ARMClient](connection-monitor-preview-create-using-arm-client.md)  
