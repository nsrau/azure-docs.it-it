---
title: Funzionalità Performance Monitor nella soluzione Monitoraggio prestazioni rete in Azure Log Analytics | Microsoft Docs
description: La funzionalità Performance Monitor in Monitoraggio prestazioni rete consente di monitorare la connettività tra vari punti della rete. È possibile monitorare distribuzioni cloud e sedi locali, diversi data center e succursali e applicazioni o microservizi multilivello di importanza cruciale.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: ''
ms.openlocfilehash: 8b4ed19ede70c3c9b768cfd368e22b0df3e71212
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52430536"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Soluzione Monitoraggio prestazioni rete - Monitoraggio delle prestazioni

La funzionalità Performance Monitor in [Monitoraggio prestazioni rete](network-performance-monitor.md) consente di monitorare la connettività tra vari punti della rete. È possibile monitorare distribuzioni cloud e sedi locali, diversi data center e succursali e applicazioni o microservizi multilivello di importanza cruciale. La funzionalità Performance Monitor consente di rilevare tempestivamente i problemi a livello di rete. I principali vantaggi offerti sono i seguenti: 

- Monitoraggio di perdita e latenza tra varie subnet e impostazione di avvisi.
- Monitoraggio di tutti i percorsi della rete, inclusi quelli ridondanti.
- Risoluzione dei problemi di rete temporanei e temporizzati difficili da replicare.
- Individuazione dello specifico segmento della rete responsabile della riduzione di prestazioni.
- Monitoraggio dell'integrità della rete senza la necessità di usare SNMP.


![Monitoraggio delle prestazioni di rete](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configurazione
Per visualizzare la configurazione di Monitoraggio prestazioni rete, aprire la [soluzione Monitoraggio prestazioni rete](network-performance-monitor.md) e selezionare **Configura**.

![Configurare Monitoraggio prestazioni rete](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Creazione di nuove reti

Una rete in Monitoraggio prestazioni rete è un contenitore per subnet. Consente di organizzare il monitoraggio dell'infrastruttura di rete in base alle esigenze specifiche. È possibile creare una rete con un nome descrittivo e aggiungere le subnet in base alla logica di business. Ad esempio, è possibile creare una rete denominata Londra e aggiungere tutte le subnet nel data center Londra. In alternativa, è possibile creare una rete denominata *ContosoFrontEnd* e aggiungervi tutte le subnet denominate Contoso che servono il front-end dell'app. La soluzione crea automaticamente una rete predefinita che contiene tutte le subnet individuate nell'ambiente in uso. 

Ogni volta che si crea una rete viene aggiunta una subnet, che viene quindi rimossa dalla rete predefinita. Se si elimina una rete, tutte le relative subnet vengono restituite automaticamente alla rete predefinita. La rete predefinita serve da contenitore per tutte le subnet che non sono contenute nelle reti definite dall'utente. Non è possibile modificare o eliminare la rete predefinita, che rimane sempre nel sistema. È possibile creare tutte le reti personalizzate necessarie. Nella maggior parte dei casi, le subnet di un'organizzazione sono distribuite in più reti. Creare una o più reti per raggruppare le subnet per la logica di business.

Per creare una nuova rete:


1. Selezionare la scheda **Reti**.
1. Selezionare  **Aggiungi rete** e quindi immettere il nome e la descrizione della rete. 
2. Selezionare una o più subnet e quindi **Aggiungi**. 
3. Selezionare **Salva** per salvare la configurazione. 


### <a name="create-monitoring-rules"></a>Creare le regole di monitoraggio 

La funzionalità Performance Monitor genera eventi di integrità quando viene superata la soglia di prestazioni delle connessioni di rete tra due subnet o tra due reti. Il sistema è capace di apprendere queste soglie automaticamente. È anche possibile specificare soglie personalizzate. Viene automaticamente creata una regola predefinita, che genera un evento di integrità ogni volta che la perdita o la latenza tra due collegamenti di rete o di subnet determina il superamento della soglia appresa dal sistema. Questo processo consente alla soluzione di monitorare l'infrastruttura di rete fino a quando non vengono create regole di monitoraggio in modo esplicito. Se la regola predefinita è abilitata, tutti i nodi inviano transazioni sintetiche a tutti gli altri nodi abilitati per il monitoraggio. La regola predefinita è utile nel caso di reti di piccole dimensioni, ad esempio in uno scenario in cui sono presenti pochi server che eseguono un microservizio e si vuole verificare la connettività tra tutti i server.

>[!NOTE]
> È consigliabile disabilitare la regola predefinita e creare regole di monitoraggio personalizzate, soprattutto nel caso di reti di grandi dimensioni in cui si usa un numero elevato di nodi per il monitoraggio. Le regole di monitoraggio personalizzate consentono di ridurre il traffico generato dalla soluzione e organizzare il monitoraggio della rete.

Creare regole di monitoraggio in base alla logica di business. Ad esempio, può essere opportuno monitorare le prestazioni della connettività di rete tra due sedi amministrative e la sede centrale. Raggruppare tutte le subnet della sede amministrativa 1 nella rete O1. Raggruppare quindi tutte le subnet della sede amministrativa 2 nella rete O2. Infine, raggruppare tutte le subnet della sede centrale nella rete H. Creare due regole di monitoraggio, una tra O1 e H e l'altra tra O2 e H. 

Per creare regole di monitoraggio personalizzate:

1. Selezionare **Aggiungi regola** nella scheda **Monitoraggio** e immettere il nome e la descrizione della regola.
2. Selezionare dall'elenco la coppia di collegamenti di rete o subnet da monitorare. 
3. Selezionare la rete che contiene le subnet desiderate nell'elenco a discesa delle reti. Selezionare quindi le subnet nel corrispondente elenco a discesa delle subnet. Se si vogliono monitorare tutte le subnet di un collegamento di rete, selezionare **Tutte le subnet**. Analogamente, selezionare le altre subnet desiderate. Per escludere il monitoraggio di singoli collegamenti di subnet dalle selezioni effettuate, selezionare **Aggiungi eccezione**. 
4. Scegliere tra i protocolli ICMP e TCP per l'esecuzione di transazioni sintetiche. 
5. Se non si vogliono creare eventi di integrità per gli elementi selezionati, deselezionare **Abilita il monitoraggio dello stato sui collegamenti previsti da questa regola**. 
6. Scegliere le condizioni di monitoraggio. Per impostare soglie personalizzate per la generazione di eventi di integrità, immettere valori di soglia. Ogni volta che il valore della condizione supera la soglia selezionata per la coppia di reti o subnet selezionata, viene generato un evento di integrità. 
7. Selezionare **Salva** per salvare la configurazione. 

Dopo aver salvato una regola di monitoraggio, è possibile integrarla con Gestione avvisi selezionando **Crea avviso**. Una regola di avviso viene creata automaticamente con la query di ricerca. Altri parametri obbligatori vengono compilati automaticamente. Usando una regola di avviso è possibile ricevere avvisi basati su posta elettronica, oltre a quelli esistenti in Monitoraggio prestazioni rete. Gli avvisi possono anche attivare azioni correttive con i runbook oppure possono essere integrati con le soluzioni esistenti di gestione dei servizi tramite l'uso di webhook. Selezionare **Manage Alert** (Gestisci avviso) per modificare le impostazioni dell'avviso. 

È ora possibile creare altre regole di Performance Monitor o passare al dashboard della soluzione per iniziare a usare la funzionalità.

### <a name="choose-the-protocol"></a>Scegliere il protocollo

Monitoraggio prestazioni rete usa transazioni sintetiche per calcolare le metriche delle prestazioni di rete, ad esempio la perdita di pacchetti e la latenza del collegamento. Per comprendere meglio questo concetto, si consideri un agente di Monitoraggio prestazioni rete connesso a un'estremità di un collegamento di rete. Questo agente invia pacchetti di probe a un secondo agente di Monitoraggio prestazioni rete connesso a un'altra estremità della rete. Il secondo agente risponde con pacchetti di risposta. Questo processo si ripete diverse volte. Misurando il numero di risposte e il tempo impiegato per ricevere ogni risposta, il primo agente di Monitoraggio prestazioni rete valuta la latenza del collegamento e le perdite di pacchetti. 

Il formato, le dimensioni e la sequenza di questi pacchetti dipendono dal protocollo scelto durante la creazione delle regole di monitoraggio. A seconda del protocollo dei pacchetti, i dispositivi di rete intermedi, ad esempio router e commutatori, possono elaborare questi pacchetti in modo diverso. Di conseguenza, la scelta del protocollo influisce sulla precisione dei risultati. La scelta del protocollo determina anche l'eventuale necessità di eseguire procedure manuali dopo aver distribuito la soluzione Monitoraggio prestazioni rete. 

Monitoraggio prestazioni rete offre la possibilità di scegliere tra i protocolli ICMP e TCP per l'esecuzione di transazioni sintetiche. Se si sceglie il protocollo ICMP quando si crea una regola di transazione sintetica, gli agenti di Monitoraggio prestazioni rete usano messaggi ECHO ICMP per calcolare la latenza di rete e la perdita di pacchetti. ECHO ICMP usa lo stesso messaggio inviato dall'utilità ping tradizionale. Quando si usa TCP come protocollo, gli agenti di Monitoraggio prestazioni rete inviano in rete pacchetti SYN TCP. Questa operazione è seguita dal completamento di un handshake TCP e la connessione viene rimossa tramite pacchetti RST. 

Per scegliere il protocollo, prendere in considerazione le informazioni seguenti: 

* **Individuazione di più route di rete.** TCP offre maggiore precisione nell'individuazione di più route e necessita di un minor numero di agenti in ogni subnet. Ad esempio, uno o due agenti con protocollo TCP sono capaci di individuare tutti i percorsi ridondanti tra le subnet. Per ottenere risultati analoghi sono necessari diversi agenti con protocollo ICMP. Con ICMP, se è presente un numero N di route tra due subnet, è necessario usare un numero di agenti pari a oltre N5 in una subnet di origine o di destinazione.

* **Precisione dei risultati.** I router e i commutatori tendono ad assegnare una priorità inferiore ai pacchetti ICMP ECHO rispetto ai pacchetti TCP. In determinati casi, quando è presente un carico eccessivo dei dispositivi di rete, i dati ottenuti dal protocollo TCP riflettono più da vicino la perdita e la latenza sperimentate dalle applicazioni. Ciò si verifica perché la maggior parte del traffico delle applicazioni fluisce attraverso TCP. In questi casi, il protocollo ICMP offre risultati meno precisi rispetto a TCP. 

* **Configurazione del firewall.** Il protocollo TCP prevede che i pacchetti TCP vengano inviati a una porta di destinazione. La porta predefinita usata dagli agenti di Monitoraggio prestazioni rete è 8084. È possibile cambiare la porta quando si configurano gli agenti. Verificare che i firewall di rete o le regole dei gruppi di sicurezza di rete (in Azure) consentano il traffico sulla porta. È necessario assicurarsi che anche il firewall locale nei computer in cui sono installati gli agenti sia configurato per consentire il traffico su questa porta. È possibile usare script di PowerShell per configurare le regole del firewall nei computer che eseguono Windows, mentre il firewall di rete deve essere configurato manualmente. Al contrario, il protocollo ICMP non funziona attraverso una porta. Nella maggior parte degli scenari aziendali il traffico ICMP può attraversare i firewall per consentire l'uso di strumenti di diagnostica di rete come l'utilità ping. Se si riesce a eseguire il ping di un computer da un altro computer, è possibile usare il protocollo ICMP senza dover configurare i firewall manualmente.

>[!NOTE] 
> Alcuni firewall possono bloccare ICMP, causando potenzialmente una ritrasmissione che genera un numero elevato di eventi nel sistema di gestione delle informazioni di sicurezza e degli eventi. Verificare che il protocollo scelto non sia bloccato da un firewall di rete o un gruppo di sicurezza di rete, altrimenti Monitoraggio prestazioni rete non può monitorare il segmento di rete. È consigliabile usare TCP per il monitoraggio. Usare ICMP negli scenari in cui non è possibile usare TCP, ad esempio quando: 
>
> - Si usano nodi basati su client Windows, perché i socket di tipo raw TCP non sono consentiti nei client Windows.
> - Il firewall di rete o il gruppo di sicurezza di rete blocca TCP.
> - Non si è capaci di cambiare protocollo.

Se si sceglie di usare ICMP durante la distribuzione, è possibile passare a TCP in qualsiasi momento modificando la regola di monitoraggio predefinita.

1. Passare a **Prestazioni di rete** > **Monitoraggio** > **Configura** > **Monitoraggio**. Selezionare quindi  **Default rule** (Regola predefinita). 
2. Scorrere fino alla sezione **Protocollo** e selezionare il protocollo che si vuole usare. 
3. Selezionare **Salva** per applicare l'impostazione. 

Anche se la regola predefinita usa un protocollo specifico, è possibile creare nuove regole con un protocollo diverso. È anche possibile creare una combinazione di regole in cui alcune usano ICMP e altre TCP. 

## <a name="walkthrough"></a>Procedura dettagliata 

Si prenda ora in esame un'analisi semplice della causa radice di un evento di integrità.

Nel dashboard della soluzione è presente un evento di integrità che mostra un collegamento di rete non integro. Per analizzare il problema, selezionare il riquadro **Collegamenti di rete monitorati**.

La pagina di drill-down indica che il collegamento di rete **DMZ2-DMZ1** non è integro. Selezionare **Visualizza collegamenti subnet** per questo collegamento di rete. 


La pagina di drill-down mostra tutti i collegamenti di subnet inclusi nel collegamento di rete **DMZ2-DMZ1**. Per entrambi i collegamenti di subnet, la latenza ha superato la soglia, compromettendo l'integrità del collegamento di rete. È anche possibile vedere le tendenze della latenza di entrambi i collegamenti di subnet. Usare il controllo di selezione di data e ora nel grafico per delimitare l'intervallo di tempo necessario. È possibile vedere l'ora del giorno in cui la latenza ha raggiunto il valore di picco. Per analizzare il problema, cercare successivamente nei log questo intervallo di tempo. Selezionare **Visualizza collegamenti nodo** per eseguire ulteriori operazioni di drill-down. 
 
 ![Pagina Collegamenti della subnet](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

In modo simile alla pagina precedente, nella pagina di drill-down di questo collegamento di subnet vengono elencati i relativi collegamenti di nodo. È possibile eseguire azioni simili, come nel passaggio precedente. Selezionare **Visualizza topologia** per visualizzare la topologia tra i due nodi. 
 
 ![Pagina Collegamenti nodo](media/network-performance-monitor-performance-monitor/node-links.png) 

Tutti i percorsi tra i due nodi selezionati vengono tracciati nella mappa topologica. È possibile visualizzare la topologia hop-by-hop delle route tra due nodi nella mappa topologica. Tale mappa offre un quadro preciso del numero di route esistenti tra i due nodi e dei percorsi intrapresi dai pacchetti di dati. I colli di bottiglia delle prestazioni di rete sono visualizzati in rosso. Per individuare connessioni o dispositivi di rete difettosi, identificare gli elementi in rosso nella mappa della topologia. 

 ![Dashboard Topologia con mappa della topologia](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

È possibile rivedere i dati relativi a perdita, latenza e numero di hop di ogni percorso nel riquadro **Azione**. Usare la barra di scorrimento per visualizzare i dettagli dei percorsi non integri. Usare i filtri per selezionare i percorsi con l'hop non integro, in modo che venga tracciata la topologia solo per i percorsi selezionati. Per ingrandire o ridurre la mappa della topologia, usare la rotellina del mouse. 

Nella figura seguente la causa radice delle aree problematiche relative alla sezione specifica della rete è visualizzata negli hop e nei percorsi di colore rosso. Selezionare un nodo nella mappa della topologia per visualizzare le proprietà di tale nodo, inclusi il nome di dominio completo e l'indirizzo IP. Selezionando un hop viene visualizzato il relativo indirizzo IP. 
 
![Mappa della topologia con proprietà del nodo selezionato](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Passaggi successivi
[Effettuare una ricerca nei log](../../log-analytics/log-analytics-queries.md) per visualizzare i record dettagliati dei dati delle prestazioni di rete.
