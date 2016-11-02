<properties
   pageTitle="Linee guida di monitoraggio e diagnostica | Microsoft Azure"
   description="Procedure consigliate per il monitoraggio di applicazioni distribuite nel cloud."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="masashin"/>


# <a name="monitoring-and-diagnostics-guidance"></a>Indicazioni di monitoraggio e diagnostica

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Overview
Le applicazioni distribuite e i servizi in esecuzione nel cloud sono, per loro natura, componenti software complessi che comprendono molte parti mobili. In un ambiente di produzione è importante essere in grado di rilevare il modo in cui gli utenti usano il sistema, di tracciare l'utilizzo delle risorse e, in generale, di monitorare l'integrità e le prestazioni del sistema stesso. Queste informazioni possono essere usate come strumento diagnostico per rilevare e correggere i problemi, nonché per individuare potenziali problemi e impedire che si verifichino.

## <a name="monitoring-and-diagnostics-scenarios"></a>Scenari di monitoraggio e diagnostica
È possibile usare il monitoraggio per ottenere informazioni dettagliate sulla correttezza del funzionamento di un sistema. Il monitoraggio è una parte fondamentale della gestione degli obiettivi di qualità del servizio. Alcuni scenari comuni per la raccolta dei dati di monitoraggio sono:

- Assicurarsi che il sistema rimanga integro.
- Tracciare la disponibilità del sistema e dei relativi elementi componenti.
- Gestione delle prestazioni per assicurarsi che la velocità effettiva del sistema non si riduca in modo imprevisto all’aumento del volume di lavoro.
- Garantire che il sistema soddisfi eventuali contratti di servizio (SLA) definiti con i clienti.
- Proteggere la privacy e la sicurezza del sistema, utenti e i relativi dati.
- Tracciare le operazioni eseguite a scopo di controllo o a fini normativi.
- Monitorare l'utilizzo quotidiano del sistema e individuare le tendenze che, se non affrontate, potrebbero creare problemi.
- Rilevamento di problemi che si verificano, dal report iniziale tramite l’analisi delle possibili cause, la rettifica, gli aggiornamenti software conseguenti e la distribuzione.
- Operazioni di rilevamento e debug di versioni del software.

> [AZURE.NOTE] Questo elenco non vuole essere completo. In questo documento vengono trattati principalmente questi scenari, perché rappresentano le situazioni più comuni in cui viene eseguito il monitoraggio. Potrebbero esistere altre situazioni meno comuni o specifiche dell'ambiente in uso.

Le sezioni seguenti descrivono questi scenari in modo più dettagliato. Le informazioni per ogni scenario sono esaminate nel seguente formato:

1. Breve panoramica dello scenario.
2. Requisiti tipici di questo scenario.
3. Dati di strumentazione non elaborati richiesti per supportare lo scenario e le possibili origini di queste informazioni.
4. Modalità di analisi dei dati non elaborati e possibili combinazioni di questi per generare informazioni diagnostiche significative.

## <a name="health-monitoring"></a>Monitoraggio dell’integrità
Un sistema è integro se funziona ed è in grado di elaborare le richieste. Lo scopo del monitoraggio dello stato è generare uno snapshot dello stato attuale di integrità del sistema che consenta di verificare che tutti i componenti del sistema funzionino come previsto.

### <a name="requirements-for-health-monitoring"></a>Requisiti per il monitoraggio dell’integrità
Un operatore deve ricevere un avviso rapidamente (entro pochi secondi) se qualsiasi parte del sistema è ritenuta non integra. L'operatore deve essere in grado di verificare quali parti del sistema funzionano normalmente e in quali parti si verificano problemi. L'integrità del sistema può essere evidenziata tramite un sistema di semafori:

- Rosso, che indica mancanza di integrità (sistema arrestato)
- Giallo, che indica integrità parziale (il sistema è in esecuzione con funzionalità ridotte)
- Verde, che indica integrità totale

Un sistema di monitoraggio completo consente a un operatore di eseguire il drill-down attraverso il sistema per visualizzare lo stato di integrità di sottosistemi e componenti. Ad esempio, se tutto il sistema viene rappresentato come parzialmente integro, l'operatore deve essere in grado di eseguire lo zoom avanti e determinare quale funzionalità sia attualmente disponibile.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
I dati non elaborati richiesti per supportare il monitoraggio dello stato possono essere generati come risultato di:

- Tracciare l'esecuzione delle richieste degli utenti. Queste informazioni permettono di determinare quali richieste hanno avuto esito positivo, quali esito negativo e la durata di ogni richiesta.
- Monitoraggio sintetico degli utenti. Questo processo simula i passaggi eseguiti da un utente e segue una serie di passaggi predefiniti. I risultati di ogni passaggio devono essere acquisiti.
- Eccezioni di registrazione, errori e avvisi. Queste informazioni possono essere acquisite a seguito di istruzioni di traccia incorporate nel codice dell'applicazione e recuperando informazioni dai registri eventi dei servizi a cui il sistema fa riferimento.
- Monitoraggio dell'integrità di eventuali servizi di terze parti usati dal sistema. Questo monitoraggio potrebbe richiedere il recupero e l'analisi dei dati di integrità forniti da questi servizi. Queste informazioni possono avere diversi formati.
- Monitoraggio endpoint Questo meccanismo è descritto più dettagliatamente nella sezione "Monitoraggio della disponibilità".
- Raccolta di informazioni sulle prestazioni dell’ambiente, ad esempio l'utilizzo della CPU in background o un'attività I/O (rete compresa).

### <a name="analyzing-health-data"></a>Analisi dei dati di integrità
L'obiettivo principale del monitoraggio dell’integrità è indicare rapidamente se il sistema è in esecuzione. L'analisi critica dei dati immediati può attivare un avviso se un componente critico viene rilevato come non integro, perché, ad esempio, non risponde a una serie consecutiva di ping. L'operatore può quindi richiedere l'azione correttiva appropriata.

Un sistema più avanzato potrebbe includere un elemento predittivo che esegue un'analisi a freddo sui carichi di lavoro recenti e attuali. Un'analisi a freddo è in grado di individuare tendenze e di determinare se è probabile che il sistema rimanga integro o se necessita di risorse aggiuntive. Questo elemento predittivo deve basarsi sulle metriche di prestazioni critiche, ad esempio:

- La frequenza delle richieste dirette a ogni servizio o sottosistema.
- I tempi di risposta di tali richieste.
- Il volume del flusso dei dati in entrata e in uscita da ogni servizio.

Se il valore di qualsiasi metrica supera una soglia definita, il sistema può generare un avviso per consentire a un operatore o alla scalabilità automatica (se disponibile) di eseguire le azioni preventive necessarie per garantire l'integrità del sistema. Queste azioni possono implicare l'aggiunta di risorse, riavviare uno o più i servizi con esito negativo o applicare la limitazione per le richieste di priorità inferiore.

## <a name="availability-monitoring"></a>Monitoraggio della disponibilità
Un sistema realmente integro richiede la disponibilità dei componenti e dei sottosistemi che lo costituiscono. Il monitoraggio della disponibilità è strettamente correlato al monitoraggio dello stato, ma mentre il monitoraggio dello stato fornisce una visualizzazione immediata dell'integrità del sistema in quel momento, il monitoraggio della disponibilità rileva la disponibilità del sistema e dei relativi componenti per generare statistiche riguardanti il tempo di attività del sistema stesso.

In molti sistemi, alcuni componenti (ad esempio un database) sono configurati con ridondanza incorporata per consentire il failover rapido in caso di errore grave o perdita di connettività. In una situazione ideale, gli utenti non dovrebbero rendersi conto che si è verificato un errore di questo tipo, ma dal punto di vista di monitoraggio della disponibilità è necessario raccogliere quante più informazioni possibile su tali errori per determinarne la causa e intraprendere azioni correttive per impedire che si verifichino di nuovo.

I dati necessari per tener traccia della disponibilità potrebbero dipendere da diversi fattori di basso livello, molti dei quali potrebbero essere specifici dell'applicazione, del sistema e dell'ambiente. Un sistema di monitoraggio efficace acquisisce i dati di disponibilità che corrispondono a questi fattori di basso livello e quindi vengono aggregati in modo da offrire una panoramica del sistema. Ad esempio, in un sistema di e-commerce, la funzionalità di business che consente a un cliente di effettuare ordini può dipendere dall'archivio in cui vengono archiviati i dettagli dell'ordine e dal sistema di pagamento che gestisce le transazioni monetarie di pagamento di tali ordini. La disponibilità della parte del sistema relativa alla creazione degli ordini è pertanto funzione della disponibilità dell'archivio e del sottosistema di pagamento.

### <a name="requirements-for-availability-monitoring"></a>Requisiti per il monitoraggio della disponibilità
Un operatore deve anche essere in grado di visualizzare la disponibilità cronologica di ogni sistema e sottosistema e usare queste informazioni per individuare eventuali tendenze che possono causare periodicamente errori di uno o più sottosistemi. Gli errori dei servizi iniziano a una determinata ora,corrispondente a un picco delle operazioni di elaborazione?

Una soluzione di monitoraggio deve offrire una visualizzazione immediata e cronologica della disponibilità o dell'indisponibilità di ogni sistema. Deve anche essere in grado di avvisare rapidamente un operatore quando uno o più servizi non funzionano o quando gli utenti non riescono a connettersi ai servizi. Non si tratta semplicemente di monitorare ogni servizio ma anche di esaminare le azioni eseguite da ogni utente, se tali azioni hanno esito negativo quando tentano di comunicare con un servizio. Entro certi limiti qualche problema di connettività è normale e può essere dovuto a errori temporanei, ma può essere utile consentire al sistema di generare un avviso relativo al numero di errori di connessione a un sottosistema specifico durante un determinato periodo di tempo.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
Come nel monitoraggio dello stato, i dati non elaborati necessari per supportare il monitoraggio della disponibilità possono essere generati tramite Synthetic User Monitoring e mediante registrazione delle eccezioni, degli errori e degli avvisi che potrebbero verificarsi. Inoltre, è possibile ottenere i dati sulla disponibilità dal monitoraggio degli endpoint. L'applicazione può esporre uno o più endpoint di integrità, ogni test di accesso a un'area funzionale all'interno del sistema. Il sistema di monitoraggio può eseguire il ping di ogni endpoint in base a una pianificazione definita e raccogliere i risultati (esito positivo o esito negativo).

Tutti i timeout, gli errori di connettività di rete e i tentativi di connessione devono essere registrati. Tutti i dati devono riportare un timestamp.

<a name="analyzing-availability-data"></a>
### <a name="analyzing-availability-data"></a>Analisi dei dati di disponibilità
I dati di strumentazione devono essere aggregati e correlati per supportare i seguenti tipi di analisi:

- La disponibilità immediata del sistema e dei sottosistemi.
- La frequenza degli errori di disponibilità del sistema e dei sottosistemi. In una situazione ideale, un operatore deve essere in grado di mettere in relazione gli errori con attività specifiche, identificando il problema alla base di un errore del sistema.
- Una visualizzazione cronologica della frequenza degli errori del sistema o di qualsiasi sottosistema in qualsiasi periodo di tempo specificato e il carico d lavoro del sistema, ad esempio il numero di richieste degli utenti, al momento dell'errore.
- I motivi della mancata disponibilità del sistema o dei sottosistemi. Ad esempio, servizio non in esecuzione, perdita di connettività, timeout della connessione o ricezione di errori.

È possibile calcolare la percentuale di disponibilità di un servizio in un periodo di tempo specifico tramite la formula seguente:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Ciò è utile per gli scopi dei contratti di servizio. Il [monitoraggio del contratto di servizio](#SLA-monitoring) è descritto in dettaglio più avanti in queste indicazioni. La definizione di _tempo di inattività_ dipende dal servizio. Ad esempio, il Servizio di compilazione Visual Studio Team Services definisce il tempo di inattività come il periodo durante il quale il Servizio di compilazione non è disponibile (minuti totali accumulati). Un minuto è considerato tempo di non disponibilità se tutte le richieste HTTP continue al Servizio di compilazione per l'esecuzione di operazioni avviate dal cliente per l'intero minuto generano un codice errore o non restituiscono alcuna risposta.

## <a name="performance-monitoring"></a>Monitoraggio delle prestazioni
Quando il sistema si trova in condizioni di carico sempre maggiore per l'aumento del volume di utenti, la dimensione dei set di dati a cui tali utenti accedono aumenta, così come la probabilità di errori di uno o più componenti. Spesso, il guasto di un componente è preceduto da una riduzione delle prestazioni. Se si è in grado di rilevare tale riduzione, è possibile adottare misure proattive per ovviare al problema.

Le prestazioni del sistema dipendono da numerosi fattori. Ogni fattore in genere viene misurato tramite indicatori di prestazioni chiave (KPI, Key Performance Indicator), ad esempio il numero di transazioni di database al secondo o il volume di richieste di rete gestite correttamente nell'intervallo di tempo specificato. Alcuni di questi indicatori KPI possono essere disponibili come misure di prestazioni specifiche, mentre altri possono derivare da una combinazione di metriche.

> [AZURE.NOTE] Determinando buone o scarse prestazioni, è necessario comprendere il livello di prestazioni in cui il sistema deve essere in grado di funzionare. È necessario osservare il sistema mentre è sottoposto al carico di lavoro abituale e acquisire i dati per ogni indicatore KPI in un determinato periodo di tempo. Questo potrebbe comportare l'esecuzione del sistema sotto un carico simulato in un ambiente di test e raccogliere i dati appropriati prima di distribuire il sistema in un ambiente di produzione.

> Assicurarsi poi che il monitoraggio delle prestazioni non diventi un onere per il sistema. È possibile regolare dinamicamente il livello di dettaglio relativo ai dati raccolti dal processo di monitoraggio delle prestazioni.

### <a name="requirements-for-performance-monitoring"></a>Requisiti per il monitoraggio delle prestazioni
Per esaminare le prestazioni del sistema, un operatore in genere deve visualizzare le informazioni relative a:

- Velocità di risposta per le richieste degli utenti.
- Il numero di richieste utente simultanee.
- Il volume di traffico di rete.
- La velocità a cui le transazioni aziendali vengono completate.
- Tempo medio di elaborazione delle richieste.

Può inoltre essere utile fornire strumenti che consentono a un operatore di individuare delle correlazioni, ad esempio:

- Il numero di utenti simultanei rispetto al tempo di latenza della richiesta, ovvero il tempo che trascorre tra l'invio di una richiesta da parte di un utente e l'inizio dell'elaborazione della richiesta stessa.
- Il numero di utenti simultanei rispetto al tempo medio di risposta, ovvero il tempo necessario per il completamento di una richiesta dall'avvio dell'elaborazione.
- Il volume di richieste rispetto al numero di errori di elaborazione.

Oltre a queste informazioni funzionali di alto livello, un operatore deve anche essere in grado di ottenere una visualizzazione dettagliata delle prestazioni per ogni componente nel sistema. Questi dati vengono in genere forniti tramite contatori di prestazioni di basso livello che tengono traccia di informazioni quali:

- Utilizzo della memoria.
- Numero di thread.
- Tempo di elaborazione della CPU.
- Lunghezza della coda di richieste.
- Velocità ed errori di I/O del disco o della rete.
- Numero di byte scritti o letti.
- Indicatori di middleware, ad esempio lunghezza della coda.

Tutte le visualizzazioni devono consentire a un operatore di specificare un periodo di tempo. I dati visualizzati possono costituire lo snapshot della situazione corrente e/o la visualizzazione cronologica delle prestazioni.

Un operatore deve essere in grado di generare un avviso in base alla misura di qualsiasi prestazione per qualsiasi valore dato durante un intervallo di tempo specificato.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
È possibile raccogliere dati di alto livello relativi alle prestazioni (velocità effettiva, numero di utenti simultanei, numero di transazioni aziendali, frequenza di errori e così via) tramite il monitoraggio dello stato delle richieste degli utenti nel momento in cui arrivano e passano attraverso il sistema. Ciò implica che in corrispondenza di punti chiave del codice dell'applicazione siano presenti istruzioni di traccia e informazioni di temporizzazione. Tutti gli errori, le eccezioni e gli avvisi devono essere acquisiti con i dati sufficienti a metterli in relazione alle richieste che li hanno causati. Un'altra utile fonte di informazioni è costituita dal log di Internet Information Services (IIS).

Se possibile, è inoltre consigliabile acquisire dati sulle prestazioni per i sistemi esterni utilizzati dall'applicazione. Questi sistemi esterni potrebbero fornire i propri contatori delle prestazioni o altre funzionalità per la richiesta di dati sulle prestazioni. Se ciò non è possibile, registrare informazioni quali l'ora di inizio e l'ora di fine di ogni richiesta effettuata a un sistema esterno insieme allo stato dell'operazione (esito positivo, errore o avviso). Per le richieste di tempo, ad esempio, è possibile usare un approccio simile al funzionamento di un cronometro, avviando un timer al momento dell'avvio della richiesta e arrestando il timer al completamento della richiesta stessa.

Dati di basso livello sulle prestazioni per singoli componenti all'interno di un sistema possono essere disponibili tramite funzionalità e servizi quali i contatori delle prestazioni Windows e Diagnostica di Azure.

### <a name="analyzing-performance-data"></a>Analisi dei dati sulle prestazioni
Gran parte delle attività di analisi consistono nell'aggregazione dei dati sulle prestazioni per tipo di richiesta utente e/o sottosistema o servizio a cui viene inviata ogni richiesta. Esempi di richiesta utente sono l'aggiunta di un articolo al carrello o la procedura di completamento della transazione in un sistema di e-commerce.

Un altro requisito comune è il riepilogo dei dati sulle prestazioni in percentili selezionati. Un operatore, ad esempio, può determinare i tempi di risposta per il 99%, il 95% e il 70% delle richieste. Per ogni percentile potrebbero essere impostati obiettivi del contratto di servizio o altri obiettivi. I risultati devono essere segnalati quasi in tempo reale per consentire il rilevamento di problemi immediati. I risultati devono anche essere aggregati in base a periodi più lunghi per scopi statistici.

Nel caso di problemi di latenza che influiscono sulle prestazioni, un operatore deve essere rapidamente in grado di identificare la causa del collo di bottiglia esaminando la latenza di ogni passaggio di ogni richiesta. I dati sulle prestazioni devono pertanto fornire un mezzo di correlazione di misure delle prestazioni per ogni passaggio per collegarle a una richiesta specifica.

A seconda dei requisiti di visualizzazione, potrebbe essere utile generare e archiviare un cubo dati contenente visualizzazioni dei dati non elaborati. Il cubo consente di eseguire query ad hoc e analisi complesse delle informazioni relative alle prestazioni.

## <a name="security-monitoring"></a>Monitoraggio della protezione
Tutti i sistemi commerciali che contengono dati riservati devono implementare una struttura di protezione. La complessità del meccanismo di sicurezza è in genere una funzione di riservatezza dei dati. In un sistema che richiede l'autenticazione degli utenti è necessario registrare:

- Tutti i tentativi di accesso, sia quelli con esito positivo che quelli con esito negativo.
- Tutte le operazioni eseguite da un utente autenticato, con i dettagli relativi a tutte le risorse usate.
- Data e ora in cui l'utente ha terminato una sessione e si è disconnesso.

Il monitoraggio può consentire di rilevare attacchi al sistema. Un numero elevato di tentativi di accesso non riusciti, ad esempio, potrebbe indicare un attacco di forza bruta. Un aumento imprevisto delle richieste potrebbe essere il risultato di attacchi Distributed Denial of Service (DDoS). È necessario monitorare tutte le richieste a tutte le risorse, indipendentemente dall'origine di tali richieste. Un sistema che presenta una vulnerabilità di accesso potrebbe esporre accidentalmente le risorse al mondo esterno senza che un utente debba effettivamente accedere.

### <a name="requirements-for-security-monitoring"></a>Requisiti per il monitoraggio della protezione
Gli aspetti più critici del monitoraggio della protezione devono consentire rapidamente a un operatore di:

- Rilevare tentativi di intrusione di entità non autenticate.
- Identificare i tentativi di esecuzione di operazioni su dati da parte di entità non autorizzate ad accedere a tali dati.
- Determinare se il sistema o una parte di questo è sotto attacco dall'esterno o dall'interno, ad esempio se un utente malintenzionato autenticato sta tentando di arrestare il sistema.

Per supportare tali requisiti, un operatore deve ricevere una notifica:

- Se un account esegue una serie di tentativi non riusciti di accesso entro un periodo specificato.
- Se un solo account autenticato tenta ripetutamente di accedere a una risorsa non autorizzata durante un periodo specificato.
- Se si verifica un numero elevato di richieste non autenticate o non autorizzate durante un periodo specificato.

Le informazioni fornite a un operatore devono includere l'indirizzo dell'host di origine di ogni richiesta. Se si verificano regolarmente violazioni della sicurezza da un intervallo di indirizzi specifico, è possibile bloccare gli host corrispondenti.

Una parte fondamentale nella gestione della sicurezza di un sistema è in grado di rilevare rapidamente azioni che si discostano dai modello abituale. Per facilitare la rilevazione di picchi di attività in orari insoliti, è possibile rappresentare visivamente informazioni quali il numero di richieste di accesso con esito positivo e/o con esito negativo. Un esempio di attività insolita è l'accesso di utenti alle 3.00 e l'esecuzione di un gran numero di operazioni fino all'inizio della giornata di lavoro, alle 9.00. Queste informazioni possono essere usate anche per semplificare la configurazione della scalabilità automatica pianificata. Se ad esempio un operatore osserva che un numero elevato di utenti accede regolarmente a una determinata ora del giorno, può disporre l'avvio di servizi di autenticazione aggiuntivi per gestire il volume di lavoro, arrestandoli quando il periodo di picco è stato superato.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
La sicurezza è un aspetto onnicomprensivo della maggior parte dei sistemi distribuiti. È probabile che i dati pertinenti vengano generati in più punti all'interno di un sistema. È consigliabile adottare un approccio di gestione delle informazioni e degli eventi di sicurezza (SIEM, Security Information and Event Management) per raccogliere le informazioni relative alla sicurezza derivanti da eventi generati dall'applicazione nonché da apparecchiature di rete, server, firewall, software antivirus e altri elementi di prevenzione delle intrusioni.

Il monitoraggio della sicurezza può incorporare dati da strumenti che non fanno parte dell'applicazione, ad esempio utilità che identificano attività di analisi delle porte da parte di agenzie esterne o filtri di rete in grado di rilevare tentativi di accesso non autenticato all'applicazione e ai dati.

In tutti i casi i dati raccolti devono consentire a un amministratore di determinare la natura di qualsiasi attacco e di intraprendere le contromisure appropriate.

### <a name="analyzing-security-data"></a>Analisi dei dati di protezione
Una funzionalità di monitoraggio della protezione è la varietà di origini da cui si verificano i dati. I diversi formati e il diverso livello di dettaglio richiedono spesso complesse analisi dei dati acquisiti perché sia possibile collegare tali dati tra loro in un thread di informazioni coerente. A parte i casi più semplici, ad esempio la rilevazione di un numero elevato di accessi non riusciti o di tentativi ripetuti di accesso non autorizzato a risorse critiche, non è sempre possibile eseguire in modo automatizzato elaborazioni complesse dei dati relativi alla sicurezza. Potrebbe invece essere preferibile scrivere i dati, nella forma originale, a parte l'aggiunta del timestamp, in un archivio protetto per consentirne l'analisi manuale da parte di esperti.

<a name="SLA-monitoring"></a>

## <a name="sla-monitoring"></a>monitoraggio del contratto di servizio 
Molti sistemi commerciali che supportano i clienti paganti forniscono garanzie sulle prestazioni del sistema sotto forma di contratti di servizio. In pratica, un contratto di servizio dichiara che il sistema è in grado di gestire un volume di lavoro definito all'interno di un intervallo di tempo concordato e senza perdita di dati critici. Il monitoraggio del contratto di servizio si occupa di garantire che il sistema è in grado di soddisfare i contratti di servizio misurabili.

> [AZURE.NOTE] Il monitoraggio del contratto di servizio è strettamente correlato al monitoraggio delle prestazioni, ma mentre il monitoraggio delle prestazioni si occupa di garantire il funzionamento _ottimale_ del sistema, il monitoraggio del contratto di servizio è disciplinato da obblighi contrattuali che definiscono il significato di _ottimale_.

I contratti di servizio vengono spesso definiti in termini di:

- Disponibilità complessiva del sistema. Ad esempio, un'organizzazione può garantire la disponibilità del sistema per il 99,9% del tempo. Ciò equivale a non più di 9 ore di inattività all'anno o a circa 10 minuti alla settimana.
- Velocità effettiva operativa. Questo aspetto viene spesso espresso sotto forma di uno o più limiti massimi, garantendo, ad esempio, che il sistema è in grado di supportare fino a 100.000 richieste utente simultanee o di gestire 10.000 transazioni aziendali simultanee.
- Tempo di risposta operativa. Il sistema potrebbe anche offrire garanzie in relazione alla velocità di elaborazione delle richieste, garantendo, ad esempio, il completamento del 99% di tutte le transazioni aziendali entro 2 secondi e una durata massima di ciascuna transazione non superiore a 10 secondi.

> [AZURE.NOTE] Alcuni contratti relativi a sistemi commerciali possono includere anche clausole relative all'assistenza clienti, garantendo, ad esempio, che tutte le richieste all'help desk ricevano una risposta entro 5 minuti e che il 99% di tutti i problemi venga affrontato entro un giorno lavorativo. Un'efficace [gestione dei problemi](#issue-tracking), descritta più avanti in questa sezione, è la chiave per soddisfare i contratti di servizio di questo tipo.

### <a name="requirements-for-sla-monitoring"></a>Requisiti per il monitoraggio del contratto di servizio
Al livello più alto, un operatore deve essere in grado di determinare a colpo d'occhio se il sistema soddisfa i contratti di servizio concordati o meno. In caso negativo, l'operatore deve essere in grado di eseguire il drill-down dei fattori sottostanti e di esaminarli per determinare i motivi delle prestazioni insoddisfacenti.

Gli indicatori di alto livello tipici che possono essere rappresentati visivamente includono:

- La percentuale di tempo di attività di servizio.
- La velocità effettiva dell'applicazione (misurata in termini di transazioni completate e/o operazioni al secondo).
- Il numero di richieste dell’applicazione con esito positivo/negativo.
- Il numero di errori di sistema e delle applicazioni, le eccezioni e gli avvisi.

Tutti questi indicatori devono poter essere filtrati per un periodo di tempo specificato.

Un'applicazione cloud includerà probabilmente un numero di componenti e sottosistemi. Un operatore deve essere in grado di selezionare un indicatore di alto livello e identificarne la composizione dallo stato degli elementi sottostanti. Ad esempio, se il tempo di attività dell'intero sistema scende al di sotto di un valore accettabile, un operatore deve essere in grado di eseguire lo zoom avanti e determinare quali elementi contribuiscono a questo problema.

> [AZURE.NOTE] Il tempo di attività del sistema deve essere definito accuratamente. In un sistema che usa la ridondanza per garantire la massima disponibilità, il sistema può continuare a funzionare anche se singole istanze di alcuni elementi non funzionano. I tempi di corretto funzionamento visualizzato dal monitoraggio dell'integrità devono indicare i tempi di corretto funzionamento aggregato di ogni elemento e non necessariamente se il sistema è stato effettivamente interrotto. Gli errori, poi, possono essere isolati. Quindi, anche se un sistema specifico non è disponibile, il resto del sistema può esserlo, anche se con una diminuzione della funzionalità. In un sistema di e-commerce, ad esempio, un errore nel sistema potrebbe impedire ai clienti di creare ordini, ma non di sfogliare il catalogo dei prodotti.

A scopo di avvertimento, il sistema deve essere in grado di generare un evento se uno qualsiasi degli indicatori di alto livello supera una soglia specificata. I dettagli di basso livello dei diversi fattori che costituiscono l'indicatore di alto livello devono essere disponibili per il sistema di generazione degli avvisi come dati contestuali.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
I dati non elaborati necessari per supportare il monitoraggio del contratto di servizio sono simili a quelli richiesti per il monitoraggio delle prestazioni, con alcuni aspetti del monitoraggio dello stato e della disponibilità. Per altri dettagli, vedere le sezioni corrispondenti. È possibile acquisire dati da:

- Esecuzione del monitoraggio degli endpoint.
- Eccezioni di registrazione, errori e avvisi.
- Traccia dell'esecuzione delle richieste degli utenti.
- Monitoraggio della disponibilità dei servizi di terze parti usati dal sistema.
- Utilizzo di contatori e delle metriche delle prestazioni.

Tutti i dati devono essere programmati e corredati di timestamp.

### <a name="analyzing-sla-data"></a>Analisi dei dati del contratto di servizio
I dati di strumentazione devono essere aggregati per generare un'immagine delle prestazioni complessive del sistema. I dati aggregati devono anche supportare il drill down per consentire l'analisi delle prestazioni dei sottosistemi sottostanti. Ad esempio, essere in grado di:

- Calcolare il numero totale delle richieste degli utenti durante il periodo specificato e determinare la frequenza della riuscita o dell'esito negativo di queste richieste.
- Combinare i tempi di risposta delle richieste degli utenti per generare una panoramica globale dei tempi di risposta del sistema.
- Analizzare lo stato delle richieste degli utenti per suddividere il tempo di risposta complessivo di una richiesta nei tempi di risposta per i singoli elementi di lavoro di tale richiesta.  
- Determinare la disponibilità complessiva del sistema sotto forma di percentuale del tempo di attività per un periodo specifico.
- Analizzare la percentuale del tempo di disponibilità dei singoli componenti e dei singoli servizi del sistema. Questo potrebbe comportare l'analisi dei log generati dai servizi di terze parti.

Molti sistemi commerciali sono necessari per segnalare i dati di performance reali rispetto al contratto di servizio concordato per un periodo specificato, in genere un mese. Queste informazioni possono essere utilizzate per calcolare i crediti o altre forme di rimborso per i clienti se durante questo periodo non vengono soddisfatti i contratti di servizio. È possibile calcolare la disponibilità di un servizio usando la tecnica descritta nella sezione [Analisi dei dati di disponibilità](#analyzing-availability-data).

Per scopi interni, un'organizzazione potrebbe inoltre monitorare il numero e la natura degli eventi imprevisti che causato guasti ai servizi. Informazioni su come risolvere questi problemi rapidamente o eliminarli completamente, consentiranno di ridurre i tempi di inattività e di soddisfare i contratti di servizio.

## <a name="auditing"></a>Controllo
A seconda della natura dell'applicazione, potrebbero esserci disposizioni imposte per legge o altre normative legali che specificano i requisiti per il controllo delle operazioni degli utenti e la registrazione dell'accesso a tutti i dati. Il controllo può collegare in modo dimostrabile i clienti a richieste specifiche. Il non ripudio è un fattore importante in molti sistemi di e-business per mantenere la fiducia tra un cliente e l'organizzazione responsabile dell'applicazione o del servizio.

### <a name="requirements-for-auditing"></a>Requisiti per il controllo
Per ricostruire le azioni degli utenti, un analista deve essere in grado di tener traccia della sequenza di operazioni commerciali eseguite dagli utenti stessi. Ciò potrebbe servire semplicemente per l'archivio oppure nell'ambito di un'indagine giudiziaria.

Le informazioni di controllo sono estremamente riservate. Contengono probabilmente dati che identificano gli utenti del sistema e le attività eseguite da questi. Per questo motivo, è molto probabile che le informazioni di controllo abbiano la forma di report disponibili solo per analisti di fiducia, anziché di sistema interattivo in grado di supportare il drill down di grafici. Un analista deve essere in grado di generare un'ampia gamma di report, ad esempio l'elenco delle attività di tutti gli utenti entro un intervallo di tempo specificato con i dettagli sulla cronologia dell'attività per ogni utente, oppure l'elenco della sequenza di operazioni eseguite su una o più risorse.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
Le fonti principali di informazioni per il controllo possono includere:

- Il sistema di sicurezza che gestisce l'autenticazione utente.
- Log di traccia che registrano l'attività dell'utente.
- Log di sicurezza che effettuano il monitoraggio di tutte le richieste di rete identificabili e non identificabili.

Il formato dei dati di controllo e il modo in cui vengono archiviati potrebbe dipendere dai requisiti normativi, ad esempio potrebbe non essere consentito cancellare in alcun modo i dati, che devono essere registrati nel formato originale. L'accesso all'archivio in cui sono mantenuti deve essere protetto dalle manomissioni.

### <a name="analyzing-audit-data"></a>Analisi dei dati di controllo
Un analista deve essere in grado di accedere ai dati non elaborati nella loro interezza e nella loro forma originale. A parte la necessità di generare report di controllo comuni, è probabile che gli strumenti per l'analisi dei dati siano specializzati ed esterni al sistema.

## <a name="usage-monitoring"></a>Monitoraggio dell'utilizzo
Il monitoraggio dell'utilizzo rileva l'utilizzo delle funzionalità e dei componenti di un'applicazione. L'operatore può usare i dati raccolti per:

- Determinare quali funzionalità siano utilizzate di frequente e determinare le potenziali aree sensibili nel sistema. Gli elementi a traffico elevato potrebbero trarre vantaggio dal partizionamento funzionale o persino dalla replica per distribuire il carico in modo più uniforme. Un operatore può usare queste informazioni consentono anche per verificare quali funzionalità vengono usate raramente e possono essere prese in considerazione per il ritiro o la sostituzione in una versione futura del sistema.
- Ottenere informazioni sugli eventi operativi del sistema durante il normale utilizzo. In un sito di e-commerce, ad esempio, è possibile registrare le informazioni statistiche sul numero di transazioni e sul volume di clienti che ne è responsabile. Queste informazioni possono essere usate per la pianificazione della capacità man mano che il numero di clienti aumenta.
- Consente di rilevare (possibilmente in modo indiretto) soddisfazione dell'utente con le prestazioni o la funzionalità del sistema. Ad esempio, se un numero elevato di clienti in un sistema di e-commerce abbandona regolarmente il carrello, questo potrebbe dipendere da un problema della funzionalità di completamento delle transazioni.
- Genera informazioni di fatturazione. Un'applicazione commerciale o un servizio multi-tenant può addebitare ai clienti le risorse usate.
- Applicare quote. Se un utente in un sistema multi-tenant supera la quota acquistata per l'utilizzo di tempo o risorse di elaborazione durante un periodo specificato, possono essere attivati limiti di accesso o di elaborazione.

### <a name="requirements-for-usage-monitoring"></a>Requisiti per il monitoraggio dell'utilizzo
Per esaminare l'utilizzo del sistema, un operatore in genere deve visualizzare le informazioni relative a:

- Numero di richieste elaborate per ogni sottosistema e dirette a ogni risorsa.
- Lavoro eseguito da ogni utente.
- Volume dell'archivio dati occupato da ogni utente.
- Risorse a cui ogni utente accede.

Un operatore deve anche essere in grado di generare grafici. Un grafico, ad esempio, può visualizzare gli utenti che usano più risorse o le risorse o le funzionalità di sistema usate con maggiore frequenza.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
Il monitoraggio dell'utilizzo può essere eseguito a un livello relativamente elevato. Può rilevare l'ora di inizio e fine di ogni richiesta e la natura della richiesta stessa, ovvero di lettura, di scrittura e così via, a seconda della risorsa in questione. È possibile ottenere queste informazioni da:

- Traccia attività dell'utente.
- Acquisizione di contatori delle prestazioni che misurano l'utilizzo per ogni risorsa.
- Monitoraggio dell'utilizzo di risorse da parte di ogni utente.

Per scopi di misurazione è anche necessario essere in grado di identificare gli utenti responsabili dell'esecuzione delle operazioni e le risorse usate da queste operazioni. Le informazioni raccolte devono contenere dettagli sufficienti a consentire una fatturazione corretta.

<a name="issue-tracking"></a>
## <a name="issue-tracking"></a>Gestione dei problemi
I clienti e gli altri utenti possono segnalare problemi se nel sistema si verificano eventi o comportamenti imprevisti. Il rilevamento dei problemi riguarda la loro gestione, la loro associazione al tentativo di risolvere qualsiasi problema di fondo nel sistema e informare i clienti sulle possibili risoluzioni.

### <a name="requirements-for-issue-tracking"></a>Requisiti per la gestione dei problemi
Gli operatori spesso eseguono la gestione dei problemi tramite un sistema distinto che consente di registrare e segnalare i dettagli dei problemi segnalati dagli utenti. Questi dettagli possono includere le attività che l'utente stava tentando di eseguire, i sintomi del problema, la sequenza di eventi e i messaggi di errore o di avviso visualizzati.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
L'origine dati iniziale per i dati di gestione di un problema è l'utente che ha segnalato il problema. L'utente potrebbe essere in grado di fornire dati aggiuntivi, ad esempio:

- I dettagli dell'arresto anomalo del sistema, se l'applicazione include un componente in esecuzione nel desktop dell'utente.
- Uno snapshot della schermata.
- La data e l'ora in cui si è verificato l'errore, insieme a eventuali altre informazioni a livello di ambiente, ad esempio la posizione dell'utente.

Queste informazioni possono essere usate per facilitare il debug e la creazione di un backlog per le versioni future del software.

### <a name="analyzing-issue-tracking-data"></a>Analisi dei dati di gestione dei problemi
Utenti diversi potrebbero segnalare lo stesso problema. Il sistema di gestione dei problemi deve associare le segnalazioni comuni.

Per ogni segnalazione di problemi deve essere registrato lo stato del debug. Una volta risolto il problema, si può informare il cliente della soluzione.

Se un utente segnala un problema con una soluzione presente nel sistema di gestione dei problemi, l'operatore deve essere in grado di informare immediatamente l'utente della soluzione.

## <a name="tracing-operations-and-debugging-software-releases"></a>Operazioni di rilevamento e debug di versioni del software.
Quando un utente segnala un problema, spesso è consapevole solo dell'impatto immediato sulle operazioni che lo riguardano da vicino. Può quindi segnalare a un operatore responsabile della gestione del sistema solo i risultati della propria esperienza. Queste esperienze sono in genere solo un sintomo visibile di uno o più problemi fondamentali. In molti casi, per stabilire la causa radice del problema un analista deve esaminare la cronologia delle operazioni sottostanti. Questo processo è detto _analisi della causa radice_.

> [AZURE.NOTE] L'analisi della causa radice può rivelare inefficienze nella progettazione di un'applicazione. In queste situazioni, è possibile rielaborare gli elementi interessati e distribuirli nell'ambito di una versione successiva. Questo processo richiede un attento controllo e i componenti aggiornati devono essere controllati attentamente.

### <a name="requirements-for-tracing-and-debugging"></a>Requisiti per la traccia e il  debug
Per la traccia di eventi imprevisti e di altri problemi, è fondamentale che i dati di monitoraggio forniscano informazioni sufficienti a consentire a un analista di risalire all'origine dei problemi e ricostruire la sequenza degli eventi. Queste informazioni devono essere sufficienti a consentire a un analista di diagnosticare la causa radice dei problemi. Uno sviluppatore può quindi apportare le modifiche necessarie per impedire che i problemi si ripetano.

### <a name="data-sources,-instrumentation,-and-data-collection-requirements"></a>Requisiti per le origini, la raccolta dati e la strumentazione
La risoluzione dei problemi può implicare la traccia di tutti i metodi, e dei relativi parametri, richiamati nell'ambito di un'operazione per creare un albero che rappresenti il flusso logico attraverso il sistema quando un cliente effettua una richiesta specifica. Eccezioni e avvisi generati dal sistema a seguito di questo flusso devono essere acquisiti e registrati.

Per supportare il debug, il sistema può fornire hook che consentono a un operatore di acquisire informazioni sullo stato in corrispondenza di punti critici del sistema. In alternativa, il sistema può fornire informazioni dettagliate man mano che le operazioni selezionate progrediscono. L'acquisizione di dati a questo livello di dettaglio può imporre un carico aggiuntivo al sistema e deve essere un processo temporaneo. Un operatore usa questo processo principalmente quando si verifica una serie di eventi molto insolita e difficile da replicare o quando una nuova versione di uno o più elementi in un sistema richiede un attento monitoraggio, per garantire che tali elementi funzionino come previsto.

## <a name="the-monitoring-and-diagnostics-pipeline"></a>La pipeline di monitoraggio e diagnostica
Il monitoraggio di un sistema distribuito su vasta scala costituisce una sfida significativa. Non si deve ritenere che ognuno degli scenari descritti nella sezione precedente si presenti in modo isolato. È probabile che ci sia una sovrapposizione significativa nei dati di monitoraggio e di diagnostica necessari per ogni situazione, anche se questi dati devono essere elaborati e presentati in modi diversi. Per questi motivi, è necessario avere una visione olistica di monitoraggio e diagnostica.

È possibile prevedere l’intero processo di diagnostica e monitoraggio come una pipeline che comprende le fasi illustrate nella figura 1.

![Fasi della pipeline di monitoraggio e diagnostica](media/best-practices-monitoring/Pipeline.png)

_Figura 1. Le fasi della pipeline di monitoraggio e diagnostica_

La figura 1 evidenzia come i dati per il monitoraggio e la diagnostica possono provenire da una varietà di origini dati. Le fasi di strumentazione e raccolta riguardano l'individuazione delle fonti da cui è necessario acquisire i dati, la determinazione dei dati da acquisire, la modalità di acquisizione dei dati e il tipo di formattazione di questi per semplificarne l'analisi. La fase di analisi/diagnosi riceve i dati non elaborati e li usa per generare informazioni significative utilizzabili da un operatore per determinare lo stato del sistema. L'operatore può usare queste informazioni per decidere le possibili azioni da intraprendere e quindi immettere i risultati nelle fasi di strumentazione e raccolta. La fase di visualizzazione/avviso presenta una visualizzazione riproducibile dello stato del sistema in grado di visualizzare informazioni quasi in tempo reale tramite una serie di dashboard. Può anche generare report, grafici e diagrammi per fornire una visualizzazione cronologica dei dati che consentono di identificare tendenze a lungo termine. Se le informazioni indicano che probabilmente un indicatore KPI supererà i limiti accettabili, questa fase può anche attivare un avviso per un operatore. In alcuni casi, è possibile usare un avviso anche per attivare un processo automatizzato che tenta di effettuare operazioni correttive, ad esempio la scalabilità automatica.

Si noti che questi passaggi costituiscono un processo di flusso continuo in cui le fasi si verificano in parallelo. In una situazione ideale, tutte le fasi sono configurabili dinamicamente. In alcuni momenti, soprattutto se un sistema è stato appena distribuito o si sono verificati problemi, potrebbe essere necessario raccogliere dati estesi con maggiore frequenza. In altri momenti, per verificare che il sistema funzioni correttamente è possibile ripristinare l'acquisizione di un livello base di informazioni essenziali.

L'intero processo di monitoraggio, poi, deve essere considerato una soluzione in corso in tempo reale, soggetta a miglioramenti e ottimizzazione grazie al feedback ricevuto. Ad esempio, per determinare l'integrità del sistema, all'inizio è possibile misurare molti fattori, ma nel tempo l'analisi potrebbe perfezionarsi con l'eliminazione di misure non importanti e una maggiore concentrazione sui dati necessari, riducendo al minimo eventuali rumori di fondo.

## <a name="sources-of-monitoring-and-diagnostic-data"></a>Origini dei dati di monitoraggio e diagnostici
Le informazioni usate dal processo di monitoraggio possono provenire da origini diverse, come illustrato nella figura 1. A livello di applicazione, le informazioni provengono dai log di traccia incorporati nel codice del sistema. Per tenere traccia del flusso di controllo attraverso il codice, gli sviluppatori devono seguire un approccio standard. Ad esempio, l'ingresso in un metodo può generare un messaggio di traccia che specifichi il nome del metodo, l'ora corrente, il valore di ogni parametro e qualsiasi altra informazione utile. Può essere utile anche registrare l'ora di ingresso e di uscita.

È necessario registrare tutte le eccezioni e gli avvisi e assicurarsi di mantenere un'analisi completa di tutte le eccezioni annidate e di tutti gli avvisi. In una situazione ideale, è anche consigliabile acquisire informazioni che identifichino l'utente che esegue il codice, unitamente alle informazioni di correlazione delle attività, per tenere traccia delle richieste durante il passaggio di queste attraverso il sistema. È anche consigliabile eseguire tentativi di accesso a tutte le risorse, ad esempio code di messaggi, database, file e altri servizi dipendenti. Queste informazioni possono essere usate per scopi di controllo e misurazione.

Molte applicazioni utilizzano librerie e framework per eseguire attività comuni, ad esempio l'accesso a un archivio dati o la comunicazione in rete. Questi framework possono essere configurati in modo da generare messaggi di traccia specifici e particolari informazioni di diagnostica non elaborate, ad esempio la frequenza delle transazioni e l'esito positivo o negativo della trasmissione di dati.

> [AZURE.NOTE] Molti framework moderni pubblicano automaticamente gli eventi prestazioni e di traccia. Per acquisire queste informazioni è sufficiente fornire un mezzo per recuperarle e archiviarle dove possono essere elaborate e analizzate.

Il sistema operativo in cui è in esecuzione l'applicazione può essere fonte di informazioni di basso livello a livello di sistema, ad esempio i contatori delle prestazioni che indicano la velocità di I/O e l'utilizzo della memoria e della CPU. È anche possibile segnalare errori del sistema operativo, ad esempio l'impossibilità di aprire un file.

Si consiglia inoltre l'infrastruttura sottostante e dei componenti in cui viene eseguito il sistema. Macchine virtuali, reti virtuali e servizi di archiviazione possono essere origine di contatori delle prestazioni a livello di infrastruttura importanti e di altri dati di diagnostica.

Se l'applicazione utilizza servizi esterni, ad esempio un server web o un sistema di gestione di database, questi servizi possono pubblicare le proprie informazioni di traccia, log e i contatori delle prestazioni. Tra i possibili esempi, le viste a gestione dinamica di SQL Server per la traccia delle operazioni eseguite in un database SQL Server e i log di traccia di IIS per la registrazione delle richieste inviate a un server Web.

Appena i componenti di un sistema vengono modificati e le nuove versioni vengono distribuite, è importante essere in grado di riconoscere i problemi, gli eventi e le metriche di ogni versione. Queste informazioni devono essere collegate alla pipeline di rilascio in modo che  i problemi con una versione specifica di un componente siano rilevati e corretti rapidamente.

Problemi di protezione che potrebbero verificarsi in qualsiasi punto nel sistema. Ad esempio, un utente potrebbe tentare di accedere con un ID utente o una password non valida. Un utente autenticato potrebbe tentare di ottenere un accesso non autorizzato a una risorsa o un utente potrebbe specificare una chiave non valida o non aggiornata per accedere a informazioni crittografate. Informazioni relative alla sicurezza per le richieste con esito positivo e negativo devono sempre essere registrate.

La sezione [Strumentazione di un'applicazione](#instrumenting-an-application) contiene altre indicazioni sulle informazioni che è consigliabile acquisire. Per raccogliere queste informazioni, tuttavia, è possibile usare strategie diverse:

- **Monitoraggio applicazione/sistema**. Questa strategia usa origini interne comprese all'interno dell'applicazione, dei framework di applicazione, del sistema operativo e dell'infrastruttura. Durante il ciclo di vita di una richiesta client il codice dell'applicazione può generare dati di monitoraggio specifici in corrispondenza di punti significativi. L'applicazione può includere istruzioni di traccia che possono essere abilitate o disabilitate in modo selettivo a seconda delle circostanze. È anche possibile inserire dinamicamente dati di diagnostica usando un framework di diagnostica. In genere, questi framework sono dotati di plug-in collegabili a diversi punti di strumentazione del codice in corrispondenza dei quali possono acquisire dati di traccia.

    Il codice e/o l'infrastruttura sottostante possono anche generare eventi in corrispondenza di punti critici. Il monitoraggio degli agenti che sono configurati per l'ascolto di questi eventi può registrare le informazioni sull'evento.

- **Real User Monitoring**. Questo approccio registra le interazioni tra un utente e l'applicazione e osserva il flusso di ogni richiesta e risposta. Queste informazioni possono avere un duplice scopo: possono essere usate per la misurazione dell'utilizzo da parte di ogni utente e per determinare se gli utenti ricevono una qualità del servizio appropriata, ad esempio tempi di risposta rapidi, bassa latenza ed errori minimi. È possibile usare i dati acquisiti per identificare aree problematiche in cui si verificano più spesso errori. È anche possibile usare i dati per identificare gli elementi in corrispondenza dei quali il sistema subisce un rallentamento, probabilmente a causa di aree sensibili nell'applicazione o di altre forme di collo di bottiglia. Se si implementa questo approccio con attenzione, è possibile ricostruire i flussi degli utenti attraverso l'applicazione a scopo di debug e test.

    > [AZURE.IMPORTANT] È necessario tenere conto del fatto che i dati acquisiti tramite Real User Monitoring sono estremamente sensibili, perché potrebbero includere materiale riservato. Se si salvano i dati acquisiti, archiviarli in modo protetto. Se si vogliono usare i dati a scopo di monitoraggio delle prestazioni o di debug, eliminare prima di tutto tutte le informazioni personali.

- **Synthetic User Monitoring**. In questo approccio, è possibile scrivere il proprio client di test che simula un utente ed esegue una serie configurabile ma tipica delle operazioni. È possibile rilevare le prestazioni dei client di test per determinare lo stato del sistema. È inoltre possibile utilizzare più istanze del client di test come parte di un'operazione di test di carico per stabilire la risposta del sistema in condizioni di stress e il tipo di monitoraggio di output che viene generato in queste condizioni.

    > [AZURE.NOTE] È possibile implementare il monitoraggio degli utenti reali e sintetici includendo il codice che registra regola l'esecuzione di chiamate al metodo e altre parti critiche di un'applicazione.

- **Profiling**. Questo approccio è principalmente destinato al monitoraggio e al miglioramento delle prestazioni dell'applicazione. Invece di operare al livello di funzionalità di Real User Monitoring e Synthetic User Monitoring, questo approccio acquisisce informazioni di basso livello durante l'esecuzione dell'applicazione. È possibile implementare la profilatura tramite campionamento periodico dello stato di esecuzione di un'applicazione, determinando quale parte di codice l'applicazione sta eseguendo in un determinato momento. È anche possibile usare strumentazione che inserisca sonde all'interno del codice in corrispondenza di punti nevralgici, ad esempio l'inizio e la fine della chiamata di un metodo, e registri i metodi richiamati, l'ora di ogni chiamata e la durata di ciascuna. È quindi possibile analizzare questi dati per stabilire quali parti dell'applicazione potrebbero causare problemi di prestazioni.

- **Monitoraggio degli endpoint**. Questa tecnica usa uno o più endpoint diagnostici esposti dall'applicazione proprio per abilitare il monitoraggio. Un endpoint fornisce un percorso nel codice dell'applicazione e può restituire informazioni sull’integrità del sistema. Endpoint diversi possono concentrarsi su diversi aspetti della funzionalità. È possibile scrivere il proprio client di diagnostica che invia richieste periodiche a questi endpoint e assimilare le risposte. Questo approccio è descritto in modo più esteso nell'articolo relativo al [modello di monitoraggio degli endpoint di integrità](https://msdn.microsoft.com/library/dn589789.aspx) nel sito Web Microsoft.

Per la massima copertura, è consigliabile usare una combinazione di queste tecniche.

<a name="instrumenting-an-application"></a>
## <a name="instrumenting-an-application"></a>Strumentazione di un'applicazione
La strumentazione è una parte essenziale del processo di monitoraggio. È possibile prendere decisioni significative sulle prestazioni e l'integrità di un sistema solo se prima si acquisiscono i dati che consentono di prendere queste decisioni. Le informazioni raccolte tramite la strumentazione dovrebbero essere sufficienti per valutare le prestazioni, diagnosticare i problemi e prendere decisioni senza dover accedere a un server di produzione remoto ed eseguire la traccia e il debug manualmente. I dati di strumentazione di solito includono metriche e informazioni scritte nei log di traccia.

I contenuti di un log di traccia possono essere il risultato di dati testuali scritti dall'applicazione o di dati binari creati a seguito di un evento di traccia, se l'applicazione usa ETW (Event Tracing for Windows). Possono essere creati anche partendo dai log di sistema che registrano gli eventi generati dalle parti dell'infrastruttura, ad esempio un server Web. I messaggi di testo dei log sono spesso progettati per essere leggibili, ma devono anche essere scritti in un formato che ne consenta facilmente l'analisi da parte di un sistema automatizzato.

È anche consigliabile suddividere i log in categorie. Evitare di scrivere tutti i dati di traccia in un unico log, ma usare log separati per registrare l'output di traccia dei diversi aspetti operativi del sistema. Sarà quindi possibile filtrare rapidamente i messaggi di log leggendoli nel log appropriato, anziché dover elaborare un unico file di notevole lunghezza. Non si devono mai scrivere informazioni con requisiti di sicurezza diversi (ad esempio informazioni di controllo e i dati di debug) per lo stesso log.

> [AZURE.NOTE] Un log può essere implementato sotto forma di file nel file system o in un altro formato, ad esempio un BLOB nell'archiviazione BLOB. Informazioni sul log potrebbero essere conservate anche in un servizio di archiviazione più strutturato, ad esempio le righe di una tabella.

Una metrica è in genere una misura o un conteggio di alcuni aspetti o di alcune risorse del sistema in un momento specifico. A una metrica sono associati uno o più tag o dimensioni, che nel loro insieme sono detti talvolta _campione_. Una singola istanza isolata di una metrica in genere non è utile. Le metriche, invece, devono essere acquisite nel corso del tempo. La questione chiave da considerare è la scelta delle metriche da registrare e la frequenza di registrazione. La generazione troppo frequente di dati per le metriche può imporre un carico aggiuntivo significativo al sistema, mentre l'acquisizione sporadica di metriche può far sì che vengano ignorate circostanze alla base di eventi significativi. Le considerazioni possono variare da una metrica all’altra. Ad esempio, l'utilizzo della CPU in un server può variare significativamente da secondo a secondo, ma un utilizzo elevato diventa un problema solo se dura diversi minuti.

<a name="information-for-correlating-data"></a>
### <a name="information-for-correlating-data"></a>Informazioni per la correlazione dei dati
È possibile usare diversi file di log per eseguire con facilità attività quali monitorare singoli contatori di prestazioni a livello di sistema, acquisire metriche per le risorse e ottenere informazioni di traccia per le applicazioni. La pipeline di alcuni tipi di monitoraggio, tuttavia, contiene una fase di analisi e diagnostica il cui scopo è di mettere in correlazione i dati recuperati da origini diverse. Sotto forma di dati non elaborati, questi dati possono assumere forme diverse ed è necessario fornire al processo di analisi dati di strumentazione sufficienti per il mapping di queste. A livello di framework applicazione, ad esempio, un'attività può essere identificata da un ID di thread. All'interno di un'applicazione, la stessa attività può essere associata all'ID dell'utente che la sta eseguendo.

È improbabile, poi, che esista un mapping 1:1 tra thread e richieste utente, poiché le operazioni asincrone possono usare più volte gli stessi thread per eseguire operazioni per conto di più utenti. A complicare ancora di più il tutto, man mano che l'esecuzione interessa una dopo l'altra le diverse parti del sistema, una singola richiesta può essere gestita da più thread. Se possibile, è consigliabile associare ogni richiesta a un ID attività univoco propagato attraverso il sistema nell'ambito del contesto della richiesta. La tecnica per la generazione e l'inserimento di ID attività nelle informazioni di traccia dipende dalla tecnologia usata per acquisire i dati di traccia.

Tutti i dati di monitoraggio devono riportare il timestamp nello stesso modo. Per coerenza, registrare tutte le date e le ore tramite Coordinated Universal Time. Ciò consentirà di rintracciare più facilmente le sequenze di eventi.

> [AZURE.NOTE] Computer che operano in reti e fusi orari diversi potrebbero non essere sincronizzati. Evitare di affidarsi solo ai timestamp per la correlazione dei dati di strumentazione che si estendono su più computer.

### <a name="information-to-include-in-the-instrumentation-data"></a>Informazioni da includere nei dati di strumentazione
Quando si decide quali dati di strumentazione è necessario raccogliere, tenere presente quanto segue:

- Assicurarsi che le informazioni acquisite dagli eventi di traccia siano leggibili per i computer e per gli utenti. Adottare schemi ben definiti per queste informazioni per facilitare l'elaborazione automatizzata tra sistemi diversi dei dati di log e garantire coerenza al personale operativo e tecnico che deve leggere i log. Includere informazioni ambientali, ad esempio l'ambiente di distribuzione, il computer in cui viene eseguito il processo, i dettagli del processo e lo stack di chiamate.  
- Abilitare la profilatura solo se necessario, perché può sovraccaricare in modo significativo il sistema. La profilatura tramite strumentazione registra un evento, ad esempio la chiamata di un metodo, ogni volta che questo si verifica, mentre il campionamento registra solo eventi selezionati. La selezione potrebbe essere basata sul tempo (una volta ogni *n* secondi) o sulla frequenza (una volta ogni *n* richieste). Se gli eventi si verificano molto spesso, la profilatura tramite strumentazione potrebbe rappresentare un onere eccessivo e influire essa stessa sulle prestazioni globali. In questo caso è preferibile l'approccio tramite campionamento. Se la frequenza degli eventi è bassa, tuttavia, il campionamento potrebbe perderli. In questo caso, la strumentazione potrebbe essere l'approccio migliore.
- Fornire un contesto sufficiente per consentire a uno sviluppatore o a un amministratore di determinare l'origine di ogni richiesta. Questo può includere una qualche forma di ID di attività che identifichi un'istanza specifica di una richiesta. Potrebbe includere anche informazioni utilizzabili per correlare l'attività con le operazioni di calcolo eseguite e le risorse usate. Si noti che queste operazioni possono svolgersi su più processi e in più computer. Per la misurazione, il contesto deve includere, direttamente o indirettamente tramite altre informazioni correlate, anche un riferimento al cliente che ha effettuato la richiesta. Questo contesto fornisce informazioni utili sullo stato dell'applicazione nel momento in cui sono stati acquisiti i dati di monitoraggio.
- Registrare tutte le richieste e le posizioni o aree da cui queste richieste vengono effettuate. Queste informazioni rendono più semplice determinare l'eventuale presenza di aree sensibili specifiche di una posizione. Queste informazioni sono utili anche per stabilire se ripartizionare un'applicazione o i dati che questa usa.
- Registrare e acquisire i dettagli delle eccezioni con attenzione. Informazioni di debug critiche vengono spesso perse a causa di una gestione inefficiente delle eccezioni. È necessario acquisire tutti i dettagli delle eccezioni generate dall'applicazione, inclusa qualsiasi eccezione interna e altre informazioni di contesto. Includere lo stack di chiamate, se possibile.
- È necessario garantire la coerenza dei dati acquisiti dai diversi elementi dell'applicazione, poiché questo rende più semplice l'analisi degli eventi e la correlazione di questi con le richieste degli utenti. Per raccogliere le informazioni, prendere in considerazione l'uso di un pacchetto di registrazione completo e configurabile, anziché fare affidamento sul fatto che gli sviluppatori adottino lo stesso approccio nell'implementazione delle diverse parti del sistema. Raccogliere dati dai contatori di prestazioni chiave, ad esempio il volume di I/O eseguiti, l'utilizzo della rete, il numero di richieste, l’utilizzo della memoria e l'utilizzo della CPU. Alcuni servizi di infrastruttura possono fornire contatori delle prestazioni specifici, ad esempio il numero di connessioni a un database, la velocità di esecuzione delle transazioni e il numero di transazioni con esito positivo o negativo. Le applicazioni potrebbero inoltre definire i propri specifici contatori delle prestazioni.
- Registrare tutte le chiamate a servizi esterni, ad esempio sistemi di database, servizi Web o altri servizi a livello di sistema all'interno dell'infrastruttura. Registrare le informazioni relative al tempo impiegato per eseguire ogni chiamata e  il relativo esito positivo o negativo. Se possibile, acquisire informazioni su tutti i tentativi ed errori per eventuali errori temporanei.

### <a name="ensuring-compatibility-with-telemetry-systems"></a>Garantire la compatibilità con sistemi di telemetria
In molti casi, le informazioni prodotte dalla strumentazione sono generate sotto forma di una serie di eventi e vengono passate a un sistema di telemetria separato per l'elaborazione e l'analisi. Un sistema di telemetria è in genere indipendente da qualsiasi applicazione o tecnologia specifica ma richiede che le informazioni seguano un formato specifico, in genere definito da uno schema. Lo schema specifica in modo efficace un contratto che definisce i campi e i tipi di dati che il sistema di telemetria è in grado di acquisire. Lo schema deve essere generalizzato per consentire dati provenienti da una gamma di dispositivi e piattaforme.

Uno schema comune deve includere i campi comuni a tutti gli eventi di strumentazione, ad esempio il nome e l'ora dell'evento, l'indirizzo IP del mittente e i dettagli necessari per la correlazione con altri eventi, ad esempio un ID utente, un ID dispositivo e un ID applicazione. Si tenga presente che gli eventi possono essere generati da qualsiasi numero di dispositivi. Lo schema, quindi, non deve dipendere dal tipo di dispositivo. Dispositivi diversi, poi, possono generare eventi per la stessa applicazione e questa potrebbe supportare il roaming o un'altra forma di distribuzione tra più dispositivi.

Lo schema può includere anche campi di dominio pertinenti a uno scenario specifico comune ad applicazioni diverse. Potrebbe trattarsi di informazioni sulle eccezioni, di eventi di avvio e fine dell'applicazione e/o di errori di chiamate di API di servizi Web. Tutte le applicazioni che utilizzano lo stesso set di campi di dominio devono creare lo stesso set di eventi, l'abilitazione di un set di report e analisi dei dati da generare comuni.

Infine, uno schema può contenere campi personalizzati per l'acquisizione dei dettagli di eventi specifici dell'applicazione.

### <a name="best-practices-for-instrumenting-applications"></a>Procedure consigliate per le applicazioni di strumentazione
Nell'elenco seguente sono riepilogate le procedure consigliate per la strumentazione di un'applicazione distribuita in esecuzione nel cloud.

- Semplificare i registri per la lettura e l’analisi. Utilizzare la registrazione strutturata dove possibile. Essere concisi e descrittivi nei messaggi di log.
- In tutti i log, identificare l'origine e fornire il contesto e le informazioni di temporizzazione come ogni record di log.
- Usare lo stesso fuso orario e lo stesso formato per tutti i timestamp. Questo consente di correlare gli eventi per le operazioni che si estendono su hardware e servizi in esecuzione in aree geografiche diverse.
- Classificare i registri e scrivere messaggi nel file di log appropriato.
- Non rivelare informazioni riservate sul sistema o informazioni personali degli utenti. Eseguire lo scrubbing di queste informazioni prima di registrarle, assicurandosi che i dettagli pertinenti vengano mantenuti. Ad esempio, rimuovere l'ID e la password da qualsiasi stringa di connessione di database, ma scrivere le informazioni rimanenti nel log in modo che un analista possa determinare che il sistema accede al database corretto. Registrare tutte le eccezioni critiche, ma consentire agli amministratori di attivare e disattivare la registrazione per i livelli inferiori di eccezioni e avvisi. Inoltre, acquisire e registrare tutte le informazioni di logica di ripetizione. Questi dati possono essere utili per monitorare l'integrità del sistema temporaneo.
- Tracciare le chiamate di processo, ad esempio le richieste a servizi Web o a database esterni.
- Non combinare i messaggi di log con requisiti di sicurezza diversi nello stesso file di log. Ad esempio, non scrivere debug e informazioni per lo stesso log.
- Fatta eccezione per gli eventi di controllo, assicurarsi che tutte le chiamate di registrazione siano operazioni "Fire and Forget" che non devono bloccare l'esecuzione delle operazioni aziendali. Gli eventi di controllo sono eccezionali perché sono critici per l'azienda e possono essere classificati come una parte fondamentale delle operazioni aziendali.
- Assicurarsi che la registrazione sia estensibile e non abbia dipendenze dirette da una destinazione concreta. Ad esempio, anziché scrivere informazioni usando _System.Diagnostics.Trace_, definire un'interfaccia astratta, ad esempio _ILogger_, che esponga metodi di registrazione e possa essere implementata con qualsiasi mezzo appropriato.
- Assicurarsi che tutte le registrazioni siano a prova di errore e non attivino errori a catena. La registrazione non deve generare eccezioni.
- Considerare la strumentazione come un processo iterativo in corso ed esaminare i registri regolarmente, non solo quando si verifica un problema.

## <a name="collecting-and-storing-data"></a>Raccolta e archiviazione dei dati
La fase di raccolta del processo di monitoraggio riguarda il recupero di informazioni generate da strumentazione, la formattazione di questi dati per renderne più semplice l'uso in fase di analisi/diagnosi e il salvataggio dei dati trasformati in un archivio affidabile. I dati di strumentazione raccolti da diverse parti di un sistema distribuito possono essere mantenuti in svariate posizioni e in diversi formati. Ad esempio, il codice dell'applicazione potrebbe generare file di log di traccia e dati del log eventi dell'applicazione, mentre i contatori delle prestazioni che consentono di monitorare gli aspetti chiave dell'infrastruttura usata dall'applicazione possono essere acquisiti tramite altre tecnologie. Eventuali componenti e servizi di terze parti usati dall'applicazione potrebbero fornire informazioni di strumentazione in formati diversi tramite file di traccia separati, archiviazione BLOB o persino un archivio dati personalizzato.

La raccolta dei dati viene spesso eseguita tramite un servizio di raccolta che può essere eseguito in modo autonomo dall'applicazione che genera i dati di strumentazione. La Figura 2 illustra un esempio di questa architettura, evidenziando il sottosistema di raccolta dei dati di strumentazione.

![Esempi di raccolta di dati di strumentazione](media/best-practices-monitoring/TelemetryService.png)

_Figura 2. Raccolta di dati di strumentazione_

Si tratta di una vista semplificata. Il servizio di raccolta non è necessariamente un unico processo e può comprendere molti elementi costitutivi in esecuzione in computer diversi, come descritto nelle sezioni seguenti. Se è anche necessario eseguire rapidamente l'analisi di alcuni dati di telemetria (analisi critica, come descritto nella sezione [Supporto di analisi critiche, a caldo e a freddo](#supporting-hot-warm-and-cold-analysis) più avanti in questo documento), i componenti locali che operano all'esterno del servizio di raccolta possono eseguire l'attività di analisi immediatamente. La Figura 2 illustra questa situazione per eventi selezionati. Dopo l'elaborazione analitica i risultati possono essere inviati direttamente al sottosistema di visualizzazione e avviso. Mentre attendono di essere elaborati, i dati sottoposti ad analisi a caldo o a freddo vengono mantenuti nell'archivio.

Per i servizi e le applicazioni di Azure, Diagnostica di Azure fornisce una possibile soluzione per l'acquisizione di dati. Per ogni nodo di calcolo Diagnostica di Azure raccoglie i dati dalle origini riportate di seguito, li aggrega e quindi li carica in Archiviazione di Azure:

- Log di IIS
- Log delle richieste non riuscite di IIS
- Registri eventi di Windows
- Contatori delle prestazioni
- Dump di arresto anomalo del sistema
- Log dell'infrastruttura Diagnostica di Azure  
- Log degli errori personalizzati
- EventSource .NET
- ETW basato su manifesto

Per altre informazioni, vedere l'articolo [Azure: nozioni fondamentali di telemetria e risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx).

### <a name="strategies-for-collecting-instrumentation-data"></a>Strategie per la raccolta dei dati di strumentazione
Considerando la natura elastica del cloud e per evitare la necessità di recuperare manualmente i dati di telemetria da ogni nodo nel sistema, è consigliabile organizzare il trasferimento dei dati in una posizione centrale e il loro consolidamento. In un sistema che si estende su più centri dati, potrebbe essere utile prima raccogliere, consolidare e archiviare i dati area per area e quindi aggregare i dati di ogni area in un unico sistema centrale.

Per ottimizzare l'utilizzo della larghezza di banda, è possibile scegliere di trasferire i dati meno urgenti in blocchi, come batch. Tuttavia, i dati non devono essere rimandati indefinitamente, soprattutto se contengono informazioni sensibili al tempo.

#### <a name="_pulling-and-pushing-instrumentation-data_"></a>_Eseguire il pull e push dei dati di strumentazione_
Il sottosistema di raccolta dei dati di strumentazione può recuperare attivamente i dati di strumentazione dai diversi log e da altre origini per ogni istanza dell'applicazione ( _modello pull_). In alternativa, può fungere da ricevitore passivo che attende che i dati vengano inviati dai componenti che costituiscono ogni istanza dell'applicazione ( _modello push_).

Un approccio all'implementazione del modello pull consiste nell'uso di agenti di monitoraggio eseguiti in locale con ogni istanza dell'applicazione. Un agente di monitoraggio è un processo separato che recupera periodicamente (pull) i dati di telemetria raccolti in corrispondenza del nodo locale e scrive queste informazioni direttamente nell'archivio centralizzato condiviso da tutte le istanze della condivisione delle applicazioni. Questo è il meccanismo implementato da Diagnostica di Azure. Ogni istanza di un ruolo Web o di lavoro di Azure può essere configurata per l'acquisizione di informazioni di diagnostica e di altre informazioni di traccia archiviate in locale. L'agente di monitoraggio eseguito insieme a ogni istanza copia i dati specificati in Archiviazione di Azure. L'articolo [Abilitazione di Diagnostica in Servizi cloud e nelle macchine virtuali di Azure in servizi cloud di Azure](./cloud-services/cloud-services-dotnet-diagnostics.md) offre altri dettagli su questo processo. Alcuni elementi, ad esempio i log IIS, i dump di arresto anomalo del sistema e il log degli errori personalizzati, vengono scritti nell'archiviazione BLOB. I dati del registro eventi di Windows, gli eventi ETW e i contatori delle prestazioni vengono registrati nell'archiviazione tabelle. Nella Figura 3 viene illustrato questo meccanismo.

![Illustrazione dell'uso di un agente di monitoraggio per l'estrazione di informazioni e la scrittura in un archivio condiviso](media/best-practices-monitoring/PullModel.png)

_Figura 3. Utilizzo di un agente di monitoraggio per l’estrazione di informazioni e la scrittura in archiviazione condivisa_

> [AZURE.NOTE] L'uso di un agente di monitoraggio è particolarmente indicato per l'acquisizione dei dati di strumentazione estratti in modo naturale da un'origine dati. Un esempio è costituito dalle informazioni delle viste a gestione dinamica di SQL Server o dalla lunghezza di una coda del bus di servizio di Azure.


È possibile usare l'approccio appena descritto per archiviare i dati di telemetria per un'applicazione su piccola scala in esecuzione in un numero limitato di nodi in un'unica posizione. Tuttavia, un'applicazione cloud globale, estremamente scalabile e complessa potrebbe generare grandi volumi di dati da centinaia di ruoli Web e di lavoro, da partizioni di database e da altri servizi. Con un'unica posizione centralizzata, questa enorme quantità di dati può facilmente sovraccaricare la larghezza di banda di I/O disponibile. La soluzione di telemetria deve quindi essere scalabile, per evitare che, man mano che il sistema si espande, la soluzione stessa costituisca un collo di bottiglia. In una situazione ideale, la soluzione dovrebbe comprendere un livello di ridondanza, per ridurre il rischio di perdere importanti informazioni di monitoraggio, ad esempio dati di fatturazione o di controllo, in caso di errori di una parte del sistema.

Per risolvere questi problemi, è possibile implementare l'accodamento, come illustrato nella Figura 4. In questa architettura l'agente di monitoraggio locale, se può essere configurato in modo appropriato, o il servizio di raccolta dati personalizzato, se l'agente di monitoraggio locale non può essere configurato, pubblica i dati in una coda. Un processo separato in esecuzione in modo asincrono (il servizio di scrittura nell'archivio nella Figura 4) riceve i dati di questa coda e li scrive nell'archivio condiviso. Una coda di messaggi è adatta a questo scenario, poiché almeno una volta fornisce la semantica che consente di garantire che, una volta pubblicati, i dati nella coda non andranno persi. È possibile implementare il servizio di scrittura nell'archivio tramite un ruolo di lavoro distinto.

![Illustrazione dell'uso di una coda per il buffer dei dati di strumentazione](media/best-practices-monitoring/BufferedQueue.png)

_Figura 4. Utilizzo di una coda per dati di strumentazione del buffer_

Il servizio di raccolta dati locale può aggiungere dati a una coda immediatamente dopo averli ricevuti. La coda funge da buffer e il servizio di scrittura nell'archivio può recuperare e scrivere i dati con il proprio ritmo. Per impostazione predefinita, una coda opera in base al principio First In, First-Out. È tuttavia possibile assegnare la priorità ai messaggi, per accelerare il passaggio di questi attraverso la coda se contengono dati che devono essere gestiti più rapidamente. Per ulteriori informazioni, vedere il [coda con priorità](https://msdn.microsoft.com/library/dn589794.aspx) modello. In alternativa, è possibile usare diversi canali, ad esempio gli argomenti sui bus di servizio, per dirigere i dati verso destinazioni diverse a seconda della forma di elaborazione analitica necessaria.

Per la scalabilità è possibile eseguire più istanze del servizio di scrittura nell'archivio. Se è presente un volume elevato di eventi, è possibile usare un hub di eventi per inviare i dati a risorse di calcolo diverse per l'elaborazione e l'archiviazione.

<a name="consolidating-instrumentation-data"></a>
#### <a name="_consolidating-instrumentation-data_"></a>_Consolidamento dei dati di strumentazione_
I dati di strumentazione recuperati dal servizio di raccolta dati da una singola istanza di un'applicazione offrono una visualizzazione localizzata dell'integrità e delle prestazioni di tale istanza. Per valutare l'integrità complessiva del sistema, è necessario consolidare alcuni aspetti dei dati nelle viste locali. È possibile eseguire questa operazione dopo che i dati sono stati archiviati. In alcuni casi, però, ciò è possibile anche non appena i dati sono stati raccolti. Anziché essere scritti direttamente nell'archivio condiviso, i dati di strumentazione possono passare attraverso un servizio di consolidamento separato che combina i dati e funge da processo di filtro e pulizia. Ad esempio, possono essere combinati in questo modo i dati di strumentazione che includono le stesse informazioni di correlazione, ad esempio un ID attività. È possibile che un utente avvii un'operazione aziendale in un nodo e che quindi la trasferisca in un altro nodo in caso di errore del nodo originario o a seconda della configurazione del bilanciamento del carico. Questo processo consente di rilevare e rimuovere eventuali dati duplicati (sempre una possibilità se il servizio dati di telemetria utilizza le code di messaggi per spingere i dati di strumentazione in archiviazione). Figura 5 illustra un esempio di questa struttura.

![Esempio di uso di un servizio per consolidare i dati di strumentazione](media/best-practices-monitoring/Consolidation.png)

_Figura 5. Utilizzo di un servizio separato per consolidare e pulire i dati di strumentazione_

### <a name="storing-instrumentation-data"></a>Archiviazione dei dati di strumentazione
Le discussioni precedenti hanno rappresentato una panoramica semplicistica del modo in cui vengono archiviati i dati di strumentazione. In realtà, è più sensato archiviare tipi diversi di informazioni tramite le tecnologie più adatte al modo in cui ogni tipo verrà utilizzato.

Ad esempio, l'archiviazione BLOB e l'archiviazione tabelle di Azure presentano alcune somiglianze nella modalità di accesso, ma hanno alcune limitazioni per quanto riguarda le operazioni che è possibile eseguire attraverso di esse. Anche la granularità dei dati al loro interno è diversa. Se è necessario eseguire altre operazioni di analisi o servono funzionalità di ricerca full-text sui dati, è preferibile usare l'archiviazione dei dati che fornisce funzionalità ottimizzate per specifici tipi di query e accesso ai dati. Ad esempio:

- I dati dei contatori delle prestazioni possono essere archiviati in un database SQL per abilitare l'analisi ad hoc.
- I log di traccia possono essere archiviati in modo più efficiente in Azure DocumentDB.
- Le informazioni relative alla sicurezza possono essere scritte in HDFS.
- Le informazioni che richiedono la ricerca full-text possono essere archiviate tramite Elasticsearch, che può anche rendere più veloci le ricerche mediante l'indicizzazione avanzata.

È possibile implementare un servizio aggiuntivo che recupera periodicamente i dati dall'archiviazione condivisa, partiziona e filtra i dati in base ai relativi scopi e quindi li scrive in un set appropriato di archivi dati, come illustrato nella figura 6. Un approccio alternativo consiste nell'includere questa funzionalità nel processo di consolidamento e pulizia e nello scrivere i dati direttamente in questi archivi man mano che vengono recuperati, piuttosto che salvarli in un'area di archiviazione intermedia condivisa. Ogni approccio presenta vantaggi e svantaggi. L'implementazione di un servizio di partizionamento separato riduce il carico del servizio di consolidamento e pulizia e consente la rigenerazione di almeno una parte dei dati partizionati, se necessario, a seconda della quantità dei dati mantenuti nell'archivio condiviso. Tuttavia, ciò comporta un consumo aggiuntivo di risorse. Potrebbe anche verificarsi un ritardo tra la ricezione dei dati di strumentazione da ogni istanza dell'applicazione e la conversione dei dati in informazioni di utilità operativa.

![Partizionamento e archiviazione di dati](media/best-practices-monitoring/DataStorage.png)

_Figura 6. Partizionamento dei dati in base alle analisi e ai requisiti di archiviazione_

Gli stessi dati di strumentazione potrebbero essere necessari per più scopi. Ad esempio, è possibile usare i contatori delle prestazioni per offrire una visualizzazione cronologica delle prestazioni del sistema nel tempo. Queste informazioni potrebbero essere combinate con altri dati sull'utilizzo per generare informazioni di fatturazione clienti. In queste situazioni, gli stessi dati potrebbero essere inviati a più destinazioni, ad esempio a un database di documenti che può fungere da archivio a lungo termine per le informazioni di fatturazione e a un archivio multidimensionale per la gestione di analisi complesse delle prestazioni.

È necessario considerare l’urgenza dei dati. È indispensabile accedere rapidamente ai dati che forniscono informazioni per l'invio degli avvisi, pertanto questi dati devono essere mantenuti all'interno di risorse di archiviazione rapide e indicizzati o strutturati in modo da ottimizzare le query eseguite dal sistema di generazione degli avvisi. In alcuni casi il servizio di telemetria, che raccoglie i dati in ciascun nodo, potrebbe avere bisogno di formattare e salvare i dati in locale, in modo che un'istanza locale del sistema di generazione degli avvisi possa notificare rapidamente eventuali problemi. Gli stessi dati possono essere inviati al servizio di scrittura nell'archivio visualizzato nei diagrammi precedenti e archiviati in modo centralizzato, se necessari anche per altri scopi.

Le informazioni usate per analisi più ponderate, per la creazione di report e per l'individuazione di tendenze cronologiche sono meno urgenti e possono essere archiviate in un modo che supporti data mining e query ad hoc. Per altre informazioni, vedere la sezione [Supporto di analisi critiche, a caldo e a freddo](#supporting-hot-warm-and-cold-analysis) più avanti in questo documento.

#### <a name="_log-rotation-and-data-retention_"></a>_Rotazione dei registri e conservazione dei dati_
La strumentazione può generare una notevole quantità di dati. Questi dati possono essere contenuti in diverse posizioni, dai file di registro non elaborati, i file di traccia e altre informazioni acquisite in ogni nodo alla visualizzazione consolidata, pulita e partizionata dei dati contenuti in uno spazio di archiviazione condiviso. In alcuni casi, dopo che i dati sono stati elaborati e trasferiti, i dati non elaborati di origine possono essere rimossi da ogni nodo. In altri casi, potrebbe essere necessario o semplicemente utile salvare le informazioni non elaborate. Ad esempio, potrebbe essere più opportuno lasciare disponibili i dati generati a scopo di debug nella loro forma non elaborata, eliminandoli rapidamente dopo la rimozione di tutti i bug.

I dati sulle prestazioni hanno spesso una durata maggiore perché possono essere usati per identificare le tendenze delle prestazioni e pianificare la capacità. La visualizzazione consolidata di questi dati viene generalmente mantenuta online per un periodo limitato, per consentirne l'accesso rapido. In seguito, questi dati possono essere archiviati o eliminati. I dati raccolti per la misurazione e la fatturazione ai clienti potrebbero dover essere salvati a tempo indeterminato. Alcuni requisiti normativi, poi, potrebbero richiedere l'archiviazione e il salvataggio anche delle informazioni raccolte a scopo di controllo e sicurezza. Anche questi dati sono riservati e potrebbe essere necessario crittografarli o proteggerli in altro modo per evitare manomissioni. Non registrare mai le password degli utenti o altre informazioni che potrebbero essere usate per commettere furti di identità. Tali dettagli devono essere eliminati dai dati prima che questi vengano archiviati.

#### <a name="_down-sampling_"></a>_Sottocampionamento_
Archiviare dati cronologici è utile per individuare le tendenze a lungo termine. Anziché salvare dati obsoleti nella loro interezza, è possibile eseguire il sottocampionamento dei dati per ridurne la risoluzione e risparmiare sui costi di archiviazione. Ad esempio, anziché salvare gli indicatori di prestazioni minuto per minuto, è possibile consolidare i dati che risalgono a più di un mese prima in una visualizzazione ora per ora.

### <a name="best-practices-for-collecting-and-storing-logging-information"></a>Procedure consigliate per la raccolta e archiviazione delle informazioni di registrazione
Nell'elenco seguente sono riepilogate le procedure consigliate per l'acquisizione e l'archiviazione delle informazioni di registrazione.

- L'agente di monitoraggio o il servizio di raccolta dati deve essere eseguito come servizio out-of-process e deve essere semplice da distribuire.
- Tutto l'output dell'agente di monitoraggio o del servizio di raccolta dati deve essere in un formato sconosciuto indipendente dal computer, dal sistema operativo o dal protocollo di rete. Ad esempio, generare informazioni in un formato autodescrittivo come JSON, MessagePack o Protobuf anziché ETL/ETW. L'uso di un formato standard consente al sistema di costruire pipeline di elaborazione. In questo modo è possibile integrare facilmente i componenti che consentono di leggere, trasformare e inviare i dati nel formato concordato.
- Il processo di monitoraggio e raccolta dati deve essere a prova di errore e non deve attivare condizioni di errore a catena.
- In caso di errore temporaneo nell'invio di informazioni a un sink dati, l'agente di monitoraggio o il servizio di raccolta dati deve essere pronto a riordinare i dati di telemetria in modo che le informazioni più recenti vengano inviate per prime. L'agente di monitoraggio o il servizio di raccolta dati potrebbe scegliere di eliminare i dati meno recenti o di salvarli in locale e trasmetterli in un secondo momento, a propria discrezione.

## <a name="analyzing-data-and-diagnosing-issues"></a>Analisi dei dati e diagnosi dei problemi
Una parte importante del processo di monitoraggio e diagnostica è l'analisi dei dati raccolti per ottenere un'immagine dello stato complessivo del sistema. È necessario aver definito indicatori KPI e metriche delle prestazioni personalizzati ed è importante comprendere in che modo strutturare i dati raccolti per soddisfare i propri requisiti di analisi. È anche importante comprendere le correlazioni tra i dati acquisiti in diverse metriche e i file di log, poiché queste informazioni possono essere fondamentali per tener traccia di una sequenza di eventi e consentire la diagnosi dei problemi che potrebbero verificarsi.

Come descritto nella sezione [Consolidamento dei dati di strumentazione](#consolidating-instrumentation-data), i dati di ogni parte del sistema vengono di solito acquisiti in locale, ma devono essere abbinati a dati generati in altri siti che fanno parte del sistema. Queste informazioni richiedono un'attenta correlazione per garantire che i dati vengono combinati in modo accurato. Ad esempio, i dati di utilizzo per un'operazione possono estendersi a un nodo che ospita un sito Web a cui si connette un utente, a un nodo che esegue un servizio distinto accessibile nell'ambito dell'operazione stessa e all'archivio dati presente in un altro nodo. Queste informazioni devono essere collegate tra loro per fornire una panoramica sull'utilizzo delle risorse e dell'elaborazione per l'operazione. Nel nodo in cui i dati vengono acquisiti potrebbero essere eseguite operazioni di pre-elaborazione e filtro dei dati, mentre l'esecuzione di operazioni di aggregazione e formattazione è più probabile in un nodo centrale.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### <a name="supporting-hot,-warm,-and-cold-analysis"></a>Supporto di analisi critiche, a caldo e a freddo
L’analisi e la riformattazione dei dati per scopi di virtualizzazione, creazione di report e invio di avvisi possono essere processi complessi che utilizzano un proprio set di risorse. Per alcune forme di monitoraggio il tempo è un fattore critico. Per essere efficaci, tali forme di monitoraggio richiedono un'immediata analisi dei dati. Ciò è noto come _analisi critica_. Tra i possibili esempi, le analisi necessarie per l'invio degli avvisi e alcuni aspetti del monitoraggio della sicurezza, ad esempio il rilevamento di un attacco al sistema. I dati necessari per questi scopi devono essere rapidamente disponibili e strutturati per un'elaborazione efficiente. In alcuni casi, potrebbe essere necessario spostare l'elaborazione dell'analisi nei singoli nodi in cui vengono mantenuti i dati.

Altre forme di analisi sono meno dipendenti dal tempo ma, una volta ricevuti i dati non elaborati, potrebbero richiedere alcuni calcoli e alcune operazioni di aggregazione. Questo tipo di analisi è detto _analisi a caldo_. L’analisi delle prestazioni spesso rientra in questa categoria. In questo caso, è improbabile che un unico evento prestazioni isolato sia statisticamente significativo. Potrebbe essere causato da un picco o da un problema improvviso. I dati di una serie di eventi dovrebbero fornire un quadro più affidabile delle prestazioni del sistema.

È possibile utilizzare l’analisi a caldo anche per diagnosticare problemi di integrità. Un evento di integrità viene di solito elaborato tramite un'analisi critica e può generare un avviso immediatamente. Un operatore deve essere in grado di approfondire i motivi di tale evento esaminando i dati del percorso a caldo. Tali dati devono contenere informazioni sugli eventi che conducono al problema che ha causato l'evento di integrità.

Alcuni tipi di monitoraggio generano più dati a lungo termine. Questa analisi può essere eseguita in un giorno successivo, se possibile secondo una pianificazione predefinita. In alcuni casi è possibile che l'analisi debba eseguire operazioni complesse di filtro su grandi volumi di dati acquisiti durante un certo periodo. Questa analisi è detta _analisi a freddo_. Il requisito chiave è che i dati, una volta acquisiti, vengano archiviati in modo sicuro. Il controllo e il monitoraggio dell'utilizzo, ad esempio, richiedono un quadro preciso dello stato del sistema a intervalli regolari nel tempo, ma non è necessario che tali informazioni siano disponibili per l'elaborazione immediatamente dopo essere state raccolte.

Un operatore può usare l'analisi a freddo anche per fornire i dati per l'analisi predittiva di integrità. L'operatore può raccogliere informazioni cronologiche per un periodo specifico e usarle in combinazione con i dati di integrità correnti, recuperati dal percorso critico, per individuare tendenze che potrebbero presto causare problemi di integrità. In questi casi, potrebbe essere necessario generare un avviso per consentire di intraprendere un'azione correttiva.

### <a name="correlating-data"></a>Correlazione dei dati
I dati acquisiti dalla strumentazione possono offrire uno snapshot dello stato del sistema, ma lo scopo dell'analisi è rendere tali dati utilizzabili. Ad esempio:

- Che cosa ha causato un carico I/O elevato a livello di sistema a un'ora specifica?
- È il risultato di un numero elevato di operazioni di database?
- Questo influisce sui tempi di risposta del database, sul numero di transazioni al secondo e sui tempi di risposta dell’applicazione nello stesso momento?

In tal caso, un'azione correttiva che potrebbe ridurre il carico potrebbe essere la partizione dei dati su più server. Le eccezioni possono anche essere la conseguenza di un errore a qualsiasi livello del sistema. Un'eccezione in corrispondenza di un livello spesso provoca un altro errore al livello superiore.

Per questi motivi è necessario essere in grado di mettere in relazione i diversi tipi di dati di monitoraggio a ogni livello per produrre una visualizzazione complessiva dello stato del sistema e delle applicazioni in esecuzione all'interno di questo. È quindi possibile utilizzare queste informazioni per decidere se il sistema funzioni in modo accettabile o meno e determinare cosa può essere fatto per migliorare la qualità del sistema.

Come descritto nella sezione [Informazioni per la correlazione dei dati](#information-for-correlating-data), è necessario assicurarsi che i dati di strumentazione non elaborati includano informazioni sufficienti su ID attività e contesto per supportare le aggregazioni necessarie per la correlazione degli eventi. Tali dati potrebbero anche essere mantenuti in diversi formati e potrebbe essere necessario analizzare queste informazioni per convertirle in un formato standard per l'analisi.

### <a name="troubleshooting-and-diagnosing-issues"></a>Diagnosi e risoluzione dei problemi
Per effettuare una diagnosi è necessario essere in grado di determinare la causa di errori o comportamenti imprevisti e di eseguire l'analisi delle cause radice. Le informazioni necessarie in genere sono:

- Informazioni dettagliate dei registri eventi e di traccia per l'intero sistema o per un sottosistema specifico nell'intervallo di tempo indicato.
- Tracce complete dello stack risultanti da eccezioni ed errori di qualsiasi livello specificato che si verificano all'interno del sistema o di un sottosistema specifico durante il periodo di tempo indicato.
- Dump di arresto anomalo del sistema per qualsiasi processo non riusciti in un punto qualsiasi del sistema o per un sottosistema specificato durante un intervallo di tempo indicato.
- Registri attività con l'indicazione delle operazioni eseguite da tutti gli utenti o da utenti selezionati durante il periodo di tempo indicato.

L'analisi dei dati per la risoluzione dei problemi spesso richiede una conoscenza tecnica approfondita dell'architettura del sistema e dei vari componenti che costituiscono la soluzione. Di conseguenza, è spesso necessario un numero elevato di interventi manuali per interpretare i dati, stabilire la causa dei problemi e consigliare una strategia appropriata per correggerli. Potrebbe essere opportuno semplicemente archiviare una copia di queste informazioni nel formato originale e renderla disponibile per l'analisi a freddo da parte di un esperto.

## <a name="visualizing-data-and-raising-alerts"></a>Visualizzazione dei dati e generazione di avvisi
Un aspetto importante di qualsiasi sistema di monitoraggio è la possibilità di presentare i dati in modo che un operatore possa individuare rapidamente eventuali tendenze o problemi. È importante anche la possibilità di informare rapidamente un operatore nel caso si verifichi un evento significativo che potrebbe richiedere attenzione.

La presentazione dei dati può assumere varie forme, tra cui la visualizzazione tramite dashboard, avvisi e creazione di report.

### <a name="visualization-by-using-dashboards"></a>Visualizzazione tramite dashboard
Il modo più comune per visualizzare i dati consiste nell'usare dashboard che possono visualizzare le informazioni sotto forma di una serie di diagrammi, grafici o illustrazioni di altro tipo. Tali elementi possono contenere parametri e un analista dovrebbe essere in grado di selezionare i parametri importanti, ad esempio il periodo di tempo, per una qualsiasi situazione specifica.

I dashboard possono essere organizzati in modo gerarchico. I dashboard di livello superiore possono offrire una visione complessiva di ogni aspetto del sistema, consentendo però a un operatore di scendere nel dettaglio. Ad esempio, un dashboard che raffigura le operazioni di I/O complessive dei dischi del sistema deve consentire a un analista di visualizzare la frequenza di I/O di ogni disco per determinare se per uno o più account dispositivo specifici viene rilevato un volume di traffico eccessivo. In una situazione ideale, il dashboard dovrebbe visualizzare anche le informazioni correlate, ad esempio l'origine di ogni richiesta (utente o attività) che genera queste operazioni di I/O. Queste informazioni possono quindi essere usate per determinare se e come distribuire il carico in modo più uniforme tra i dispositivi e se il sistema possa offrire prestazioni migliori con l'aggiunta di altri dispositivi.

Un dashboard potrebbe anche usare una codifica tramite colori o altri segnali visivi per indicare i valori apparentemente anomali o al di fuori dell'intervallo previsto. Riprendendo l'esempio precedente:

- Un disco che presenta una frequenza I/O vicina alla capacità massima per un periodo prolungato (un disco in condizioni critiche) potrebbe essere evidenziato in rosso.
- Un disco con una frequenza I/O che periodicamente raggiunge il limite massimo per brevi periodi (un disco in condizioni difficili) potrebbe essere evidenziato in giallo.
- Un disco che presenta un utilizzo normale potrebbe essere visualizzato in verde.

Si noti che perché un sistema di dashboard funzioni in modo efficace, deve avere a disposizione dati non elaborati con cui lavorare. Durante la creazione del proprio sistema di dashboard o l'uso di un dashboard sviluppato da un'altra organizzazione è necessario comprendere quali dati di strumentazione è necessario raccogliere, a quale livello di granularità devono presentarsi e quale formato devono avere perché siano utilizzabili dal dashboard.

Un dashboard efficiente non solo visualizza le informazioni, ma consente a un analista di formulare domande ad hoc su tali informazioni. Alcuni sistemi forniscono strumenti di gestione che l’operatore può utilizzare per eseguire queste attività ed esplorare i dati sottostanti. In alternativa, a seconda dell'archivio usato per mantenere queste informazioni, è possibile eseguire query direttamente sui dati o importare questi ultimi all'interno di strumenti quali Microsoft Excel per ulteriore analisi e la creazione di report.

> [AZURE.NOTE] È consigliabile consentire l'accesso ai dashboard solo a personale autorizzato. Queste informazioni, infatti, potrebbero essere delicate dal punto di vista commerciale. È anche necessario proteggere i dati sottostanti destinati al dashboard per impedire agli utenti di modificarli.

### <a name="raising-alerts"></a>Creazione di avvisi
L’invio di avvisi è il processo con cui vengono analizzati i dati di monitoraggio e strumentazione e viene generata una notifica se viene rilevato un evento significativo.

L'invio di avvisi consente di garantire che il sistema rimanga integro, reattivo e protetto. Si tratta di una parte importante di qualsiasi sistema che offre agli utenti garanzie in termini di prestazioni, disponibilità e privacy e in cui potrebbe essere necessario agire immediatamente sui dati. Potrebbe essere necessario informare un operatore dell'evento che ha attivato l'avviso. L’invio di avvisi può essere utilizzato anche per richiamare funzioni di sistema come ad esempio il ridimensionamento automatico.

L’invio di avvisi dipende generalmente dai seguenti dati di strumentazione:

- Eventi di protezione. Se i registri eventi indicano il verificarsi di errori ripetuti di autenticazione e/o autorizzazione, il sistema potrebbe essere sotto attacco ed è necessario informare un operatore.
- Metriche delle prestazioni. Il sistema deve rispondere rapidamente se una determinata metrica delle prestazioni supera una soglia specificata.
- Informazioni sulla disponibilità. Se viene rilevato un errore, potrebbe essere necessario riavviare rapidamente uno o più sottosistemi o eseguire il failover su una risorsa di backup. Errori ripetuti in un sottosistema possono indicare problemi più gravi.

Gli operatori potrebbero ricevere informazioni sugli avvisi tramite molti canali di recapito, ad esempio posta elettronica, cercapersone o SMS. Un avviso può includere anche l'indicazione del grado di criticità della situazione. Molti sistemi di invio di avvisi supportano i gruppi di sottoscrizione. Tutti gli operatori che fanno parte di uno stesso gruppo possono ricevere lo stesso set di avvisi.

Un sistema di invio di avvisi dovrebbe essere personalizzabile, con la possibilità di specificare come parametri i valori appropriati dei dati di strumentazione sottostanti. Questo approccio consente a un operatore di filtrare i dati e concentrarsi sulle soglie o sulle combinazioni di valori di interesse. Si noti che in alcuni casi è possibile fornire al sistema di invio degli avvisi i dati di strumentazione non elaborati. In altre situazioni potrebbe essere più appropriato fornire i dati aggregati. Ad esempio, è possibile attivare un avviso se negli ultimi 10 minuti l'utilizzo della CPU per un nodo ha superato il 90%. I dettagli forniti al sistema di invio degli avvisi devono includere anche le informazioni di riepilogo e di contesto appropriate. Tali dati possono contribuire a ridurre la possibilità di attivazione di avvisi da parte di eventi falsi positivi.

### <a name="reporting"></a>Creazione di report
La creazione di report viene usata per generare una visualizzazione complessiva del sistema. Oltre alle informazioni correnti può includere dati cronologici. I requisiti per la creazione di report sono suddivisi in due ampie categorie: creazione di report operativi e creazione di report di sicurezza.

Il report sui dati operativi in genere include i seguenti aspetti:

- Aggregazione di statistiche che è possibile usare per comprendere l'utilizzo delle risorse del sistema nel suo complesso o di sottosistemi specifici durante un intervallo di tempo specificato
- Identificazione delle tendenze nell'utilizzo delle risorse per l'intero sistema o sottosistemi specifici durante un periodo di tempo specificato
- Monitoraggio delle eccezioni che si sono verificate in tutto il sistema o in sottosistemi specifici durante un periodo di tempo specificato
- Determinazione dell'efficienza dell'applicazione in termini di risorse distribuite e della possibilità di riduzione del volume delle risorse e dei relativi costi associati senza influire inutilmente sulle prestazioni.

La creazione di report di sicurezza prevede il rilevamento dell'uso del sistema da parte dei clienti. Può includere:

- Controllo delle operazioni eseguite dall'utente. Ciò richiede la registrazione di ogni richiesta eseguita da ogni utente, con la data e l'ora corrispondenti. I dati devono essere strutturati in modo da consentire a un amministratore di ricostruire rapidamente la sequenza delle operazioni eseguite da un utente specifico in un determinato periodo di tempo.
- Rilevamento dell'uso delle risorse per utente. Ciò richiede la registrazione della modalità e della durata dell'accesso da parte di ogni richiesta di ciascun utente alle diverse risorse che costituiscono il sistema. Un amministratore deve essere in grado di usare questi dati per generare un report di utilizzo per utente per un periodo di tempo specificato, anche a scopo di fatturazione.

In molti casi, i processi batch possono generare report in base a una pianificazione definita. La latenza in genere non è un problema. Devono però essere disponibili, se necessario, anche processi per la generazione ad hoc. Ad esempio, se si memorizzano i dati in un database relazionale, come Database SQL Azure, è possibile utilizzare uno strumento quale SQL Server Reporting Services per estrarre e formattare i dati e presentarli come una serie di report.

## <a name="related-patterns-and-guidance"></a>Modelli correlati e informazioni aggiuntive
- [Indicazioni sulla scalabilità automatica](best-practices-auto-scaling.md) descrive come ridurre il sovraccarico di gestione riducendo la necessità che operatore effettui continuamente il monitoraggio delle prestazioni di un sistema e prenda decisioni sull'aggiunta o la rimozione di risorse.
- [modello di monitoraggio degli endpoint di integrità](https://msdn.microsoft.com/library/dn589789.aspx) descrive come implementare controlli funzionali all'interno di un'applicazione a cui strumenti esterni possono accedere tramite gli endpoint esposti a intervalli regolari.
- [modello di coda con priorità](https://msdn.microsoft.com/library/dn589794.aspx) illustra come definire le priorità dei messaggi in coda in modo che le richieste urgenti siano ricevute e possano essere elaborate prima dei messaggi meno urgenti.

## <a name="more-information"></a>Altre informazioni
- [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md)
- [Azure: nozioni fondamentali di telemetria e risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx)
- [Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure](./cloud-services/cloud-services-dotnet-diagnostics.md)
- [Cache Redis di Azure](https://azure.microsoft.com/services/cache/), [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) e [HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Come usare le code del bus di servizio](./service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
- [SQL Server Business Intelligence in Macchine virtuali di Azure](./virtual-machines/virtual-machines-windows-classic-ps-sql-bi.md)
- [Ricevere notifiche di avviso](./azure-portal/insights-receive-alert-notifications.md) e [Tenere traccia dell'integrità del servizio](./azure-portal/insights-service-health.md)
- [Application Insights](./application-insights/app-insights-overview.md)



<!--HONumber=Oct16_HO2-->


