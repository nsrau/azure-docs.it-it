---
title: "Funzionalità Performance Monitor nella soluzione Monitoraggio prestazioni rete in Azure Log Analytics | Microsoft Docs"
description: "La funzionalità Performance Monitor nella soluzione Monitoraggio prestazioni rete consente di monitorare la connettività di rete tra i vari punti della rete, ad esempio distribuzioni cloud e percorsi locali, più data center e succursali, applicazioni/microservizi multilivello cruciali."
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
ms.openlocfilehash: a90ab3bc857b704d9d94daf96d17611844abb1a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution---performance-monitoring"></a>Soluzione Monitoraggio prestazioni rete - Monitoraggio delle prestazioni

La funzionalità Performance Monitor nella soluzione [Monitoraggio prestazioni rete](log-analytics-network-performance-monitor.md) consente di monitorare la connettività di rete tra i vari punti della rete, ad esempio distribuzioni cloud e percorsi locali, più data center e succursali, applicazioni/microservizi multilivello cruciali. La funzionalità Performance Monitor consente di rilevare tempestivamente i problemi a livello di rete. I vantaggi chiave sono: 

- Monitorare perdita e latenza tra varie subnet e configurare avvisi 
- Monitorare tutti i percorsi (inclusi i percorsi ridondanti) sulla rete 
- Risolvere i problemi di rete temporanei e temporizzati che sono difficili da replicare 
- Determinare un segmento specifico nella rete che è responsabile della riduzione delle prestazioni 
- Monitorare l'integrità della rete senza la necessità di SNMP 


![Monitoraggio delle prestazioni di rete](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configurazione
Per aprire la configurazione per Monitoraggio prestazioni rete, aprire la [soluzione Monitoraggio prestazioni rete](log-analytics-network-performance-monitor.md) e fare clic sul pulsante **Configura**.

![Configurare Monitoraggio prestazioni rete](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Creazione di nuove reti

In Monitoraggio prestazioni rete una rete è un contenitore logico per le subnet. Consente di organizzare il monitoraggio dell'infrastruttura di rete in base alle esigenze di monitoraggio. È possibile creare una rete con un nome descrittivo e aggiungere le subnet in base alla logica di business. Ad esempio, si può creare una rete denominata Londra e aggiungervi tutte le subnet del data center Londra oppure una rete denominata *ContosoFrontEnd* e aggiungervi tutte le subnet che servono il front-end dell'app denominata Contoso a tale rete. La soluzione crea automaticamente una rete predefinita che contiene tutte le subnet individuate nell'ambiente in uso. Tutte le volte che si crea una rete, viene aggiunta una subnet che viene rimossa dalla rete predefinita. Se si elimina una rete, tutte le sue subnet vengono restituite automaticamente alla rete predefinita. La rete Predefinita è perciò il contenitore per tutte le subnet che non sono contenute in una rete definita dall'utente. Non è possibile modificare o eliminare la rete predefinita, che rimane sempre nel sistema. Tuttavia è possibile creare tutte le reti personalizzate necessarie. Nella maggior parte dei casi, le subnet all'interno dell'organizzazione vengono disposte in più di una rete ed è necessario creare una o più reti per raggruppare le subnet in base alla logica di business.

Per creare una nuova rete:


1. Fare clic sulla scheda **Reti**.
1. Fare clic su  **Aggiungi rete** e quindi digitare il nome e la descrizione della rete. 
2. Selezionare una o più subnet e quindi fare clic su **Aggiungi**. 
3. Fare clic su **Salva** per salvare la configurazione. 


### <a name="create-monitoring-rules"></a>Creare le regole di monitoraggio 

La funzionalità Performance Monitor genera eventi di integrità quando viene superata la soglia di prestazioni delle connessioni di rete tra due subnet o tra due reti. Queste soglie possono essere apprese automaticamente dal sistema oppure è possibile configurare soglie personalizzate. Il sistema crea automaticamente una regola predefinita che genera un evento di integrità ogni volta che la perdita o la latenza tra qualsiasi coppia di reti o collegamenti di rete/subnet supera la soglia appresa dal sistema. In questo modo la soluzione monitora l'infrastruttura di rete fino a quando non vengono create esplicitamente regole di monitoraggio. Se è abilitata la **regola predefinita**, tutti i nodi inviano transazioni sintetiche a tutti gli altri nodi che sono stati abilitati per il monitoraggio. La regola predefinita è utile con reti di piccole dimensioni, ad esempio in uno scenario in cui si ha un numero ridotto di server che eseguono un microservizio e si vuole assicurarsi che vi sia connettività fra tutti i server. 

>[!NOTE]
> Si consiglia di disabilitare la **regola predefinita** e creare regole di monitoraggio personalizzate, soprattutto se si usa un numero elevato di nodi per il monitoraggio di reti di grandi dimensioni. Ciò consente di ridurre il traffico generato dalla soluzione e di organizzare il monitoraggio della rete. 

Creare regole di monitoraggio in base alla logica di business. Ad esempio, se si vuole monitorare le prestazioni della connettività di rete fra due uffici periferici e la sede centrale, raggruppare tutte le subnet dell'ufficio 1 nella rete O1, tutte le subnet dell'ufficio 2 nella rete O2 e tutte le subnet della sede centrale nella rete H. Creare 2 regole di monitoraggio: una tra O1 e H e l'altra tra O2 e H. 

Per creare regole di monitoraggio personalizzate:

1. Fare clic su **Aggiungi regola** nella scheda **Monitoraggio**, quindi immettere il nome e la descrizione della regola. 
2. Selezionare dall'elenco la coppia di collegamenti di rete o subnet da monitorare. 
3. Innanzitutto selezionare la rete che contiene una o più subnet di interesse nell'elenco a discesa di rete, quindi selezionare le subnet dal corrispondente elenco a discesa delle subnet. Se si desidera monitorare tutte le subnet in un collegamento di rete, scegliere **Tutte le subnet**. Analogamente, selezionare una o più altre subnet di interesse. Inoltre è possibile fare clic su **Aggiungi eccezione** per escludere il monitoraggio per particolari collegamenti di subnet dalla selezione effettuata. 
4. Scegliere tra i protocolli TCP e ICMP per l'esecuzione di transazioni sintetiche. 
5. Se non si desidera creare eventi di integrità per gli elementi selezionati, deselezionare **Abilita monitoraggio dell'integrità nei collegamenti interessati da questa regola**. 
6. Scegliere le condizioni di monitoraggio. È possibile impostare soglie personalizzate per la generazione di eventi di integrità digitando i valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di rete/subnet selezionata, viene generato un evento di integrità. 
7. Fare clic su **Salva** per salvare la configurazione. 

Dopo avere salvato una regola di monitoraggio, è possibile integrarla con Gestione avvisi facendo clic su **Crea avviso**. Una regola di avviso viene creata automaticamente con la query di ricerca e altri parametri obbligatori automaticamente compilati. Usando una regola di avviso, è possibile ricevere avvisi basati sulla posta elettronica, oltre agli avvisi esistenti in NPM. Gli avvisi possono anche attivare azioni correttive con i runbook oppure possono essere integrati con le soluzione di gestione esistenti usando i webhook. È possibile fare clic su **Manage Alert** (Gestisci avviso) per modificare le impostazioni dell'avviso. 

È ora possibile creare più regole di monitoraggio delle prestazioni o passare al dashboard della soluzione per iniziare a usare la funzionalità. 

### <a name="choose-the-protocol"></a>Scegliere il protocollo

Monitoraggio prestazioni rete (NPM) usa le transazioni sintetiche per calcolare le metriche delle prestazioni di rete come la latenza di collegamento e la perdita di pacchetti. Per comprendere meglio questo concetto, si consideri un agente NPM connesso a un'estremità di un collegamento di rete. L'agente NPM invia pacchetti probe a un secondo agente NPM connesso a un'altra estremità della rete. Il secondo agente risponde con pacchetti di risposta. Questo processo viene ripetuto più volte. Misurando il numero di risposte e il tempo impiegato per ricevere ogni risposta, il primo agente NPM valuta la latenza del collegamento e la perdita dei pacchetti. 

Il formato, le dimensioni e la sequenza di questi pacchetti dipendono dal protocollo scelto durante la creazione delle regole di monitoraggio. In base al protocollo dei pacchetti, i dispositivi di rete intermedi (router, commutatori e così via) potrebbero elaborare questi pacchetti in modo diverso. Di conseguenza, la scelta del protocollo influisce sulla precisione dei risultati. Inoltre la scelta del protocollo determina anche l'eventuale necessità di eseguire alcuni passaggi manuali dopo aver distribuito la soluzione NPM. 

NPM offre la possibilità di scegliere tra i protocolli TCP e ICMP per l'esecuzione di transazioni sintetiche. Se si sceglie il protocollo ICMP quando si crea una regola di transazione sintetica, gli agenti NPM usano messaggi ICMP ECHO per calcolare la latenza di rete e la perdita di pacchetti. ICMP ECHO usa lo stesso messaggio che viene inviato dall'utilità Ping convenzionale. Quando si usa il protocollo TCP, gli agenti NPM inviano pacchetti TCP SYN sulla rete. Questa operazione è seguita dal completamento di un handshake TCP e successivamente dalla rimozione della connessione tramite pacchetti RST. 

Prima scegliere il protocollo, tenere in considerazione le informazioni seguenti: 

**Individuazione di più route di rete.**  TCP offre una maggiore precisione nell'individuazione di più route e necessita di un minor numero di agenti in ogni subnet. Ad esempio, uno o due agenti con TCP possono individuare tutti i percorsi ridondanti tra subnet. Invece, per ottenere risultati simili con il protocollo ICMP, servono più agenti. Con ICMP, se si dispone di un numero N di route tra due subnet, è necessario usare più di 5N agenti nella subnet di origine o destinazione. 

**Precisione dei risultati.** I router e i commutatori tendono ad assegnare una priorità inferiore ai pacchetti ICMP ECHO rispetto ai pacchetti TCP. In determinati casi, quando è presente un carico eccessivo dei dispositivi di rete, i dati ottenuti dal protocollo TCP riflettono più da vicino la perdita e la latenza sperimentate dalle applicazioni. Ciò si verifica perché la maggior parte del traffico delle applicazioni fluisce attraverso TCP. In questi casi, il protocollo ICMP fornisce risultati meno precisi rispetto a TCP. 

**Configurazione del firewall.** Il protocollo TCP prevede che i pacchetti TCP vengano inviati a una porta di destinazione. La porta predefinita usata dagli agenti NPM è la porta 8084, tuttavia è possibile cambiarla quando si configurano gli agenti. È quindi necessario accertarsi che i firewall di rete o regole NSG (in Azure) consentano il traffico sulla porta. È necessario assicurarsi che anche il firewall locale nei computer in cui sono installati gli agenti sia configurato per consentire il traffico su questa porta. È possibile usare gli script di PowerShell per configurare le regole del firewall nei computer che eseguono Windows. Il firewall di rete deve invece essere configurato manualmente. Il protocollo ICMP invece non opera tramite porta. Nella maggior parte degli scenari aziendali, il traffico ICMP può fluire attraverso i firewall per consentire l'uso degli strumenti di diagnostica di rete come l'utilità Ping. Pertanto, se è possibile eseguire il Ping da un computer ad un altro, è possibile usare il protocollo ICMP senza dover configurare i firewall manualmente. 

>[!NOTE] 
> Alcuni firewall possono bloccare ICMP, causando potenzialmente una ritrasmissione che genera un numero elevato di eventi nel sistema di gestione delle informazioni di sicurezza e degli eventi. Accertarsi che il protocollo scelto non sia bloccato da un firewall/NSG di rete, perché in caso contrario Monitoraggio prestazioni rete non riuscirà a monitorare il segmento di rete. Per questo motivo si consiglia di usare TCP per il monitoraggio. Si deve usare ICMP negli scenari in cui non è possibile usare TCP, ad esempio quando: 
>
> - Si usano nodi basati su client Windows, poiché i raw socket TCP non sono consentiti nel client Windows
> - Il firewall/NSG di rete blocca TCP
> - Come cambiare protocollo 

Se si sceglie di usare ICMP durante la distribuzione, è possibile passare a TCP in qualsiasi momento modificando la regola di monitoraggio predefinita:

1. Passare a **Monitoraggio prestazioni** > **rete** > **Configura** > **Monitor** e quindi fare clic su  **Regola predefinita**. 
2. Passare alla sezione **Protocollo** e selezionare il protocollo che si desidera usare. 
3. Fare clic su **Salva** per salvare le modifiche. 

Anche se la regola predefinita usa un protocollo specifico, è possibile creare nuove regole con un protocollo diverso. È anche possibile creare una combinazione di regole in cui alcune usano ICMP e altre usano TCP. 

## <a name="walkthrough"></a>Procedura dettagliata 

Ora che è stata eseguita una panoramica sul monitoraggio delle prestazioni, si passerà a una semplice indagine sulla causa radice di un avviso di integrità.  

Nel dashboard della soluzione si può notare un evento di integrità, ovvero un collegamento di rete non integro. Si decide di analizzare il problema facendo clic nel riquadro **Collegamenti di rete monitorati**.

Nella pagina di drill-down si noti che il collegamento di rete **DMZ2 DMZ1** non è integro. Fare clic sul collegamento **Visualizza collegamenti subnet** (View subnet links) per questo collegamento di rete. 


Nella pagina di drill-down vengono visualizzati tutti i collegamenti di subnet nel collegamento di rete **DMZ2-DMZ1**. Si può notare che per entrambi i collegamenti di subnet, la latenza ha superato la soglia, rendendo non integro il collegamento di rete. È inoltre possibile visualizzare le tendenze di latenza di entrambi i collegamenti di subnet. È possibile usare il controllo di selezione dell'ora nel grafico per concentrarsi sull'intervallo di tempo richiesto. È possibile visualizzare l'ora del giorno in cui la latenza ha raggiunto il picco. In un secondo momento, è possibile cercare questo periodo di tempo nei log per analizzare il problema. Fare clic su **Visualizza collegamenti del nodo** per un drill-down più approfondito. 
 
 ![Collegamenti di subnet](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

In modo simile alla pagina precedente, nella pagina di drill-down per un particolare collegamento di subnet vengono elencati i collegamenti del nodo che lo costituiscono. È possibile eseguire azioni simili, come nel passaggio precedente. Fare clic su **Visualizza topologia** per visualizzare la topologia tra i due nodi. 
 
 ![Collegamenti dei nodi](media/log-analytics-network-performance-monitor/node-links.png) 

Tutti i percorsi tra i due nodi selezionati vengono tracciati nella mappa topologica. È possibile visualizzare la topologia hop-by-hop delle route tra due nodi nella mappa topologica. Offre un quadro preciso del numero di route esistenti tra i due nodi e dei percorsi intrapresi dai pacchetti di dati. I colli di bottiglia delle prestazioni di rete sono contrassegnati in rosso. È possibile individuare connessioni o dispositivi di rete difettosi esaminando gli elementi in rosso sulla mappa topologica. 

 ![Dashboard delle topologie](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

È possibile visualizzare i dati relativi a perdita, latenza e numero di hop di ogni percorso nel riquadro **Azione**. Usare la barra di scorrimento per visualizzare i dettagli di tali percorsi. Usare i filtri per selezionare i percorsi con l'hop non integro, in modo che venga tracciata la topologia solo per i percorsi selezionati. È possibile usare la rotellina del mouse per ingrandire o ridurre la mappa topologica. 

Nell'immagine seguente si può vedere chiaramente la causa radice delle aree relative al problema nella sezione specifica della rete analizzando i percorsi e gli hop in rosso. Facendo clic su un nodo nella mappa topologica vengono visualizzate le proprietà del nodo, inclusi FQDN e indirizzo IP. Facendo clic su un hop viene mostrato il relativo indirizzo IP. 
 
![Dashboard delle topologie](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare una ricerca nei log](log-analytics-log-searches.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
