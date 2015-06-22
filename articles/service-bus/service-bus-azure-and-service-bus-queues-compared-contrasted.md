<properties 
   pageTitle="Analogie e differenze tra le code di Azure e le code del bus di servizio"
   description="analizza i punti in comune e le differenze tra i due tipi di code offerti da Azure"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sethm" />

# Analogie e differenze tra le code di Azure e le code del bus di servizio

Questo articolo analizza le differenze e le analogie presenti tra i due tipi di code offerte attualmente da Microsoft Azure: code di Azure e code del bus di servizio. Grazie a queste informazioni, è possibile confrontare e contrapporre le rispettive tecnologie ed essere quindi in grado di fare una scelta più oculata riguardo alla soluzione che soddisfa meglio le proprie esigenze.

## Introduzione

Microsoft Azure supporta due tipi di meccanismi di code: **code di Azure** e **code del bus di servizio**.

Le **code di Azure**, che fanno parte dell'infrastruttura di [archiviazione di Azure](http://azure.microsoft.com/services/storage/), offrono un'interfaccia per operazioni di ricezione/inserimento/visualizzazione basata su REST, che fornisce messaggistica affidabile e persistente in e tra i servizi.

Le **code del bus di servizio** fanno parte di un'infrastruttura di [messaggistica di Azure](http://azure.microsoft.com/services/messaging/) più ampia che supporta l'accodamento, oltre a modelli di pubblicazione, sottoscrizione, comunicazione remota del servizio Web e integrazione. Per altre informazioni su code, argomenti, sottoscrizioni e inoltri del bus di servizio, vedere [Panoramica dei modelli di messaggistica del bus di servizio](https://msdn.microsoft.com/library/hh410103.aspx).

Anche se entrambe le tecnologie di accodamento sono disponibili contemporaneamente, le code di Azure sono state introdotte prima, come meccanismo dedicato di archiviazione delle code creato nei servizi di archiviazione di Azure. Le code del bus di servizio vengono create nell'infrastruttura di "messaggistica negoziata" più ampia progettata per integrare applicazioni e componenti delle applicazioni in grado di estendere più protocolli di comunicazione, contratti dati, domini trusted e/o ambienti di rete.

Questo articolo confronta le due tecnologie di accodamento offerte da Azure, illustrando le differenze di comportamento e implementazione delle funzionalità fornite da ognuna. L'articolo offre anche informazioni aggiuntive per la scelta delle funzionalità che possono soddisfare meglio le proprie esigenze di sviluppo di applicazioni.

## Considerazioni sulla selezione della tecnologia

Sia le code di Azure sia quelle del bus di servizio sono implementazioni del servizio di accodamento messaggi attualmente disponibile in Microsoft Azure. Ogni tecnologia presenta un set di funzionalità leggermente diverso, pertanto è possibile scegliere l'una o l'altra, o entrambe, a seconda delle esigenze della particolare soluzione in uso o di un problema aziendale o tecnico da risolvere.

Quando si stabilisce la tecnologia di accodamento che soddisfa lo scopo di una determinata soluzione, è consigliabile che gli architetti e gli sviluppatori di soluzioni tengano in considerazione le indicazioni seguenti. Per altri dettagli, vedere la sezione successiva.

In qualità di architetto o sviluppatore di soluzioni, **considerare l'uso delle code di Azure** quando:

- L'applicazione deve archiviare in una coda oltre 80 GB di messaggi la cui durata è inferiore a 7 giorni.

- L'applicazione deve tenere traccia dello stato dell'elaborazione di un messaggio all'interno della coda. Ciò si rivela utile se si verifica un arresto anomalo del processo di lavoro tramite cui si elabora il messaggio. Queste informazioni possono quindi essere usate in un processo di lavoro successivo per continuare dal punto in cui è stato interrotto il processo di lavoro precedente.

- Sono necessari i log sul lato server di tutte le transazioni eseguite nelle code.

In qualità di architetto o sviluppatore di soluzioni, **considerare l'uso delle code del bus di servizio** quando:

- La soluzione deve essere in grado di ricevere messaggi senza dover eseguire il polling della coda. Con il bus di servizio, è possibile ottenere questo risultato mediante l'operazione di ricezione con polling prolungato che usa i protocolli basati su TCP supportati dal bus di servizio.

- La soluzione deve garantire il recapito ordinato FIFO (First In First Out) da parte della coda.

- Si vuole una configurazione simmetrica in Azure e su Windows Server (cloud privato). Per altre informazioni, vedere [Bus di servizio per Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- La soluzione deve essere in grado di eseguire il rilevamento duplicato automatico.

- Si vuole che l'applicazione elabori i messaggi come flussi paralleli a esecuzione prolungata (i messaggi sono associati a un flusso mediante la proprietà [SessionId](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)). In questo modello ogni nodo dell'applicazione che utilizza il servizio entra in competizione con gli altri nodi per l'acquisizione dei flussi anziché dei messaggi. Quando un flusso viene assegnato a un nodo basato sul servizio, tale nodo può esaminare lo stato del flusso dell'applicazione mediante transazioni.

- Per la soluzione sono necessari atomicità e comportamento transazionale in caso di invio o ricezione di più messaggi da una coda.

- La caratteristica relativa alla durata (TTL) del carico di lavoro specifico dell'applicazione può superare il periodo di 7 giorni.

- L'applicazione gestisce messaggi che possono superare i 64 KB ma che probabilmente non si avvicineranno al limite dei 256 KB

- È necessario fornire un modello di accesso basato sui ruoli alle code e autorizzazioni/diritti diversi per mittenti e destinatari.

- Le dimensioni della coda non supereranno gli 80 GB.

- Si vuole usare il broker di messaggistica basato su standard AMQP 1.0. Per altre informazioni su AMQP, vedere [Panoramica del protocollo AMQP per il bus di servizio](https://msdn.microsoft.com/library/jj841072.aspx).

- È possibile prevedere un'eventuale migrazione dalla comunicazione punto a punto basata sulla coda a un modello di scambio dei messaggi mediante il quale viene garantita un'integrazione continua di destinatari aggiuntivi (sottoscrittori), mediante ognuno dei quali vengono ricevute copie separate di alcuni o di tutti i messaggi inviati alla coda. Quest'ultima viene definita funzionalità di pubblicazione/sottoscrizione a livello nativo fornita dal bus di servizio.

- La soluzione di messaggistica deve garantire il recapito "At-Most-Once" senza che sia necessario sviluppare i componenti aggiuntivi dell'infrastruttura.

- Si desidera essere in grado di pubblicare e usare batch di messaggi.

- È necessaria l'integrazione completa con lo stack di comunicazione Windows Communication Foundation (WCF) in .NET Framework.

## Confronto tra le code di Azure e le code del bus di servizio

Le tabelle nelle sezioni seguenti forniscono un raggruppamento logico delle funzionalità relative alle code e offrono la possibilità di un confronto immediato delle funzionalità disponibili sia nelle code di Azure sia in quelle del bus di servizio.

## Funzionalità fondamentali

Questa sezione confronta alcune delle funzionalità di accodamento fondamentali fornite dalle code di Azure e da quelle del bus di servizio.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Garanzia di ordinamento|**No** <br/><br>Per altre informazioni, vedere la prima nota nella sezione "Informazioni aggiuntive".</br>|**Sì - First In First Out (FIFO)**<br/><br>(tramite l'uso di sessioni di messaggistica)|
|Garanzia di recapito|**At-Least-Once**|**At-Least-Once**<br/><br/>**At-Most-Once**|
|Supporto delle transazioni|**No**|**Sì**<br/><br/>(tramite l'uso di transazioni locali)|
|Comportamento di ricezione|**Senza blocco**<br/><br/>(completamento immediato in caso di assenza di nuovi messaggi)|**Blocco con/senza timeout**<br/><br/>(disponibilità di polling prolungato o ["tecnica Comet"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Senza blocco**<br/><br/>(solo tramite l'uso dell'API gestita .NET)|
|API di tipo push|**No**|**Sì**<br/><br/>API .NET di [OnMessage](https://msdn.microsoft.com/library/jj908682.aspx) e [sessioni OnMessage](https://msdn.microsoft.com/library/dn790528.aspx).|
|Modalità di ricezione|**Visualizzazione e lease**|**Visualizzazione e blocco**<br/><br/>**Ricezione ed eliminazione**|
|Modalità di accesso esclusivo|**Basato sul lease**|**Basato sul blocco**|
|Durata lease/blocco|**30 secondi (impostazione predefinita)**<br/><br/>**7 giorni (durata massima)** (È possibile rinnovare o rilasciare un lease di un messaggio usando l'API [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx)).|**60 secondi (impostazione predefinita)**<br/><br/>È possibile rinnovare un blocco su un messaggio usando l'API [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).|
|Precisione lease/blocco|**A livello di messaggio**<br/><br/>(ogni messaggio può avere un valore di timeout diverso. Durante l'elaborazione del messaggio, è possibile aggiornare tale valore nel modo necessario usando l'API [UpdateMessage](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx)).|**A livello di coda**<br/><br/>(ogni coda ha una precisione di blocco applicata a tutti i relativi messaggi, tuttavia è possibile rinnovare il blocco usando l'API [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx)).|
|Ricezione in batch|**Sì**<br/><br/>(specifica esplicita del numero di messaggi durante il recupero di questi ultimi, fino a un massimo di 32 messaggi).|**Sì**<br/><br/>(abilitazione implicita di una proprietà di prelettura o esplicita tramite l'uso di transazioni).|
|Invio in batch|**No**|**Sì**<br/><br/>(tramite l'uso di transazioni o invio in batch sul lato client).|

### Informazioni aggiuntive

- In genere i messaggi nelle code di Azure sono ordinati in base al principio FIFO (First-In-First-Out), ma in alcuni casi possono avere un ordine diverso. Ad esempio, quando la durata del timeout di visibilità di un messaggio scade come risultato dell'arresto di un'applicazione client durante l'elaborazione. Quando il timeout di visibilità scade, il messaggio risulta nuovamente visibile nella coda in modo che un altro utente possa rimuoverlo. A questo punto, il messaggio reso nuovamente visibile può essere posizionato nella coda (per poter essere rimosso di nuovo) dopo un messaggio originariamente accodato dopo quest'ultimo.

- Se all'uso di BLOB e tabelle di Archiviazione di Microsoft Azure si affianca quello delle code, viene garantita una disponibilità pari al 99,9%. Se si usano tabelle o BLOB con code del bus di servizio, la disponibilità risulta ridotta.

- Il modello FIFO garantito nelle code del bus di servizio richiede l'uso di sessioni di messaggistica. In caso di arresto anomalo dell'applicazione durante l'elaborazione di un messaggio ricevuto nella modalità **Visualizzazione e blocco**, alla prossima accettazione di una sessione di messaggistica da parte di un destinatario di code, l'avvio verrà eseguito con il messaggio non recapitato dopo la scadenza della relativa durata (TTL).

- Le code di Azure sono progettate per supportare scenari di accodamento standard, ad esempio componenti dell'applicazione per il disaccoppiamento per aumentare la scalabilità e la tolleranza di errore, il livellamento del carico e i flussi di lavoro per la definizione dei processi.

- Le code del bus di servizio supportano la garanzia di recapito *At-Least-Once*. Inoltre, la semantica *At-Most-Once*  può essere supportata tramite lo stato della sessione per archiviare lo stato dell'applicazione e tramite le transazioni per ricevere i messaggi in modo atomico e aggiornare lo stato della sessione. Questa tecnica viene usata dal servizio Flusso di lavoro di Azure per garantire il recapito con semantica At-Most-Once.

- Le code di Azure offrono un modello di programmazione uniforme e coerente tra code, tabelle e BLOB, che può essere usato sia dagli sviluppatori sia dai team addetti alle operazioni.

- Le code del bus di servizio offrono supporto per le transazioni locali nel contesto di una singola coda.

- La modalità *Ricezione ed eliminazione* supportata dal bus di servizio offre la possibilità di ridurre il numero di operazioni di messaggistica (e relativo costo) in cambio di una garanzia di recapito più bassa.

- Le code di Azure forniscono lease con possibilità di estensione per i messaggi. In questo modo i processi di lavoro possono gestire lease brevi nei messaggi. Pertanto, se un processo di lavoro viene arrestato in modo anomalo, il messaggio può essere elaborato di nuovo rapidamente da un altro processo di lavoro. Inoltre, tramite un processo di lavoro è possibile estendere il lease in un messaggio se l'elaborazione di quest'ultimo richiede più tempo del lease corrente.

- Le code di Azure offrono un timeout di visibilità che può essere impostato durante l'inserimento o la rimozione dalla coda di un messaggio. È inoltre possibile aggiornare un messaggio con valori di lease diversi in fase di esecuzione, nonché aggiornare valori diversi nei messaggi presenti nella stessa coda. I timeout dei blocchi del bus di servizio vengono definiti nei metadati della coda. È tuttavia possibile rinnovare il blocco chiamando il metodo [RenewLock](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).

- Il timeout massimo per un'operazione di ricezione del blocco nelle code del bus di servizio è di 24 giorni. I timeout basati su REST, tuttavia, hanno un valore massimo di 55 secondi.

- Il batch sul lato client fornito dal bus di servizio consente a un client di coda di inviare in batch più messaggi in una singola operazione di invio. L'invio in batch è disponibile solo per le operazioni di invio asincrone.

- Caratteristiche quali la capacità massima di 200 TB (capacità ancora superiore se gli account vengono virtualizzati) e la disponibilità in numero illimitato rendono le code di Azure una piattaforma ideale per i provider SaaS.

- Le code di Azure forniscono un meccanismo di controllo di accesso delegato flessibile ed efficiente.

## Funzionalità avanzate

Questa sezione confronta le funzionalità avanzate fornite dalle code di Azure e da quelle del bus di servizio.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Recapito pianificato|**Sì**|**Sì**|
|Mancato recapito automatico dei messaggi|**No**|**Sì**|
|Valore TTL di accodamento aumentato|**Sì**<br/><br/>(tramite aggiornamento sul posto di timeout di visibilità)|**Sì**<br/><br/>(fornito tramite una funzione API dedicata)|
|Supporto messaggi non elaborabili|**Sì**|**Sì**|
|Aggiornamento sul posto|**Sì**|**Sì**|
|Log delle transazioni sul lato server|**Sì**|**No**|
|Metriche di archiviazione|**Sì**<br/><br/>**Metrica al minuto**: fornisce metriche in tempo reale relative a disponibilità, TPS, numero di chiamate API, numero di errori e molto altro (aggregate per minuto e segnalate entro pochi minuti rispetto agli eventi in fase di produzione). Per altre informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](https://msdn.microsoft.com/library/hh343258.aspx).|**Sì**<br/><br/>(query in blocco chiamando [GetQueues](https://msdn.microsoft.com/library/hh293128.aspx))|
|Gestione dello stato|**No**|**Sì**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.entitystatus.aspx)|
|Inoltro automatico dei messaggi|**No**|**Sì**|
|Funzione di eliminazione della coda|**Sì**|**No**|
|Gruppi di messaggi|**No**|**Sì**<br/><br/>(tramite l'uso di sessioni di messaggistica)|
|Stato dell'applicazione per gruppo di messaggi|**No**|**Sì**|
|Rilevamento duplicati|**No**|**Sì**<br/><br/>(configurabile sul lato del mittente)|
|Integrazione WCF|**No**|**Sì**<br/><br/>(disponibilità di associazioni WCF predefinite)|
|Integrazione WF|**Impostazione personalizzata**<br/><br/>(compilazione di un'attività WF personalizzata necessaria)|**Impostazione nativa**<br/><br/>(disponibilità di attività WF predefinite)|
|Esplorazione di gruppi di messaggi|**No**|**Sì**|
|Recupero delle sessioni di messaggistica per ID|**No**|**Sì**|

### Informazioni aggiuntive

- Entrambe le tecnologie di accodamento consentono di pianificare il recapito di un messaggio in un momento successivo.

- La funzionalità di inoltro automatico consente a migliaia di code di inoltrare automaticamente i propri messaggi a una singola coda dalla quale l'applicazione ricevente preleva il messaggio. Questo meccanismo consente di ottenere un valore elevato di sicurezza, di controllare il flusso e di isolare le aree di archiviazione tra i server di pubblicazione dei messaggi.

- Le code di Azure offrono supporto per l'aggiornamento del contenuto del messaggio. Questa funzionalità consente di rendere permanenti le informazioni sullo stato e gli aggiornamenti sull'avanzamento incrementale all'interno del messaggio, in modo che sia possibile elaborare quest'ultimo dall'ultimo checkpoint noto anziché dall'inizio. Con le code del bus di servizio è possibile abilitare lo stesso scenario mediante sessioni di messaggistica Le sessioni consentono di salvare e recuperare lo stato di elaborazione dell'applicazione usando [SetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) e [GetState](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx).

- Il mancato recapito dei messaggi, funzionalità supportata solo dalle code del bus di servizio, può essere utile per isolare i messaggi che non possono essere elaborati correttamente dall'applicazione ricevente o quando i messaggi non possono essere recapitati a causa di una proprietà di durata (TTL) scaduta. Tramite il valore TTL viene specificata la durata del messaggio nella coda. Con il bus di servizio, il messaggio verrà spostato in una coda speciale denominata $DeadLetterQueue allo scadere del periodo TTL.

- Per trovare messaggi "non elaborabili" nelle code di Azure, durante la rimozione di un messaggio dalla coda, tramite l'applicazione viene esaminata la proprietà DequeueCount del messaggio. Se il valore della proprietà DequeueCount supera il valore soglia specificato, il messaggio viene spostato dall'applicazione in una coda di "messaggio non recapitabile" definita dall'applicazione.

- Le code di Azure consentono di ottenere un log dettagliato di tutte le transazioni eseguite nella coda, nonché le metriche di aggregazione. Queste opzioni sono entrambe utili per eseguire il debug e comprendere la modalità di utilizzo delle code di Azure da parte dell'applicazione. Inoltre, sono utili per l'ottimizzazione delle prestazioni dell'applicazione e per la riduzione dei costi di utilizzo delle code.

- Il concetto di "sessione di messaggistica" supportato dal bus di servizio consente di associare a uno specifico destinatario i messaggi appartenenti a un determinato gruppo logico. Il destinatario crea a sua volta un'affinità di tipo sessione tra i messaggi e i relativi destinatari. È possibile abilitare questa funzionalità avanzata nel bus di servizio impostando la proprietà [SessionID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) in un messaggio. I destinatari possono quindi restare in ascolto su un ID di sessione specifico e ricevere messaggi in cui viene condiviso l'identificatore di sessione specificato.

- La funzionalità di rilevamento di duplicazione supportata dalle code del bus di servizio rimuove automaticamente i messaggi duplicati inviati a una coda o a un argomento, in base al valore della proprietà [MessageID](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx).

## Capacità e quote

Questa sezione confronta le code di Azure e quelle del bus di servizio relativamente alla capacità e alle quote.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Dimensioni massime della coda|**200 TB**<br/><br/>(limitate alla capacità di un singolo account di archiviazione)|**Da 1 GB a 80 GB**<br/><br/>(valori definiti al momento della creazione della coda e dell'[abilitazione del partizionamento](https://msdn.microsoft.com/library/dn520246.aspx). Vedere la sezione "Informazioni aggiuntive").|
|Dimensioni massime del messaggio|**64 KB**<br/><br/>(48 KB quando si usa la codifica **Base64**)<br/><br/>Grazie alla combinazione di code e BLOB, Azure supporta messaggi di grandi dimensioni consentendo di accodare fino a 200 GB per un singolo elemento.|**256 KB**<br/><br/>(inclusi sia l'intestazione sia il corpo, dimensioni massime dell'intestazione: 64 KB)|
|Durata TTL massima del messaggio|**7 giorni**|**Illimitato**|
|Numero massimo di code|**Illimitato**|**10.000**<br/><br/>(per spazio dei nomi del servizio, può essere aumentato)|
|Numero massimo di client concorrenti|**Illimitato**|**Illimitato**<br/><br/>(limite di 100 connessioni simultanee applicato solo alla comunicazione basata su protocollo TCP)|

### Informazioni aggiuntive

- Bus di servizio impone l'applicazione dei limiti di dimensione della coda. Le dimensioni massime della coda vengono specificate al momento della creazione della coda stessa e possono avere un valore compreso tra 1 e 80 GB. Se viene raggiunto il valore delle dimensioni della coda impostato al momento della creazione, i successivi messaggi in arrivo verranno rifiutati e il codice chiamante riceverà un'eccezione. Per altre informazioni sulle quote nel bus di servizio, vedere [Quote del bus di servizio](https://msdn.microsoft.com/library/ee732538.aspx).

- È possibile creare code del bus di servizio in dimensioni di 1, 2, 3, 4 o 5 GB (il valore predefinito è 1 GB). Con il partizionamento abilitato (ovvero l'impostazione predefinita), il bus di servizio crea 16 partizioni per ciascun GB specificato. Di conseguenza, se si crea una coda di 5 GB di dimensioni, con 16 partizioni la dimensione massima della coda diventa (5 * 16) = 80 GB. È possibile visualizzare la dimensione massima della coda o dell'argomento partizionato esaminando la relativa voce nel portale di gestione di Azure.

- Con le code di Azure, se il contenuto del messaggio non è XML-safe, deve avere la codifica **Base64**. Se per il messaggio non è stata usata la codifica **Base64**, il payload dell'utente può essere fino a 48 KB, anziché 64.

- Con le code del bus di servizio, ogni messaggio archiviato in una coda è costituito da due parti: un'intestazione e un corpo. Le dimensioni totali del messaggio non possono superare i 256 KB.

- Se le comunicazioni tra client e code del bus di servizio vengono stabilite tramite il protocollo TCP, il numero massimo di connessioni simultanee a una singola coda del bus di servizio è limitato a 100. Questo numero è condiviso tra mittenti e destinatari. Se viene raggiunta questa quota, le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Questo limite non viene imposto ai client tramite cui viene effettuata la connessione alle code mediante l'API basata su REST.

- Se si richiedono più di 10.000 code in un unico spazio dei nomi del bus di servizio, è possibile contattare il team di supporto tecnico di Azure e richiedere un aumento. Per ridimensionare più di 10.000 code con il bus di servizio, è anche possibile creare spazi dei nomi aggiuntivi tramite il portale di gestione di Azure.

## Gestione e operazioni

Questa sezione confronta le funzionalità di gestione fornite dalle code di Azure e da quelle del bus di servizio.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Protocollo di gestione|**REST su HTTP/HTTPS**|**REST su HTTPS**|
|Protocollo runtime|**REST su HTTP/HTTPS**|**REST su HTTPS**<br/><br/>**Standard AMQP 1.0 (TCP con TLS)**|
|API gestita .NET|**Sì**<br/><br/>(API del client di archiviazione gestita .NET)|**Sì**<br/><br/>(API di messaggistica negoziata gestita .NET)|
|C++ nativo|**Sì**|**No**|
|API Java|**Sì**|**Sì**|
|API PHP|**Sì**|**Sì**|
|API Node.js|**Sì**|**Sì**|
|Supporto metadati arbitrario|**Sì**|**No**|
|Regole di denominazione delle code|**Lunghezza massima pari a 63 caratteri**<br/><br/>(le lettere del nome di una coda devono essere minuscole)|**Lunghezza massima pari a 260 caratteri**<br/><br/>(nomi di coda tra maiuscole e minuscole)|
|Funzione di recupero della lunghezza della coda|**Sì**<br/><br/>(valore indicativo se i messaggi scadono oltre il TTL senza essere eliminati)|**Sì**<br/><br/>(esatto, valore temporizzato)|
|Funzione di visualizzazione|**Sì**|**Sì**|

### Informazioni aggiuntive

- Le code di Azure forniscono supporto per gli attributi arbitrari applicabili alla descrizione della coda, sotto forma di coppie nome/valore.

- Entrambe le tecnologie di coda consentono di visualizzare un messaggio senza doverlo bloccare. Questa funzionalità può essere utile quando si implementa uno strumento di esplorazione delle code.

- Le API gestite .NET di messaggistica negoziata del bus di servizio usano le connessioni TCP full-duplex per ottenere migliori livelli di prestazioni rispetto a REST su HTTP e supportano il protocollo standard AMQP 1.0.

- I nomi delle code di Azure possono avere una lunghezza compresa tra 3 e 63 caratteri e contenere lettere minuscole, numeri e trattini. Per altre informazioni, vedere [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx).

- I nomi delle code del bus di servizio possono avere una lunghezza massima di 260 caratteri e presentano regole di denominazione meno restrittive.  I nomi delle code del bus di servizio possono contenere lettere, numeri, punti (.), trattini (-) e caratteri di sottolineatura (_).

## Prestazioni

Questa sezione confronta le code di Azure e quelle del bus di servizio relativamente alle prestazioni.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Velocità effettiva massima|**Fino a 2000 messaggi al secondo**<br/><br/>(in base al benchmark con messaggi di 1 KB)|**Fino a 2000 messaggi al secondo**<br/><br/>(in base al benchmark con messaggi di 1 KB)|
|Latenza media|**10 ms**<br/><br/>(con l'algoritmo di [Nagle di TCP](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) disabilitato)|**20-25 ms**|
|Comportamento di limitazione|**Rifiuto con codice HTTP 503**<br/><br/>(le richieste limitate non vengono considerate fatturabili)|**Rifiuto con codice eccezione/HTTP 503**<br/><br/>(le richieste limitate non vengono considerate fatturabili)|

### Informazioni aggiuntive

- Una singola coda di Azure può elaborare fino a 2000 transazioni al secondo. Una transazione è un'operazione **Put**, **Get** o **Delete**. L'invio di un singolo messaggio a una coda (**Put**) viene contato come una transazione, ma la ricezione di un messaggio è spesso un processo a due passaggi che comporta il recupero (**Get**), seguito da una richiesta di rimozione del messaggio dalla coda (**Delete**). Di conseguenza, un'operazione di rimozione dalla coda completata comporta in genere due transazioni. L'impatto di questo inconveniente può essere ridotto mediante il recupero in batch di più messaggi. È infatti possibile eseguire un'operazione di recupero **Get** per un numero massimo di 32 messaggi, seguita da un'operazione di eliminazione **Delete** per ciascuno di essi. Per ottenere una migliore velocità effettiva è possibile creare più code (un account di archiviazione può avere un numero illimitato di code).

- Quando l'applicazione raggiunge la velocità effettiva massima per una coda di Azure, il servizio di accodamento restituisce in genere un errore HTTP 503 in cui si indica che il server è occupato. In questo caso, tramite l'applicazione deve essere attivata la logica di ripetizione dei tentativi con ritardo esponenziale backoff.

- Quando si gestiscono messaggi di piccole dimensioni (minori di 10 KB) da un servizio ospitato ubicato nello stesso percorso (area) dell'account di archiviazione, la latenza media delle code di Azure è 10 millisecondi.

- Sia tramite le code di Azure sia tramite quelle del bus di servizio viene applicato il comportamento di limitazione rifiutando richieste a una coda limitata. Tuttavia, le richieste limitate non vengono considerate come fatturabili da entrambi i tipi di code.

- I benchmark relativi alle code del bus di servizio hanno dimostrato che una singola coda può raggiungere una velocità effettiva massima di 2000 messaggi al secondo, per messaggi di dimensioni pari a circa 1 KB. Per ottenere una velocità effettiva più elevata, usare più code. Per altre informazioni sull'ottimizzazione delle prestazioni del bus di servizio, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio](https://msdn.microsoft.com/library/hh528527.aspx).

- Quando viene raggiunta la velocità effettiva massima per una coda del bus di servizio, viene restituita al client della coda una risposta [ServerBusyException](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) (se si usa l'API di messaggistica negoziata .NET) o HTTP 503 (se si usa l'API basata su REST) a indicare che la coda è limitata.

## Autenticazione e autorizzazione

Questa sezione illustra le funzionalità di autenticazione e autorizzazione supportate dalle code di Azure e da quelle del bus di servizio.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Autenticazione|**Chiave simmetrica**|**Chiave simmetrica**|
|Modello di controllo di accesso|Accesso delegato tramite token di firma di accesso condiviso.|RBAC tramite ACS|
|Federazione del provider di identità|**No**|**Sì**|

### Informazioni aggiuntive

- Ogni richiesta a entrambe le tecnologie di accodamento deve essere autenticata. Le code pubbliche con accesso anonimo non sono supportate. L'uso della firma di accesso condiviso consente di ovviare a questo inconveniente mediante la pubblicazione di una firma di accesso condiviso di sola scrittura, di sola lettura o anche di accesso completo.

- Lo schema di autenticazione fornito dalle code di Azure prevede l'uso di una chiave simmetrica, ovvero un codice HMAC (Hash Message Authentication Code) calcolato dall'algoritmo SHA-256 e codificato come stringa **Base64**. Per altre informazioni sul relativo protocollo, vedere [Autenticazione dell'accesso all'account di archiviazione](https://msdn.microsoft.com/library/hh225339.aspx). Le code del bus di servizio supportano un modello simile mediante l'uso di chiavi simmetriche. Per altre informazioni, vedere [Autenticazione della firma di accesso condiviso con il bus di servizio](https://msdn.microsoft.com/library/dn170477.aspx).

- Microsoft Azure AD Access Control (noto anche come Servizio di controllo di accesso o ACS) supportato dal bus di servizio offre tre ruoli distinti, **amministratore**, **mittente** e **destinatario**, e non è attualmente supportato dalle code di Azure.

- Poiché il bus di servizio offre l'integrazione di ACS, è possibile attuare la federazione con Active Directory (tramite l'uso di ADFS) e altri comuni provider di identità Web.

## Costi

Questa sezione confronta le code di Azure e quelle del bus di servizio relativamente ai costi.

|Criteri di confronto|Code di Azure|Code del bus di servizio|
|---|---|---|
|Costo della transazione della coda|**$ 0,0005**<br/><br/>(per 10.000 transazioni)|**Livello Basic**: **$ 0,05**<br/><br/>(per milioni di operazioni)|
|Operazioni fatturabili|**Tutte**|**Solo invio/ricezione**<br/><br/>(nessun addebito per altre operazioni)|
|Transazioni inattive|**Fatturabile**<br/><br/>(l'esecuzione di query su una coda vuota viene contata come transazione fatturabile)|**Fatturabile**<br/><br/>(una ricezione in una coda vuota è considerata un messaggio fatturabile)|
|Costo di archiviazione|**$ 0,07**<br/><br/>(per GB/mese)|**$ 0,00**|
|Costi di trasferimento dei dati in uscita|**$ 0,12 - $ 0,19**<br/><br/>(a seconda dell'area geografica)|**$ 0,12 - $ 0,19**<br/><br/>(a seconda dell'area geografica)|

### Informazioni aggiuntive

- I trasferimenti dei dati vengono addebitati in base alla quantità totale di dati che vengono trasferiti dai data center di Azure tramite Internet in un determinato periodo di fatturazione.

- I trasferimenti dei dati tra servizi di Azure ubicati all'interno della stessa area non sono soggetti ad addebito.

- Al momento della stesura di questo documento, tutti i trasferimenti dei dati in ingresso non sono soggetti ad addebito.

- Quando si effettuano operazioni di messaggistica su code del bus di servizio, il costo delle transazioni ACS è trascurabile. Il bus di servizio acquisisce un token ACS per una singola istanza dell'oggetto factory di messaggistica. Il token viene quindi riutilizzato fino alla scadenza, dopo circa 20 minuti. Pertanto, il volume di operazioni di messaggistica nel bus di servizio non è direttamente proporzionale alla quantità di transazioni ACS necessarie per supportare queste operazioni.

- In base al supporto per polling prolungato, l'uso di code del bus di servizio può essere economicamente conveniente in situazioni in cui è richiesto il recapito a bassa latenza.

>[AZURE.NOTE]Tutti i costi sono soggetti a modifiche. Questa tabella presenta il prezzo corrente al momento della stesura di questo articolo e non include i costi di eventuali offerte promozionali che possono essere attualmente disponibili. Per informazioni aggiornate sui prezzi di Azure, vedere la pagina relativa ai [prezzi di Azure](http://azure.microsoft.com/pricing/). Per altre informazioni sui prezzi del bus di servizio, vedere la pagina relativa ai [prezzi del bus di servizio]((http://azure.microsoft.com/pricing/details/service-bus/).

## Conclusioni

Acquisendo una comprensione più profonda delle due tecnologie, si sarà in grado di prendere una decisione più oculata circa la tecnologia di accodamento da usare e il momento in cui adottarla. La scelta di usare le code di Azure o quelle del bus di servizio si basa chiaramente su diversi fattori, che possono dipendere soprattutto dalle singole esigenze dell'applicazione in uso e dalla relativa architettura. Se l'applicazione usa già le funzionalità principali di Microsoft Azure, è possibile scegliere le code di Azure, specialmente se sono richieste la comunicazione e la messaggistica di base tra servizi o se sono necessarie code che possono avere dimensioni maggiori di 80 GB.

Poiché offrono numerose funzionalità avanzate quali sessioni, transazioni rilevamento duplicati, mancato recapito automatico dei messaggi e funzionalità di pubblicazione e sottoscrizione permanente, le code del bus di servizio possono rappresentare la soluzione migliore quando si sviluppa un'applicazione ibrida o se l'applicazione in uso richiede comunque tali funzionalità.

## Passaggi successivi

Gli articoli seguenti offrono indicazioni e informazioni sull'uso delle code di Azure e di quelle del bus di servizio.

- [Come usare le code del bus di servizio](service-bus-dotnet-how-to-use-queues.md)
- [Come usare l'archiviazione di accodamento](../storage-dotnet-how-to-use-queues.md)
- [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio](https://msdn.microsoft.com/library/hh528527.aspx)
- [Introduzione alle code e agli argomenti del bus di servizio di Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Guida per gli sviluppatori del bus di servizio](http://www.cloudcasts.net/devguide/)
- ["Analisi dettagliata delle tabelle e delle code di Azure"](http://www.microsoftpdc.com/2009/SVC09)
- [Architettura di Archiviazione di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Uso del servizio di accodamento in Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Informazioni sulla fatturazione di Archiviazione di Azure – Larghezza di banda, transazioni e capacità](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

<!---HONumber=58--> 