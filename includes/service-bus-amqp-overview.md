
# Supporto per il protocollo AMQP 1.0 nel bus di servizio

> Il protocollo AMQP (Advanced Message Queueing Protocol) 1.0 è
> supportato sia nel bus di servizio di Azure che nel bus di servizio
> per Windows Server (Service Bus 1.1). AMQP consente di creare
> applicazioni ibride multipiattaforma utilizzando un protocollo aperto
> standard. È possibile creare applicazioni utilizzando componenti
> creati in linguaggi e framework diversi e in esecuzione su sistemi
> operativi diversi. Tutti questi componenti possono connettersi al bus
> di servizio e scambiare in modo lineare ed efficiente messaggi di
> business strutturati con assoluta fedeltà.

## Introduzione: Informazioni sul protocollo AMQP 1.0 e sulla sua rilevanza

I prodotti middleware orientati ai messaggi hanno tradizionalmente utilizzato protocolli proprietari per le comunicazioni tra applicazioni client e broker. Dopo la selezione di un broker di messaggistica di un fornitore specifico, è pertanto necessario utilizzare le librerie di tale fornitore per connettere le applicazioni client a tale broker. Ne risulta un certo livello di blocco rispetto a tale fornitore, poiché il trasferimento di un'applicazione a un prodotto diverso richiede la scrittura di nuovo codice per tutte le applicazioni connesse.

La connessione di broker di messaggistica da fornitori diversi è inoltre complicata e richiede in genere bridging a livello di applicazione per lo spostamento dei messaggi da un sistema all'altro e per la conversione nei diversi formati di messaggio proprietari. Questa esigenza è molto comune, ad esempio, quando si rende disponibile una nuova interfaccia unificata per sistemi diversi precedenti oppure quando si integrano sistemi IT diversi dopo una fusione tra società.

Il settore software è caratterizzato da rapido sviluppo. Nuovi linguaggi di programmazione e framework applicazione vengono inventati a ritmo sorprendente. Analogamente, i requisiti dei sistemi IT si evolvono nel tempo e gli sviluppatori desiderano avvalersi delle possibilità offerte dai linguaggi e dai framework più recenti. A volte, tuttavia, il fornitore di messaggistica selezionato non supporta tali piattaforme. Poiché vengono utilizzati protocolli proprietari, non è possibile offrire librerie diverse per le nuove piattaforme. Si è pertanto costretti a creare gateway e a ricorrere a bridging e ad altri approcci analoghi.

Il protocollo AMQP (Advanced Message Queuing Protocol) 1.0 è stato sviluppato per risolvere questi problemi presso JP Morgan Chase, che, come la maggior parte delle aziende del settore dei servizi finanziari, fa ampio uso di prodotti middleware orientati alla messaggistica. L'obiettivo da raggiungere era semplice: creare un protocollo di messaggistica a standard aperto che consentisse di creare applicazioni basate sui messaggi utilizzando componenti creati in linguaggi, framework e sistemi operativi diversi, avvalendosi dei migliori prodotti offerti da diversi fornitori.

## Caratteristiche tecniche del protocollo AMQP 1.0

AMQP 1.0 è un protocollo di messaggistica wire-level efficiente e affidabile che può essere utilizzato per creare applicazioni di messaggistica multipiattaforma e affidabili. Questo protocollo assolve a uno scopo semplice, ovvero definire i meccanismi per la trasmissione sicura, affidabile ed efficiente dei messaggi tra due parti. I messaggi stessi vengono codificati utilizzando una rappresentazione dati portatile che consente a mittenti e destinatari eterogenei di scambiarsi messaggi di business strutturati con assoluta fedeltà. Di seguito sono riepilogate le caratteristiche più importanti:

* **Efficienza**: AMQP 1.0 è un protocollo orientato alla connessione
  che utilizza una codifica binaria per le istruzioni del protocollo e i
  messaggi di business trasmessi su di esso. Integra sofisticati schemi
  di controllo di flusso per massimizzare l'utilizzo della rete e dei
  componenti connessi. In ogni caso, il protocollo è stato progettato
  per assicurare un equilibrio tra efficienza, flessibilità e
  interoperabilità.
* **Affidabilità**: Il protocollo AMQP 1.0 consente lo scambio di
  messaggi con una serie di garanzie di affidabilità, dal
  "fire-and-forget" al recapito confermato affidabile di tipo
  "exactly-once".?
* **Flessibilità**: AMQP 1.0 è un protocollo flessibile che consente di
  supportare diverse topologie. È possibile utilizzare lo stesso
  protocollo per le comunicazioni client-client, client-broker e
  broker-broker.
* **Indipendenza dal modello di broker**: Il protocollo AMQP 1.0 non
  prevede requisiti relativi al modello di messaggistica utilizzato da
  un broker. È pertanto possibile aggiungere con facilità il supporto
  per il protocollo AMQP 1.0 ai broker di messaggistica esistenti.

## AMQP 1.0 è uno standard affermato

Lo sviluppo di AMQP 1.0 procede dal 2008 a opera di un gruppo fondamentale di oltre 20 società, di cui fanno parte fornitori di tecnologie e aziende utenti finali. In questo lasso di tempo, le aziende utenti hanno comunicato i propri requisiti aziendali reali e i fornitori di tecnologie hanno proseguito lo sviluppo del protocollo con l'intento di soddisfarli. Nel corso del processo i fornitori hanno partecipato a workshop, che hanno permesso loro di collaborare per verificare l'interoperabilità tra le diverse implementazioni.

A ottobre 2011 il lavoro di sviluppo è passato a un comitato tecnico all'interno della Organization for the Advancement of Structured Information Standards (OASIS) e a ottobre 2012 è stato rilasciato lo standard OASIS AMQP 1.0. Del comitato tecnico hanno fatto parte le seguenti aziende durante lo sviluppo dello standard:

* **Fornitori di tecnologie**: Axway Software, Huawei Technologies, IIT
  Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation,
  Primeton Technologies, Progress Software, Red Hat, SITA, Software AG,
  Solace Systems, VMware, WSO2, Zenika.
* **Aziende utenti**: Bank of America, Credit Suisse, Deutsche Boerse,
  Goldman Sachs, JPMorgan Chase.

Di seguito sono indicati alcuni dei vantaggi più apprezzati degli standard aperti:

* Minore possibilità di blocco da parte del fornitore
* Interoperabilità
* Ampia disponibilità di librerie e strumenti
* Protezione dall'obsolescenza
* Disponibilità di personale competente
* Rischio ridotto e gestibile

## AMQP 1.0 e bus di servizio

Grazie al protocollo AMQP 1.0 è ora possibile utilizzare le funzionalità di accodamento e di messaggistica negoziata di pubblicazione/sottoscrizione offerte dal bus di servizio da numerose piattaforme, utilizzando un protocollo binario efficiente. Inoltre, è possibile compilare applicazioni costituite da componenti creati con un insieme di linguaggi, framework e sistemi operativi.

Nel diagramma seguente è illustrata una distribuzione di esempio, in cui i client Java in esecuzione su Linux, scritti utilizzando l'API JMS (Java Message Service) standard, e i client .NET in esecuzione su Windows si scambiano messaggi tramite il bus di servizio utilizzando il protocollo AMQP 1.0.

![](./media/service-bus-amqp-overview/Example1.png)

**Figura 1: scenario di distribuzione di esempio, in cui per la messaggistica tra diverse piattaforme si utilizzano il bus di servizio e il protocollo AMQP 1.0**

È attualmente possibile utilizzare le librerie client seguenti con il bus di servizio:

<table>
  <tr>
    <th>Linguaggio</th>

    <th>Libreria</th>

  </tr>

  <tr>
    <td>Java</td>

    <td>Client Apache Qpid Java Message Service (JMS)<br  />
 Client IIT Software SwiftMQ Java</td>

  </tr>

  <tr>
    <td>C</td>

    <td>Apache Qpid Proton-C</td>

  </tr>

  <tr>
    <td>PHP</td>

    <td>Apache Qpid Proton-PHP</td>

  </tr>

  <tr>
    <td>Python</td>

    <td>Apache Qpid Proton-Python</td>

  </tr>


</table>

**Figura 2: tabella delle librerie client del protocollo AMQP 1.0**

Per ulteriori informazioni su come ottenere e utilizzare queste librerie con Service Bus, vedere la Guida per sviluppatori di AMQP per il bus di servizio. Per ulteriori informazioni, vedere la sezione "Riferimenti" più avanti.

## Riepilogo

* AMQP 1.0 è un protocollo di messaggistica aperto e affidabile, che può essere utilizzato per creare applicazioni ibride multipiattaforma. AMQP 1.0 è uno standard OASIS.
* Il supporto per il protocollo AMQP 1.0 è ora disponibile nel bus di servizio di Azure e bus di servizio per Windows Server (Service Bus 1.1). La determinazione dei prezzi è analoga a quella dei protocolli esistenti.

## Riferimenti

* [Come utilizzare AMQP 1.0 con l'API . NET di Service Bus][1]
* [Come utilizzare l'API JMS (Java Message Service) con Service Bus e
  AMQP 1.0][2]
* [AMQP per Service Bus 1.0 - Guida per sviluppatori][3]
* [Specifiche relative al protocollo OASIS Advanced Message Queuing
  Protocol (AMQP) Versione 1.0][4]



[1]: http://aka.ms/lym3vk
[2]: http://aka.ms/ll1fm3
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj841071.aspx
[4]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf