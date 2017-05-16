---
title: Guida al protocollo AMQP 1.0 in Hub eventi e nel bus di servizio di Azure | Microsoft Docs
description: Guida al protocollo per le espressioni e descrizione di AMQP 1.0 nel bus di servizio e in Hub eventi di Azure
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: 
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2017
ms.author: clemensv;jotaub;hillaryc;sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9197e429f25f5431d7ea36170c3339cd4d4bc32d
ms.lasthandoff: 04/12/2017


---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Guida al protocollo AMQP 1.0 nel bus di servizio e in Hub eventi di Azure

AMQP (Advanced Message Queueing Protocol) 1.0 è un protocollo di frame e di trasferimento che consente di trasferire messaggi tra due parti in modo asincrono, sicuro e affidabile. È il protocollo principale della messaggistica del bus di servizio e di Hub eventi di Azure. Entrambi i servizi supportano anche HTTPS. Il protocollo SBMP proprietario supportato verrà gradualmente sostituito da AMQP.

AMQP 1.0 è il risultato di un'ampia collaborazione a livello di settore, tra fornitori di middleware, ad esempio Microsoft e Red Hat, e molti utenti di middleware di messaggistica, ad esempio JP Morgan Chase, che rappresenta il settore di servizi finanziari. Il forum relativo alla standardizzazione tecnica per il protocollo AMQP e le specifiche relative alle estensioni è OASIS e ha ottenuto l'approvazione formale come standard internazionale in base alle norme ISO/IEC 19494.

## <a name="goals"></a>Obiettivi

Questo articolo riepiloga brevemente i concetti di base delle specifiche di messaggistica AMQP 1.0, oltre a un set ridotto di specifiche di estensioni provvisorie attualmente in fase di finalizzazione nel comitato tecnico di OASIS per AMQP, e spiega in che modo il bus di servizio di Azure implementa queste specifiche e si basa su di esse.

L'obiettivo consiste nel permettere a tutti gli sviluppatori che usano uno stack client AMQP 1.0 esistente su qualsiasi piattaforma di interagire con il bus di servizio di Azure tramite AMQP 1.0.

Gli stack generici AMQP 1.0 comuni, ad esempio Apache Proton o AMQP.NET Lite, implementano già tutti i gesti principali di AMQP 1.0. Questi gesti di base sono a volte inclusi in un'API di livello superiore. Apache Proton offre anche due API, ovvero l'essenziale Messenger API e l'API reattiva Reactor API.

Nella discussione seguente si presuppone che la gestione di connessioni, sessioni e collegamenti di AMQP e la gestione di trasferimenti di frame e del controllo di flusso siano a carico del rispettivo stack, ad esempio Apache Proton-C, e non richiedano attenzione specifica da parte degli sviluppatori di applicazioni. Si presuppone in modo astratto l'esistenza di alcune interfacce API primitive , ad esempio la possibilità di connettersi e di creare qualche genere di oggetti di astrazione *sender* e *receiver*, che includono rispettivamente un tipo di operazione `send()` e `receive()`.

Durante l'analisi delle funzionalità avanzate del bus di servizio di Azure, ad esempio l'esplorazione dei messaggi o la gestione delle sessioni, questi aspetti vengono illustrati dal punto di vista di AMQP, ma anche dal punto di vista della pseudo-implementazione a più livelli sopra questa presupposta astrazione di API.

## <a name="what-is-amqp"></a>Informazioni su AMQP

AMQP è un protocollo di frame e di trasferimento. Un protocollo di frame fornisce una struttura per i flussi di dati in ogni direzione di una connessione di rete. La struttura fornisce una descrizione di blocchi distinti di dati, detti *frame*, da scambiare tra le parti connesse. Le funzionalità di trasferimento assicurano che entrambe le parti possano raggiungere un accordo condiviso in merito al momento in cui i frame devono essere trasferiti e al momento in cui i trasferimenti devono essere considerati completi.

A differenza delle versioni provvisorie scadute precedenti prodotte dal gruppo di lavoro di AMQP e ancora in uso da parte di alcuni broker messaggi, il protocollo AMQP 1.0 finale e standardizzato del gruppo di lavoro non richiede la presenza di un broker messaggi o di qualsiasi topologia specifica per entità all'interno di un broker messaggi.

Il protocollo può essere usato per la comunicazione peer-to-peer simmetrica con broker messaggi che supportano le query e pubblicano/sottoscrivono entità, analogamente al bus di servizio di Azure. Può essere usato anche per l'interazione con l'infrastruttura di messaggistica, in cui i modelli di interazione sono diversi rispetto alle code regolari, come nel caso di Hub eventi di Azure. Un hub eventi funziona in modo analogo a una coda in caso di invio di eventi all'hub, ma funziona in modo più simile a un servizio di archiviazione seriale quando gli eventi vengono letti dall'hub. È in qualche modo simile a un'unità a nastro. Il client sceglie un offset nel flusso di dati disponibile e riceve quindi tutti gli eventi da tale offset, fino all'evento più recente disponibile.

Il protocollo AMQP 1.0 è progettato in modo da essere estensibile e da supportare specifiche aggiuntive che ne migliorano le funzionalità. Questo aspetto è illustrato dalle tre specifiche di estensioni incluse in questo argomento. Per comunicazioni sull'infrastruttura HTTPS/WebSocket esistente, in cui la configurazione di porte TCP AMQP native potrebbe risultare difficile, una specifica di associazione definisce il modo in cui creare livelli di AMQP su WebSocket. Per l'interazione con l'infrastruttura di messaggistica in una modalità di richiesta/risposta per finalità di gestione o per offrire funzionalità avanzate, la specifica relativa alla gestione di AMQP definisce le primitive di interazione di base necessarie. Per l'integrazione con il modello di autorizzazione federata, la specifica relativa alla sicurezza basata su attestazioni di AMQP definisce il modo in cui associare e rinnovare i token di autorizzazione associati ai collegamenti.

## <a name="basic-amqp-scenarios"></a>Scenari AMQP di base

Questa sezione illustra l'uso di base di AMQP 1.0 con il bus di servizio di Azure, che include la creazione di connessioni, sessioni e collegamenti e il trasferimento di messaggi a e da entità del bus di servizio quali code, argomenti e sottoscrizioni.

La fonte più autorevole per informazioni sul funzionamento di AMQP è costituita dalla specifica relativa ad AMQP 1.0, ma questa specifica è stata scritta in modo da illustrare l'implementazione, non per fornire istruzioni relative al protocollo. Questa sezione è incentrata sull'introduzione della terminologia necessaria per descrivere l'uso di AMQP 1.0 da parte del bus di servizio. Per un'introduzione più completa ad AMQP e per una discussione più ampia su AMQP 1.0, vedere [questa esercitazione video][this video course].

### <a name="connections-and-sessions"></a>Connessioni e sessioni

AMQP definisce *contenitori* i programmi di comunicazione. I contenitori includono *nodi*, ovvero entità che comunicano all'interno di tali contenitori. Una coda può essere un nodo di questo tipo. AMQP consente il multiplexing, quindi una singola connessione può essere usata per molti percorsi di comunicazione tra i nodi. Un client applicazione, ad esempio, può ricevere contemporaneamente da una coda e inviare a un'altra coda sulla stessa connessione di rete.

![][1]

La connessione di rete viene quindi ancorata al contenitore. Viene avviata dal contenitore nel ruolo client che effettua una connessione in uscita tramite socket TCP a un contenitore nel ruolo del ricevitore che è in ascolto e accetta connessioni TCP in ingresso. L'handshake di connessione include la negoziazione della versione del protocollo, la dichiarazione o la negoziazione dell'uso di TLS/SSL (Transport Level Security) e un handshake di autenticazione/autorizzazione nell'ambito di connessione basato su SASL.

Il bus di servizio di Azure richiede sempre l'uso di TLS. Supporta connessioni sulla porta TCP 5671. La connessione TCP viene prima di tutto sovrapposta a TLS prima dell'handshake del protocollo AMQP e supporta anche connessioni sulla porta TCP 5672, in cui il server offre immediatamente un aggiornamento obbligatorio della connessione a TLS usando il modello prescritto da AMQP. L'associazione a WebSocket AMQP crea un tunnel sulla porta TCP 443 che è quindi equivalente a connessioni di tipo AMQP 5671.

Dopo la configurazione della connessione e di TLS, il bus di servizio offre due opzioni per il meccanismo SASL:

* L'opzione SASL PLAIN viene usata solitamente per passare credenziali di tipo nome utente e password a un server. Il bus di servizio non ha account, ma [regole di sicurezza di accesso condiviso](service-bus-sas.md) denominate, che conferiscono diritti e sono associate a una chiave. Il nome di una regola viene usato come nome utente e la chiave, sotto forma di testo con codifica Base64, viene usata come password. I diritti associati alla regola scelta determinano le operazioni consentite sulla connessione.
* L'opzione SASL ANONYMOUS viene usata per ignorare l'autorizzazione SASL quando il client vuole usare il modello di sicurezza basato sulle attestazioni che verrà illustrato più avanti. Questa opzione consente di stabilire una connessione client in modo anonimo per un breve periodo di tempo, durante il quale il client può interagire solo con l'endpoint CBS e l'handshake CBS deve essere completato.

Dopo la creazione della connessione di trasporto, ogni contenitore dichiara una dimensione massima per i frame che è disposto a gestire e dopo il timeout di inattività corrispondente ogni contenitore si disconnette in modo unilaterale in caso di assenza di attività sulla connessione.

I contenitori dichiarano anche il numero di canali simultanei supportati. Un canale è un percorso di trasferimento virtuale, unidirezionale e in uscita sulla connessione. Una sessione accetta un canale da ogni contenitore interconnesso per formare un percorso di comunicazione bidirezionale.

Le sessioni hanno un modello di controllo del flusso basato su una finestra. Quando viene creata una sessione, ogni parte dichiara il numero di frame che è disposta ad accettare durante la rispettiva finestra di ricezione. Quando le parti si scambiano i frame, i frame trasferiti completano tale finestra e i trasferimenti si arrestano quando la finestra è completa e fino a quando la finestra non viene reimpostata o espansa usando la *performativa di flusso*. *Performativa* è il termine di AMQP per i gesti a livello di protocollo scambiati tra le due parti.

Il modello basato su finestra è quasi equivalente al concetto TCP del controllo di flusso basato su finestra, ma a livello di sessione all'interno del socket. Il concetto del protocollo che permette più sessioni contemporanee consente di dare la precedenza al traffico a priorità elevata rispetto al traffico normale con limitazioni.

Il bus di servizio di Azure usa attualmente esattamente una sessione per ogni connessione. La dimensione massima di frame per il bus di servizio è di 262.144 byte (256 KB) per il bus di servizio Standard e Hub eventi. È pari a 1.048.576 (1 MB) per il bus di servizio Premium. Il bus di servizio non impone alcuna finestra di limitazione specifica a livello di sessione, ma reimposta regolarmente la finestra come parte del controllo di flusso a livello di collegamento. Vedere la [sezione successiva](#links).

Le connessioni, le sessioni e i canali sono temporanei. In caso di interruzione della connessione sottostante, è necessario ristabilire le connessioni, il tunnel TLS, il contesto di autorizzazione SASL e le sessioni.

### <a name="links"></a>Collegamenti

AMQP trasferisce i messaggi sui collegamenti. Un collegamento è un percorso di comunicazione creato su una sessione che consente il trasferimento di messaggi in una direzione. La negoziazione dello stato del trasferimento viene effettuata sul collegamento ed è bidirezionale tra le parti connesse.

![][2]

I collegamenti possono essere creati da uno dei contenitori in qualsiasi momento e su una sessione esistente. Ciò rende il protocollo AMQP diverso da molti altri protocolli, inclusi HTTP e MQTT, dove l'inizializzazione dei trasferimenti e il percorso di trasferimento sono un privilegio esclusivo della parte che crea la connessione socket.

Il contenitore che inizializza il collegamento chiede al contenitore opposto di accettare un collegamento e sceglie un ruolo di mittente o ricevitore. Ogni contenitore può quindi inizializzare la creazione di percorsi di comunicazione unidirezionali o bidirezionali. I percorsi bidirezionali vengono modellati come coppie di collegamenti.

I collegamenti sono denominati e sono associati ai nodi. Come indicato all'inizio, i nodi sono entità comunicanti all'interno di un contenitore.

Nel bus di servizio un nodo corrisponde direttamente a una coda, un argomento, una sottoscrizione o una coda secondaria di messaggi non recapitabili di una coda o una sottoscrizione. Il nome del nodo usato in AMQP è quindi il nome relativo dell'entità all'interno dello spazio dei nomi del bus di servizio. Se una coda è denominata **myqueue**, tale nome corrisponde anche al nome del nodo AMQP. Una sottoscrizione dell'argomento segue la convenzione dell'interfaccia API HTTP e viene ordinata in una raccolta di risorse "subscriptions". Pertanto una sottoscrizione **sub** o un argomento **mytopic** ha il nome di nodo AMQP **mytopic/subscriptions/sub**.

Il client che si connette deve anche usare un nome di nodo locale per la creazione di collegamenti. Il bus di servizio non fornisce prescrizioni specifiche sui nomi dei nodi e non li interpreterà. Gli stack del client AMQP 1.0 usano in genere uno schema per assicurare che i nomi di nodi temporanei siano univoci nell'ambito del client.

### <a name="transfers"></a>Trasferimenti

Dopo la creazione di un collegamento, è possibile trasferire i messaggi su tale collegamento. In AMQP un trasferimento viene eseguito con un gesto esplicito del protocollo, ovvero la performativa *transfer*, che sposta un messaggio dal mittente al ricevitore tramite un collegamento. Un trasferimento è completo quando è "finalizzato", ovvero quando entrambe le parti hanno raggiunto un accordo condiviso dell'esito del trasferimento.

![][3]

Nel caso più semplice il mittente può scegliere di inviare messaggi "pre-finalizzati", ovvero il client non è interessato all'esito e il ricevitore non fornirà alcun commento sull'esito dell'operazione. Questa modalità è supportata dal bus di servizio a livello del protocollo AMQP, ma non viene esposta in nessuna API client.

La situazione normale prevede l'invio di messaggi non finalizzati. Il ricevitore indicherà l'accettazione o il rifiuto usando la performativa *disposition*. Il rifiuto si verifica quando il ricevitore non può accettare il messaggio per qualsiasi motivo e il messaggio di rifiuto contiene informazioni sul motivo, ovvero una struttura di errore definita da AMQP. Se i messaggi vengono rifiutati a causa di errori interni nel bus di servizio, il servizio restituisce informazioni aggiuntive all'interno della struttura. Queste informazioni possono essere usate per offrire suggerimenti di diagnostica al personale di supporto tecnico in caso di invio di richieste di supporto. Per informazioni dettagliate sugli errori, vedere più avanti.

Una forma speciale di rifiuto è costituita dallo stato *released*, che indica che il ricevitore non ha alcuna obiezione tecnica al trasferimento, ma non è al tempo stesso interessato alla finalizzazione del trasferimento. È ad esempio possibile che un messaggio venga recapitato a un client del bus di servizio e che il client scelga di "abbandonare" il messaggio perché non è in grado di eseguire le operazioni risultanti dall'elaborazione del messaggio stesso, anche se l'operazione di recapito non presenta errori. Una variazione di questo stato è lo stato *modified*, che consente modifiche al messaggio durante il rilascio. Questo stato non viene attualmente usato dal bus di servizio.

La specifica AMQP 1.0 definisce uno stato di disposizione aggiuntivo denominato *received*, che semplifica in modo specifico la gestione del recupero del collegamento. Il recupero del collegamento consente di ricostituire lo stato di un collegamento e di eventuali recapiti in sospeso su una nuova connessione e sessione, in caso di perdita della connessione e della sessione precedente.

Il bus di servizio non supporta il recupero del collegamento. Se il client perde la connessione al bus di servizio con un trasferimento di messaggio non finalizzato in sospeso, il trasferimento del messaggio va perso e il client deve riconnettersi, ristabilire il collegamento e riprovare il trasferimento.

Il bus di servizio e gli hub eventi supportano quindi i trasferimenti di tipo "almeno una volta", in cui il mittente può essere sicuro che il messaggio è stato archiviato e accettato, ma non supportano i trasferimenti di tipo "esattamente una volta" a livello AMQP, in cui il sistema prova a recuperare il collegamento e continua a negoziare lo stato del recapito per evitare la duplicazione del trasferimento del messaggio.

Per compensare possibili invii duplicati, il bus di servizio supporta il rilevamento dei duplicati come funzionalità facoltativa nelle code e negli argomenti. Il rilevamento dei duplicati registra gli ID di tutti i messaggi in arrivo durante un intervallo di tempo definito dall'utente, quindi rilascia automaticamente tutti i messaggi inviati con gli stessi ID di messaggio durante lo stesso intervallo.

### <a name="flow-control"></a>Controllo di flusso

Oltre al modello di controllo di flusso a livello di sessione illustrato in precedenza, ogni collegamento ha il proprio modello di controllo di flusso. Il controllo di flusso a livello di sessione protegge il contenitore dalla necessità di gestire un numero eccessivo di frame alla volta. Il controllo di flusso a livello di collegamento assegna all'applicazione la responsabilità di specificare il numero di messaggi da gestire da un collegamento e del momento in cui gestirli.

![][4]

I trasferimenti su un collegamento possono essere eseguiti solo quando il mittente dispone di *credito di collegamento* sufficiente. Il credito di collegamento è un contatore impostato dal ricevitore usando la performativa *flow*, che ha come ambito un collegamento. Quando riceve credito di collegamento, il mittente prova a usare completamente tale credito recapitando messaggi. Ogni recapito di messaggi riduce di 1 il credito di collegamento rimanente. Quando il credito di collegamento viene usato completamente, i recapiti si interrompono.

Quando il bus di servizio ha il ruolo di ricevitore, fornisce immediatamente al mittente un credito di collegamento molto ampio per consentire l'invio immediato dei messaggi. Durante l'uso del credito di collegamento, il bus di servizio invia di tanto in tanto una performativa *flow* al mittente per aggiornare il saldo del credito di collegamento.

Il bus di servizio con ruolo del mittente invierà i messaggi in modo da usare completamente un eventuale credito di collegamento residuo.

Una chiamata di "ricezione" a livello di API viene convertita in una performativa *flow* inviata al bus di servizio dal client e il bus di servizio usa tale credito selezionando il primo messaggio non bloccato disponibile dalla coda, bloccandolo e trasferendolo. Se non sono disponibili messaggi pronti per il recapito, un eventuale credito residuo da parte di un collegamento stabilito con quell'entità specifica rimane registrato in ordine di arrivo e i messaggi vengono bloccati e trasferiti non appena disponibili, in modo da usare l'eventuale credito residuo.

Il blocco di un messaggio viene rilasciato quando il trasferimento viene finalizzato in uno degli stati terminali, ovvero *accepted*, *rejected* o *released*. Il messaggio viene rimosso dal bus di servizio quando lo stato terminale è *accepted*. Il messaggio rimane nel bus di servizio e viene recapitato al ricevitore successivo quando il trasferimento raggiunge uno degli altri stati. Il bus di servizio sposterà automaticamente il messaggio nella coda di messaggi non recapitabili dell'entità quando raggiunge il numero massimo di recapiti consentiti per l'entità a causa di rifiuti o rilasci ripetuti.

Anche se attualmente le API del bus di servizio non presentano direttamente un'opzione di questo tipo, un client di protocollo AMQP di livello inferiore può usare il modello del credito di collegamento per trasformare l'interazione di "tipo pull" che emette un'unità di credito per ogni richiesta di ricezione in un modello di "tipo push", che emette un numero molto elevato di crediti di collegamento e quindi può ricevere i messaggi non appena disponibili e senza altre interazioni. La modalità push è supportata tramite le impostazioni delle proprietà [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) o [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount). Quando sono diverse da zero, il client AMQP le usa come credito di collegamento.

In questo contesto è importante notare che il clock per la scadenza del blocco sul messaggio nell'entità viene avviato quando il messaggio viene accettato dall'entità e non all'inizio della fase di trasmissione. Quando il client indica che è pronto per ricevere messaggi emettendo un credito di collegamento, si prevede quindi che esegua attivamente il pull dei messaggi sulla rete che sia pronto a gestirli. In caso contrario, è possibile che il blocco del messaggio scada prima che il messaggio venga recapitato. L'uso del controllo di flusso del credito di collegamento deve riflettere in modo diretto la capacità immediata di gestire i messaggi disponibili inviati al ricevitore.

In breve, le sezioni seguenti forniscono una panoramica schematica del flusso performativo durante diverse interazioni con le API. Ogni sezione descrive un'operazione logica diversa. Alcune interazioni possono essere "differite", ovvero essere eseguite solo quando richieste. La creazione di un mittente del messaggio potrebbe non provocare alcuna interazione di rete fino al primo invio o alla prima richiesta di messaggio.

Le frecce della seguente tabella visualizzano la direzione del flusso performativo.

#### <a name="create-message-receiver"></a>Creare il ricevitore dei messaggi

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> attach(<br/>name={nome collegamento},<br/>handle={handle numerico},<br/>role=**receiver**,<br/>source={nome entità},<br/>target={ID collegamento client}<br/>) |Il client si collega a un'entità come ricevitore |
| Il bus di servizio risponde collegando la propria estremità del collegamento |<-- attach(<br/>name={nome collegamento},<br/>handle={handle numerico},<br/>role=**sender**,<br/>source={nome entità},<br/>target={ID collegamento client}<br/>) |

#### <a name="create-message-sender"></a>Creare il mittente dei messaggi

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> attach(<br/>name={nome collegamento},<br/>handle={handle numerico},<br/>role=**sender**,<br/>source={ID collegamento client},<br/>target={nome entità}<br/>) |Nessuna azione |
| Nessuna azione |<-- attach(<br/>name={nome collegamento},<br/>handle={handle numerico},<br/>role=**receiver**,<br/>source={ID collegamento client},<br/>target={nome entità}<br/>) |

#### <a name="create-message-sender-error"></a>Creare il mittente dei messaggi (errore)

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> attach(<br/>name={nome collegamento},<br/>handle={handle numerico},<br/>role=**sender**,<br/>source={ID collegamento client},<br/>target={nome entità}<br/>) |Nessuna azione |
| Nessuna azione |<-- attach(<br/>name={nome collegamento},<br/>handle={handle numerico},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={handle numerico},<br/>closed=**true**,<br/>error={informazioni errore}<br/>) |

#### <a name="close-message-receiversender"></a>Chiudere il ricevitore/mittente dei messaggi

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> detach(<br/>handle={handle numerico},<br/>closed=**true**<br/>) |Nessuna azione |
| Nessuna azione |<-- detach(<br/>handle={handle numerico},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Inviare (operazione riuscita)

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> transfer(<br/>delivery-id={handle numerico},<br/>delivery-tag={handle binario},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nessuna azione |
| Nessuna azione |<-- disposition(<br/>role=receiver,<br/>first={ID consegna},<br/>last={ID consegna},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Inviare (errore)

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> transfer(<br/>delivery-id={handle numerico},<br/>delivery-tag={handle binario},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nessuna azione |
| Nessuna azione |<-- disposition(<br/>role=receiver,<br/>first={ID consegna},<br/>last={ID consegna},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={informazioni errore}<br/>)<br/>) |

#### <a name="receive"></a>Ricevere

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Nessuna azione |
| Nessuna azione |< transfer(<br/>delivery-id={handle numerico},<br/>delivery-tag={handle binario},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={ID consegna},<br/>last={ID consegna},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Nessuna azione |

#### <a name="multi-message-receive"></a>Ricevere più messaggi

| Client | BUS DI SERVIZIO |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Nessuna azione |
| Nessuna azione |< transfer(<br/>delivery-id={handle numerico},<br/>delivery-tag={handle binario},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nessuna azione |< transfer(<br/>delivery-id={hanlde numerico+1},<br/>delivery-tag={handle binario},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nessuna azione |< transfer(<br/>delivery-id={handle numerico+2},<br/>delivery-tag={handle binario},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={ID consegna},<br/>last={ID consegna+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Nessuna azione |

### <a name="messages"></a>Messaggi

Le sezioni seguenti spiegano quali proprietà delle sessioni di messaggi AMQP standard vengono usate dal bus di servizio e ne illustrano il mapping al set di API del bus di servizio.

#### <a name="header"></a>intestazione

| Nome campo | Utilizzo | Nome API |
| --- | --- | --- |
| durable |- |- |
| priority |- |- |
| ttl |Durata di questo messaggio |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Nome campo | Utilizzo | Nome API |
| --- | --- | --- |
| message-id |Identificatore freeform definito dall'applicazione per questo messaggio. Usato per il rilevamento dei duplicati. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| user-id |Identificatore dell'utente definito dall'applicazione, non interpretato dal bus di servizio. |Non è accessibile tramite l'API del bus di servizio. |
| to |Identificatore della destinazione definito dall'applicazione, non interpretato dal bus di servizio. |[To](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| subject |Identificatore dello scopo del messaggio definito dall'applicazione, non interpretato dal bus di servizio. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| reply-to |Indicatore del percorso di risposta definito dall'applicazione, non interpretato dal bus di servizio. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| correlation-id |Identificatore della correlazione definito dall'applicazione, non interpretato dal bus di servizio. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| content-type |Indicatore del tipo di contenuto definito dall'applicazione per il corpo, non interpretato dal bus di servizio. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| content-encoding |Indicatore della codifica del contenuto definito dall'applicazione per il corpo, non interpretato dal bus di servizio. |Non è accessibile tramite l'API del bus di servizio. |
| absolute-expiry-time |Indica l'istante assoluto in cui scadrà il messaggio. Viene ignorato nell'input (viene osservato il valore TTL dell'intestazione), viene considerato autorevole nell'output. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| creation-time |Dichiara l'ora di creazione del messaggio. Non usato dal bus di servizio |Non è accessibile tramite l'API del bus di servizio. |
| group-id |Identificatore definito dall'applicazione per un set correlato di messaggi. Usato per le sessioni del bus di servizio. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| group-sequence |Contatore che identifica il numero di sequenza relativo al messaggio in una sessione. Ignorato dal bus di servizio. |Non è accessibile tramite l'API del bus di servizio. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

## <a name="advanced-service-bus-capabilities"></a>Funzionalità avanzate del bus di servizio

Questa sezione illustra le funzionalità avanzate del bus di servizio di Azure basate sulle estensioni provvisorie per AMQP attualmente in fase di sviluppo nel comitato tecnico OASIS per AMQP. Il bus di servizio implementa la versione più recente di queste estensioni provvisorie e adotterà le modifiche introdotte non appena le versioni provvisorie acquisiscono lo stato standard.

> [!NOTE]
> Le operazioni avanzate per la messaggistica del bus di servizio sono supportate tramite un modello di richiesta/risposta. I dettagli di queste operazioni sono descritti nel documento [AMQP 1.0 in Service Bus: request/response-based operations](service-bus-amqp-request-response.md) (AMQP 1.0 nel bus di servizio: operazioni basate su richiesta/risposta).
> 
> 

### <a name="amqp-management"></a>Gestione di AMQP

La specifica di gestione di AMQP è la prima estensione provvisoria illustrata in questo articolo. Questa specifica definisce un set di gesti del protocollo posizionati sopra il protocollo AMQP che consentono interazioni di gestione con l'infrastruttura di messaggistica su AMQP. La specifica definisce operazioni generiche, ad esempio *create*, *read*, *update* e *delete* per la gestione di entità all'interno di un'infrastruttura di messaggistica e di un set di operazioni di query.

Tutti questi gesti richiedono un'interazione di tipo richiesta/risposta tra il client e l'infrastruttura di messaggistica. Pertanto la specifica definisce come modellare tale schema di interazione su AMQP: il client si connette alla struttura di messaggistica, inizializza una sessione e quindi crea una coppia di collegamenti. In un collegamento il client funge da mittente e nell'altro funge da ricevitore, creando quindi una coppia di collegamenti che può fungere da canale bidirezionale.

| Operazione logica | Client | Bus di servizio |
| --- | --- | --- |
| Creare un percorso di richiesta/risposta |--> attach(<br/>name={*nome collegamento*},<br/>handle={*handle numerico*},<br/>role=**sender**,<br/>source=**null**,<br/>target="myentity/$management"<br/>) |Nessuna azione |
| Creare un percorso di richiesta/risposta |Nessuna azione |\<-- attach(<br/>name={*nome collegamento*},<br/>handle={*handle numerico*},<br/>role=**receiver**,<br/>source=null,<br/>target="myentity"<br/>) |
| Creare un percorso di richiesta/risposta |--> attach(<br/>name={*nome collegamento*},<br/>handle={*handle numerico*},<br/>role=**receiver**,<br/>source="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Creare un percorso di richiesta/risposta |Nessuna azione |\<-- attach(<br/>name={*nome collegamento*},<br/>handle={*handle numerico*},<br/>role=**sender**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

Se la coppia di collegamenti è disponibile, l'implementazione del percorso di richiesta/risposta è molto semplice: una richiesta è un messaggio inviato a un'entità all'interno dell'infrastruttura di messaggistica che comprende questo modello. Nel messaggio di richiesta il campo *reply-to* nella sezione *properties* è impostato sull'identificatore *target* per il collegamento in cui recapitare la risposta. L'entità di gestione elaborerà la richiesta e recapiterà la risposta sul collegamento il cui identificatore *target* corrisponde all'identificatore *reply-to* indicato.

Il modello richiede ovviamente che il contenitore client e l'identificatore generato dal client per la destinazione di risposta siano univoci in tutti i client e, per motivi di sicurezza, che siano anche difficili da prevedere.

Gli scambi di messaggi usati per il protocollo di gestione e per tutti gli altri protocolli che usano lo stesso modello vengono eseguiti a livello di applicazione. Non definiscono nuovi gesti a livello di protocollo AMQP. Questo approccio è intenzionale e consente alle applicazioni di sfruttare immediatamente i vantaggi di queste estensioni con gli stack conformi ad AMQP 1.0.

Il bus di servizio non implementa attualmente alcuna funzionalità di base della specifica di gestione, ma il modello di richiesta/risposta definito dalla specifica di gestione è fondamentale per la funzionalità di sicurezza basata sulle attestazioni e per la maggior parte delle funzionalità avanzate che verranno illustrate nelle sezioni successive.

### <a name="claims-based-authorization"></a>Autorizzazione basata sulle attestazioni

La bozza di specifica dell'autorizzazione basata sulle attestazioni (CBS, Claims-Based-Authorization specification) di AMQP si basa sul modello richiesta/risposta della specifica di gestione e illustra un modello generalizzato per l'uso dei token di sicurezza federati con AMQP.

Il modello di sicurezza predefinito di AMQP illustrato nell'introduzione è basato su SASL e si integra con l'handshake di connessione di AMQP. L'uso di SASL risulta vantaggioso perché fornisce un modello estendibile per cui è stato definito un set di meccanismi, che possono risultare utili per qualsiasi protocollo che si basa formalmente su SASL. Questi meccanismi includono ad esempio "PLAIN" per il trasferimento di nomi utente e password, "EXTERNAL" per l'associazione alla sicurezza a livello TLS, "ANONYMOUS" per indicare l'assenza di un'autenticazione/autorizzazione esplicita e una vasta gamma di meccanismi aggiuntivi che consentono il passaggio di credenziali di autenticazione e/o autorizzazione o token.

L'integrazione di AMQP con SASL presenta due svantaggi:

* Tutte le credenziali e i token hanno come ambito la connessione. È possibile che in un'infrastruttura di messaggistica si desideri specificare un controllo di accesso differenziato in base alle singole entità. Ad esempio è possibile consentire al titolare di un token di inviare messaggi alla coda A ma non alla coda B. Se il contesto di autorizzazione è ancorato alla connessione, non è possibile usare una singola connessione e al tempo stesso usare token di accesso diversi per la coda A e la coda B.
* I token di accesso sono in genere validi solo per un periodo limitato di tempo. L'utente dovrà quindi riacquisire periodicamente i token e l'autorità emittente dei token potrà rifiutare l'emissione di un nuovo token se le autorizzazioni di accesso dell'utente sono state modificate. Le connessioni AMQP possono durare molto a lungo. Il modello SASL consente solo di impostare un token in fase di connessione, ovvero l'infrastruttura di messaggistica deve disconnettere il client alla scadenza del token oppure deve accettare il rischio derivante dalla comunicazione continua con un client i cui diritti di accesso potrebbero essere stati revocati nel frattempo.

La specifica CBS per AMQP, implementata dal bus di servizio, offre una soluzione ideale entrambi i problemi: consente al client di associare token di accesso a ogni nodo e di aggiornare questi token prima della scadenza, senza interrompere il flusso di messaggi.

CBS definisce un nodo di gestione virtuale, denominato *$cbs*, che deve essere specificato dall'infrastruttura di messaggistica. Il nodo di gestione accetta i token per conto di qualsiasi altro nodo nell'infrastruttura di messaggistica.

Il gesto del protocollo è uno scambio di tipo richiesta/risposta, in base a quanto definito dalla specifica di gestione. Ciò significa che il client stabilisce una coppia di collegamenti con il nodo *$cbs*, passa una richiesta al collegamento in uscita e infine attende la risposta sul collegamento in ingresso.

Ecco le proprietà dell'applicazione per il messaggio di richiesta:

| Chiave | Facoltativo | Tipo di valore | Contenuti del valore |
| --- | --- | --- | --- |
| operation |No |string |**put-token** |
| type |No |string |Tipo di token inserito. |
| name |No |string |"Destinatari" a cui è applicabile il token. |
| expiration |Sì |timestamp |Ora di scadenza del token. |

La proprietà *name* identifica l'entità a cui deve essere associato il token. Nel bus di servizio corrisponde al percorso della coda o dell'argomento/sottoscrizione. La proprietà *type* identifica il tipo di token:

| Tipo di token | Descrizione del token | Tipo di corpo | Note |
| --- | --- | --- | --- |
| amqp:jwt |Token Web JSON (JWT) |Valore AMQP (stringa) |Non ancora disponibile. |
| amqp:swt |Token Web semplice (SWT) |Valore AMQP (stringa) |Supportato solo per token Web semplici emessi da AAD/ACS |
| servicebus.windows.net:sastoken |Token SAS del bus di servizio |Valore AMQP (stringa) |- |

I token conferiscono diritti. Il bus di servizio riconosce tre diritti fondamentali: "Send" per consentire l'invio, "Listen" per consentire la ricezione e "Manage" per consentire la modifica delle entità. I token Web semplici emessi da AAD/ACS includono esplicitamente questi diritti come attestazioni. I token di firma di accesso condiviso del bus di servizio fanno riferimento a regole configurate nello spazio dei nomi o nell'entità e queste regole sono configurate con diritti. Se si firma il token con la chiave associata a quella regola, il token esprimerà i rispettivi diritti. Il token associato a un'entità che usa *put-token* consente al client connesso di interagire con l'entità in base ai diritti del token. Un collegamento in cui il client ha il ruolo *sender* richiede il diritto "Send", mentre per il ruolo *receiver* richiede il diritto "Listen".

Il messaggio di risposta ha i valori *application-properties* seguenti:

| Chiave | Facoltativo | Tipo di valore | Contenuti del valore |
| --- | --- | --- | --- |
| status-code |No |int |Codice di risposta HTTP **[RFC2616]**. |
| status-description |Sì |string |Descrizione dello stato. |

Il client può chiamare *put-token* ripetutamente e per qualsiasi entità nell'infrastruttura di messaggistica. I token hanno come ambito il client corrente e sono ancorati alla connessione corrente, quindi il server eliminerà eventuali token conservati al termine della connessione.

L'implementazione corrente del bus di servizio consente CBS solo insieme al metodo "ANONYMOUS" di SASL. Una connessione SSL/TLS deve sempre esistere prima dell'handshake SASL.

Il meccanismo ANONYMOUS deve quindi essere supportato dal client AMQP 1.0 scelto. Per accesso anonimo si intende che l'handshake di connessione iniziale, inclusa la creazione della sessione iniziale, viene eseguito senza che il bus di servizio sappia chi sta creando la connessione.

Dopo aver stabilito la connessione e la sessione, le uniche operazioni consentite sono l'associazione dei collegamenti al nodo *$cbs* e l'invio della richiesta *put-token*. Un token valido deve essere impostato correttamente usando una richiesta *put-token* per un nodo di entità entro 20 secondi dall'avvio della connessione. In caso contrario, la connessione viene interrotta unilateralmente dal bus di servizio.

Il client è successivamente responsabile della verifica della scadenza del token. Alla scadenza di un token il bus di servizio elimina immediatamente tutti i collegamenti alla rispettiva entità nella connessione. Per evitare questo problema, il client può sostituire il token per il nodo con un nuovo token in qualsiasi momento tramite il nodo di gestione virtuale *$cbs* con lo stesso gesto *put-token* e senza ostacolare il traffico di payload tra i diversi collegamenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su AMQP, visitare i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]
* [AMQP nel bus di servizio per Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Supporto di AMQP 1.0 per code e argomenti partizionati del bus di servizio]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP nel bus di servizio per Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

