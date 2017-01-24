---
title: Soluzione di monitoraggio delle prestazioni di rete in OMS | Microsoft Docs
description: Il monitoraggio delle prestazioni di rete consente di monitorare le prestazioni delle reti quasi in tempo reale per rilevare e trovare i colli di bottiglia delle prestazioni di rete.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 57df4ab0b2a1df6631eb6e67a90f69cebb1dfe75
ms.openlocfilehash: 2334540ec0eeec32d0c4b3a6d9597a290bad6ec0


---
# <a name="network-performance-monitor-preview-solution-in-oms"></a>Soluzione (anteprima) di monitoraggio delle prestazioni di rete in OMS
> [!NOTE]
> Si tratta di una [soluzione di anteprima](log-analytics-add-solutions.md#preview-management-solutions-and-features).
>
>

In questo documento viene descritto come configurare e usare la soluzione di monitoraggio delle prestazioni di rete in OMS, che consente di monitorare le prestazioni delle reti quasi in tempo reale per rilevare e trovare i colli di bottiglia delle prestazioni di rete. Con la soluzione di monitoraggio delle prestazioni di rete, è possibile monitorare la perdita e la latenza tra due reti, subnet o server. Il monitoraggio delle prestazioni di rete rileva problemi di rete come il blackholing del traffico, gli errori di routing e i problemi che i metodi di monitoraggio della rete tradizionali non sono in grado di rilevare. Il monitoraggio delle prestazioni di rete genera avvisi e invia notifiche quando viene superata una soglia per un collegamento di rete. Queste soglie possono essere acquisite automaticamente dal sistema oppure possono essere configurate in modo che usino regole di avviso personalizzate. Il monitoraggio delle prestazioni di rete garantisce una tempestiva individuazione dei problemi legati alle prestazioni di rete e localizza l'origine del problema in un particolare segmento di rete o un dispositivo.

È possibile rilevare i problemi di rete con il dashobard della soluzione, che mostra un riepilogo delle informazioni sulla rete, tra cui i recenti eventi di integrità di rete, i collegamenti di rete danneggiati e i collegamenti di subnet con problemi di grande perdita di pacchetti e di latenza elevata. È possibile eseguire il drill-down in un collegamento di rete per visualizzare lo stato di integrità corrente dei collegamenti di subnet, nonché dei collegamenti tra nodi. È anche possibile visualizzare le tendenze cronologiche di perdita e latenza a livello di rete, subnet e tra nodi. È possibile rilevare i problemi di rete temporanei visualizzando i grafici sulle tendenze cronologiche relative a perdita di pacchetti e latenza e individuare colli di bottiglia di rete su una mappa topologica. Il grafico interattivo della topologia consente di visualizzare le route di rete hop-by-hop e determinare l'origine del problema. Come in qualsiasi altra soluzione, è possibile usare la ricerca nei log per i vari requisiti di analisi al fine di creare report personalizzati in base ai dati raccolti dal monitoraggio delle prestazioni di rete.

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
Usare i processi di base per installare gli agenti descritti in [Connettere computer Windows a Log Analytics](log-analytics-windows-agents.md) e [Connettere Operations Manager a Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Per poter avere dati sufficienti per individuare e monitorare le risorse di rete, è necessario installare almeno 2 agenti. In caso contrario, la soluzione rimarrà in uno stato di configurazione fino a quando non verranno installati e configurati altri agenti.
>
>

### <a name="where-to-install-the-agents"></a>Dove installare gli agenti
Prima di installare gli agenti, prendere in considerazione la topologia della rete e le parti della rete che si desidera monitorare. Si consiglia di installare più di un agente per ogni subnet che si desidera monitorare. In altre parole, per ogni subnet che si desidera monitorare, selezionare due o più server o VM e installarvi l'agente.

Se non si conosce la topologia della propria rete, installare gli agenti sui server con carichi di lavoro critici in cui si desidera monitorare le prestazioni di rete. Ad esempio, si potrebbe voler tenere traccia di una connessione di rete tra un server Web e un server che esegue SQL Server. In questo esempio, viene installato un agente su entrambi i server.

Gli agenti monitorano la connettività di rete (collegamenti) tra gli host, non gli host stessi. Pertanto, per monitorare un collegamento di rete, è necessario installare gli agenti su entrambi gli endpoint del collegamento.

### <a name="configure-agents"></a>Configurazione degli agenti
Dopo aver installato gli agenti, è necessario aprire le porte del firewall per tali computer per assicurarsi che gli agenti possano comunicare. È necessario scaricare ed eseguire lo [script di PowerShell EnableRules.ps1](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) senza parametri in una finestra di PowerShell con privilegi amministrativi

Lo script crea le chiavi del Registro di sistema richieste dal monitoraggio delle prestazioni di rete e crea regole del firewall Windows per consentire agli agenti di creare connessioni TCP tra loro. Le chiavi del Registro di sistema create dallo script inoltre specificano se registrare i log di debug e il percorso al file di log. Inoltre definiscono la porta TCP agente usata per la comunicazione. I valori per queste chiavi vengono impostati automaticamente dallo script, quindi non bisogna modificare manualmente le chiavi.

La porta aperta per impostazione predefinita è 8084. È possibile usare una porta personalizzata fornendo il parametro `portNumber` allo script. Tuttavia, è necessario usare la stessa porta su tutti i computer in cui viene eseguito lo script.

> [!NOTE]
> Lo script EnableRules.ps1 configura le regole del firewall Windows solo nel computer in cui viene eseguito lo script. Se si dispone di un firewall di rete, è necessario assicurarsi che consenta il traffico destinato alla porta TCP usata dal monitoraggio delle prestazioni di rete.
>
>

## <a name="configuring-the-solution"></a>Configurazione della soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

1. La soluzione di monitoraggio delle prestazioni di rete acquisisce i dati dai computer che eseguono Windows Server 2008 SP 1 o versioni successive o Windows 7 SP1 o versioni successive, che sono gli stessi requisiti di Microsoft Monitoring Agent (MMA).
2. Aggiungere la soluzione di monitoraggio delle prestazioni di rete all'area di lavoro di OMS usando la procedura descritta nell'articolo [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).  
   ![Simbolo del monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. Nel portale di OMS si noterà un nuovo riquadro chiamato **Monitoraggio delle prestazioni di rete** con il messaggio *La soluzione richiede configurazione aggiuntiva*. È necessario configurare la soluzione in modo da aggiungere reti in base alle subnet e ai nodi rilevati dagli agenti. Fare clic su **Monitoraggio delle prestazioni di rete** per avviare la configurazione della rete predefinita.  
   ![La soluzione richiede configurazione aggiuntiva](./media/log-analytics-network-performance-monitor/npm-config.png)

### <a name="configure-the-solution-with-a-default-network"></a>Configurazione della soluzione con una rete predefinita
Nella pagina di configurazione si noterà una singola rete chiamata **Predefinita**. Quando non sono definite le reti, tutte le subnet individuate automaticamente si trovano nella rete predefinita.

Tutte le volte che si crea una rete, viene aggiunta una subnet che viene rimossa dalla rete predefinita. Se si elimina una rete, tutte le sue subnet vengono restituite automaticamente alla rete predefinita.

In altre parole, la rete predefinita è il contenitore per tutte le subnet che non sono contenute in una rete definita dall'utente. Non è possibile modificare o eliminare la rete predefinita, che rimane sempre nel sistema. Tuttavia, è possibile creare tutte le reti necessarie.

Nella maggior parte dei casi, le subnet all'interno dell'organizzazione verranno disposte in più di una rete e sarà necessario creare una o più reti per raggruppare logicamente le subnet.

### <a name="create-new-networks"></a>Creazione di nuove reti
Una rete del monitoraggio delle prestazioni di rete è un contenitore per le subnet. È possibile creare una rete con qualsiasi nome e aggiungere subnet alla rete. Ad esempio, è possibile creare una rete di nome *Building1* e quindi aggiungere le subnet oppure è possibile creare una rete di nome *DMZ* e quindi aggiungere tutte le subnet appartenenti alla rete perimetrale.

#### <a name="to-create-a-new-network"></a>Per creare una nuova rete
1. Fare clic su **Aggiungi rete** e quindi digitare il nome e la descrizione della rete.
2. Selezionare una o più subnet e quindi fare clic su **Aggiungi**.
3. Fare clic su **Salva** per salvare la configurazione.  
   ![aggiunta della rete](./media/log-analytics-network-performance-monitor/npm-add-network.png)

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
3. Fare clic su **Salva** per salvare la configurazione.  
   ![modifica della subnet](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Selezionare i nodi da monitorare
Tutti i nodi su cui è installato un agente sono elencati nella scheda **Nodi**.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Per abilitare o disabilitare il monitoraggio per i nodi
1. Selezionare o deselezionare i nodi per cui si desidera abilitare o interrompere il monitoraggio.
2. Fare clic su **Usa per il monitoraggio** oppure deselezionare l'opzione in base alle esigenze.
3. Fare clic su **Save**.  
   ![abilitazione del monitoraggio dei nodi](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Set di regole di monitoraggio
Il monitoraggio delle prestazioni di rete genera eventi di integrità sulla connettività tra una coppia di nodi oppure collegamenti di rete o subnet quando viene superata una soglia. Queste soglie possono essere acquisite automaticamente dal sistema oppure possono essere configurate come regole di avviso personalizzate.

La *regola predefinita* viene creata dal sistema e crea un evento di integrità ogni volta che la perdita o la latenza tra qualsiasi coppia di reti o collegamenti di subnet supera la soglia acquisita dal sistema. È possibile scegliere di disabilitare la regola predefinita e creare regole di monitoraggio personalizzate

#### <a name="to-create-custom-monitoring-rules"></a>Per creare regole di monitoraggio personalizzate
1. Fare clic su **Aggiungi regola** nella scheda **Monitoraggio**, quindi immettere il nome e la descrizione della regola.
2. Selezionare dall'elenco la coppia di collegamenti di rete o subnet da monitorare.
3. Innanzitutto selezionare la rete che contiene una o più subnet di interesse nell'elenco a discesa di rete, quindi selezionare le subnet dal corrispondente elenco a discesa delle subnet.
   Se si desidera monitorare tutte le subnet in un collegamento di rete, scegliere **Tutte le subnet**. Analogamente, selezionare una o più altre subnet di interesse. Inoltre è possibile fare clic su **Aggiungi eccezione** per escludere il monitoraggio per particolari collegamenti di subnet dalla selezione effettuata.
4. Se non si desidera creare eventi di integrità per gli elementi selezionati, deselezionare **Abilita monitoraggio dell'integrità nei collegamenti interessati da questa regola**.
5. Scegliere le condizioni di monitoraggio.
   È possibile impostare soglie personalizzate per la generazione di eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità.
6. Fare clic su **Salva** per salvare la configurazione.  
   ![creazione di una regola di monitoraggio personalizzata](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

## <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati
Il monitoraggio delle prestazioni di rete usa i pacchetti di handshake TCP SYN-SYNACK-ACK per raccogliere informazioni su perdita e latenza mentre viene usato anche il tracciamento delle route per recuperare informazioni sulla topologia.

Nella tabella seguente vengono illustrati i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per il monitoraggio delle prestazioni di rete.

| Piattaforma | Agente diretto | Agente SCOM | Archiviazione di Azure | SCOM obbligatorio? | Dati dell'agente SCOM inviati con il gruppo di gestione | frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sì](./media/log-analytics-network-performance-monitor/oms-bullet-green.png) |![Sì](./media/log-analytics-network-performance-monitor/oms-bullet-green.png) |![No](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |![No](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |![No](./media/log-analytics-network-performance-monitor/oms-bullet-red.png) |TCP esegue handshake ogni 5 secondi e i dati vengono inviati ogni 3 minuti |

La soluzione usa le transazioni sintetiche per valutare l'integrità della rete. Gli agenti OMS installati in vari punti della rete scambiano pacchetti TCP gli uni con gli altri e, nel processo, acquisiscono eventuali informazioni sul tempo di round trip e perdita dei pacchetti. Periodicamente, ogni agente esegue inoltre un comando di tracciamento delle route agli altri agenti per trovare tutte le varie route nella rete che deve essere verificata. Usando questi dati, gli agenti sono in grado di dedurre i valori relativi a latenza di rete e perdita di pacchetti. I test vengono ripetuti ogni cinque secondi e i dati vengono aggregati per tre minuti dagli agenti prima del caricamento su OMS.

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

#### <a name="trend-charts"></a>Grafici delle tendenze
In ogni livello del drill-down, è possibile vedere la tendenza di perdite e latenza per un collegamento di rete. I grafici di tendenza sono disponibili anche per i collegamenti della subnet e del nodo. È possibile modificare l'intervallo di tempo per il grafico da tracciare usando il controllo del tempo nella parte superiore del grafico.

I grafici delle tendenze mostrano una prospettiva cronologica delle prestazioni di un collegamento di rete. Alcuni problemi di rete sono di natura temporanea e sarebbero difficili da rilevare solo esaminando lo stato corrente della rete. Infatti i problemi possono venire alla luce rapidamente e sparire prima che qualcuno li noti, per poi riapparire in un secondo momento. Tali problemi temporanei inoltre possono essere difficili per gli amministratori delle applicazioni, poiché spesso si presentano come aumenti inspiegabili nel tempo di risposta delle applicazioni, anche quando tutti i componenti delle applicazioni in apparenza vengono eseguiti correttamente.

È possibile rilevare facilmente questi tipi di problemi esaminando un grafico delle tendenze in cui il problema verrà visualizzato come un picco improvviso di latenza di rete o di perdita dei pacchetti.

![grafico di tendenza](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Mappa topologica hop-by-hop
Il monitoraggio delle prestazioni di rete mostra la topologia hop-by-hop delle route tra due nodi in una mappa topologica interattiva. È possibile visualizzare la mappa topologica selezionando un collegamento del nodo e quindi facendo clic su **Visualizza topologia**. Inoltre, è possibile visualizzare la mappa topologica facendo clic sul riquadro **Percorsi** del dashboard. Quando si fa clic sul riquadro **Percorsi** del dashboard, sarà necessario selezionare i nodi di origine e di destinazione dal pannello a sinistra, quindi fare clic su **Traccia** per tracciare le route tra i due nodi.

La mappa topologica visualizza la quantità di route presenti tra i due nodi e i percorsi intrapresi dai pacchetti di dati. I colli di bottiglia delle prestazioni di rete sono contrassegnati in rosso nella mappa topologica. È possibile individuare connessioni o dispositivi di rete difettosi esaminando gli elementi in rosso sulla mappa topologica.

Quando si fa clic su un nodo o si passa il puntatore del mouse su tale nodo nella mappa topologica, vengono visualizzate le proprietà del nodo, ad esempio FQDN e indirizzo IP. Fare clic su un hop per visualizzarne l'indirizzo IP. È possibile evidenziare particolari route deselezionandole e poi selezionando solo quelle che si desidera evidenziare sulla mappa. È possibile ingrandire o ridurre la mappa topologica usando la rotellina del mouse.

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

1. Nella pagina Panoramica è possibile trovare una rapida panoramica dell'integrità della rete osservando il riquadro **Monitoraggio delle prestazioni di rete**. Notare che, sugli 80 collegamenti di subnet, 43 non sono integri. Questo rende necessaria un'analisi. Fare clic sul riquadro per visualizzare il dashboard della soluzione.  
   ![Riquadro Monitoraggio delle prestazioni di rete](./media/log-analytics-network-performance-monitor/npm-investigation01.png)
2. Nella seguente immagine di esempio al momento esistono 4 eventi di integrità e 4 collegamenti di rete non integri. Analizzare il problema e fare clic sul collegamento **Sharepoint-Web** per scoprire la radice del problema.  
   ![esempio di collegamento di rete non integro](./media/log-analytics-network-performance-monitor/npm-investigation02.png)
3. Nella pagina di drill-down vengono visualizzati tutti i collegamenti di subnet nel collegamento di rete **Sharepoint-Web**. Si noterà che per entrambi i collegamenti di subnet, la latenza ha superato la soglia, rendendo non integro il collegamento di rete. È inoltre possibile visualizzare le tendenze di latenza di entrambi i collegamenti di subnet. È possibile usare il controllo di selezione dell'ora nel grafico per concentrarsi sull'intervallo di tempo richiesto. È possibile visualizzare l'ora del giorno in cui la latenza ha raggiunto il picco. In un secondo momento, è possibile cercare questo periodo di tempo nei log per analizzare il problema. Fare clic su **Visualizza collegamenti del nodo** per un drill-down più approfondito.  
   ![esempio di collegamenti della subnet non integri](./media/log-analytics-network-performance-monitor/npm-investigation03.png)
4. In modo simile alla pagina precedente, nella pagina di drill-down per un particolare collegamento di subnet vengono elencati i collegamenti del nodo che lo costituiscono. È possibile eseguire azioni simili, come nel passaggio precedente. Fare clic su **Visualizza topologia** per visualizzare la topologia tra i 2 nodi.  
   ![esempio di collegamenti del nodo non integri](./media/log-analytics-network-performance-monitor/npm-investigation04.png)
5. Tutti i percorsi tra i 2 nodi selezionati vengono tracciati nella mappa topologica. È possibile visualizzare la topologia hop-by-hop delle route tra due nodi nella mappa topologica. Offre un quadro preciso del numero di route esistenti tra i due nodi e dei percorsi intrapresi dai pacchetti di dati. I colli di bottiglia delle prestazioni di rete sono contrassegnati in rosso. È possibile individuare connessioni o dispositivi di rete difettosi esaminando gli elementi in rosso sulla mappa topologica.  
   ![esempio di visualizzazione della topologia non integra](./media/log-analytics-network-performance-monitor/npm-investigation05.png)
6. È possibile visualizzare i dati relativi a perdita, latenza e numero di hop di ogni percorso nel riquadro **Dettagli percorso**. In questo esempio sono presenti 3 percorsi non integri, come indicato nel riquadro. Usare la barra di scorrimento per visualizzare i dettagli di tali percorsi.  Usare le caselle di controllo per selezionare uno dei percorsi in modo che venga tracciata la topologia per un solo percorso. È possibile usare la rotellina del mouse per ingrandire o ridurre la mappa topologica.

   Nell'immagine seguente si può vedere chiaramente la causa radice delle aree relative al problema nella sezione specifica della rete analizzando i percorsi e gli hop in rosso. Facendo clic su un nodo nella mappa topologica vengono visualizzate le proprietà del nodo, inclusi FQDN e indirizzo IP. Facendo clic su un hop viene mostrato il relativo indirizzo IP.  
   ![topologia non integra - esempio di dettagli del percorso](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare una ricerca nei log](log-analytics-log-searches.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.



<!--HONumber=Dec16_HO1-->


