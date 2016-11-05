---
title: Domande frequenti sul bus di servizio | Microsoft Docs
description: Risposte ad alcune delle domande frequenti sul bus di servizio di Azure.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm;juconway

---
# <a name="service-bus-faq"></a>Domande frequenti sul bus di servizio
Questo articolo risponde ad alcune domande frequenti sul bus di servizio di Microsoft Azure. Per informazioni generali sui prezzi e sul supporto di Azure, vedere [Domande frequenti sul supporto di Azure](http://go.microsoft.com/fwlink/?LinkID=185083) . Vengono trattati gli argomenti seguenti:

* [Domande generali sulla messaggistica del bus di servizio di Azure](#general-questions-about-azure-service-bus-messaging)
* [Procedure consigliate del bus di servizio](#service-bus-best-practices)
* [Prezzi del bus di servizio](#service-bus-pricing)
* [Quote del bus di servizio](#service-bus-quotas)
* [Gestione di sottoscrizioni e spazio dei nomi](#subscription-and-namespace-management)
* [Risoluzione dei problemi](#service-bus-troubleshooting)

## <a name="general-questions-about-azure-service-bus-messaging"></a>Domande generali sulla messaggistica del bus di servizio di Azure
### <a name="what-is-azure-service-bus-messaging?"></a>Cos'è la messaggistica del bus di servizio di Azure?
La [messaggistica del bus di servizio di Azure](service-bus-messaging-overview.md) è una piattaforma cloud di messaggistica asincrona che consente di scambiare dati tra sistemi disaccoppiati. Microsoft offre questa funzionalità come servizio. Non è pertanto necessario un hardware per poterla usare.

### <a name="what-is-a-service-bus-namespace?"></a>Cos'è uno spazio dei nomi del bus di servizio?
Lo [spazio dei nomi](service-bus-create-namespace-portal.md) è un contenitore per le risorse del bus di servizio all'interno dell'applicazione. La creazione di uno spazio dei nomi è necessaria per usare il bus di servizio ed è uno dei primi passaggi delle attività iniziali.

### <a name="what-is-an-azure-service-bus-queue?"></a>Cos'è una coda del bus di servizio di Azure?
La [coda del bus di servizio](service-bus-queues-topics-subscriptions.md) è un'entità in cui vengono archiviati i messaggi. Le code sono particolarmente utili in presenza di più applicazioni o più parti di un'applicazione distribuita che devono comunicare tra loro. La coda è simile a un centro di distribuzione perché più prodotti (messaggi) vengono ricevuti e quindi inviati da tale posizione.

### <a name="what-are-azure-service-bus-topics-and-subscriptions?"></a>Cosa sono gli argomenti e le sottoscrizioni del bus di servizio?
Un argomento può essere visualizzato come coda e quando si usano più sottoscrizioni diventa un modello di messaggistica più completo. Si tratta essenzialmente di uno strumento di comunicazione uno-a-molti. Questo modello di pubblicazione/sottoscrizione, detto anche *Pub/Sub*, consente a un'applicazione che invia un messaggio a un argomento con più sottoscrizioni di garantire la ricezione di tale messaggio da parte di più applicazioni.

### <a name="what-is-the-azure-service-bus-relay-service?"></a>Cos'è il servizio di inoltro del bus di servizio di Azure?
Il [servizio di inoltro](../service-bus-relay/service-bus-relay-overview.md) consente di ospitare in modo trasparente e di accedere ai servizi WCF da qualsiasi posizione. In altre parole, consente di creare applicazioni ibride eseguite sia in un data center di Azure che nell'ambiente aziendale locale.

### <a name="what-is-a-partitioned-entity?"></a>Cos'è un'entità partizionata?
Una coda o un argomento convenzionale è gestito da un singolo broker messaggi e archiviato in un archivio di messaggistica. Le [code o gli argomenti partizionati](service-bus-partitioning.md) vengono gestiti da più broker dei messaggi e salvati in più archivi di messaggistica. Questo significa che la velocità effettiva complessiva di una coda o di un argomento partizionato non è più limitata dalle prestazioni di un singolo broker messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea dell'alimentazione di un archivio di messaggistica non determina la mancanza di disponibilità di una coda o di un argomento partizionato.

Se si usano entità di partizionamento, l'ordinamento non è garantito. Se una partizione non è disponibile è comunque possibile inviare e ricevere messaggi da altre partizioni.

## <a name="service-bus-best-practices"></a>Procedure consigliate per il bus di servizio
### <a name="what-are-some-azure-service-bus-best-practices?"></a>Quali sono alcune procedure consigliate per il bus di servizio di Azure?
* [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio][Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio]: questo articolo descrive come ottimizzare le prestazioni durante lo scambio di messaggi negoziati.
* [Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio][Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio]: questo articolo illustra le procedure migliori per proteggere endpoint di inoltro, code, argomenti e replica attiva e passiva da guasti del data center.

### <a name="what-should-i-know-before-creating-messaging-entities?"></a>Cosa è necessario sapere prima di creare entità di messaggistica?
Le proprietà seguenti di code e argomenti non sono modificabili. Tenerne conto quando si effettua il provisioning delle entità perché non è possibile apportare modifiche senza creare una nuova entità sostitutiva.

* Dimensione
* Partizionamento
* Sessioni
* Rilevamento duplicati
* Entità espressa

## <a name="service-bus-pricing"></a>Prezzi per il bus di servizio
In questa sezione vengono fornite le risposte ad alcune delle domande più frequenti sul modello di prezzo del bus di servizio. Per informazioni generali sui prezzi di Microsoft Azure, vedere le [Domande frequenti su Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083) . Per informazioni complete sui prezzi del bus di servizio, vedere la pagina relativa ai [prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus?"></a>Quali sono le modalità di addebito per il bus di servizio?
Per informazioni complete sui prezzi del bus di servizio, vedere i [dettagli sui prezzi del bus di servizio][Panoramica sui prezzi]. Oltre ai prezzi indicati, vengono addebitati i trasferimenti di dati associati in uscita dal data center in cui è stato effettuato il provisioning dell'applicazione.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer?-what-is-not?"></a>Quale tipo di utilizzo del bus di servizio è soggetto all'addebito per trasferimento di dati e quale non lo è?
Qualsiasi trasferimento di dati all'interno di una specifica area di Azure non è soggetto ad alcun addebito. Per trasferimenti all'esterno di un'area verranno invece addebitati i costi per trasferimento in uscita, alla tariffa di 0,15 USD per GB dalle aree di America del Nord ed Europa e di 0,20 per GB dall'area Asia Pacifico. Per tutti i trasferimenti di dati in ingresso non è previsto alcun addebito.

### <a name="how-is-the-relay-hours-meter-calculated?"></a>Come vengono calcolate le unità di ore di inoltro?
Le ore di inoltro vengono fatturate per il periodo di tempo cumulato in cui ogni inoltro del bus di servizio resta "aperto" nell'ambito di uno specifico periodo di fatturazione. Viene creata implicitamente un'istanza dell'inoltro, che viene aperto presso un determinato indirizzo del bus di servizio (URL dello spazio dei nomi servizio) quando un servizio WCF abilitato per l'inoltro ("listener di inoltro") si connette per la prima volta a tale indirizzo. L'inoltro viene chiuso solo quando l'ultimo listener si disconnette dal relativo indirizzo. Di conseguenza, ai fini della fatturazione, un inoltro viene considerato "aperto" dal momento in cui il primo listener di inoltro si connette fino al momento in cui l'ultimo si disconnette dall'indirizzo del bus di servizio di tale inoltro. In altri termini, un inoltro viene considerato "aperto" ogni volta che uno o più listener di inoltro si connettono al relativo indirizzo del bus di servizio.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-given-relay?"></a>Che cosa succede se più di un listener è connesso a un inoltro specifico?
In alcuni casi a un singolo inoltro del bus di servizio possono essere connessi più listener. Questo può verificarsi con servizi con carico bilanciato che usano i binding WCF netTCPRelay o HttpRelay oppure con listener di eventi di broadcast che usano il binding WCF netEventRelay. Un inoltro nel bus di servizio viene considerato "aperto" quando vi è connesso almeno un listener di inoltro. L'aggiunta di altri listener a un inoltro aperto non ne cambia lo stato ai fini della fatturazione. Sul calcolo delle ore di inoltro non ha effetto neanche il numero di mittenti di inoltro, ossia client che richiamano o inviano messaggi agli inoltri, connessi a un inoltro.

### <a name="how-is-the-messages-meter-calculated-for-relays?"></a>Come vengono calcolate le unità di messaggi per gli inoltri?
In generale, per gli inoltri i messaggi fatturabili vengono calcolati usando lo stesso metodo illustrato in precedenza per le entità negoziate, quali code, argomenti e sottoscrizioni. Esistono tuttavia numerose differenze significative:

L'invio di un messaggio a un inoltro del bus di servizio viene considerato come un invio "diretto" al listener di inoltro che lo riceve, anziché come un invio all'inoltro del bus di servizio seguito da un recapito al listener di inoltro. Di conseguenza, una chiamata al servizio di tipo richiesta-risposta di dimensioni fino a 64 KB a un listener di inoltro genererà due messaggi fatturabili, uno per la richiesta a l'altro per la risposta, presupponendo che anche la risposta sia \<= a 64 KB. Questo comportamento è diverso dall'uso di una coda per la mediazione tra un client e un servizio. In quest'ultimo caso, lo stesso modello di richiesta-risposta renderebbe necessario l'invio di una richiesta alla coda, seguito da un annullamento dell'accodamento e/o dal recapito dalla coda al servizio, seguito dall'invio di una risposta a un'altra coda, seguito da un annullamento dell'accodamento e/o da un recapito da tale coda al client. Usando sempre gli stessi presupposti per le dimensioni, vale a dire \<= 64 KB, il modello di accodamento mediato genererebbe quindi quattro messaggi fatturabili, ossia un numero doppio rispetto a quello fatturato per implementare lo stesso modello con l'inoltro. L'uso delle code per applicare questo modello presenta ovviamente alcuni vantaggi, tra cui durabilità e livellamento del carico. Questi vantaggi possono giustificare i costi aggiuntivi.

Gli inoltri aperti con il binding WCF netTCPRelay considerano i messaggi non come singoli messaggi, ma come un flusso di dati che attraversa il sistema. In altri termini, solo il mittente e il listener hanno visibilità sulla struttura dei singoli messaggi inviati/ricevuti con questo binding. Di conseguenza, per gli inoltri che usano il binding netTCPRelay , tutti i dati vengono considerati come un flusso ai fini del calcolo dei messaggi fatturabili. In questo caso, il bus di servizio calcolerà la quantità totale di dati inviati o ricevuti attraverso ciascun inoltro ogni 5 minuti e dividerà il totale per 64 KB per determinare il numero dei messaggi fatturabili per l'inoltro in questione durante il periodo di tempo specificato.

### <a name="does-service-bus-charge-for-storage?"></a>Per il bus di servizio viene addebitato lo spazio di archiviazione?
No, per il bus di servizio non viene addebitato lo spazio di archiviazione. È tuttavia prevista una quota che limita la quantità massima di dati che è possibile salvare in modo permanente per ogni coda e argomento. Vedere la risposta alla domanda successiva.

## <a name="service-bus-quotas"></a>Quote del bus di servizio
Per un elenco di limiti e quote del bus di servizio, vedere [Quote del bus di servizio][Quote del bus di servizio].

### <a name="does-service-bus-have-any-usage-quotas?"></a>Sono previste quote di utilizzo per il bus di servizio?
Per impostazione predefinita, per qualsiasi servizio cloud, Microsoft imposta una quota di utilizzo mensile aggregata che viene calcolata su tutte le sottoscrizioni di un cliente. Dal momento che i limiti previsti potrebbero non essere sufficienti, è possibile rivolgersi in qualsiasi momento al servizio clienti, che identificherà le esigenze specifiche e modificherà di conseguenza i limiti. Per il bus di servizio, le quote di utilizzo aggregate sono le seguenti:

* 5 miliardi di messaggi
* 2 milioni di ore di inoltro

Anche se Microsoft si riserva il diritto di disabilitare l'account di un cliente che abbia superato le quote di utilizzo previste in un determinato mese, invierà una notifica tramite posta elettronica ed effettuerà diversi tentativi di contattare il cliente prima di intraprendere qualsiasi azione. I clienti che superano tali quote saranno comunque responsabili per gli addebiti delle eccedenze.

Come gli altri servizi in Azure, il bus di servizio applica un set specifico di quote per garantire un utilizzo corretto delle risorse. Le quote di utilizzo applicate dal servizio sono le seguenti:

#### <a name="queue/topic-size"></a>Dimensioni coda/argomento
È possibile specificare le dimensioni massime di una coda o di un argomento al momento della creazione. La quota può avere un valore pari a 1, 2, 3, 4 o 5 GB. Se vengono raggiunte le dimensioni massime, i messaggi successivi verranno rifiutati e il codice chiamante riceverà un'eccezione.

#### <a name="naming-restrictions"></a>Limitazioni relative all'assegnazione dei nomi
La lunghezza del nome di uno spazio dei nomi del bus di servizio deve essere compresa tra 6 e 50 caratteri. Il numero limite di caratteri per ogni coda, argomento e sottoscrizione è compreso tra 1 e 50 caratteri.

#### <a name="number-of-concurrent-connections"></a>Numero di connessioni simultanee
Coda/argomento/sottoscrizione: il numero di connessioni TCP simultanee per una coda, un argomento o una sottoscrizione è limitato a 100. Se viene raggiunta questa quota, le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Per ogni factory di messaggistica, il bus di servizio mantiene una connessione TCP se uno qualsiasi dei client creati da tale factory ha un'operazione attiva in sospeso oppure ha completato un'operazione da meno di 60 secondi. Le operazioni REST non vengono conteggiate ai fini del numero di connessioni TCP simultanee.

#### <a name="number-of-concurrent-listeners-on-a-relay"></a>Numero di listener simultanei per un inoltro
Il numero di listener **netTcpRelay** e **netHttpRelay** simultanei per un inoltro è limitato a 25, ovvero 1 per un inoltro **NetOneway**.

#### <a name="number-of-concurrent-relay-listeners-per-namespace"></a>Numero di listener di inoltro simultanei per spazio dei nomi
Il bus di servizio applica un limite di 2000 listener di inoltro simultanei per ogni spazio dei nomi del servizio. Se viene raggiunta questa quota, le successive richieste di apertura di altri listener di inoltro verranno rifiutate e il codice chiamante riceverà un'eccezione.

#### <a name="number-of-topics/queues-per-service-namespace"></a>Numero di argomenti/code per spazio dei nomi servizio
Il numero massimo di argomenti o code (entità con archiviazione permanente) per uno spazio dei nomi del servizio è limitato a 10.000. Se viene raggiunta questa quota, le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi del servizio verranno rifiutate. In questo caso, nel portale di Azure classico verrà visualizzato un messaggio di errore oppure al codice client chiamante verrà inviata un'eccezione, a seconda che il tentativo di creazione sia stato eseguito tramite il portale o nel codice client.

### <a name="message-size-quotas"></a>Quote per le dimensioni dei messaggi
#### <a name="queue/topic/subscription"></a>Coda/argomento/sottoscrizione
**Dimensioni dei messaggi** : le dimensioni complessive di ogni messaggio non possono superare 256 KB, incluse le intestazioni.

**Dimensioni delle intestazioni dei messaggi** : per ogni intestazione del messaggio è previsto il limite di 64 KB.

**Inoltri NetOneway e NetEvent** : le dimensioni complessive di ogni messaggio non possono superare 64 KB, incluse le intestazioni.

**Inoltri HTTP e NetTcp** : il bus di servizio non impone un limite massimo per la dimensione di questi messaggi.

I messaggi che superano queste quote di dimensioni verranno rifiutati e il codice chiamante riceverà un'eccezione.

**Numero di sottoscrizioni per argomento** : il numero massimo di sottoscrizioni per argomento è limitato a 2.000. Se viene raggiunta questa quota, le successive richieste di creazione di altre sottoscrizioni per l'argomento verranno rifiutate. In questo caso, nel portale di Azure classico verrà visualizzato un messaggio di errore oppure al codice client chiamante verrà inviata un'eccezione, a seconda che il tentativo di creazione sia stato eseguito tramite il portale o nel codice client.

**Numero di filtri SQL per argomento** : il numero massimo di filtri SQL per argomento è limitato a 2.000. Se viene raggiunta questa quota, le successive richieste di creazione di altri filtri per l'argomento verranno rifiutate e il codice chiamante riceverà un'eccezione.

**Numero di filtri di correlazione per argomento** : il numero massimo di filtri di correlazione per argomento è limitato a 100.000. Se viene raggiunta questa quota, le successive richieste di creazione di altri filtri per l'argomento verranno rifiutate e il codice chiamante riceverà un'eccezione.

## <a name="subscription-and-namespace-management"></a>Gestione di sottoscrizioni e spazi dei nomi
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription?"></a>Come si esegue la migrazione di uno spazio dei nomi a un'altra sottoscrizione di Azure?
È possibile usare i comandi di PowerShell descritti in [questo articolo][] per spostare uno spazio dei nomi da una sottoscrizione di Azure a un'altra. Per eseguire l'operazione, lo spazio dei nomi deve essere già attivo. L'utente che esegue i comandi deve anche essere un amministratore delle sottoscrizioni di origine e destinazione.

## <a name="service-bus-troubleshooting"></a>Risoluzione dei problemi relativi al bus di servizio
[Panoramica sulle eccezioni][Panoramica sulle eccezioni]

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-messaging-apis-and-their-suggested-actions?"></a>Quali sono alcune delle eccezioni generate dalle API del sistema di messaggistica del bus di servizio di Azure e le azioni consigliate?
Le eccezioni che possono essere generate dalle API del sistema di messaggistica rientrano nelle categorie seguenti:

* Errore di codifica utente
* Errore di installazione/configurazione
* Eccezioni temporanee
* Altre eccezioni

L'articolo [Eccezioni di messaggistica del bus di servizio][Panoramica sulle eccezioni] descrive alcune eccezioni con le azioni consigliate.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature?"></a>Cos'è una firma di accesso condiviso e quali linguaggi supportano la generazione di una firma?
Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. Per informazioni su come generare le firme in linguaggio Node, PHP, Java e C\#, vedere l'articolo [Firme di accesso condiviso][Firme di accesso condiviso].

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

* [Introduzione alla messaggistica Premium del bus di servizio di Azure (post di blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduzione alla messaggistica Premium del bus di servizio di Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Bus di servizio di Azure](service-bus-fundamentals-hybrid-solutions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)

[Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio]: service-bus-performance-improvements.md
[Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio]: service-bus-outages-disasters.md
[Panoramica sui prezzi]: https://azure.microsoft.com/pricing/details/service-bus/
[Quote del bus di servizio]: service-bus-quotas.md
[in questo articolo]: service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription
[Panoramica sulle eccezioni]: service-bus-messaging-exceptions.md
[Firme di accesso condiviso]: service-bus-sas-overview.md



<!--HONumber=Oct16_HO2-->


