---
title: Soluzione di monitoraggio delle prestazioni di rete in Azure Log Analytics | Microsoft Docs
description: Il monitoraggio delle prestazioni di rete in Azure Log Analytics consente di monitorare le prestazioni delle reti quasi in tempo reale per rilevare e trovare i colli di bottiglia delle prestazioni di rete.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: banders
ms.openlocfilehash: d5d5ec1b524fa455c8d2231c7c16fd7942f713c4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Soluzione di monitoraggio delle prestazioni di rete in Log Analytics

![Simbolo di Monitoraggio prestazioni rete](./media/log-analytics-network-performance-monitor/npm-symbol.png)

Questo documento descrive come configurare e usare la soluzione di monitoraggio delle prestazioni di rete in Log Analytics, che consente di monitorare le prestazioni delle reti quasi in tempo reale per rilevare e trovare i colli di bottiglia delle prestazioni di rete. Con la soluzione di monitoraggio delle prestazioni di rete, è possibile monitorare la perdita e la latenza tra due reti, subnet o server. Il monitoraggio delle prestazioni di rete rileva problemi di rete come il blackholing del traffico, gli errori di routing e i problemi che i metodi di monitoraggio della rete tradizionali non sono in grado di rilevare. Il monitoraggio delle prestazioni di rete genera avvisi e invia notifiche quando viene superata una soglia per un collegamento di rete. Queste soglie possono essere acquisite automaticamente dal sistema oppure possono essere configurate in modo che usino regole di avviso personalizzate. Il monitoraggio delle prestazioni di rete garantisce una tempestiva individuazione dei problemi legati alle prestazioni di rete e localizza l'origine del problema in un particolare segmento di rete o un dispositivo.

È possibile rilevare i problemi di rete con il dashboard della soluzione, che mostra un riepilogo delle informazioni sulla rete, tra cui i recenti eventi di integrità di rete, i collegamenti di rete danneggiati e i collegamenti di subnet con problemi di grande perdita di pacchetti e di latenza elevata. È possibile eseguire il drill-down in un collegamento di rete per visualizzare lo stato di integrità corrente dei collegamenti di subnet, nonché dei collegamenti tra nodi. È anche possibile visualizzare le tendenze cronologiche di perdita e latenza a livello di rete, subnet e tra nodi. È possibile rilevare i problemi di rete temporanei visualizzando i grafici sulle tendenze cronologiche relative a perdita di pacchetti e latenza e individuare colli di bottiglia di rete su una mappa topologica. Il grafico interattivo della topologia consente di visualizzare le route di rete hop-by-hop e determinare l'origine del problema. Come in qualsiasi altra soluzione, è possibile usare la ricerca nei log per i vari requisiti di analisi al fine di creare report personalizzati in base ai dati raccolti dal monitoraggio delle prestazioni di rete.

La soluzione usa le transazioni sintetiche come meccanismo principale per rilevare gli errori di rete. Pertanto, è possibile usarla senza tener conto del particolare fornitore o modello di un dispositivo di rete. Funziona in ambienti ibridi, cloud (IaaS) e locali. La soluzione rileva automaticamente la topologia di rete e le varie route della rete.

I tipici prodotti di rete si concentrano sul monitoraggio dell'integrità dei dispositivi di rete (router, commutatori e così via) ma non forniscono informazioni approfondite sulla qualità effettiva della connettività di rete tra due punti, cosa che invece il monitoraggio delle prestazioni di rete fa.

### <a name="using-the-solution-standalone"></a>Uso della soluzione autonoma
Se si desidera monitorare la qualità delle connessioni di rete tra carichi di lavoro, reti, data center o uffici remoti critici, è possibile usare la soluzione di monitoraggio delle prestazioni di rete da sola per monitorare l'integrità della connettività tra:

* più data center o uffici remoti connessi con tramite una rete pubblica o privata
* carichi di lavoro critici che eseguono applicazioni line-of-business
* servizi di cloud pubblico come Microsoft Azure o Amazon Web Services (AWS) e reti locali, se si dispone di IaaS (VM) disponibile e di gateway configurati in modo da consentire la comunicazione tra reti locali e cloud
* Reti locali e Azure quando si usa ExpressRoute

### <a name="using-the-solution-with-other-networking-tools"></a>Uso della soluzione con altri strumenti di rete
Se si desidera monitorare un'applicazione line-of-business, è possibile usare la soluzione di monitoraggio delle prestazioni di rete come soluzione complementare ad altri strumenti di rete. Una rete lenta può causare applicazioni lente e il monitoraggio delle prestazioni di rete può aiutare ad analizzare i problemi legati alle prestazioni delle applicazioni causati da problemi di rete sottostanti. Poiché la soluzione non richiede accesso ai dispositivi di rete, non è necessario che l'amministratore dell'applicazione si affidi a un team di rete per fornire informazioni su come la rete influisce sulle applicazioni.

Inoltre, se si investe già in altri strumenti di monitoraggio della rete, la soluzione può completarli perché le più tradizionali soluzioni di monitoraggio di rete non forniscono informazioni dettagliate sulle metriche delle prestazioni di rete end-to-end come perdita e la latenza.  La soluzione di monitoraggio delle prestazioni di rete aiuterà a colmare il divario.

## <a name="installing-and-configuring-agents-for-the-solution"></a>Installazione e configurazione degli agenti per la soluzione
Usare i processi di base per installare gli agenti descritti in [Connettere computer Windows a Log Analytics](log-analytics-windows-agent.md) e [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Per poter avere dati sufficienti per individuare e monitorare le risorse di rete, è necessario installare almeno 2 agenti. In caso contrario, la soluzione rimarrà in uno stato di configurazione fino a quando non verranno installati e configurati altri agenti.
>
>

### <a name="where-to-install-the-agents"></a>Dove installare gli agenti
Prima di installare gli agenti, prendere in considerazione la topologia della rete e le parti della rete che si desidera monitorare. Si consiglia di installare più di un agente per ogni subnet che si desidera monitorare. In altre parole, per ogni subnet che si desidera monitorare, selezionare due o più server o VM e installarvi l'agente.

Se non si conosce la topologia della propria rete, installare gli agenti sui server con carichi di lavoro critici in cui si desidera monitorare le prestazioni di rete. Ad esempio, si potrebbe voler tenere traccia di una connessione di rete tra un server Web e un server che esegue SQL Server. In questo esempio, viene installato un agente su entrambi i server.

Gli agenti monitorano la connettività di rete (collegamenti) tra gli host, non gli host stessi. Pertanto, per monitorare un collegamento di rete, è necessario installare gli agenti su entrambi gli endpoint del collegamento.

### <a name="configure-agents"></a>Configurazione degli agenti

Se si prevede di usare il protocollo ICMP per le transazioni sintetiche, è necessario abilitare le seguenti regole firewall per usare in modo affidabile il protocollo:

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Se si vuole usare il protocollo TCP, è necessario aprire le porte del firewall per questi computer per assicurarsi che gli agenti possano comunicare. È necessario scaricare ed eseguire lo [script di PowerShell EnableRules.ps1](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) senza parametri in una finestra di PowerShell con privilegi amministrativi.

Lo script crea le chiavi del Registro di sistema richieste dal monitoraggio delle prestazioni di rete e crea regole del firewall Windows per consentire agli agenti di creare connessioni TCP tra loro. Le chiavi del Registro di sistema create dallo script inoltre specificano se registrare i log di debug e il percorso al file di log. Inoltre definiscono la porta TCP agente usata per la comunicazione. I valori per queste chiavi vengono impostati automaticamente dallo script, quindi non bisogna modificare manualmente le chiavi.

La porta aperta per impostazione predefinita è 8084. È possibile usare una porta personalizzata fornendo il parametro `portNumber` allo script. Tuttavia, è necessario usare la stessa porta su tutti i computer in cui viene eseguito lo script.

> [!NOTE]
> Lo script EnableRules.ps1 configura le regole del firewall Windows solo nel computer in cui viene eseguito lo script. Se si dispone di un firewall di rete, è necessario assicurarsi che consenta il traffico destinato alla porta TCP usata dal monitoraggio delle prestazioni di rete.
>
>

## <a name="configuring-the-solution"></a>Configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

1. La soluzione di monitoraggio delle prestazioni di rete acquisisce i dati dai computer che eseguono Windows Server 2008 SP 1 o versioni successive o Windows 7 SP1 o versioni successive, che sono gli stessi requisiti di Microsoft Monitoring Agent (MMA). Gli agenti NPM possono essere eseguiti anche in sistemi operativi desktop/client Windows (Windows 10, Windows 8.1, Windows 8 e Windows 7).
    >[!NOTE]
    >Gli agenti per i sistemi operativi server Windows supportano sia TCP che ICMP come protocolli per la transazione sintetica. Tuttavia, gli agenti per i sistemi operativi client Windows supportano solo TCP come protocollo per la transazione sintetica.

2. Aggiungere la soluzione di monitoraggio delle prestazioni di rete all'area di lavoro da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) o usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).<br><br> ![Simbolo del monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-symbol.png)  
3. Nel portale di OMS si noterà un nuovo riquadro chiamato **Monitoraggio delle prestazioni di rete** con il messaggio *La soluzione richiede configurazione aggiuntiva*. Fare clic sul riquadro per passare alla scheda **Distribuzione** e selezionare il protocollo da usare per eseguire le transazioni sintetiche per monitorare la rete.  Per informazioni su come scegliere il protocollo adatto per la rete, vedere [Scegliere il protocollo ICMP o TCP corretto](#choose-the-right-protocol-icmp-or-tcp).<br><br> ![la soluzione richiede la selezione del protocollo](media/log-analytics-network-performance-monitor/log-analytics-netmon-perf-welcome.png)<br><br>

4. Dopo aver scelto il protocollo si verrà reindirizzati alla pagina della **panoramica di OMS**. Mentre la soluzione aggrega i dati dalla rete, il riquadro della panoramica di Monitoraggio prestazioni rete mostra il messaggio *Aggregazione dei dati in corso*.<br><br> ![la soluzione sta aggregando i dati](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-01.png)<br><br>
5. Una volta che i dati sono stati raccolti e indicizzati, il riquadro della panoramica cambia e indica che è necessario eseguire una configurazione aggiuntiva.<br><br> ![il riquadro della soluzione richiede configurazioni aggiuntive](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-02.png)<br><br>
6. Fare clic sul riquadro e iniziare a configurare la soluzione con i seguenti passaggi.

### <a name="create-new-networks"></a>Creazione di nuove reti
Una rete in Monitoraggio prestazioni rete è un contenitore per subnet. È possibile creare una rete con un nome descrittivo e aggiungere le subnet in base alla logica di business. Ad esempio, si può creare una rete denominata *Londra* e aggiungervi tutte le subnet del data center Londra oppure una rete denominata *FrontEndContoso* e aggiungervi tutte le subnet che servono il front-end dell'app denominata Contoso a tale rete.
Nella pagina di configurazione si noterà una rete chiamata **Predefinita** nella scheda Reti. Se non sono state create reti, tutte le subnet rilevate automaticamente vengono messe nella rete Predefinita.
Tutte le volte che si crea una rete, viene aggiunta una subnet che viene rimossa dalla rete predefinita. Se si elimina una rete, tutte le sue subnet vengono restituite automaticamente alla rete predefinita.
La rete Predefinita è perciò il contenitore per tutte le subnet che non sono contenute in una rete definita dall'utente. Non è possibile modificare o eliminare la rete predefinita, che rimane sempre nel sistema. Tuttavia è possibile creare tutte le reti personalizzate necessarie.
Nella maggior parte dei casi, le subnet all'interno dell'organizzazione verranno disposte in più di una rete e sarà necessario creare una o più reti per raggruppare le subnet in base alla logica di business

#### <a name="to-create-a-new-network"></a>Per creare una nuova rete
1. Fare clic su **Aggiungi rete** e quindi digitare il nome e la descrizione della rete.
2. Selezionare una o più subnet e quindi fare clic su **Aggiungi**.
3. Fare clic su **Salva** per salvare la configurazione.<br><br> ![aggiunta della rete](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Attesa dell'aggregazione dei dati
Dopo aver salvato la configurazione per la prima volta, la soluzione inizia a raccogliere informazioni su latenza e perdita di pacchetti di rete tra i nodi in cui sono installati gli agenti. Questo processo può richiedere del tempo, talvolta oltre 30 minuti. In questo stato il riquadro Monitoraggio delle prestazioni di rete nella pagina di panoramica visualizza il messaggio *Aggregazione dei dati in corso*.

![aggregazione dei dati in corso](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

Quando i dati sono stati caricati, il riquadro Monitoraggio delle prestazioni di rete aggiornato mostrerà i dati.

![Riquadro Monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-tile.png)

Fare clic sul riquadro per visualizzare il dashboard di monitoraggio delle prestazioni di rete.

![Dashboard di monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Modifica delle impostazioni di monitoraggio per le subnet
Tutte le subnet in cui è stato installato almeno un agente sono elencate nella scheda **Subnet** della pagina di configurazione.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Per abilitare o disabilitare il monitoraggio per determinate subnet
1. Selezionare o deselezionare la casella accanto all'**ID subnet** quindi verificare che sia selezionata o deselezionata l'opzione **Usa per il monitoraggio** in base alle esigenze. È possibile selezionare o deselezionare più subnet. Se disabilitate, le subnet non vengono monitorate perché gli agenti verranno aggiornati in modo da interrompere l'esecuzione di ping di altri agenti.
2. Scegliere i nodi da monitorare per una determinata subnet selezionando la subnet dall'elenco e spostando i nodi necessari tra gli elenchi che contengono nodi monitorati e non monitorati.
   Se si desidera, è possibile aggiungere una **descrizione** alla subnet.
3. Fare clic su **Salva** per salvare la configurazione.<br><br> ![modifica della subnet](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Selezionare i nodi da monitorare
Tutti i nodi su cui è installato un agente sono elencati nella scheda **Nodi**.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Per abilitare o disabilitare il monitoraggio per i nodi
1. Selezionare o deselezionare i nodi per cui si desidera abilitare o interrompere il monitoraggio.
2. Fare clic su **Usa per il monitoraggio** oppure deselezionare l'opzione in base alle esigenze.
3. Fare clic su **Save**.<br><br> ![abilitazione del monitoraggio dei nodi](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Set di regole di monitoraggio
Monitoraggio prestazioni rete genera eventi di integrità quando viene superata la soglia di prestazioni delle connessioni di rete tra 2 subnet o tra 2 reti. Queste soglie possono essere apprese automaticamente dal sistema oppure è possibile configurare soglie personalizzate.
Il sistema crea automaticamente una regola predefinita che genera un evento di integrità ogni volta che la perdita o la latenza tra qualsiasi coppia di reti o collegamenti di rete/subnet supera la soglia appresa dal sistema. In questo modo la soluzione monitora l'infrastruttura di rete fino a quando non vengono create esplicitamente regole di monitoraggio. Se è abilitata la regola predefinita, tutti i nodi inviano transazioni sintetiche a tutti gli altri nodi che sono stati abilitati per il monitoraggio. La regola predefinita è utile in caso di reti di piccole dimensioni, ad esempio in uno scenario in cui si ha un numero ridotto di server che eseguono un microservizio e si vuole assicurarsi che vi sia connettività fra tutti i server.

>[!NOTE]
>Si consiglia di disabilitare la regola predefinita, e creare regole di monitoraggio personalizzate, soprattutto se si usa un numero elevato di nodi per il monitoraggio di reti di grandi dimensioni. Ciò ridurrà il traffico generato dalla soluzione e aiuterà a organizzare il monitoraggio della rete.

Creare regole di monitoraggio personalizzate in base alla logica di business. Ad esempio, se si vuole monitorare le prestazioni della connettività di rete fra 2 uffici periferici e la sede centrale, raggruppare tutte le subnet dell'ufficio 1 nella rete O1, tutte le subnet dell'ufficio 2 nella rete O2 e tutte le subnet della sede centrale nella rete H. Creare 2 regole di monitoraggio: una tra O1 e H e l'altra tra O2 e H.


#### <a name="to-create-custom-monitoring-rules"></a>Per creare regole di monitoraggio personalizzate
1. Fare clic su **Aggiungi regola** nella scheda **Monitoraggio**, quindi immettere il nome e la descrizione della regola.
2. Selezionare dall'elenco la coppia di collegamenti di rete o subnet da monitorare.
3. Innanzitutto selezionare la rete che contiene una o più subnet di interesse nell'elenco a discesa di rete, quindi selezionare le subnet dal corrispondente elenco a discesa delle subnet.
   Se si desidera monitorare tutte le subnet in un collegamento di rete, scegliere **Tutte le subnet**. Analogamente, selezionare una o più altre subnet di interesse. Inoltre è possibile fare clic su **Aggiungi eccezione** per escludere il monitoraggio per particolari collegamenti di subnet dalla selezione effettuata.
4. [Scegliere tra i protocolli TCP e ICMP](#choose-the-right-protocol-icmp-or-tcp) per l'esecuzione di transazioni sintetiche.
5. Se non si desidera creare eventi di integrità per gli elementi selezionati, deselezionare **Abilita monitoraggio dell'integrità nei collegamenti interessati da questa regola**.
6. Scegliere le condizioni di monitoraggio.
   È possibile impostare soglie personalizzate per la generazione di eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità.
7. Fare clic su **Salva** per salvare la configurazione.<br><br> ![creazione di una regola di monitoraggio personalizzata](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

Dopo avere salvato una regola di monitoraggio, è possibile integrarla con Gestione avvisi facendo clic su **Crea avviso**. Una regola di avviso viene creata automaticamente con la query di ricerca e altri parametri obbligatori automaticamente compilati. Usando una regola di avviso, è possibile ricevere avvisi basati sulla posta elettronica, oltre agli avvisi esistenti in NPM. Gli avvisi possono anche attivare azioni correttive con i runbook oppure possono essere integrati con le soluzione di gestione esistenti usando i webhook. È possibile fare clic su **Manage Alert** (Gestisci avviso) per modificare le impostazioni dell'avviso.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Scegliere il protocollo ICMP o TCP corretto

Monitoraggio prestazioni rete (NPM) usa le transazioni sintetiche per calcolare le metriche delle prestazioni di rete come la latenza di collegamento e la perdita di pacchetti. Per comprendere meglio questo concetto, si consideri un agente NPM connesso a un'estremità di un collegamento di rete. L'agente NPM invia pacchetti probe a un secondo agente NPM connesso a un'altra estremità della rete. Il secondo agente risponde con pacchetti di risposta. Questo processo viene ripetuto più volte. Misurando il numero di risposte e il tempo impiegato per ricevere ogni risposta, il primo agente NPM valuta la latenza del collegamento e la perdita dei pacchetti.

Il formato, le dimensioni e la sequenza di questi pacchetti dipendono dal protocollo scelto durante la creazione delle regole di monitoraggio. In base al protocollo dei pacchetti, i dispositivi di rete intermedi (router, commutatori e così via) potrebbero elaborare questi pacchetti in modo diverso. Di conseguenza, la scelta del protocollo influisce sulla precisione dei risultati. Inoltre la scelta del protocollo determina anche l'eventuale necessità di eseguire alcuni passaggi manuali dopo aver distribuito la soluzione NPM.

NPM offre la possibilità di scegliere tra i protocolli TCP e ICMP per l'esecuzione di transazioni sintetiche.
Se si sceglie il protocollo ICMP quando si crea una regola di transazione sintetica, gli agenti NPM usano messaggi ICMP ECHO per calcolare la latenza di rete e la perdita di pacchetti. ICMP ECHO usa lo stesso messaggio che viene inviato dall'utilità Ping convenzionale. Quando si usa il protocollo TCP, gli agenti NPM inviano pacchetti TCP SYN sulla rete. Questa operazione è seguita dal completamento di un handshake TCP e successivamente dalla rimozione della connessione tramite pacchetti RST.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>Aspetti da considerare prima di scegliere il protocollo
Prima scegliere il protocollo, tenere in considerazione le informazioni seguenti:

##### <a name="discovering-multiple-network-routes"></a>Individuazione di più route di rete
TCP offre una maggiore precisione nell'individuazione di più route e necessita di un minor numero di agenti in ogni subnet. Ad esempio, uno o due agenti con TCP possono individuare tutti i percorsi ridondanti tra subnet. Invece, per ottenere risultati simili con il protocollo ICMP, servono più agenti. Con ICMP, se si dispone di *N* numero di route tra due subnet, è necessario usare più di 5*N* agenti nella subnet di origine o destinazione.

##### <a name="accuracy-of-results"></a>Precisione dei risultati
I router e i commutatori tendono ad assegnare una priorità inferiore ai pacchetti ICMP ECHO rispetto ai pacchetti TCP. In determinati casi, quando è presente un carico eccessivo dei dispositivi di rete, i dati ottenuti dal protocollo TCP riflettono più da vicino la perdita e la latenza sperimentate dalle applicazioni. Ciò si verifica perché la maggior parte del traffico delle applicazioni fluisce attraverso TCP. In questi casi, il protocollo ICMP fornisce risultati meno precisi rispetto a TCP.

##### <a name="firewall-configuration"></a>Configurazione del firewall
Il protocollo TCP prevede che i pacchetti TCP vengano inviati a una porta di destinazione. La porta predefinita usata dagli agenti NPM è la porta 8084, tuttavia è possibile cambiarla quando si configurano gli agenti. È quindi necessario accertarsi che i firewall di rete o regole NSG (in Azure) consentano il traffico sulla porta. È necessario assicurarsi che anche il firewall locale nei computer in cui sono installati gli agenti sia configurato per consentire il traffico su questa porta.

È possibile usare gli script di PowerShell per configurare le regole del firewall nei computer che eseguono Windows. Il firewall di rete deve invece essere configurato manualmente.

Il protocollo ICMP invece non opera tramite porta. Nella maggior parte degli scenari aziendali, il traffico ICMP può fluire attraverso i firewall per consentire l'uso degli strumenti di diagnostica di rete come l'utilità Ping. Pertanto, se è possibile eseguire il Ping da un computer ad un altro, è possibile usare il protocollo ICMP senza dover configurare i firewall manualmente.

> [!NOTE]
> Alcuni firewall possono bloccare ICMP, causando potenzialmente una ritrasmissione che genera un numero elevato di eventi nel sistema di gestione delle informazioni di sicurezza e degli eventi. Accertarsi che il protocollo scelto non sia bloccato da un firewall/NSG di rete, perché in caso contrario Monitoraggio prestazioni rete non riuscirà a monitorare il segmento di rete.  Per questo motivo si consiglia di usare TCP per il monitoraggio. Si deve usare ICMP negli scenari in cui non è possibile usare TCP, ad esempio quando:
> * Si usano nodi basati su client Windows, poiché i raw socket TCP non sono consentiti nel client Windows
> * Il firewall/NSG di rete blocca TCP


#### <a name="how-to-switch-the-protocol"></a>Come cambiare protocollo

Se si sceglie di usare ICMP durante la distribuzione, è possibile passare a TCP in qualsiasi momento modificando la regola di monitoraggio predefinita.

##### <a name="to-edit-the-default-monitoring-rule"></a>Per modificare la regola di monitoraggio predefinita
1.  Passare a **Monitoraggio prestazioni** > **rete** > **Configura** > **Monitor** e quindi fare clic su **Regola predefinita**.
2.  Passare alla sezione **Protocollo** e selezionare il protocollo che si desidera usare.
3.  Fare clic su **Salva** per salvare le modifiche.

Anche se la regola predefinita usa un protocollo specifico, è possibile creare nuove regole con un protocollo diverso. È anche possibile creare una combinazione di regole in cui alcune usano ICMP e altre usano TCP.


## <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati
Monitoraggio delle prestazioni di rete usa pacchetti di handshake TCP SYN-SYNACK-ACK quando viene scelto TCP e ICMP ECHO ICMP ECHO REPLY quando viene scelto ICMP come protocollo per raccogliere informazioni sulle perdite e sulla latenza. Viene anche usato Traceroute per ottenere informazioni sulla topologia.

Nella tabella seguente vengono illustrati i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per il monitoraggio delle prestazioni di rete.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Handshake TCP/messaggi ICMP ECHO ogni 5 secondi, dati inviati ogni 3 minuti |

La soluzione usa le transazioni sintetiche per valutare l'integrità della rete. Gli agenti OMS installati in vari punti della rete si scambiano pacchetti TCP o ICMP Echo (a seconda del protocollo selezionato per il monitoraggio). Durante il processo, gli agenti vengono informati del tempo di round trip e di eventuali perdite di pacchetti. Periodicamente, ogni agente esegue anche un comando di tracciamento delle route agli altri agenti per trovare tutte le varie route nella rete che devono essere verificate. Usando questi dati, gli agenti possono dedurre i valori relativi a latenza di rete e perdita di pacchetti. I test vengono ripetuti ogni cinque secondi e i dati vengono aggregati per tre minuti dagli agenti prima del caricamento nel servizio Log Analytics.

> [!NOTE]
> Anche se gli agenti comunicano spesso tra loro, non generano un traffico di rete intenso durante l'esecuzione dei test. Gli agenti si basano solo sui pacchetti di handshake TCP SYN-SYNACK-ACK per determinare la perdita e la latenza, senza scambio di pacchetti di dati. Durante questo processo, gli agenti comunicano tra loro solo quando è necessario e la topologia di comunicazione degli agenti è ottimizzata per ridurre il traffico di rete.
>
>

## <a name="using-the-solution"></a>Uso della soluzione
In questa sezione vengono illustrate tutte le funzioni del dashboard e si spiega come usarle.

### <a name="solution-overview-tile"></a>Riquadro Panoramica della soluzione
Dopo aver abilitato la soluzione di monitoraggio delle prestazioni di rete, il riquadro della soluzione alla pagina Panoramica di OMS offre una rapida panoramica dell'integrità della rete. Viene visualizzato un grafico ad anello che mostra il numero di collegamenti di subnet integri e non integri. Quando si fa clic sul riquadro, viene aperto il dashboard della soluzione.

![Riquadro Monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Dashboard della soluzione di monitoraggio delle prestazioni di rete
Il pannello **Riepilogo reti** mostra un riepilogo delle reti e le relative dimensioni. Il riepilogo è seguito da riquadri che mostrano il numero totale di collegamenti di rete, collegamenti di subnet e percorsi nel sistema (un percorso è costituito dagli indirizzi IP di due host con gli agenti e tutti gli hop tra di loro).

Il pannello **Principali eventi di integrità della rete** fornisce un elenco dei più recenti avvisi ed eventi di integrità nel sistema e specifica da quanto tempo è attivo l'evento. Un avviso o un evento di integrità viene generato ogni volta che la latenza o la perdita di pacchetti di un collegamento di rete o subnet supera una soglia.

Nel pannello **Principali collegamenti di rete non integri** viene visualizzato un elenco di collegamenti di rete non integri. Si tratta dei collegamenti di rete che al momento presentano uno o più eventi di integrità collaterali.

Nei pannelli **Principali collegamenti di subnet con maggiore perdita** e **Collegamenti di subnet con maggiore latenza** vengono visualizzati i principali collegamenti di subnet rispettivamente in base alla perdita di pacchetti e alla latenza. In determinati collegamenti di rete ci si può aspettare una latenza elevata o una certa perdita di pacchetti. Questi collegamenti vengono visualizzati fra i primi dieci in elenco ma non vengono contrassegnati come non integri.

Il pannello **Query comuni** contiene una serie di query di ricerca che recuperano direttamente i dati di monitoraggio di rete non elaborati. È possibile usare queste query come punto di partenza per creare le proprie query per l'elaborazione di report personalizzati.

![Dashboard di monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Drill-down in profondità
È possibile fare clic su diversi collegamenti nel dashboard della soluzione per eseguire il drill-down più approfondito in qualsiasi area di interesse. Ad esempio, quando viene visualizzato un avviso o un collegamento di rete non integro del dashboard, è possibile fare clic per indagare più a fondo. Si accederà a una pagina con un elenco di tutti i collegamenti di subnet per un particolare collegamento di rete. Sarà quindi possibile visualizzare lo stato di perdita, latenza e l'integrità di ogni collegamento di subnet e trovare rapidamente i collegamenti di subnet che causano il problema. È quindi possibile fare clic su **Visualizza collegamenti nodo** per visualizzare tutti i collegamenti del nodo per il collegamento di subnet non integro. È possibile vedere i singoli collegamenti tra nodi e trovare i collegamenti del nodo non integri.

È possibile fare clic su **Visualizza topologia** per vedere la topologia hop-by-hop delle route tra i nodi di origine e di destinazione. Le route o gli hop non integri vengono mostrati in rosso per consentire di associare rapidamente il problema a una parte specifica della rete.

![drill-down dei dati](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Utilità di registrazione dello stato di rete

Ogni visualizzazione mostra uno snapshot dell'integrità della rete in un determinato momento. Per impostazione predefinita, viene visualizzato lo stato più recente. La barra nella parte superiore della pagina indica il momento per cui viene visualizzato lo stato. È possibile scegliere di tornare indietro nel tempo e visualizzare lo snapshot dell'integrità della rete facendo clic su **Azioni** sulla barra. È anche possibile scegliere di abilitare o disabilitare l'aggiornamento automatico per qualsiasi pagina mentre si visualizza lo stato più recente.

![Stato della rete](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Grafici delle tendenze
In ogni livello del drill-down, è possibile vedere la tendenza di perdite e latenza per un collegamento di rete. I grafici di tendenza sono disponibili anche per i collegamenti della subnet e del nodo. È possibile modificare l'intervallo di tempo per il grafico da tracciare usando il controllo del tempo nella parte superiore del grafico.

I grafici delle tendenze mostrano una prospettiva cronologica delle prestazioni di un collegamento di rete. Alcuni problemi di rete sono di natura temporanea e sarebbero difficili da rilevare solo esaminando lo stato corrente della rete. Infatti i problemi possono venire alla luce rapidamente e sparire prima che qualcuno li noti, per poi riapparire in un secondo momento. Tali problemi temporanei inoltre possono essere difficili per gli amministratori delle applicazioni, poiché spesso si presentano come aumenti inspiegabili nel tempo di risposta delle applicazioni, anche quando tutti i componenti delle applicazioni in apparenza vengono eseguiti correttamente.

È possibile rilevare facilmente questi tipi di problemi esaminando un grafico delle tendenze in cui il problema verrà visualizzato come un picco improvviso di latenza di rete o di perdita dei pacchetti.

![grafico di tendenza](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Mappa topologica hop-by-hop
Il monitoraggio delle prestazioni di rete mostra la topologia hop-by-hop delle route tra due nodi in una mappa topologica interattiva. È possibile visualizzare la mappa topologica selezionando un collegamento del nodo e quindi facendo clic su **Visualizza topologia**. Inoltre, è possibile visualizzare la mappa topologica facendo clic sul riquadro **Percorsi** del dashboard. Quando si fa clic sul riquadro **Percorsi** del dashboard, sarà necessario selezionare i nodi di origine e di destinazione dal pannello a sinistra, quindi fare clic su **Traccia** per tracciare le route tra i due nodi.

La mappa topologica visualizza la quantità di route presenti tra i due nodi e i percorsi intrapresi dai pacchetti di dati. I colli di bottiglia delle prestazioni di rete sono contrassegnati in rosso nella mappa topologica. È possibile individuare connessioni o dispositivi di rete difettosi esaminando gli elementi in rosso sulla mappa topologica.

Quando si fa clic su un nodo o si passa il puntatore del mouse su tale nodo nella mappa topologica, vengono visualizzate le proprietà del nodo, ad esempio FQDN e indirizzo IP. Fare clic su un hop per visualizzarne l'indirizzo IP. È possibile scegliere di filtrare determinate route usando i filtri nel riquadro azioni comprimibile. È anche possibile semplificare le topologie di rete nascondendo gli hop intermedi con il dispositivo di scorrimento nel riquadro azioni. È possibile ingrandire o ridurre la mappa topologica usando la rotellina del mouse.

Si noti che la topologia illustrata sulla mappa è di livello 3 e non contiene connessioni e dispositivi di livello 2.

![mappa topologica hop-by-hop](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Individuazione degli errori
Il monitoraggio delle prestazioni di rete è in grado di individuare i colli di bottiglia di rete senza connettersi a dispositivi di rete. In base ai dati raccolti dalla rete e applicando algoritmi avanzati sul grafico della rete, il monitoraggio delle prestazioni di rete esegue una stima probabilistica delle parti della rete che hanno più probabilità di aver causato l'errore.

Questo approccio è utile per determinare i colli di bottiglia di rete quando l'accesso agli hop non è disponibile perché non richiede la raccolta di dati da dispositivi di rete come router o switch. È utile anche quando l'utente non dispone del controllo amministrativo sugli hop tra due nodi. Ad esempio, gli hop possono essere router ISP.

### <a name="log-analytics-search"></a>Ricerca di Log Analytics
Tutti i dati esposti graficamente attraverso il dashboard di monitoraggio delle prestazioni di rete e le pagine di drill-down sono disponibili in modalità nativa nella ricerca di Log Analytics. È possibile eseguire query sui dati usando il linguaggio delle query di ricerca e creare report personalizzati esportando i dati in Excel o Power BI. Nel pannello **Query comuni** del dashboard sono riportate alcune query utili che è possibile usare come punto di partenza per creare query e report.

![query di ricerca](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>Indagare sulla causa radice di un avviso di integrità
Ora che è stata eseguita una panoramica sul monitoraggio delle prestazioni di rete, si passerà a una semplice indagine sulla causa radice di un avviso di integrità.

1. Nella pagina Panoramica è possibile trovare una rapida panoramica dell'integrità della rete osservando il riquadro **Monitoraggio delle prestazioni di rete**. Notare che, sui 6 collegamenti di subnet, 2 non sono integri. Questo rende necessaria un'analisi. Fare clic sul riquadro per visualizzare il dashboard della soluzione.<br><br> ![Riquadro Monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-investigation01.png)  
2. Nell'immagine di esempio seguente si può notare un evento di integrità, un collegamento di rete non integro. Analizzare il problema e fare clic sul collegamento **DMZ2-DMZ1** per scoprire la radice del problema.<br><br> ![esempio di collegamento di rete non integro](./media/log-analytics-network-performance-monitor/npm-investigation02.png)  
3. Nella pagina di drill-down vengono visualizzati tutti i collegamenti di subnet nel collegamento di rete **DMZ2-DMZ1**. Si noterà che per entrambi i collegamenti di subnet, la latenza ha superato la soglia, rendendo non integro il collegamento di rete. È inoltre possibile visualizzare le tendenze di latenza di entrambi i collegamenti di subnet. È possibile usare il controllo di selezione dell'ora nel grafico per concentrarsi sull'intervallo di tempo richiesto. È possibile visualizzare l'ora del giorno in cui la latenza ha raggiunto il picco. In un secondo momento, è possibile cercare questo periodo di tempo nei log per analizzare il problema. Fare clic su **Visualizza collegamenti del nodo** per un drill-down più approfondito.<br><br> ![esempio di collegamenti della subnet non integri](./media/log-analytics-network-performance-monitor/npm-investigation03.png) 
4. In modo simile alla pagina precedente, nella pagina di drill-down per un particolare collegamento di subnet vengono elencati i collegamenti del nodo che lo costituiscono. È possibile eseguire azioni simili, come nel passaggio precedente. Fare clic su **Visualizza topologia** per visualizzare la topologia tra i 2 nodi.<br><br> ![esempio di collegamenti del nodo non integri](./media/log-analytics-network-performance-monitor/npm-investigation04.png)  
5. Tutti i percorsi tra i 2 nodi selezionati vengono tracciati nella mappa topologica. È possibile visualizzare la topologia hop-by-hop delle route tra due nodi nella mappa topologica. Offre un quadro preciso del numero di route esistenti tra i due nodi e dei percorsi intrapresi dai pacchetti di dati. I colli di bottiglia delle prestazioni di rete sono contrassegnati in rosso. È possibile individuare connessioni o dispositivi di rete difettosi esaminando gli elementi in rosso sulla mappa topologica.<br><br> ![esempio di visualizzazione della topologia non integra](./media/log-analytics-network-performance-monitor/npm-investigation05.png)  
6. È possibile visualizzare i dati relativi a perdita, latenza e numero di hop di ogni percorso nel riquadro **Azione**. Usare la barra di scorrimento per visualizzare i dettagli di tali percorsi.  Usare i filtri per selezionare i percorsi con l'hop non integro, in modo che venga tracciata la topologia solo per i percorsi selezionati. È possibile usare la rotellina del mouse per ingrandire o ridurre la mappa topologica.

   Nell'immagine seguente si può vedere chiaramente la causa radice delle aree relative al problema nella sezione specifica della rete analizzando i percorsi e gli hop in rosso. Facendo clic su un nodo nella mappa topologica vengono visualizzate le proprietà del nodo, inclusi FQDN e indirizzo IP. Facendo clic su un hop viene mostrato il relativo indirizzo IP.<br><br> ![topologia non integra - esempio di dettagli del percorso](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

- **UserVoice**: è possibile pubblicare le proprie idee sulle funzionalità di monitoraggio delle prestazioni di rete che si ritiene possano essere migliorate. Visitare la [pagina UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Partecipa alla coorte**: l'aggiunta di nuovi clienti alla coorte è sempre motivo di grande interesse. In questo contesto si otterrà un accesso in anteprima alle nuove funzionalità e si contribuirà a migliorare il monitoraggio delle prestazioni di rete. Per partecipare, compilare questo [sondaggio rapido](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare una ricerca nei log](log-analytics-log-searches.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
