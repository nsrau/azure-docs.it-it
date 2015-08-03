<properties
   pageTitle="Linee guida di monitoraggio e diagnostica | Microsoft Azure"
   description="Procedure consigliate per il monitoraggio di applicazioni distribuite nel cloud."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Indicazioni di monitoraggio e diagnostica

![](media/best-practices-monitoring/pnp-logo.png)

## Panoramica
Le applicazioni distribuite e i servizi in esecuzione nel cloud sono, per loro natura, complessi componenti software che comprendono molte parti mobili. In un ambiente di produzione, è importante essere in grado di rilevare il modo in cui gli utenti utilizzano il sistema, tracciare l'utilizzo delle risorse e, generalmente, monitorare l'integrità e le prestazioni del sistema. Queste informazioni possono essere utilizzate come uno strumento diagnostico per rilevare e correggere i problemi e per consentire di individuare potenziali problemi e impedire che si verifichino.

## Scenari di monitoraggio e diagnostica
Il monitoraggio consente di ottenere informazioni sul funzionamento di un sistema e costituisce una parte fondamentale per mantenere gli obiettivi di qualità del servizio. Alcuni scenari comuni per la raccolta dei dati di monitoraggio includono:

- Assicurarsi che il sistema rimanga integro.
- Tracciare la disponibilità del sistema e dei relativi elementi componenti.
- Gestione delle prestazioni per assicurarsi che la velocità effettiva del sistema non si riduca in modo imprevisto all’aumento del volume di lavoro.
- Garantire che il sistema soddisfi ogni contratto di servizio concordato con i clienti.
- Proteggere la privacy e la sicurezza del sistema, utenti e i relativi dati.
- Tracciare le operazioni eseguite a scopo di controllo o a fini normativi.
- Monitorare l’utilizzo quotidiano del sistema e individuare i trend che potrebbero portare a problemi se non vengono affrontati.
- Rilevamento di problemi che si verificano, dal report iniziale tramite l’analisi delle possibili cause, la rettifica, gli aggiornamenti software conseguenti e la distribuzione.
- Operazioni di rilevamento e debug di versioni del software.

> [AZURE.NOTE]Questo elenco non vuole essere completo. Questo documento focalizza questi scenari come le situazioni più comuni di esecuzione del monitoraggio ma altri scenari, meno comuni o specifici per questo ambiente, sono possibili.

Le sezioni seguenti descrivono questi scenari in modo più dettagliato. Le informazioni per ogni scenario sono esaminate nel seguente formato:

- Una breve panoramica dello scenario.
- Requisiti tipici di questo scenario.
- I dati di strumentazione non elaborati richiesti per supportare lo scenario e le possibili origini di queste informazioni.
- Come questi dati non elaborati possono essere analizzati e combinati per generare informazioni diagnostiche significative.

## Monitoraggio dell’integrità
Un sistema è integro se funziona ed è in grado di elaborare le richieste. Lo scopo del monitoraggio dell’integrità è generare uno snapshot dello stato attuale di integrità del sistema per poter verificare che tutti i componenti del sistema funzionino come previsto.

### Requisiti per il monitoraggio dell’integrità
Un operatore deve ricevere un avviso rapidamente (entro pochi secondi) se qualsiasi parte del sistema è ritenuta non integra. L'operatore deve essere in grado di verificare quali parti del sistema funzionano normalmente e in quali parti si verificano problemi. Integrità del sistema può essere evidenziata utilizzando un sistema semaforo; rosso per non integro (sistema arrestato), giallo per parzialmente Integro (il sistema è in esecuzione con funzionalità ridotte) e verde per completamente integro.

Un sistema di monitoraggio completo consente a un operatore di eseguire il drill-down attraverso il sistema per visualizzare lo stato di integrità di sottosistemi e componenti. Ad esempio, se tutto il sistema viene rappresentato come parzialmente integro, l'operatore deve essere in grado di eseguire lo zoom avanti e determinare quale funzionalità sia attualmente disponibile.

### Requisiti di origine dati, strumentazione e raccolta dati
I dati non elaborati richiesti per supportare il monitoraggio dell’integrità possono essere generati come risultato di:

- Tracciare l'esecuzione delle richieste degli utenti. Queste informazioni permettono di determinare quali richieste hanno avuto esito positivo, quali esito negativo e la durata di ogni richiesta.
- Monitoraggio sintetico degli utenti. Questo processo simula i passaggi eseguiti da un utente e segue una serie di passaggi predefiniti. I risultati di ogni passaggio devono essere acquisiti.
- Eccezioni di registrazione, errori e avvisi. Queste informazioni possono essere acquisite a seguito di istruzioni di traccia incorporate nel codice dell’applicazione e recuperando informazioni dai registri eventi dei servizi a cui fa riferimento il sistema.
- Monitoraggio dell'integrità di qualsiasi servizio di terze parti utilizzato dal sistema. Ciò può richiedere il recupero e l'analisi dei dati di integrità forniti da questi servizi e queste informazioni potrebbero richiedere una varietà di formati.
- Monitoraggio endpoint Questo meccanismo è descritto più dettagliatamente nella sezione Monitoraggio della disponibilità.
- Raccolta di informazioni sulle prestazioni dell’ambiente, ad esempio l'utilizzo della CPU in background o un'attività I/O (rete compresa).

### Analisi dei dati di integrità
L'obiettivo principale del monitoraggio dell’integrità è indicare rapidamente se il sistema è in esecuzione. L’analisi critica dei dati immediati può attivare un avviso se viene rilevato un componente critico non integro (non riesce a rispondere a una serie consecutiva di ping, ad esempio). L'operatore può quindi richiedere l'azione correttiva appropriata.

Un sistema più avanzato può includere un elemento predittivo che esegue un'analisi a freddo di carichi di lavoro recenti e attuali per individuare le tendenze e determinare se il sistema potrebbe rimanere integro o se saranno necessarie risorse aggiuntive. Questo elemento predittivo deve basarsi sulle metriche di prestazioni critiche, ad esempio la frequenza di richieste dirette a ogni servizio o sottosistema, i tempi di risposta di tali richieste e il volume dei dati trasferiti in e da ogni servizio. Se il valore di qualsiasi metrica supera una soglia definita, il sistema può generare un avviso per consentire a un operatore o alla scalabilità automatica (se disponibile) di eseguire le azioni preventive necessarie per garantire l'integrità del sistema. Queste azioni possono implicare l'aggiunta di risorse, riavviare uno o più i servizi con esito negativo o applicare la limitazione per le richieste di priorità inferiore.

## Monitoraggio della disponibilità
Un sistema realmente integro richiede che i componenti e sottosistemi che costituiscono il sistema siano disponibili. Monitoraggio della disponibilità è strettamente correlato al monitoraggio dell'integrità, ma mentre il monitoraggio dell’integrità stato fornisce una visualizzazione immediata dell'attuale integrità del sistema, il monitoraggio della disponibilità riguarda il rilevamento della disponibilità del sistema e dei relativi componenti per generare statistiche riguardanti il tempo di corretto funzionamento del sistema.

In molti sistemi, alcuni componenti (ad esempio un database) sono configurati con ridondanza incorporata per consentire il failover rapido in caso di errore grave o perdita di connettività. In teoria, gli utenti non devono accorgersi che si è verificato un errore di questo tipo, ma da una punto di vista di monitoraggio della disponibilità è necessario raccogliere quante più informazioni possibili su tali errori per provare e determinare la causa e intraprendere azioni correttive per impedire che si verifichino di nuovo.

I dati necessari per rilevare la disponibilità possono dipendere da diversi fattori di basso livello, molti dei quali potrebbero essere specifiche dell'applicazione, del sistema e dell’ambiente. Un sistema di monitoraggio efficace acquisisce i dati di disponibilità che corrispondono a questi fattori di basso livello e quindi vengono aggregati in modo da offrire una panoramica del sistema. Ad esempio, in un sistema di e-commerce, la funzionalità di business che consente a un cliente di effettuare ordini può dipendere dal repository dove vengono archiviati i dettagli dell'ordine e dal sistema di pagamento che gestisce le transazioni monetarie per pagare per tali ordini. La disponibilità della parte del posizionamento di ordine del sistema è pertanto una funzione della disponibilità dell'archivio e del sistema di pagamento secondario.

### Requisiti per il monitoraggio della disponibilità
Un operatore deve inoltre essere in grado di visualizzare la disponibilità cronologica di ogni sistema e sottosistema e utilizzare queste informazioni per individuare eventuali tendenze che possono causare uno o più sottosistemi periodicamente con esito negativo (i servizi iniziano a fallire a una determinata ora del giorno che corrispondente agli orari in cui si verifica il picco dell’elaborazione?)

Fornendo inoltre una visualizzazione immediata e cronologica sulla disponibilità o in caso contrario di ogni sottosistema, una soluzione di monitoraggio deve inoltre poter rapidamente avvisare un operatore quando uno o più servizi hanno esito negativo o gli utenti non sono in grado di connettersi ai servizi. Non si tratta semplicemente di monitorare ogni servizio ma anche di esaminare le azioni eseguite da ogni utente, se tali azioni hanno esito negativo quando tentano di comunicare con un servizio. In alcuni casi, un livello di errore di connettività è normale e può essere dovuto a errori temporanei, ma può essere utile consentire al sistema di generare un avviso sul numero degli errori di connettività a un sottosistema specificato durante un periodo di tempo specifico.

### Requisiti di origine dati, strumentazione e raccolta dati
Come nel monitoraggio dell’integrità, i dati non elaborati richiesti per supportare il monitoraggio della disponibilità possono essere generati come risultato del monitoraggio sintetico dell’utente e della registrazione di qualsiasi eccezione, errore e avviso che potrebbero verificarsi. Inoltre, è possibile ottenere i dati sulla disponibilità dal monitoraggio degli endpoint. L'applicazione può esporre uno o più endpoint di integrità, ogni test di accesso a un'area funzionale all'interno del sistema. Il sistema di monitoraggio può eseguire il ping di ogni endpoint in base a una pianificazione definita e raccogliere i risultati (esito positivo o esito negativo).

Tutti i timeout e gli errori di connettività di rete e i tentativi di connessione devono essere registrati. Tutti i dati devono riportare un timestamp.

<a name="analyzing-availablity-data"></a>
### Analisi dei dati di disponibilità
I dati di strumentazione devono essere aggregati e correlati per supportare i seguenti tipi di analisi:

- La disponibilità immediata del sistema e dei sottosistemi.
- La frequenza degli errori di disponibilità del sistema e dei sottosistemi. In teoria, un operatore deve essere in grado di correlare gli errori con specifiche attività. Qual è il problema quando il sistema fallisce?
- Una visualizzazione cronologica delle frequenze di errori del sistema o di qualsiasi sottosistema in qualsiasi periodo di tempo specificato e il caricamento nel sistema (numero di richieste utente ad esempio) quando si è verificato un errore.
- I motivi della mancata disponibilità del sistema o dei sottosistemi. Ad esempio, il servizio non in esecuzione, perdita di connettività, connessione ma timeout e connessione ma restituzione di errori.

È possibile calcolare la percentuale di disponibilità di un servizio in un periodo di tempo utilizzando la formula:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Ciò è utile per i contratti di servizio ([il monitoraggio del contratto di servizio ](#SLA-monitoring) è descritto in dettaglio più avanti nelle Indicazioni). La definizione di _Tempi di inattività_ dipende dal servizio. Ad esempio, Visual Studio Online definisce il tempo di inattività come il periodo durante il quale i tentativi di connessione al servizio da parte di un cliente richiedono più di 120 secondi e tutte le operazioni di base di lettura e scrittura hanno esito negativo dopo aver stabilito la connessione all'interno di tale periodo.

## Monitoraggio delle prestazioni
Quando il sistema si trova in più condizioni di stress per l’aumento del volume di utenti e le dimensioni dei set di dati che tali utenti utilizzano aumentano, il guasto di uno o più componenti diventa probabile. Spesso, il guasto di un componente è preceduto da una riduzione delle prestazioni. Se si è in grado di rilevare tale riduzione, è possibile adottare misure proattive per ovviare al problema.

Le prestazioni del sistema dipendono da numerosi fattori. Ogni fattore in genere viene misurato tramite indicatori di prestazioni chiave (KPI), ad esempio il numero di transazioni di database al secondo o il volume di richieste di rete vengono gestiti correttamente nell'intervallo di tempo specificato. Alcuni di questi indicatori KPI potrebbero essere disponibili come misure specifiche delle prestazioni, mentre altri possono derivare da una combinazione di metriche.

> [AZURE.NOTE]Determinando buone o scarse prestazioni, è necessario comprendere il livello di prestazioni in cui il sistema deve essere in grado di funzionare. È necessario osservare il sistema mentre funziona con un carico tipico e acquisire i dati per ogni indicatore KPI in un periodo di tempo. Questo potrebbe comportare l'esecuzione del sistema sotto un carico simulato in un ambiente di test e raccogliere i dati appropriati prima di distribuire il sistema in un ambiente di produzione.

> Assicurarsi inoltre che il monitoraggio delle prestazioni non diventi un onere non autorizzato dal sistema. È possibile regolare dinamicamente il livello di dettaglio relativo ai dati raccolti dal processo di monitoraggio delle prestazioni.

### Requisiti per il monitoraggio delle prestazioni
Per esaminare le prestazioni del sistema, un operatore n genere deve poter visualizzare le informazioni relative a:

- Velocità di risposta per le richieste degli utenti.
- Il numero di richieste utente simultanee.
- Il volume di traffico di rete.
- La velocità a cui le transazioni aziendali vengono completate.
- Tempo medio di elaborazione delle richieste.

Può inoltre essere utile fornire strumenti che consentono a un operatore di individuare delle correlazioni, ad esempio:

- Il numero di utenti simultanei rispetto al tempo di latenza della richiesta (quanto tempo occorre per iniziare a elaborare una richiesta dopo che l'utente l’ha inviata).
- Il numero di utenti simultanei rispetto al tempo di risposta medio (quanto tempo occorre per completare una richiesta dopo l'avvio di elaborazione).
- Il volume di richieste rispetto al numero di errori di elaborazione.

Oltre a queste informazioni ad alto livello funzionale, un operatore deve anche essere in grado di ottenere una visualizzazione dettagliata delle prestazioni per ogni componente nel sistema. Questi dati sono generalmente vengono forniti utilizzando i contatori di prestazioni di basso livello che monitorano informazioni quali:

- Utilizzo della memoria,
- Numero di thread,
- Tempo di elaborazione della CPU,
- Lunghezza coda di richieste,
- Disco o errori e velocità di I/O di rete,
- Numero di byte scritti o letti,
- Indicatori di middleware, ad esempio lunghezza della coda.

Tutte le visualizzazioni devono consentire a un operatore di specificare un periodo di tempo: i dati visualizzati potrebbero essere uno snapshot della situazione attuale e/o una visualizzazione cronologica delle prestazioni.

Un operatore deve essere in grado di generare un avviso in base a qualsiasi misura delle prestazioni per qualsiasi valore dato durante un intervallo di tempo specificato.

### Requisiti di origine dati, strumentazione e raccolta dati
I dati di prestazioni ad alto livello (velocità effettiva, numero di utenti simultanei, numero di transazioni aziendali, frequenza di errori e così via) possono essere raccolti monitorando l’avanzamento delle richieste degli utenti nel momento in cui arrivano e passano attraverso il sistema. Ciò implica che includa le istruzioni di analisi in punti chiave nel codice dell'applicazione con le informazioni di temporizzazione. Tutti gli errori, eccezioni e gli avvisi devono essere acquisiti con dati sufficienti per consentire loro di essere correlati con le richieste che li hanno causati. Il log IIS è un'altra origine utile.

Se possibile, è inoltre consigliabile acquisire dati sulle prestazioni per i sistemi esterni utilizzati dall'applicazione. Questi sistemi esterni potrebbero fornire i propri contatori delle prestazioni o altre funzionalità per la richiesta di dati sulle prestazioni. Se non è possibile, è necessario registrare informazioni quali l'ora di inizio e ora di fine di ogni richiesta effettuata a un sistema esterno, insieme allo stato dell'operazione (esito positivo, errore o avviso). Ad esempio, è possibile utilizzare un approccio ”cronometro” per le richieste di tempo. Avvia un timer al momento dell'avvio della richiesta e arresta il timer al completamento della richiesta.

Dati sulle prestazioni a basso livello per singoli componenti in un sistema potrebbero essere disponibili tramite funzionalità quali i contatori delle prestazioni di Windows e la diagnostica di Azure.

### Analisi dei dati sulle prestazioni
La maggior parte delle attività di analisi consiste nell’aggregare i dati sulle prestazioni per il tipo di richiesta utente (ad esempio l'aggiunta di un elemento al carrello acquisti o l’esecuzione di un processo di estrazione in un sistema di e-commerce) e/o il sottosistema o servizio a cui viene inviata ogni richiesta.

Un altro requisito comune è il riepilogo dei dati sulle prestazioni in percentili selezionati. Ad esempio per determinare i tempi di risposta per il 99% delle richieste, 95% delle richieste e 70% delle richieste. Potrebbero esserci destinazioni del contratto di servizio o altri obiettivi impostati per ogni percentile. I risultati in corso devono essere segnalati in tempo reale per rilevare problemi immediati, nonché per essere aggregati nel tempo per scopi statistici.

Nel caso di problemi di latenza che hanno un impatto sulle prestazioni, un operatore deve essere rapidamente in grado di identificare la causa del collo di bottiglia esaminando la latenza delle operazioni eseguite da ogni richiesta. I dati sulle prestazioni devono pertanto fornire un mezzo di correlazione di misure delle prestazioni per ogni passaggio per collegarle a una richiesta specifica.

A seconda dei requisiti di visualizzazione, può essere utile generare e archiviare un volume di dati che contengono visualizzazioni dei dati non elaborati per consentire l'esecuzione di query complesse ad hoc e analisi delle informazioni sulle prestazioni.

## Monitoraggio della protezione
Tutti i sistemi commerciali che contengono dati riservati devono implementare una struttura di protezione. La complessità del meccanismo di sicurezza è in genere una funzione di riservatezza dei dati. In un sistema che richiede l'autenticazione degli utenti, è necessario registrare tutti i tentativi di accesso, sia che abbiano esito positivo o negativo. Inoltre, tutte le operazioni eseguite e i dettagli di tutte le risorse utilizzate da un utente autenticato devono essere registrati. Quando l'utente termina la sessione ed esegue la disconnessione, anche queste informazioni devono essere registrate.

Il monitoraggio deve essere in grado di rilevare attacchi al sistema. Ad esempio, un numero elevato di tentativi di accesso potrebbe indicare un attacco a forza bruta o un aumento imprevisto delle richieste potrebbe essere il risultato di un attacco DDoS. È necessario essere pronti per monitorare tutte le richieste a tutte le risorse a prescindere dall'origine di tali richieste. un sistema con una vulnerabilità di accesso può esporre accidentalmente le risorse al mondo esterno senza richiedere che un utente vi acceda effettivamente.

### Requisiti per il monitoraggio della protezione
Gli aspetti più critici del monitoraggio della protezione devono consentire rapidamente a un operatore di:

- Rilevare tentativi di intrusione di un'entità non autenticata,
- Identificare i tentativi di soggetti di eseguire operazioni sui dati per cui essi non dispongono di accesso,
- Determinare se il sistema, o una parte del sistema, è sotto attacco dall’esterno o dall'interno (ad esempio, un utente malintenzionato autenticato può tentare di arrestare il sistema).

Per supportare tali requisiti, un operatore deve ricevere una notifica:

- Di tutti i ripetuti tentativi di accesso con esito negativo effettuati dallo stesso account entro un periodo di tempo specificato.
- Se lo stesso account autenticato tenta ripetutamente di accedere a una risorsa non autorizzata durante un periodo di tempo specificato.
- Se un numero elevato di richieste non autenticate o non autorizzati si verificano durante un periodo di tempo specificato.

Le informazioni fornite a un operatore devono includere l'indirizzo dell'host di origine per ogni richiesta. Se le violazioni della protezione si verificano regolarmente da un determinato intervallo di indirizzi, questi host potrebbero essere bloccati.

Una parte fondamentale nella gestione della sicurezza di un sistema è in grado di rilevare rapidamente azioni che si discostano dai modello abituale. Informazioni quali il numero di richieste di accesso non riusciti e/o completate può essere visualizzato visivamente per rilevare se esiste un picco nell'attività in un momento insolito (ad esempio utenti che accedono alle 3.00 e l'esecuzione di un numero elevato di operazioni durante la giornata di lavoro che inizia alle 9: 00). Queste informazioni possono essere utilizzate anche per configurare ridimensionamento automatico basato sul tempo, ad esempio, se un operatore osserva che un numero elevato di utenti accede regolarmente a una determinata ora del giorno, l'operatore può disporre di avviare i servizi di autenticazione aggiuntivi per gestire il volume di lavoro e quindi arrestare tali servizi aggiuntivi quando il periodo di picco è stato superato.

### Requisiti di origine dati, strumentazione e raccolta dati
La protezione è un aspetto comprensivo di sistemi distribuiti e i dati pertinenti probabilmente devono essere generati in più punti all'interno di un sistema. È consigliabile adottare un approccio di sicurezza e gestione di informazioni ed eventi (SIEM) per raccogliere le informazioni relative alla sicurezza derivanti da eventi generati dall'applicazione, apparecchiature di rete, server, firewall, software antivirus e altri elementi di prevenzione delle intrusioni.

Il monitoraggio della protezione può incorporare dati da strumenti che non fanno parte dell'applicazione, come utilità che consentono di identificare le attività di analisi delle porte da agenzie esterne o filtri di rete in grado di rilevare tentativi di accesso non autenticato all'applicazione e ai dati.

In tutti i casi i dati raccolti devono consentire a un amministratore di determinare la natura di qualsiasi attacco e intraprendere le contromisure appropriate.

### Analisi dei dati di protezione
Una funzionalità di monitoraggio della protezione è la varietà di origini da cui si verificano i dati. I diversi formati e il livello di dettaglio richiedono spesso complesse analisi dei dati acquisiti per legare tra loro in un thread coerente di informazioni. A parte i casi più semplici (ad esempio, il rilevamento di un numero elevato di accessi non riusciti o tentativi ripetuti di effettuare l'accesso non autorizzato alle risorse critiche), potrebbe non essere possibile eseguire qualsiasi elaborazione automatizzata complessa dei dati di protezione e invece può essere preferibile scrivere i dati, corredati di timestamp ma in caso contrario nella forma originale, a un archivio protetto per consentire l'analisi manuale degli esperti.

<a name="SLA-monitoring"></a>

## Contratto di servizio di monitoraggio
Molti sistemi commerciali che supportano i clienti paganti forniscono garanzie sulle prestazioni del sistema sotto forma di contratti di servizio. In pratica, i contratti di servizio affermano che il sistema è in grado di gestire un volume di lavoro all'interno di un intervallo di tempo concordato e senza perdita di dati critici. Il monitoraggio del contratto di servizio si occupa di garantire che il sistema è in grado di soddisfare i contratti di servizio misurabili.

> [AZURE.NOTE]Il contratto di servizio di monitoraggio è strettamente correlato al monitoraggio delle prestazioni, ma mentre il monitoraggio delle prestazioni si occupa di garantire che il sistema funzioni _in modo ottimale_, il contratto di servizio di monitoraggio è disciplinato da obbligazioni contrattuali che definiscono cosa significhi _in modo ottimale_.

I contratti di servizio vengono spesso definiti in termini di:

- Disponibilità complessiva del sistema. Ad esempio, un'organizzazione può garantire che il sistema sarà disponibile per il 99,9% del tempo; Ciò equivale a non più di 9 ore di inattività all'anno o circa 10 minuti alla settimana.
- Velocità effettiva operativa. Questo aspetto viene spesso espresso come uno o più chiavi – soglie massime, quali la garanzia che il sistema sarà in grado di supportare fino a 100.000 richieste utente simultanee o gestire 10.000 transazioni aziendali simultanee
- Tempo di risposta operativa. Il sistema potrebbe rendere garanzie la frequenza con cui vengono elaborate le richieste, ad esempio 99% di tutte le transazioni di business verrà completata entro 2 secondi e nessun singola transazione richiede più di 10 secondi.

> [AZURE.NOTE]Alcuni contratti per i sistemi commerciali possono includere anche i contratti di servizio riguardanti il supporto tecnico, ad esempio tutte le richieste di help desk richiedono una risposta entro 5 minuti e che il 99% dei problemi sia risolto completamente entro un giorno lavorativo. Un’efficace [gestione dei problemi](#issue-tracking) (descritto più avanti in questa sezione) è la chiave per soddisfare i contratti di servizio come questi.

### Requisiti per il monitoraggio del contratto di servizio
A livello più alto, un operatore deve essere in grado di determinare rapidamente se il sistema soddisfa i contratti di servizio concordati o meno e, se non è quindi in grado di eseguire il drill-down ed esaminare i fattori sottostanti per determinare i motivi le prestazioni scarse.

Gli indicatori di alto livello tipici che possono essere rappresentati visivamente includono:

- La percentuale di tempo di attività di servizio.
- La velocità effettiva dell'applicazione (misurata in termini di transazioni completate e/o operazioni al secondo).
- Il numero di richieste dell’applicazione con esito positivo/negativo.
- Il numero di errori di sistema e delle applicazioni, le eccezioni e gli avvisi.

Tutti questi indicatori devono poter essere filtrati per un periodo di tempo specificato.

Un'applicazione cloud includerà probabilmente un numero di componenti e sottosistemi. Un operatore deve essere in grado di selezionare un indicatore di alto livello e vedere come viene creato dall’integrità degli elementi sottostanti. Ad esempio, se il tempo di attività dell'intero sistema scende al di sotto di un valore accettabile, un operatore deve essere in grado di eseguire lo zoom avanti e determinare quali elementi contribuiscono a questo errore.

> [AZURE.NOTE]Il tempo di attività del sistema deve essere definito accuratamente. In un sistema che utilizza la ridondanza per garantire la massima disponibilità, potrebbero non riuscire singole istanze di elementi, ma il sistema può continuare a essere funzionale. I tempi di corretto funzionamento visualizzato dal monitoraggio dell'integrità devono indicare i tempi di corretto funzionamento aggregato di ogni elemento e non necessariamente se il sistema è stato effettivamente interrotto. Inoltre, gli errori potrebbero essere isolati, in modo che anche se non è disponibile un sistema specifico il resto del sistema potrebbe rimanere disponibile, anche se con una riduzione delle funzionalità (in un sistema di e-commerce, un errore nel sistema potrebbe impedire a un cliente l’immissione di ordini ma il cliente potrebbe essere ancora in grado di individuare il catalogo dei prodotti).

A scopo di avvertimento, il sistema deve essere in grado di generare un evento se uno qualsiasi degli indicatori di alto livello supera una soglia specificata. I dettagli di basso livello di vari fattori che costituiscono l'indicatore di alto livello devono essere disponibili come dati contestuali al sistema di avvisi.

### Requisiti di origine dati, strumentazione e raccolta dati
I dati non elaborati necessari per supportare il contratto di servizio di monitoraggio sono simili a quelli richiesti per il monitoraggio delle prestazioni con alcuni aspetti del monitoraggio di disponibilità e integrità (vedere le sezioni per ulteriori dettagli). È possibile acquisire dati da:

- Esecuzione del monitoraggio degli endpoint.
- Eccezioni di registrazione, errori e avvisi.
- Tracciare l'esecuzione delle richieste degli utenti.
- Monitoraggio della disponibilità dei servizi di terze parti utilizzati dal sistema.
- Utilizzo di contatori e delle metriche delle prestazioni.

Tutti i dati devono essere programmati e corredati di timestamp.

### Analisi dei dati del contratto di servizio
I dati di strumentazione devono essere aggregati per generare un'immagine delle prestazioni complessive del sistema e per supportare il drill-down per abilitare l'analisi delle prestazioni dei sottosistemi sottostanti. Ad esempio, essere in grado di:

- Calcolare il numero totale delle richieste degli utenti durante il periodo specificato e determinare la frequenza di esito positivo o negativo di queste richieste.
- Combinare i tempi di risposta delle richieste degli utenti per generare una panoramica globale dei tempi di risposta del sistema.
- Analizzare l’avanzamento delle richieste utente suddivide il tempo di risposta complessiva di una determinata richiesta in tempi di risposta dei singoli elementi di lavoro in tale richiesta.  
- Determinare la disponibilità complessiva del sistema come un tempo di corretto funzionamento percentuale per un periodo specifico.
- Analizzare la percentuale di disponibilità di tempo di ciascuno dei singoli componenti e dei servizi nel sistema. Questa operazione potrebbe comportare l'analisi dei log generati dai servizi di terze parti.

Molti sistemi commerciali sono necessari per segnalare i dati di performance reali rispetto al contratto di servizio concordato per un periodo specificato, in genere un mese. Queste informazioni possono essere utilizzate per calcolare i crediti o altre forme di rimborso per i clienti se durante questo periodo non vengono soddisfatti i contratti di servizio. È possibile calcolare la disponibilità di un servizio utilizzando la tecnica descritta nella sezione [Analisi dei dati di disponibilità](#analyzing-availability-data).

Per scopi interni, un'organizzazione potrebbe inoltre monitorare il numero e la natura degli eventi imprevisti che causato guasti ai servizi. Informazioni su come risolvere questi problemi rapidamente o eliminarli completamente, consentiranno di ridurre i tempi di inattività e di soddisfare i contratti di servizio.

## Controllo
A seconda della natura dell'applicazione, potrebbero esserci disposizioni imposte per legge o altre normative legali che specificano i requisiti per il controllo delle operazioni eseguite dagli utenti e per l’accesso a tutti i dati di registrazione. Il controllo può fornire la prova per collegare i clienti a richieste specifiche; il non ripudio è un fattore importante in molti sistemi di e-business per mantenere la fiducia tra un cliente e l'organizzazione responsabile dell'applicazione o del servizio.

### Requisiti per il controllo
Un analista deve essere in grado di analizzare la sequenza di operazioni aziendali eseguite dagli utenti per poter ricostruire le azioni degli utenti. Ciò potrebbe essere necessario semplicemente come una questione di record o come parte di un'analisi forense.

Informazioni di controllo sono estremamente sensibili e conterranno probabilmente dati che identificano gli utenti del sistema correlandoli alle attività da essi eseguite. Per questo motivo, è probabile che le informazioni di controllo verranno visualizzati sotto forma di report disponibili solo per gli analisti attendibili, anziché utilizzando un sistema interattivo che supporta operazioni di grafiche di drill-down. Un analista deve essere in grado di generare una gamma di report, ad esempio l’elenco delle attività di tutti gli utenti che si verificano durante un intervallo di tempo specificato, riportando in dettaglio la cronologia dell'attività per un singolo utente o l’elenco della sequenza di operazioni eseguite a fronte di una o più risorse.

### Requisiti di origine dati, strumentazione e raccolta dati
Le fonti principali di informazioni per il controllo possono includere:

- Il sistema di sicurezza che gestisce l'autenticazione utente.
- Registrazione delle attività dell'utente di log di traccia.
- I registri di sicurezza monitorano tutte le richieste di rete identificabili e non identificabili.

Il formato di dati di controllo e il modo in cui vengono archiviati potrebbe dipendere dai requisiti normativi. Ad esempio, non è possibile pulire i dati in alcun modo (devono essere registratati nel formato originale) e l'accesso al repository in cui vengono conservati deve essere protetto da eventuali manomissioni.

### Analisi dei dati di controllo
Un analista deve essere in grado di accedere ai dati non elaborati nella loro interezza e nella loro forma originale. A parte la necessità di generare comuni report di controllo, gli strumenti utilizzati per analizzare i dati rischiano di essere specializzati ed esterni al sistema.

## Monitoraggio dell'utilizzo
Il monitoraggio dell'utilizzo rileva l'utilizzo delle funzionalità e dei componenti di un'applicazione. I dati raccolti possono essere utilizzati per:

- Determinare quali funzionalità siano utilizzate di frequente e determinare le potenziali aree sensibili nel sistema. Gli elementi di traffico elevato possono trarre vantaggio dal partizionamento funzionale o anche dalla replica per ripartire il carico in modo più uniforme. Queste informazioni consentono inoltre di verificare le funzionalità utilizzate raramente e sono possibili candidati per il ritiro o la sostituzione in una versione futura del sistema.
- Ottenere informazioni sugli eventi operativi del sistema durante il normale utilizzo. Ad esempio, in un sito di e-commerce è possibile registrare le informazioni statistiche sul numero di transazioni e il volume dei clienti che ne è responsabile. Queste informazioni potrebbero essere utilizzate per la pianificazione della capacità man mano che aumenta il numero di clienti.
- Consente di rilevare (possibilmente in modo indiretto) soddisfazione dell'utente con le prestazioni o la funzionalità del sistema. Ad esempio, se un numero elevato di clienti in un sistema di e-commerce abbandona regolarmente i carrelli della spesa questi potrebbe dipendere da un problema con la funzionalità di estrazione.
- Genera informazioni di fatturazione. Un'applicazione commerciale o un servizio multi-tenant può addebitare ai clienti le risorse utilizzate.
- Applicare quote. Se un utente in un sistema multi-tenant supera la quota di utilizzo di tempo o le risorse di elaborazione durante un periodo specificato, l'accesso o l’elaborazione possono essere limitati.

### Requisiti per il monitoraggio dell'utilizzo
Per esaminare l'utilizzo di sistema, un operatore deve poter visualizzare le informazioni relative a:

- Il numero di richieste in fase di elaborazione per ogni sottosistema e dirette a ogni risorsa.
- Il lavoro eseguito da ogni utente.
- Il volume di archiviazione dei dati occupato da ogni utente.
- Le risorse cui accede ogni utente.

Un operatore deve anche essere in grado di generare grafici, ad esempio, visualizzando gli utenti più avidi di risorse o le risorse utilizzate con maggiore frequenza.

### Requisiti di origine dati, strumentazione e raccolta dati
L'utilizzo di rilevamento può essere eseguito a un livello elevato, si noti l'ora di inizio e fine di ogni richiesta e la natura della richiesta (lettura, scrittura e così via, a seconda della risorsa in questione). È possibile ottenere queste informazioni da:

- Traccia attività dell'utente.
- Acquisizione di contatori delle prestazioni misurando l'utilizzo per ogni risorsa.
- Monitoraggio dell'utilizzo della CPU e di I/O di operazioni eseguite da ogni utente.

Per scopi di controllo, è necessario essere in grado di identificare quali utenti sono responsabili dell'esecuzione di operazioni e le risorse che utilizzano queste operazioni. Le informazioni raccolte devono essere dettagliate a sufficienza per abilitare una fatturazione corretta.

<a name="issue-tracking"></a>
## Gestione dei problemi
I clienti e gli altri utenti possono segnalare problemi se nel sistema si verificano eventi o comportamenti imprevisti. Il rilevamento dei problemi riguarda la loro gestione, la loro associazione al tentativo di risolvere qualsiasi problema di fondo nel sistema e informare i clienti sulle possibili risoluzioni.

### Requisiti per la gestione dei problemi
Il rilevamento dei problemi viene spesso eseguito tramite un sistema distinto che consente agli operatori di registrare e visualizzare i dettagli dei problemi segnalati dagli utenti. Questi dettagli possono includere informazioni quali le attività che l'utente ha tentato di eseguire, i sintomi del problema, la sequenza di eventi e qualsiasi errore o messaggio di avviso che sono stati rilasciati.

### Requisiti di origine dati, strumentazione e raccolta dati
L'origine dati iniziale per i dati di rilevamento del problema è l'utente che in primo luogo ha segnalato il problema. L'utente potrebbe essere in grado di fornire dati aggiuntivi, ad esempio un dump di arresto anomalo del sistema (se l'applicazione include un componente che viene eseguito sul desktop dell'utente), uno snapshot dello schermo e la data e l’ora in cui si è verificato l'errore e altre informazioni ambientali, ad esempio la posizione.

Queste informazioni possono essere utilizzate per essere inserite nel tentativo di debugging e consentono di costruire un backlog per le versioni future del software.

### Analisi dei dati di gestione dei problemi
Diversi utenti possono segnalare il problema stesso e il sistema di rilevamento del problema deve associare report comuni.

Lo stato di avanzamento del tentativo di debugging deve essere registrato a fronte del report di ogni problema e quando il problema viene risolto il cliente può essere informato della soluzione.

Se un utente segnala un problema riconosciuto con una soluzione nota nel numero di sistema di rilevamento, l'operatore deve essere in grado di informare immediatamente l'utente della soluzione.

## Operazioni di rilevamento e debug di versioni del software.
Quando un utente segnala un problema, spesso è l’unico a conoscenza dell'impatto immediato che tale problema ha sulle proprie operazioni. L'utente può solo segnalare i risultati della propria esperienza a un operatore responsabile della gestione del sistema. Queste esperienze sono in genere solo un sintomo visibile di uno o più problemi fondamentali. In molti casi, sarà necessario che un analista che, scorrendo la cronologia delle operazioni sottostanti, stabilirà la causa principale del problema (questo processo è denominato _analisi causa principale_).

> [AZURE.NOTE]L’analisi delle Cause principali potrebbe rivelare inefficienze nella progettazione di un'applicazione. In queste situazioni, è possibile rielaborare gli elementi interessati e distribuirli come parte di una versione successiva. Questo processo richiede un attento controllo e i componenti aggiornati devono essere controllati attentamente.

### Requisiti per la traccia e il debug
Per la traccia di eventi imprevisti e altri problemi, è fondamentale che i dati di monitoraggio forniscano informazioni sufficienti non solo sui problemi che si verificano a livello elevato, ma che includano anche dettagli sufficienti per consentire a un analista di risalire all'origine di questi problemi e ricostruire la sequenza degli eventi che si sono verificati. Queste informazioni devono essere sufficienti per consentire a un analista di diagnosticare la causa principale dei problemi in modo che uno sviluppatore possa apportare le modifiche necessarie per impedire che ricorrano.

### Requisiti di origine dati, strumentazione e raccolta dati
La risoluzione dei problemi può includere la traccia di tutti i metodi (e i relativi parametri) richiamati come parte di un'operazione per creare una struttura ad albero che rappresenta il flusso logico attraverso il sistema quando un cliente effettua una richiesta specifica. Eccezioni e avvisi generati dal sistema in seguito a questo flusso devono essere acquisiti e registrati.

Per supportare il debug, il sistema può fornire collegamenti che consentono a un operatore di acquisire informazioni sullo stato nei punti cruciali del sistema o fornire informazioni dettagliate, come lo stato di avanzamento di operazioni selezionate. L’acquisizione dei dati a questo livello di dettaglio per applicare un ulteriore carico sul sistema deve essere un processo temporaneo utilizzato principalmente quando si verifica una serie di eventi inusuale difficile da replicare o quando una nuova versione di uno o più elementi in un sistema richiede un attento controllo per assicurarsi che funzionino come previsto.

## La pipeline di monitoraggio e diagnostica
Il monitoraggio di un sistema distribuito su larga scala costituisce una sfida significativa e ciascuno degli scenari descritti nella sezione precedente non deve necessariamente essere considerato in isolamento. È probabile che ci sia una sovrapposizione significativa nei dati di monitoraggio e diagnostici necessari per ogni situazione, anche se questi dati devono essere elaborati e presentati in modi diversi. Per questi motivi, è necessario avere una visione olistica di monitoraggio e diagnostica.

È possibile prevedere l’intero processo di diagnostica e monitoraggio come una pipeline che comprende le fasi illustrate nella figura 1.

![](media/best-practices-monitoring/Pipeline.png)

_Figura 1. Le fasi della pipeline di monitoraggio e diagnostica_

La figura 1 evidenzia come i dati per il monitoraggio e la diagnostica possono provenire da una varietà di origini dati. La fase di strumentazione/raccolta riguarda la strumentazione. È necessario determinare quali dati acquisire, come acquisirli e come formattarli in modo che possano essere facilmente esaminati. La fase di analisi/diagnosi accetta i dati non elaborati e li utilizza per generare informazioni significative che possono essere sfruttate per determinare lo stato del sistema. Queste informazioni consentono di scegliere le possibili azioni da intraprendere e i risultati possono essere inseriti nella fase di strumentazione/raccolta. La fase di visualizzazione/avvertimenti fase presenta una visualizzazione dello stato del sistema; è possibile visualizzare le informazioni in tempo quasi reale mediante una serie di dashboard e si possono generare rapporti, grafici per fornire una visualizzazione cronologica dei dati che consentono di identificano le tendenze a lungo termine. Se le informazioni indicano che un indicatore KPI potrebbe superare i limiti accettabili, questa fase può generare un avviso a un operatore. In alcuni casi, un avviso può essere utilizzato anche per attivare un processo automatico che tenta di effettuare operazioni correttive, quali la scalabilità automatica.

Si noti che questi passaggi costituiscono un processo di flusso continuo in cui le fasi si verificano in parallelo. In teoria, tutte le fasi devono essere dinamicamente configurabili; In alcuni momenti soprattutto quando un sistema è stato appena distribuito o si sono verificati problemi, è necessario raccogliere i dati estesi più frequentemente. In altri casi, è possibile ripristinare l'acquisizione di un livello base di informazioni essenziali per verificare che il sistema funzioni correttamente.

Inoltre, l'intero processo di monitoraggio deve essere considerato una soluzione in corso, soggetta a miglioramenti in seguito a commenti e suggerimenti di ottimizzazione. Ad esempio, è possibile iniziare con la valutazione di molti fattori per determinare l'integrità del sistema, ma l’analisi nel tempo potrebbe portare a un perfezionamento eliminando le misure che non sono importanti, consentendo di concentrarsi con maggiore precisione sui dati necessari, riducendo al minimo eventuali rumori di fondo.

## Origini dei dati di monitoraggio e diagnostici
Le informazioni utilizzate dal processo di monitoraggio possono provenire da origini diverse, come illustrato nella figura 1. A livello di applicazione, le informazioni provengono dai log di traccia incorporati nel codice del sistema. Gli sviluppatori devono seguire un approccio standard per tenere traccia del flusso di controllo mediante il codice (ad esempio, all’ingresso di un metodo emettere un messaggio di traccia che specifica il nome del metodo, l’ora corrente, il valore di ciascun parametro e ogni altra informazione utile . Registrare i tempi di ingresso e in uscita può inoltre rivelarsi utile). È necessario registrare tutte le eccezioni e gli avvisi e assicurarsi di mantenere un'analisi completa di tutte le eccezioni annidate e di tutti gli avvisi. Idealmente, è inoltre consigliabile acquisire informazioni che identificano l'utente che esegue il codice e le informazioni correlate all’attività (per tenere traccia delle richieste durante il passaggio attraverso il sistema) e i tentativi di accedere a tutte le risorse quali code di messaggi, database, file e altri servizi dipendenti. Queste informazioni possono essere utilizzate per la valutazione e a fini di controllo.

Molte applicazioni utilizzano librerie e framework per eseguire attività comuni, ad esempio l'accesso a un archivio dati o la comunicazione in rete. Questi framework possono essere configurati per i propri messaggi di traccia e informazioni di diagnostica non elaborate, ad esempio la frequenza delle transazioni, l’esito positivo e negativo della trasmissione di dati e così via.

> [AZURE.NOTE]Molti framework moderni pubblicano automaticamente le prestazioni ed eventi di traccia e per acquisire queste informazioni è sufficiente fornire un mezzo per recuperarle e archiviarle dove possono essere elaborate e analizzate.

Il sistema operativo in cui è in esecuzione l'applicazione può essere fonte di informazioni a livello di sistema di basso livello, ad esempio i contatori delle prestazioni che indicano la velocità I/O, l’utilizzo della memoria e l'utilizzo della CPU. Potrebbero inoltre risultare errori del sistema operativo (ad esempio l'errore per aprire un file correttamente).

Si consiglia inoltre l'infrastruttura sottostante e dei componenti in cui viene eseguito il sistema. Macchine virtuali, reti virtuali e servizi di archiviazione possono essere origine di contatori delle prestazioni a livello di infrastruttura importanti e di altri dati di diagnostica.

Se l'applicazione utilizza servizi esterni, ad esempio un server web o un sistema di gestione di database, questi servizi possono pubblicare le proprie informazioni di traccia, log e i contatori delle prestazioni. Esempi includono SQL Server Dynamic Management Views per tenere traccia delle operazioni eseguite su un database SQL Server e registri di traccia di Internet Information Server per la registrazione delle richieste effettuate a un server web.

Appena i componenti di un sistema vengono modificati e le nuove versioni distribuite, è importante essere in grado di attribuire problemi, eventi e metriche a ogni versione. Queste informazioni devono essere collegate alla pipeline di rilascio in modo che i problemi con una versione specifica di un componente siano rilevati e corretti rapidamente.

Problemi di protezione che potrebbero verificarsi in qualsiasi punto nel sistema. Ad esempio, un utente potrebbe tentare di accedere con un ID utente non valido o una password; un utente autenticato potrebbe tentare di ottenere l’accesso non autorizzato a una risorsa oppure un utente potrebbe fornire una chiave non valida o non aggiornata per accedere alle informazioni crittografate. Informazioni relative alla sicurezza per le richieste con esito positivo e negativo devono sempre essere registrate.

La sezione [Strumentazione di un'applicazione](#instrumenting-an-application) contiene ulteriori indicazioni sulle informazioni che è necessario acquisire, ma esiste una serie di strategie che è possibile utilizzare per raccogliere in primo luogo queste informazioni:

- **Monitoraggio applicazione/sistema**. Questa strategia utilizza origini interne all'interno dell'applicazione, i framework di applicazione, il sistema operativo e l’infrastruttura. Il codice stesso dell'applicazione può generare un proprio monitoraggio dei dati in punti importanti durante il ciclo di vita di una richiesta client. L'applicazione può includere istruzioni di traccia che possono essere abilitate o disabilitate a seconda delle circostanze in modo selettivo. È possibile inoltre inserire dinamicamente diagnostica utilizzando un framework di diagnostica. In genere, questi framework forniscono plug-in che possono essere collegati ai vari punti di strumentazione del codice e acquisire dati di traccia in questi punti.

Inoltre, il codice e/o l'infrastruttura sottostante possono generare eventi in punti critici. Il monitoraggio degli agenti che sono configurati per l'ascolto di questi eventi può registrare le informazioni sull'evento.

- **Utente reale monitoraggio**. Questo approccio registra le interazioni tra un utente e l'applicazione e osserva il flusso di ogni richiesta e risposta. Queste informazioni possono avere un duplice scopo: possono essere utilizzate per la valutazione dell'utilizzo di ogni utente e per determinare se gli utenti ricevono un’appropriata qualità del servizio (ad esempio, tempi di risposta rapidi, latenza bassa e minimi errori). I dati acquisiti possono essere utilizzati per identificare le aree del problema in cui si verificano più di frequente errori e gli elementi in cui il sistema subisce un rallentamento, probabilmente a causa di aree sensibili nell'applicazione o di un altro tipo di collo di bottiglia. Se questo approccio viene implementato con cautela, è possibile ricostruire i flussi degli utenti tramite l'applicazione per effettuare debug e test.

	> [AZURE.IMPORTANT]I dati acquisiti mediante il monitoraggio di utenti reali devono essere considerati estremamente sensibili e possono contenere materiale riservato. Se i dati acquisiti vengono salvati, devono essere archiviati in modo sicuro. Se i dati vengono utilizzati per le prestazioni di monitoraggio o a scopo di debug, è necessario estrarre prima tutte le informazioni personali.

- **Monitoraggio sintetico utente** In questo approccio, è possibile scrivere il proprio client di test che simula un utente ed esegue una serie configurabile ma tipica delle operazioni. È possibile rilevare le prestazioni dei client di test per determinare lo stato del sistema. È inoltre possibile utilizzare più istanze del client di test come parte di un'operazione di test di carico per stabilire la risposta del sistema in condizioni di stress e il tipo di monitoraggio di output che viene generato in queste condizioni.

	> [AZURE.NOTE]È possibile implementare il monitoraggio degli utenti reali e sintetici includendo il codice che registra regola l'esecuzione di chiamate al metodo e altre parti critiche di un'applicazione.

- **Profiling**. Questo approccio è principalmente destinato al monitoraggio e al miglioramento delle prestazioni dell'applicazione. Invece di operare al livello di funzionalità utilizzato dal monitoraggio degli utenti reali e sintetici, acquisisce informazioni di basso livello durante l'esecuzione dell'applicazione. La profilatura può essere implementata tramite il campionamento periodico dello stato di esecuzione di un'applicazione (determinare quale parte di codice l'applicazione esegue in un determinato punto nel tempo) o tramite la strumentazione che inserisce le ricerche nel codice in situazioni importanti (ad esempio l'inizio e fine di una chiamata al metodo) e registra quali metodi sono stati richiamati, a che ora e il tempo impiegato per ogni chiamata. Questi dati possono quindi essere analizzati per determinare quali parti dell'applicazione potrebbero causare problemi di prestazioni.

- **Monitoraggio endpoint** Questa tecnica utilizza uno o più endpoint diagnostici esposti esposti dall'applicazione in modo specifico per abilitare il monitoraggio. Un endpoint fornisce un percorso nel codice dell'applicazione e può restituire informazioni sull’integrità del sistema. Endpoint diversi possono concentrarsi su vari aspetti della funzionalità. È possibile scrivere il proprio client di diagnostica che invia richieste periodiche a questi endpoint e assimilare le risposte. Questo approccio è descritto più dettagliatamente in [Modello di monitoraggio integrità Endpoint](https://msdn.microsoft.com/library/dn589789.aspx) del sito Web Microsoft.

Per la massima copertura, è necessario utilizzare queste tecniche in combinazione tra loro.

<a name="instrumenting-an-application"></a>
## Strumentazione di un'applicazione
La strumentazione è una parte essenziale del processo di monitoraggio. Se si acquisiscono i dati che consentono di prendere queste decisioni, è possibile prendere solo decisioni significative sulle prestazioni e sull’integrità del sistema. Le informazioni raccolte tramite la strumentazione dovrebbero essere sufficienti per consentire di valutare le prestazioni, diagnosticare i problemi e prendere decisioni senza dover accedere a un server di produzione remoto per eseguire la traccia (e il debug) manualmente.

I dati di strumentazione verranno includono in genere informazioni scritte nei log di traccia e metriche:

- Il contenuto di un log di traccia può essere il risultato di dati di testo scritti dall'applicazione, dati binari creati come risultato di un evento di traccia (se l'applicazione utilizza Event Tracing per Windows – ETW) o possono essere generate dai registri di sistema che registrano gli eventi generati dalle parti dell'infrastruttura, ad esempio un server web. I messaggi di testo del registro spesso sono progettati per essere leggibili, ma devono essere scritti anche in un formato che ne consente facilmente l’analisi da parte di un sistema automatizzato. È inoltre necessario classificare i registri; non scrivere tutti i dati di traccia in un unico accesso, ma utilizzare registri separati per registrare l'output di traccia dai diversi aspetti operativi del sistema. Consente di filtrare rapidamente i messaggi di log leggendo il log appropriato, anziché dover elaborare un singolo file di lunga durata. Non si devono mai scrivere informazioni con requisiti di sicurezza diversi (ad esempio informazioni di controllo e i dati di debug) per lo stesso log.

	> [AZURE.NOTE]Un log può essere implementato come un file nel file system o potrebbero essere bloccato in un altro formato, ad esempio un BLOB nell'archiviazione BLOB. Informazioni sul log potrebbero essere conservate anche in un servizio di archiviazione più strutturato, ad esempio le righe di una tabella.

- Le metriche sono in genere semplicemente una misura o un conteggio di alcuni aspetti o delle risorse nel sistema in un momento specifico con uno o più tag o dimensioni associati (a volte definiti_esempio_). Una singola istanza di una metrica non è in genere utile in isolamento. Le metriche invece devono essere acquisite nel tempo. La cosa importante da considerare è quali metriche devono essere registrate e con quale frequenza. La generazione di dati per le metriche impone troppo spesso un carico aggiuntivo significativo nel sistema, mentre l’acquisizione sporadica di metriche può generare le circostanze che possono portare a un evento significativo. Le considerazioni possono variare da una metrica all’altra. Ad esempio, l'utilizzo della CPU in un server può variare significativamente da secondo a secondo, ma un utilizzo elevato diventa un problema solo se dura più minuti.

<a name="information-for-correlating-data"></a>
### Informazioni per la correlazione dei dati
È possibile monitorare facilmente i contatori di prestazioni a livello di sistema individuale, acquisire le metriche per le risorse e ottenere informazioni sulla traccia di applicazione da vari file di log, ma alcuni moduli di monitoraggio richiedono la fase di analisi e di diagnostica nella pipeline di monitoraggio per correlare i dati recuperati da origini diverse. Questi dati possono assumere forme diverse nei dati non elaborati e il processo di analisi deve essere fornito con i dati di strumentazione sufficienti per poter eseguire il mapping di queste diverse forme. Ad esempio, a livello di framework di applicazione, un'attività può essere identificata da un ID thread ma all'interno di un'applicazione, la stessa operazione può essere associata all'ID utente per l'utente che esegue l'attività. Inoltre, è improbabile che esista un mapping 1:1 tra i thread e le richieste utente poiché operazioni asincrone possono riutilizzare gli stessi thread per eseguire operazioni per conto di più utenti. A complicare il tutto, inoltre, una singola richiesta può essere gestita da più thread quando l’esecuzione passa attraverso il sistema. Se possibile, associare ogni richiesta con un ID univoco dell'attività che viene propagato attraverso il sistema come parte del contesto della richiesta (la tecnica per la generazione e l’inclusione dell’attività dell’ID nelle informazioni di traccia dipenderanno dalla tecnologia utilizzata per acquisire i dati di traccia).

Tutti i dati di monitoraggio devono riportare il timestamp nello stesso modo. Per coerenza, registrare tutte le date e le ore tramite Coordinated Universal Time. Ciò contribuirà a rintracciare più facilmente sequenze di eventi .

> [AZURE.NOTE]Computer che operano in reti e fusi orari diversi potrebbero non essere sincronizzati, pertanto la correlazione dei dati di strumentazione che si estende su più computer non dipende solo dall’utilizzo del timestamp.

### Quali informazioni devono includere i dati di strumentazione?
Quando si decide quali dati di strumentazione è necessario raccogliere, tenere presente quanto segue:

- Informazioni acquisite dagli eventi di traccia devono essere leggibili per la macchina e per l’uomo. È necessario adottare schemi ben definiti per queste informazioni per facilitare l'elaborazione automatizzata dei dati di registro tra i sistemi e fornire coerenza a operazioni e leggere i log del personale tecnico. Includere informazioni ambientali, ad esempio l'ambiente di distribuzione, il computer in cui viene eseguito il processo, i dettagli del processo e lo stack di chiamate.  
- La profilatura può imporre un carico significativo nel sistema e deve essere abilitata solo quando necessario. La profilatura tramite strumentazione registra un evento (ad esempio una chiamata al metodo) ogni volta che si verifica, mentre il campionamento registra solo gli eventi selezionati. La selezione potrebbe essere basata sul tempo (una volta ogni N secondi), o basata sulla frequenza (una volta ogni N richieste). Se gli eventi si verificano molto spesso, la profilatura tramite strumentazione potrebbe causare una quantità eccessiva di carico e influire sulle prestazioni globali. In questo caso è preferibile l’approccio tramite campionamento Tuttavia, se la frequenza degli eventi è bassa, il campionamento potrebbe perderli e in questo caso la strumentazione potrebbe essere l'approccio migliore.
- Fornire un contesto sufficiente per consentire a uno sviluppatore o a un amministratore di determinare l'origine di ogni richiesta. Potrebbe essere necessario includere qualche forma di ID di attività per identificare un'istanza specifica di una richiesta e informazioni che possono essere utilizzate per correlare l'attività con le operazioni di calcolo eseguite e le risorse utilizzate. Si noti che questa operazione può attraversare i limiti di processo e del computer. Per la valutazione, il contesto deve inoltre includere (direttamente o indirettamente tramite altre informazioni correlate) un riferimento al cliente che ha effettuato la richiesta. Questo contesto fornisce informazioni utili sullo stato dell'applicazione nel momento in cui sono stati acquisiti i dati di monitoraggio.
- Registrare tutte le richieste e le posizioni o aree da cui queste richieste vengono effettuate. Queste informazioni possono aiutare a determinare se sono presenti eventuali aree sensibili specifiche della posizione e fornire i dati che possono essere utili per determinare se si desidera partizionare un'applicazione o i dati che utilizza.
- Registrare e acquisire i dettagli delle eccezioni con attenzione. Informazioni di debug spesso critici sono perse a causa di una gestione insufficiente delle eccezioni. Acquisire tutti i dettagli delle eccezioni generate dall'applicazione, inclusa qualsiasi eccezione interna e altre informazioni di contesto, tra cui lo stack di chiamate, se possibile.
- È necessario essere coerenti nei dati acquisiti dai diversi elementi dell'applicazione poiché questo può fornire supporto all’analisi degli eventi e correlarli con le richieste degli utenti. Considerare di utilizzare un pacchetto di registrazione completo e configurabile per raccogliere informazioni anziché dipendere dagli sviluppatori per implementare tutte le diverse parti del sistema che adottano lo stesso approccio. Raccogliere dati dai contatori di prestazioni chiave, ad esempio il volume di I/O eseguiti, l'utilizzo della rete, il numero di richieste, l’utilizzo della memoria e l'utilizzo della CPU. Alcuni servizi di infrastruttura possono fornire i propri contatori delle prestazioni specifici, ad esempio il numero di connessioni a un database, la velocità di esecuzione delle transazioni e il numero di transazioni con esito positivo o negativo. Le applicazioni potrebbero inoltre definire i propri specifici contatori delle prestazioni.
- Registrare tutte le chiamate effettuate a servizi esterni, ad esempio sistemi di database, servizi web o altri servizi a livello di sistema forniti come parte dell'infrastruttura (ad esempio Azure caching). Registrare le informazioni relative al tempo impiegato per eseguire ogni chiamata e il relativo esito positivo o negativo. Se possibile, acquisire informazioni su tutti i tentativi ed errori per eventuali errori temporanei.

### Garantire la compatibilità con sistemi di telemetria
In molti casi, le informazioni prodotte da strumentazione sono generate come una serie di eventi e passate a un sistema di telemetria separato per l'elaborazione e l’analisi. Un sistema di telemetria è in genere indipendente da qualsiasi applicazione specifica o tecnologia, ma richiede informazioni per seguire un formato specifico in genere definito da uno schema. Lo schema specifica in modo efficace un contratto che definisce i campi dati e i tipi che il sistema di telemetria è in grado di acquisire. Lo schema deve essere generalizzato per consentire dati provenienti da una gamma di dispositivi e piattaforme.

Uno schema comune deve includere i campi che sono comuni a tutti gli eventi di strumentazione, ad esempio il nome dell'evento, l'ora dell'evento, l'indirizzo IP del mittente e i dettagli necessari per la correlazione con altri eventi (ad esempio un ID utente, un dispositivo ID e un’applicazione ID). Si tenga presente che gli eventi possono essere generati da qualsiasi numero di dispositivi, in modo che lo schema non debba essere dipendente dal tipo di dispositivo. Inoltre, gli eventi per la stessa applicazione potrebbero essere generati da un numero di dispositivi diversi; l'applicazione potrebbe supportare il roaming o un’altra forma di distribuzione tra più dispositivi. La maggior parte di questi campi deve essere idealmente associata all'output della libreria di registrazione utilizzata per acquisire gli eventi.

Lo schema può includere anche i campi di dominio che sono rilevanti per un determinato scenario comune nelle diverse applicazioni. Potrebbe trattarsi di informazioni sulle eccezioni, avvio dell'applicazione e gli eventi finali e i servizi web API di chiamata con esito positivo o negativo. Tutte le applicazioni che utilizzano lo stesso set di campi di dominio devono creare lo stesso set di eventi, l'abilitazione di un set di report e analisi dei dati da generare comuni.

Infine, uno schema può contenere campi personalizzati per acquisire i dettagli degli eventi specifici dell'applicazione.

### Procedure consigliate per le applicazioni di strumentazione
Nell'elenco seguente sono riepilogate le procedure consigliate per la strumentazione di un'applicazione distribuita in esecuzione nel cloud.

- Semplificare i registri per la lettura e l’analisi. Utilizzare la registrazione strutturata dove possibile. Essere concisi e descrittivi nei messaggi di log.
- In tutti i log, identificare l'origine e fornire il contesto e le informazioni di temporizzazione come ogni record di log.
- Utilizzare lo stesso fuso orario e formato per tutti i timestamp. Questo consente di correlare gli eventi per le operazioni che si estendono su hardware e servizi in esecuzione in aree geografiche diverse.
- Classificare i registri e scrivere messaggi nel file di log appropriato.
- Non rivelare informazioni riservate sul sistema o informazioni personali degli utenti. Eseguire lo scrubbing di queste informazioni prima di registrarle, ma assicurarsi che i relativi dettagli vengano mantenuti. Ad esempio, rimuovere l'ID e la password da qualsiasi stringa di connessione di database, ma scrivere le informazioni rimanenti nel log in modo che un analista possa determinare che il sistema accede al database corretto. Registrare tutte le eccezioni critiche, ma consentire agli amministratori di attivare e disattivare la registrazione per i livelli inferiori di eccezioni e avvisi. Inoltre, acquisire e registrare tutte le informazioni di logica di ripetizione. Questi dati possono essere utili per monitorare l'integrità del sistema temporaneo.
- Tracciare le chiamate di processo, ad esempio le richieste a servizi web esterni o database.
- Non combinare i messaggi di log con requisiti di sicurezza diversi nello stesso file di log. Ad esempio, non scrivere debug e informazioni per lo stesso log.
- Fatta eccezione per gli eventi di controllo, tutte le chiamate di registrazione devono essere operazioni “fire and forget” che non devono bloccare l’avanzamento delle operazioni aziendali. Gli eventi di controllo sono eccezionali perché sono critici per l'azienda e possono essere classificati come una parte fondamentale delle operazioni aziendali.
- La registrazione deve essere estensibile e non deve dipendere direttamente dalla destinazione concreta. Ad esempio, anziché scrivere informazioni utilizzando _System.Diagnostics.Trace_, definire un'interfaccia astratta (ad esempio _ILogger_) che espone metodi di registrazione e che può essere implementata con qualsiasi mezzo appropriato.
- La registrazione deve essere alternativa e non deve mai attivare errori a catena. La registrazione non deve generare eccezioni.
- Considerare la strumentazione come un processo iterativo in corso ed esaminare i registri regolarmente, non solo quando si verifica un problema.

## Raccolta e archiviazione dei dati
La fase di raccolta del processo di monitoraggio riguarda il recupero di informazioni generate da strumentazione, formattazione di dati per rendere più semplice l'utilizzo dalla fase di analisi/calcolo e salvare i dati trasformati in un archivio affidabile e accessibile. I dati di strumentazione raccolti da diverse parti di un sistema distribuito potrebbero essere bloccati in varie posizioni e con diversi formati. Ad esempio, il codice dell'applicazione potrebbe generare file di log di traccia e generare dati di registrazione degli eventi dell'applicazione, mentre i contatori delle prestazioni che consentono di monitorare gli aspetti principali dell'infrastruttura che l'applicazione utilizza potrebbero essere acquisiti utilizzando altre tecnologie. Eventuali componenti di terze parti e i servizi che l'applicazione utilizza possono fornire informazioni di strumentazione in formati diversi, utilizzando il file di traccia separato, archiviazione BLOB, o anche un archivio dati personalizzato.

La raccolta dei dati viene spesso eseguita implementando un servizio di raccolta che può essere eseguito in modo autonomo dall'applicazione che genera i dati di strumentazione. La figura 2 mostra un esempio di questa architettura, evidenziando il sottosistema di raccolta dati di strumentazione.

![](media/best-practices-monitoring/TelemetryService.png)

_Figura 2. Raccolta di dati di strumentazione_

Si tratta di una vista semplificata. Il servizio di raccolta non è necessariamente un unico processo e può comprendere molti elementi costitutivi in esecuzione su computer diversi, come descritto nelle sezioni seguenti. Inoltre, se deve essere eseguita rapidamente l'analisi di alcuni dati di telemetria (analisi critica, come descritto nella sezione [supporto Hot, medio e a freddo Analysis](#supporting-hot-warm-and-cold-analysis) più avanti in questo documento), i componenti locali che operano all’esterno del servizio di raccolta possono eseguire l’attività di analisi immediatamente. La figura 2 mostra questa situazione per eventi selezionati. Dopo l'elaborazione analitica i risultati possono essere inviati direttamente al sottosistema di avviso e visualizzazione. I dati sottoposti ad analisi a caldo o a freddo vengono mantenuti nel servizio di archiviazione mentre attendono di essere elaborati.

Per servizi e applicazioni Azure, diagnostica Azure (WAD) fornisce una possibile soluzione per l'acquisizione di dati. WAD raccoglie i dati dalle origini seguenti per ogni nodo di calcolo, li aggrega e quindi li carica nell'archiviazione di Azure:

- Log di Azure
- Log di IIS
- Log delle richieste non riuscite di IIS
- Registri eventi di Windows
- Contatori delle prestazioni
- Dump di arresto anomalo del sistema
- Log dell'infrastruttura diagnostica di Azure  
- Log degli errori personalizzati

Per ulteriori informazioni, consultare l'articolo [Azure: nozioni fondamentali di telemetria e risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) del sito Web Microsoft.

### Strategie per la raccolta dei dati di strumentazione
Data la natura elastica del cloud e per evitare la necessità di recuperare manualmente i dati di telemetria da ogni nodo nel sistema, è necessario organizzare i dati affinché siano trasferiti in una posizione centrale e consolidati. In un sistema che si estende su più centri dati, potrebbe essere utile prima raccogliere, consolidare e archiviare i dati da area ad area e quindi aggregare i dati locali in un unico sistema centrale.

Per ottimizzare l'utilizzo della larghezza di banda, è possibile scegliere di trasferire i dati meno urgenti in blocchi, come batch. Tuttavia, i dati non devono essere rimandati indefinitamente, soprattutto se contengono informazioni sensibili al tempo.

#### _Eseguire il pull e push dei dati di strumentazione_
Il sottosistema di raccolta dati di strumentazione può recuperare attivamente dati di strumentazione da vari registri e altre fonti per ogni istanza dell'applicazione (il _modello pull_), o può fungere da ricevitore passivo che attende i dati che devono essere inviati da componenti che costituiscono ogni istanza dell'applicazione (il _modello push_).

Un approccio all'implementazione del modello pull consiste nell'utilizzare gli agenti di monitoraggio in esecuzione in locale con ogni istanza dell'applicazione. Un agente di monitoraggio è un processo separato che recupera periodicamente i dati di telemetria (pull) raccolti nel nodo locale e scrive queste informazioni direttamente nell’archiviazione centralizzata è condiviso da tutte le istanze dell'applicazione. Si tratta del meccanismo implementato da tale funzionalità. Ogni istanza di un ruolo web o di lavoro di Azure può essere configurata per acquisizione diagnostica e altre informazioni di traccia archiviate localmente. L'agente di monitoraggio che viene eseguito insieme a ogni copia de dati specificati nell'archiviazione di Azure. Per ulteriori dettagli su questa procedura, consultare la pagina [Configurazione di diagnostica per servizi Cloud di Azure e macchine virtuali](https://msdn.microsoft.com/library/azure/dn186185.aspx) del sito Web Microsoft. Alcuni elementi, ad esempio log IIS, dump di arresto anomalo del sistema e registrazione di errori personalizzati vengono scritti nell'archiviazione BLOB, mentre i dati dal log Windows Event, ETW events e i contatori delle prestazioni sono registrati nell'archiviazione tabelle. Nella figura 3 viene illustrato questo concetto.

![](media/best-practices-monitoring/PullModel.png)

_Figura 3. Utilizzo di un agente di monitoraggio per l’estrazione di informazioni e la scrittura in archiviazione condivisa_

> [AZURE.NOTE]L’utilizzo di un agente di monitoraggio è adatto per l'acquisizione dei dati di strumentazione naturalmente estratti da un'origine dati, ad esempio informazioni da SQL Server Management Views o la lunghezza di una coda di Azure Service Bus.

Per informazioni sulla configurazione e utilizzo di diagnostica Azure, visitare la pagina [Raccogliere dati di registrazione usando Diagnostica di Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) del sito Web Microsoft.

Dati di telemetria per un'applicazione di piccole dimensioni in esecuzione su un numero limitato di nodi concretamente possono essere archiviati in un'unica posizione utilizzando l'approccio descritto in precedenza. Tuttavia, un'applicazione cloud globale, estremamente scalabile potrebbe facilmente generare grandi volumi di dati da centinaia di ruoli web e di lavoro, partizioni di database e altri servizi. Questa enorme quantità di dati può facilmente sovraccaricare la larghezza di banda di I/O disponibile con una singola posizione centrale. Pertanto la soluzione di telemetria deve essere scalabile per evitare che funga da un collo di bottiglia quando il sistema si espande e idealmente incorporare un livello di ridondanza per ridurre il rischio di perdere importanti informazioni di monitoraggio (ad esempio i dati di fatturazione o di controllo), se parte del sistema fallisce.

Per risolvere questi problemi, è possibile implementare l'accodamento. Nella figura 4 viene illustrata questa struttura. In questa architettura, l'agente di monitoraggio locale (se può essere configurato in modo appropriato) o il servizio di raccolta di dati personalizzati (se non) pubblica i dati in una coda e un processo separato che esegue in modo asincrono (il Servizio archiviazione di scrittura nella figura 4) accetta i dati in questa coda e li scrive nell’archiviazione condivisa. Una coda di messaggi è adatta per questo scenario poiché almeno una volta fornisce la semantica per garantire che, una volta registrati, i dati in coda non andranno persi. Il servizio di archiviazione scrittura può essere implementato utilizzando un ruolo di lavoro distinto.

![](media/best-practices-monitoring/BufferedQueue.png)

_Figura 4. Utilizzo di una coda per dati di strumentazione del buffer_

Il servizio di raccolta dati locali può aggiungere dati a una coda appena li riceve. La coda funge da buffer e l'archiviazione di scrittura del servizio può recuperare e scrivere i dati con il proprio ritmo. Per impostazione predefinita, una coda viene eseguita su una base first in first out, ma è possibile assegnare la priorità dei messaggi per accelerare attraverso la coda se contengono dati che devono essere gestiti più rapidamente. Per ulteriori informazioni, vedere il [coda con priorità](https://msdn.microsoft.com/library/dn589794.aspx) modello. In alternativa, è possibile utilizzare diversi canali (come argomenti di Service Bus) per indirizzare i dati a destinazioni diverse a seconda del modulo di elaborazione analitica necessario.

Per la scalabilità, è possibile eseguire più istanze del servizio di archiviazione di scrittura. Se è presente un volume elevato di eventi, è possibile utilizzare un hub di eventi a cui inviare i dati per le risorse di calcolo diverso per l'elaborazione e archiviazione.

<a name="consolidating-instrumentation-data"></a>
#### _Consolidamento dei dati di strumentazione_
I dati di strumentazione recuperati dal servizio di raccolta dati da una singola istanza di un'applicazione offrono una visualizzazione localizzata dell'integrità e delle prestazioni di tale istanza. Per valutare l'integrità complessiva del sistema, è necessario consolidare insieme alcuni aspetti dei dati in viste locali. Questa operazione può essere eseguita dopo che sono stati archiviati i dati, ma in alcuni casi anche appena i dati vengono raccolti. Anziché essere scritti direttamente nell'archiviazione condivisa, i dati di strumentazione potrebbero passare tramite un servizio di consolidamento dei dati separato che combina i dati e funge da un processo di filtro e pulizia. Ad esempio, dati di strumentazione che includono le stesse informazioni di correlazione, ad esempio un ID attività possono essere combinati (è possibile che un utente avvii un'operazione aziendale in un nodo e che la trasferisca a un altro nodo in caso di errore del nodo o a seconda della modalità di configurazione di bilanciamento del carico). Questo processo consente di rilevare e rimuovere eventuali dati duplicati (sempre una possibilità se il servizio dati di telemetria utilizza le code di messaggi per spingere i dati di strumentazione in archiviazione). Figura 5 illustra un esempio di questa struttura.

![](media/best-practices-monitoring/Consolidation.png)

_Figura 5. Utilizzo di un servizio separato per consolidare e pulire i dati di strumentazione_

### Archiviazione dei dati di strumentazione
Le discussioni precedenti hanno rappresentato una panoramica semplicistica del modo in cui vengono archiviati i dati di strumentazione. In realtà, è più sensato archiviare tipi diversi di informazioni tramite le tecnologie più adatte al modo in cui ogni tipo verrà utilizzato. Archiviazione di BLOB e tabelle di Azure, ad esempio, possono presentare alcune somiglianze nel modo di accesso ma presentano limitazioni relative alle operazioni che è possibile eseguire utilizzandole e la granularità dei dati che contengono è molto differente. Se è necessario eseguire ulteriori operazioni di analisi o si necessita di funzionalità di ricerca full-text sui dati, è preferibile utilizzare l'archiviazione dei dati che fornisce funzionalità ottimizzate per specifici tipi di query e l'accesso ai dati. Ad esempio, i dati dei contatori delle prestazioni possono essere archiviati in un database SQL per abilitare l'analisi ad hoc; i log di traccia possono essere archiviati meglio in Azure DocumentDB; le informazioni di sicurezza possono essere scritte in HDFS; le informazioni che necessitano di una ricerca full-text possono essere archiviate utilizzando la ricerca elastica (che può anche velocizzare le ricerche utilizzando l'indicizzazione avanzata.) È possibile implementare un servizio aggiuntivo che recupera periodicamente i dati dall'archiviazione condivisa, partiziona e filtra i dati in base ai relativi scopi e quindi li scrive in un set appropriato di archivi dati, come illustrato nella figura 6. Un approccio alternativo consiste nell'includere questa funzionalità nel processo di consolidamento e pulizia e scrivere i dati direttamente in questi archivi così come vengono recuperati piuttosto che salvarli in un’area di archiviazione intermedia condivisa. Ogni approccio presenta vantaggi e svantaggi. L’implementazione di un servizio di partizionamento separato riduce il carico nel servizio di consolidamento e pulitura e consente la rigenerazione di almeno alcuni dei dati partizionati, se necessario (a seconda della quantità di dati mantenuta nella memoria condivisa). Tuttavia, consuma risorse aggiuntive e potrebbe verificarsi un ritardo tra i dati di strumentazione ricevuti da ogni istanza dell'applicazione e questi dati convertiti in informazioni utilizzabili.

![](media/best-practices-monitoring/DataStorage.png)

_Figura 6. Partizionamento dei dati in base alle analisi e ai requisiti di archiviazione_

Gli stessi dati di strumentazione potrebbero essere necessari per più scopi. Ad esempio, è possibile utilizzare contatori delle prestazioni per fornire una vista cronologica delle prestazioni del sistema nel tempo, ma queste informazioni potrebbero anche essere combinate con altri dati di utilizzo per generare informazioni di fatturazione del cliente. In queste situazioni, gli stessi dati potrebbero essere inviati a più destinazioni, ad esempio un database di documento che può fungere da archivio a lungo termine per contenere le informazioni di fatturazione e un archivio multidimensionale per la gestione di analisi delle prestazioni complesse.

È necessario considerare l’urgenza dei dati. È necessario poter accedere rapidamente ai dati che forniscono informazioni per l’invio degli avvisi, pertanto questi devono essere conservati in un’archiviazione dati rapida e indicizzati o strutturati in modo da ottimizzare le query eseguite dal sistema di avvisi. In alcuni casi, il servizio di telemetria, che raccoglie i dati in ciascun nodo, potrebbe avere bisogno di formattare e salvare i dati in locale in modo che un'istanza locale del sistema di avvisi possa notificare rapidamente eventuali problemi. Gli stessi dati possono essere indirizzati al servizio di archiviazione di scrittura visualizzato nei diagrammi precedenti e archiviati centralmente se necessari anche per altri scopi.

Le informazioni utilizzate per analisi più ponderate, per la creazione di report e per l’individuazione di tendenze cronologiche sono meno urgenti e possono essere archiviate in un modo che supporti data mining e query ad hoc. Per ulteriori informazioni, vedere la sezione [Supporto di analisi critiche, a caldo e a freddo](#supporting-hot-warm-and-cold-analysis) più avanti in questo documento.

#### _Rotazione dei registri e conservazione dei dati_
La strumentazione può generare una notevole quantità di dati. Questi dati possono essere contenuti in diverse posizioni, dai file di registro non elaborati, i file di traccia e altre informazioni acquisite in ogni nodo alla visualizzazione consolidata, pulita e partizionata dei dati contenuti in uno spazio di archiviazione condiviso. In alcuni casi, una volta elaborati e trasferiti i dati, l’origine dei dati non elaborati può essere rimossa da ogni nodo. In altri casi, potrebbe essere necessario o semplicemente utile salvare le informazioni non elaborate. Ad esempio, potrebbe essere più opportuno lasciare i dati generati a scopo di debug nella loro forma non elaborata ma possono poi essere ignorati abbastanza rapidamente dopo aver rimosso tutti i bug. I dati sulle prestazioni hanno spesso una durata maggiore per poter essere utilizzati per l’identificazione delle tendenze delle prestazioni e la pianificazione della capacità. La visualizzazione consolidata di questi dati viene generalmente mantenuta in linea per un periodo limitato per consentire l'accesso rapido, dopo il quale può essere archiviata o eliminata. I dati raccolti per il controllo e la fatturazione dei clienti potrebbero dover essere salvati a tempo indefinito. Inoltre, i requisiti normativi potrebbero richiedere che anche le informazioni raccolte a scopo di controllo e sicurezza debbano essere archiviate e salvate. Anche questi dati sono riservati e potrebbero dover essere crittografati o protetti in altro modo per evitare manomissioni. Informazioni quali le password degli utenti o altre informazioni che potrebbero essere utilizzate per commettere furti di identità non devono mai essere registrate, tali dettagli devono eseguire rimossi dai dati prima dell’archiviazione.

#### _Sottocampionamento_
Spesso è utile per archiviare i dati cronologici e riuscire a individuare tendenze a lungo termine. Anziché salvare dati obsoleti nel loro complesso, potrebbe essere possibile eseguire il sottocampionamento dei dati per ridurne la risoluzione e risparmiare costi di archiviazione. Ad esempio, anziché salvare indicatori di prestazioni minuto per minuto, i dati vecchi di più di un mese potrebbero essere consolidati in una visualizzazione ora per ora.

### Procedure consigliate per la raccolta e archiviazione delle informazioni di registrazione
Nell'elenco seguente sono riepilogate le procedure consigliate per l'acquisizione e la memorizzazione delle informazioni di registrazione.

- L'agente di monitoraggio o il servizio di raccolta dati devono essere eseguiti come servizio out-of-process e devono essere semplice da distribuire.
- Tutto l'output dell'agente di monitoraggio o del servizio di raccolta dati deve essere un formato indipendente da protocollo di rete, sistema operativo o computer. Ad esempio, generare informazioni in un formato autodescrittivo come JSON, MessagePack o Protobuf anziché ETL/ETW. L’utilizzo di un formato standard consente al sistema di costruire pipeline di elaborazione; è possibile integrare facilmente i componenti che consentono di leggere, trasformare e generare dati di output nel formato concordato.
- Il processo di monitoraggio e raccolta dati deve essere sicuro e non deve innescare condizioni di errore a catena.
- In caso di errori temporanei durante l'invio di informazioni a un sink di dati, il servizio di raccolta dati o agente di monitoraggio deve essere preparato a riordinare i dati di telemetria in modo che le informazioni più recenti vengano inviate per prime (il servizio di raccolta dati/agente monitoraggio può scegliere di eliminare i dati meno recenti o salvarli in locale e li trasmetterli in seguito per recuperare il ritardo, a propria discrezione).

## Analisi dei dati e diagnosi dei problemi
Una parte importante del processo di monitoraggio e diagnostica è l’analisi dei dati raccolti per ottenere un'immagine dello stato complessivo del sistema. È necessario aver definito i propri indicatori KPI e le proprie metriche delle prestazioni ed è importante comprendere in che modo è possibile strutturare i dati raccolti per soddisfare i propri requisiti di analisi. È inoltre importante comprendere come i dati acquisiti in diverse metriche e i file di registro siano correlati, poiché queste informazioni possono essere fondamentali per monitorare una sequenza di eventi e consentono di diagnosticare i problemi che insorgono.

Come descritto nella sezione [Consolidamento dei dati di strumentazione](#consolidating-instrumentation-data), i dati di ciascuna parte del sistema vengono generalmente acquisiti in locale, ma devono essere abbinati a dati generati in altri siti che fanno parte del sistema. Queste informazioni richiedono un'attenta correlazione per garantire che i dati vengono combinati in modo accurato. Ad esempio, i dati di utilizzo per un'operazione possono estendersi su un nodo che ospita un sito web a cui si connette un utente, un nodo che esegue un servizio distinto accessibile come parte di questa operazione e l'archiviazione dei dati contenuta in un altro nodo. Queste informazioni devono essere collegate tra loro per fornire una panoramica sull'utilizzo delle risorse e dell'elaborazione per l'operazione. Nel nodo in cui i dati vengono acquisiti potrebbero verificarsi una pre-elaborazione e un filtro dei dati, mentre l'aggregazione e la formattazione si verificano più probabilmente su un nodo centrale.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Supporto di analisi di critiche, a caldo e a freddo
L’analisi e la riformattazione dei dati per scopi di virtualizzazione, creazione di report e invio di avvisi possono essere processi complessi che utilizzano un proprio set di risorse. Alcuni forme di monitoraggio hanno problemi di tempo e richiedono un’immediata analisi dei dati per essere efficaci. Ciò è noto come _analisi critica_. Ad esempio le analisi necessarie per l’invio degli avvisi e alcuni aspetti del monitoraggio della sicurezza (ad esempio il rilevamento di un attacco al sistema). I dati necessari per questi scopi devono essere rapidamente disponibili e strutturati per un'elaborazione efficiente; in alcuni casi potrebbe essere necessario spostare l'elaborazione dell’analisi ai singoli nodi in cui vengono conservati i dati.

Altre forme di analisi sono hanno meno problemi di tempo e potrebbero richiedere alcuni calcoli e aggregazioni una volta che sono stati ricevuti i dati non elaborati. Ciò è noto come _analisi a caldo_. L’analisi delle prestazioni spesso rientra in questa categoria. In questo caso, è improbabile che un evento prestazioni singolo e isolato sia significativo a livello statistico (può essere causato da un picco improvviso o un problema), mentre i dati derivanti da una serie di eventi dovrebbero fornire un quadro più affidabile delle prestazioni del sistema. È possibile utilizzare l’analisi a caldo anche per diagnosticare problemi di integrità. Un evento di integrità viene generalmente elaborato eseguendo analisi critiche e può generare immediatamente un avviso. Un operatore deve essere in grado di analizzare i motivi dell’evento di integrità esaminando i dati dal percorso a caldo; tali dati devono contenere informazioni sugli eventi che conducono al problema che ha causato l'evento di integrità.

Alcuni tipi di monitoraggio generano dati più a lungo termine e l'analisi può essere eseguita in un secondo momento, possibilmente secondo una pianificazione predefinita. In alcuni casi, l'analisi potrebbe dover eseguire operazioni di filtro complesse di grandi volumi di dati acquisiti in un periodo di tempo. Ciò è noto come _analisi a freddo_. Il requisito chiave è che i dati vengano archiviati in modo sicuro una volta che è stata acquisita. Ad esempio il controllo e il monitoraggio dell’utilizzo richiedono un quadro preciso dello stato del sistema a intervalli regolari nel tempo, ma non serve che le informazioni sullo stato siano disponibili per l'elaborazione non appena sono state raccolte. È possibile utilizzare l’analisi a freddo anche per fornire i dati per l'analisi predittiva di integrità. Le informazioni cronologiche raccolte in un periodo di tempo specifico possono essere utilizzate in combinazione con i dati dello stato corrente (recuperati dal percorso critico) per individuare tendenze che presto potrebbero causare problemi di integrità. In questi casi, potrebbe essere necessario generare un avviso per abilitare l'azione correttiva da intraprendere.

### Correlazione dei dati
I dati acquisiti dalla strumentazione possono fornire uno snapshot dello stato del sistema, ma lo scopo dell’analisi è rendere tali dati utilizzabili. Ad esempio, cosa ha causato un carico I/O elevato a livello di sistema in un momento specifico? È il risultato di un numero elevato di operazioni di database? Questo influisce sui tempi di risposta del database, sul numero di transazioni al secondo e sui tempi di risposta dell’applicazione nello stesso momento? In tal caso, un'azione correttiva che potrebbe ridurre il carico potrebbe essere la partizione dei dati su più server. Inoltre, un guasto a qualsiasi livello del sistema può generare eccezioni e un’eccezione in un livello spesso provoca un altro guasto nel livello superiore. Per questi motivi, è necessario essere in grado di correlare i diversi tipi di monitoraggio dei dati a ogni livello per produrre una visualizzazione complessiva dello stato del sistema e delle applicazioni in esecuzione su di esso. È quindi possibile utilizzare queste informazioni per decidere se il sistema funzioni in modo accettabile o meno e determinare cosa può essere fatto per migliorare la qualità del sistema.

Come descritto nella sezione [Informazioni per la correlazione dei dati](#information-for-correlating-data), è necessario assicurarsi che i dati di strumentazione non elaborati includano informazioni sufficienti di ID attività e contesto per supportare le aggregazioni necessarie per la correlazione di eventi. Inoltre, tali dati potrebbero essere conservati in diversi formati e potrebbe essere necessario analizzare queste informazioni per convertirli in un formato standardizzato per fini di analisi.

### Diagnosi e risoluzione dei problemi
Per effettuare una diagnosi è necessario essere in grado di determinare la causa di errori o comportamenti imprevisti, compresa l'esecuzione di analisi delle cause principali. Le informazioni necessarie in genere includono:

- Informazioni dettagliate dai registri eventi e dalle tracce per l'intero sistema o per un sottosistema specificato durante un intervallo di tempo indicato.
- Analisi dello stack complete risultanti da eccezioni ed errori di qualsiasi livello specificato che si verificano all'interno del sistema o di un sottosistema specificato durante un periodo di tempo indicato.
- Dump di arresto anomalo del sistema per qualsiasi processo non riusciti in un punto qualsiasi del sistema o per un sottosistema specificato durante un intervallo di tempo indicato.
- Registri attività con indicazione delle operazioni eseguite da tutti gli utenti o da utenti selezionati durante un periodo di tempo indicato.

L’analisi dei dati per la risoluzione dei problemi spesso richiede una conoscenza tecnica approfondita dell'architettura del sistema e dei vari componenti che costituiscono la soluzione. Di conseguenza, richiede spesso un elevato numero di interventi manuali per interpretare i dati, stabilire la causa di problemi e consigliare una strategia appropriata per correggerli. Potrebbe essere opportuno semplicemente archiviare una copia di queste informazioni nel formato originale e renderlo disponibile per l'analisi a freddo eseguita da esperti.

## Visualizzazione dei dati e generazione di avvisi
Un aspetto importante di qualsiasi sistema di monitoraggio è la possibilità di presentare i dati in modo che un operatore possa individuare rapidamente eventuali problemi o tendenze e di informare rapidamente un operatore nel caso si verifichi un evento significativo che richiede attenzione.

La presentazione dei dati può assumere varie forme, tra cui la visualizzazione tramite dashboard, avvisi e creazione di report.

### Visualizzazione tramite dashboard
Il modo più comune per visualizzare i dati consiste nell'utilizzare dashboard che possono visualizzare le informazioni in una serie di diagrammi, grafici o con un’altra grafica. Tali elementi potrebbero contenere parametri e un analista dovrebbe essere in grado di selezionare i parametri importanti (ad esempio il periodo di tempo) per una qualsiasi situazione specifica. I dashboard possono essere organizzati gerarchicamente, utilizzando i dashboard principali per fornire una visualizzazione complessiva di ogni aspetto del sistema, ma con la funzionalità per consentire a un operatore di andare nel dettaglio. Ad esempio, un dashboard che raffigura il disco I/O complessivo del sistema deve consentire a un analista di approfondire i dati e visualizzare le frequenze I/O di ciascun disco per determinare se uno o più dispositivi specifici hanno un volume di traffico eccessivo. Idealmente il dashboard dovrebbe inoltre visualizzare le informazioni correlate, ad esempio l'origine di ogni richiesta (utente o attività) che genera questo I/O. Queste informazioni potrebbero quindi essere utilizzate per determinare se (e come) ripartire il carico in modo più uniforme tra dispositivi e se il sistema potrebbe offrire prestazioni migliori con l’aggiunta di altri dispositivi. Un dashboard potrebbe inoltre essere in grado di utilizzare la codifica colori o altri segnali visivi per indicare i valori che risultano essere anomali o che si trovano al di fuori di un intervallo previsto. Utilizzando l'esempio precedente, un disco che presenta una frequenza I/O vicina alla capacità massima per un periodo prolungato (un disco critico) potrebbe essere evidenziato in rosso, un disco con una frequenza I/O che periodicamente raggiunge il limite massimo per brevi periodi (un disco caldo) potrebbe essere evidenziato in giallo e un disco che mostra un utilizzo normale potrebbe essere visualizzato in verde.

Tenere presente che affinché un sistema di dashboard funzioni in modo efficace, deve disporre dei dati non elaborati con cui lavorare. Durante la creazione del proprio sistema di dashboard o l’utilizzo di un dashboard sviluppato da un'altra organizzazione, è necessario comprendere quali dati di strumentazione è necessario raccogliere, a quale livello di granularità e come devono essere formattati per poter essere utilizzati dal dashboard.

Un buon dashboard non solo visualizza le informazioni, ma fornisce anche un modo per consentire a un analista di formulare domande ad hoc su tali informazioni. Alcuni sistemi forniscono strumenti di gestione che l’operatore può utilizzare per eseguire queste attività ed esplorare i dati sottostanti. In alternativa, a seconda del repository utilizzato per memorizzare queste informazioni, è possibile interrogare direttamente i dati o importarli in strumenti quali Microsoft Excel per un'ulteriore analisi e la creazione del report.

> [AZURE.NOTE]È consigliabile limitare l'accesso al dashboard a personale autorizzato; queste informazioni potrebbero essere delicate dal punto di vista commerciale. È inoltre necessario proteggere i dati sottostanti presentati dal dashboard per impedire agli utenti di modificarli.

### Creazione di avvisi
L’invio di avvisi è il processo con cui vengono analizzati i dati di monitoraggio e strumentazione e viene generata una notifica se viene rilevato un evento significativo.

L’invio di avvisi viene utilizzato per garantire che il sistema rimanga integro, reattivo e sicuro. È una parte importante di qualsiasi sistema che offre agli utenti garanzie in termini di prestazioni, disponibilità e privacy e potrebbe essere necessario agire immediatamente sui relativi dati. Potrebbe essere necessario informare un operatore dell’evento che ha innescato l’avviso. L’invio di avvisi può essere utilizzato anche per richiamare funzioni di sistema come ad esempio il ridimensionamento automatico.

L’invio di avvisi dipende generalmente dai seguenti dati di strumentazione:

- Eventi di protezione. Se i registri eventi indicano il verificarsi di errori ripetuti di autenticazione e/o autorizzazione, il sistema potrebbe essere sotto attacco ed è necessario informare un operatore.
- Metriche delle prestazioni. Il sistema deve rispondere rapidamente se una determinata metrica delle prestazioni supera una soglia specificata.
- Informazioni sulla disponibilità. Se viene rilevato un errore, potrebbe essere necessario riavviare rapidamente uno o più sottosistemi o avviare un failover su una risorsa di backup. Errori ripetuti in un sottosistema possono indicare problemi più gravi.

Gli operatori possono ricevere informazioni sugli avvisi utilizzando molti canali di recapito come ad esempio email, cercapersone o SMS. Un avviso può includere anche un'indicazione di criticità della situazione verificatasi. Molti sistemi di invio di avvisi supportano i gruppi di sottoscrizione e tutti gli operatori che fanno parte dello stesso gruppo possono ricevere lo stesso set di avvisi.

Un sistema di invio di avvisi dovrebbe essere personalizzabile e i valori appropriati dei dati di strumentazione sottostante potrebbero essere forniti come parametri. Questo approccio consente a un operatore di filtrare i dati e concentrarsi sulle soglie o le combinazioni di valori di suo interesse. Tenere presente che in alcuni casi, è possibile fornire i dati di strumentazione non elaborati al sistema di invio di avvisi, mentre in altre situazioni potrebbe essere più appropriato fornire i dati aggregati (ad esempio, potrebbe essere generato un avviso se l'utilizzo della CPU per un nodo ha superato il 90% negli ultimi 10 minuti). I dettagli forniti al sistema di invio di avvisi devono includere anche un eventuale riepilogo appropriato e informazioni di contesto; tali dati possono contribuire a ridurre la possibilità di falsi positivi nella creazione di un avviso.

### Creazione di report
Il reporting consente di generare una visualizzazione complessiva del sistema e può incorporare i dati cronologici, come le informazioni attuali. I requisiti per la creazione di report sono suddivisi in due ampie categorie: creazione di report operativi e creazione di report di sicurezza.

Il report sui dati operativi in genere include i seguenti aspetti:

- Aggregazione di statistiche che consentono di comprendere l'utilizzo delle risorse del sistema complessivo o dei sottosistemi specificati durante un intervallo di tempo specificato.
- Identificazione delle tendenze nell'utilizzo delle risorse nell'intero sistema o in sottosistemi specificati durante un periodo di tempo specificato.
- Monitoraggio delle eccezioni che si sono verificate in tutto il sistema o in sottosistemi specificati durante un periodo di tempo specificato.
- Determinare l'efficienza dell'applicazione in termini di risorse distribuite e comprendere se è possibile ridurre il volume delle risorse (e i relativi costi associati) senza influire sulle prestazioni inutilmente.

Il report di protezione si occupa di rilevare ‘utilizzo del sistema da parte del cliente e può includere:

- Operazioni di controllo utente: è necessario registrare le singole richieste eseguite da ogni utente corredate di data e ora. I dati devono essere strutturati per consentire a un amministratore di ricostruire rapidamente la sequenza di operazioni eseguite da un utente specifico in un periodo di tempo specificato.
- Utilizzo delle risorse di rilevamento da parte dell'utente. Ciò richiede la registrazione di come ogni richiesta di un utente acceda alle varie risorse che costituiscono il sistema e per quanto tempo. Un amministratore deve essere in grado di utilizzare questi dati per generare un report di utilizzo da parte dell’utente in un periodo di tempo specificato, eventualmente per la fatturazione.

In molti casi, i report possono essere generati dai processi batch in base a una pianificazione definita (la latenza normalmente non è un problema), ma devono anche essere disponibili per essere generati su una base ad hoc, se necessario. Ad esempio, se si memorizzano i dati in un database relazionale, come Database SQL Azure, è possibile utilizzare uno strumento quale SQL Server Reporting Services per estrarre e formattare i dati e presentarli come una serie di report.

## Modelli correlati e informazioni aggiuntive
- Le indicazioni di ridimensionamento automatico descrivono come ridurre l’overhead di gestione riducendo la necessità per un operatore di monitorare continuamente le prestazioni di un sistema e prendere decisioni sull'aggiunta o rimozione di risorse.
- Il [Modello di monitoraggio integrità Endpoint](https://msdn.microsoft.com/library/dn589789.aspx) descrive come implementare i controlli funzionali all'interno di un'applicazione cui strumenti esterni possono accedere tramite gli endpoint esposti a intervalli regolari.
- Il modello [Coda con priorità](https://msdn.microsoft.com/library/dn589794.aspx) mostra come definire le priorità dei messaggi in coda in modo che le richieste urgenti siano ricevute e possano essere elaborate prima dei messaggi meno urgenti.

## Ulteriori informazioni
- L'articolo [Monitoraggio, diagnosi e risoluzione dei problemi di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md) del sito Web Microsoft.
- L'articolo [Azure: nozioni fondamentali di telemetria e risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) del sito Web Microsoft.
- La pagina [Raccogliere dati di registrazione utilizzando la diagnostica Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) del sito Web Microsoft.
- La pagina [Configurazione di diagnostica per servizi Cloud di Azure e macchine virtuali](https://msdn.microsoft.com/library/azure/dn186185.aspx) del sito Web Microsoft.
- Le pagine [Cache Redis di Azure](http://azure.microsoft.com/services/cache/), [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/), e [HDInsight](http://azure.microsoft.com/services/hdinsight/) del sito Web Microsoft.
- La pagina [Come utilizzare le code del Bus di servizio](http://azure.microsoft.com/) del sito Web Microsoft.
- L'articolo [SQL Server Business Intelligence in macchine virtuali Azure](https://msdn.microsoft.com/library/azure/jj992719.aspx) del sito Web Microsoft.
- La pagina [informazioni sugli avvisi di monitoraggio e notifiche in Azure](https://msdn.microsoft.com/library/azure/dn306639.aspx) del sito Web Microsoft.
- La pagina [Application Insights](app-insights-get-started/) del sito Web Microsoft.

<!---HONumber=July15_HO4-->