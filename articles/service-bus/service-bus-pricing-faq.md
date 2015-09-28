<properties 
   pageTitle="Domande frequenti sui prezzi del bus di servizio | Microsoft Azure"
   description="Risposte ad alcune delle domande più frequenti sul modello di prezzo del bus di servizio."
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
   ms.workload="na"
   ms.date="09/09/2015"
   ms.author="sethm" />

# Domande frequenti sui prezzi del bus di servizio

In questa sezione vengono fornite le risposte ad alcune delle domande più frequenti sul modello di prezzo del bus di servizio. Per informazioni generali sui prezzi di Microsoft Azure, vedere le [Domande frequenti su Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083). Per informazioni complete sui prezzi del bus di servizio, vedere la pagina relativa ai [prezzi del bus di servizio](http://azure.microsoft.com/pricing/details/service-bus/).

>[AZURE.NOTE]Il modello di prezzi per Hub eventi è illustrato nell'argomento [Domande frequenti sulla disponibilità e il supporto di Hub eventi](event-hubs-availability-and-support-faq.md). Per informazioni dettagliate, vedere [Prezzi di Hub eventi](http://azure.microsoft.com/pricing/details/event-hubs/).

- [Quali sono le modalità di addebito per il bus di servizio?](#how-do-you-charge-for-service-bus)
- [Quale tipo di utilizzo del bus di servizio è soggetto all'addebito per trasferimento di dati? e quale non lo è](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not)
- [Che cos'è esattamente un "inoltro" del bus di servizio?](#what-exactly-is-a-service-bus-quotrelayquot)
- [Come vengono calcolate le unità di ore di inoltro?](#how-is-the-relay-hours-meter-calculated)
- [Che cosa succede se più di un listener è connesso a un inoltro specifico?](#what-if-i-have-more-than-one-listener-connected-to-a-given-relay)
- [Come vengono calcolate le unità di messaggi per gli inoltri?](#how-is-the-messages-meter-calculated-for-relays)
- [Per il bus di servizio viene addebitato lo spazio di archiviazione?](#does-service-bus-charge-for-storage)
- [Sono previste quote di utilizzo per il bus di servizio?](#does-service-bus-have-any-usage-quotas)

## Quali sono le modalità di addebito per il bus di servizio?

Per informazioni complete sui prezzi del bus di servizio, vedere le pagine relative a [prezzi e fatturazione del bus di servizio](https://msdn.microsoft.com/library/dn831889.aspx) e [dettagli sui prezzi del bus di servizio](http://azure.microsoft.com/pricing/details/service-bus/). Oltre ai prezzi indicati, vengono addebitati i trasferimenti di dati associati in uscita dal data center in cui è stato effettuato il provisioning dell'applicazione. Per informazioni più dettagliate, vedere la sezione [Quale tipo di utilizzo del bus di servizio è soggetto all'addebito per trasferimento di dati? e quale non lo è?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not) più avanti in questo documento.

## Quale tipo di utilizzo del bus di servizio è soggetto all'addebito per trasferimento di dati e quale non lo è?

Qualsiasi trasferimento di dati all'interno di una specifica area di Azure non è soggetto ad alcun addebito. Per trasferimenti all'esterno di un'area verranno invece addebitati i costi per trasferimento in uscita, alla tariffa di 0,15 USD per GB dalle aree di America del Nord ed Europa e di 0,20 per GB dall'area Asia Pacifico. Per tutti i trasferimenti di dati in ingresso non è previsto alcun addebito.

## Che cos'è esattamente un "inoltro" del bus di servizio?

Un inoltro è un'entità del bus di servizio che inoltra i messaggi tra i client e i servizi Web. L'inoltro fornisce al servizio un indirizzo del bus di servizio permanente e individuabile, una connettività affidabile con funzionalità di attraversamento firewall/NAT e altre funzionalità come il bilanciamento del carico automatico. Viene creata implicitamente un'istanza dell'inoltro, che viene aperto presso un indirizzo specifico del bus di servizio (URL dello spazio dei nomi) ogni volta che un servizio WCF abilitato per l'inoltro ("listener di inoltro") si connette a tale indirizzo. Le applicazioni creano listener di inoltro con l'API gestita .NET del bus di servizio, che fornisce versioni speciali, abilitate per l'inoltro dei binding WCF standard.

## Come vengono calcolate le unità di ore di inoltro?

Le ore di inoltro vengono fatturate per il periodo di tempo cumulato in cui ogni inoltro del bus di servizio resta "aperto" nell'ambito di uno specifico periodo di fatturazione. Viene creata implicitamente un'istanza dell'inoltro, che viene aperto presso un determinato indirizzo del bus di servizio (URL dello spazio dei nomi servizio) quando un servizio WCF abilitato per l'inoltro ("listener di inoltro") si connette per la prima volta a tale indirizzo. L'inoltro viene chiuso solo quando l'ultimo listener si disconnette dal relativo indirizzo. Di conseguenza, ai fini della fatturazione, un inoltro viene considerato "aperto" dal momento in cui il primo listener di inoltro si connette fino al momento in cui l'ultimo si disconnette dall'indirizzo del bus di servizio di tale inoltro. In altri termini, un inoltro viene considerato "aperto" ogni volta che uno o più listener di inoltro si connettono al relativo indirizzo del bus di servizio.

## Che cosa succede se più di un listener è connesso a un inoltro specifico?

In alcuni casi a un singolo inoltro del bus di servizio possono essere connessi più listener. Questo può verificarsi con servizi con carico bilanciato che usano i binding WCF **netTCPRelay** o **HttpRelay** oppure con listener di eventi di broadcast che usano il binding WCF **netEventRelay**. Un inoltro nel bus di servizio viene considerato "aperto" quando vi è connesso almeno un listener di inoltro. L'aggiunta di altri listener a un inoltro aperto non ne cambia lo stato ai fini della fatturazione. Sul calcolo delle ore di inoltro non ha effetto neanche il numero di mittenti di inoltro, ossia client che richiamano o inviano messaggi agli inoltri, connessi a un inoltro.

## Come vengono calcolate le unità di messaggi per gli inoltri?

In generale, per gli inoltri i messaggi fatturabili vengono calcolati usando lo stesso metodo illustrato in precedenza per le entità negoziate, quali code, argomenti e sottoscrizioni. Esistono tuttavia numerose differenze significative:

1. L'invio di un messaggio a un inoltro del bus di servizio viene considerato come un invio "diretto" al listener di inoltro che lo riceve, anziché come un invio all'inoltro del bus di servizio seguito da un recapito al listener di inoltro. Di conseguenza, una chiamata al servizio di tipo richiesta-risposta (di dimensioni fino a 64 KB) a un listener di inoltro genererà due messaggi fatturabili, uno per la richiesta a l'altro per la risposta (presupponendo che anche la risposta sia inferiore a 64 KB). Questo comportamento è diverso dall'uso di una coda per la mediazione tra un client e un servizio. In quest'ultimo caso, lo stesso modello di richiesta-risposta renderebbe necessario l'invio di una richiesta alla coda, seguito da un annullamento dell'accodamento e/o dal recapito dalla coda al servizio, seguito dall'invio di una risposta a un'altra coda, seguito da un annullamento dell'accodamento e/o da un recapito da tale coda al client. Usando sempre gli stessi presupposti per le dimensioni (<= 64 KB), il modello di accodamento mediato genererebbe quindi quattro messaggi fatturabili, ossia un numero doppio rispetto a quello fatturato per implementare lo stesso modello con l'inoltro. L'uso delle code per applicare questo modello presenta ovviamente alcuni vantaggi, tra cui durabilità e livellamento del carico. Questi vantaggi possono giustificare i costi aggiuntivi.

2. Gli inoltri aperti con il binding WCF **NetTCPRelay** considerano i messaggi non come singoli messaggi, ma come un flusso di dati che attraversa il sistema. In altri termini, solo il mittente e il listener hanno visibilità sulla struttura dei singoli messaggi inviati/ricevuti con questo binding. Di conseguenza, per gli inoltri che usano il binding **netTCPRelay**, tutti i dati vengono considerati come un flusso ai fini del calcolo dei messaggi fatturabili. In questo caso, il bus di servizio calcolerà la quantità totale di dati inviati o ricevuti attraverso ciascun inoltro ogni 5 minuti e dividerà il totale per 64 KB per determinare il numero dei messaggi fatturabili per l'inoltro in questione durante il periodo di tempo specificato.

## Per il bus di servizio viene addebitato lo spazio di archiviazione?

No, per il bus di servizio non viene addebitato lo spazio di archiviazione. È tuttavia prevista una quota che limita la quantità massima di dati che è possibile salvare in modo permanente per ogni coda e argomento. Vedere la risposta alla domanda successiva.

## Sono previste quote di utilizzo per il bus di servizio?

Per impostazione predefinita, per qualsiasi servizio cloud, Microsoft imposta una quota di utilizzo mensile aggregata che viene calcolata su tutte le sottoscrizioni di un cliente. Dal momento che i limiti previsti potrebbero non essere sufficienti, è possibile rivolgersi in qualsiasi momento al servizio clienti, che identificherà le esigenze specifiche e modificherà di conseguenza i limiti. Per il bus di servizio, le quote di utilizzo aggregate sono le seguenti:

- 5 miliardi di messaggi

- 2 milioni di ore di inoltro

Anche se Microsoft si riserva il diritto di disabilitare l'account di un cliente che abbia superato le quote di utilizzo previste in un determinato mese, invierà una notifica tramite posta elettronica ed effettuerà diversi tentativi di contattare il cliente prima di intraprendere qualsiasi azione. I clienti che superano tali quote saranno comunque responsabili per gli addebiti delle eccedenze.

Come gli altri servizi in Azure, il bus di servizio applica un set specifico di quote per garantire un utilizzo corretto delle risorse. Le quote di utilizzo applicate dal servizio sono le seguenti:

- **Dimensioni coda/argomento**: è possibile specificare le dimensioni massime di una coda o di un argomento al momento della creazione. La quota può avere un valore pari a 1, 2, 3, 4 o 5 GB. Se vengono raggiunte le dimensioni massime, i messaggi successivi verranno rifiutati e il codice chiamante riceverà un'eccezione.

- **Numero di connessioni simultanee**
	- **Coda/argomento/sottoscrizione**: il numero di connessioni TCP simultanee per una coda, un argomento o una sottoscrizione è limitato a 100. Se viene raggiunta questa quota, le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Per ogni factory di messaggistica, il bus di servizio mantiene una connessione TCP se uno qualsiasi dei client creati da tale factory ha un'operazione attiva in sospeso oppure ha completato un'operazione da meno di 60 secondi. Le operazioni REST non vengono conteggiate ai fini del numero di connessioni TCP simultanee.


- **Numero di listener simultanei per un inoltro**: il numero di listener **NetTcpRelay** e **NetHttpRelay** simultanei per un inoltro è limitato a 25 (1 per un inoltro **NetOneway**).

- **Numero di listener di inoltro simultanei per spazio dei nomi servizio**: il bus di servizio applica un limite di 2.000 listener di inoltro simultanei per ogni spazio dei nomi servizio. Se viene raggiunta questa quota, le successive richieste di apertura di altri listener di inoltro verranno rifiutate e il codice chiamante riceverà un'eccezione.

- **Numero di argomenti/code per spazio dei nomi servizio**: il numero massimo di argomenti o code (entità con archiviazione permanente) per uno spazio dei nomi servizio è limitato a 10.000. Se viene raggiunta questa quota, le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi servizio verranno rifiutate. In questo caso, nel portale di Azure verrà visualizzato un messaggio di errore oppure al codice client chiamante verrà inviata un'eccezione, a seconda che il tentativo di creazione sia stato eseguito tramite il portale o nel codice client.

- **Quote per le dimensioni dei messaggi**
	- **Coda/argomento/sottoscrizione**
		- **Dimensioni dei messaggi**: le dimensioni complessive di ogni messaggio non possono superare 256 KB, incluse le intestazioni.
		- **Dimensioni delle intestazioni dei messaggi**: per ogni intestazione del messaggio è previsto il limite di 64 KB.

	- **Inoltri NetOneway e NetEvent**: le dimensioni complessive di ogni messaggio non possono superare 64 KB, incluse le intestazioni.
	- **Inoltri HTTP e NetTcp**: il bus di servizio non impone un limite massimo per la dimensione di questi messaggi.

	I messaggi che superano queste quote di dimensioni verranno rifiutati e il codice chiamante riceverà un'eccezione.

- **Numero di sottoscrizioni per argomento**: il numero massimo di sottoscrizioni per argomento è limitato a 2.000. Se viene raggiunta questa quota, le successive richieste di creazione di altre sottoscrizioni per l'argomento verranno rifiutate. In questo caso, nel portale di gestione verrà visualizzato un messaggio di errore oppure al codice client chiamante verrà inviata un'eccezione, a seconda che il tentativo di creazione sia stato eseguito tramite il portale o nel codice client.

- **Numero di filtri SQL per argomento**: il numero massimo di filtri SQL per argomento è limitato a 2.000. Se viene raggiunta questa quota, le successive richieste di creazione di altri filtri per l'argomento verranno rifiutate e il codice chiamante riceverà un'eccezione.

- **Numero di filtri di correlazione per argomento**: il numero massimo di filtri di correlazione per argomento è limitato a 100.000. Se viene raggiunta questa quota, le successive richieste di creazione di altri filtri per l'argomento verranno rifiutate e il codice chiamante riceverà un'eccezione.

Per altre informazioni sulle quote, vedere [Quote del bus di servizio](service-bus-quotas.md).

## Passaggi successivi

Per ulteriori informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

- [Introduzione alla messaggistica Premium del bus di servizio di Azure (post di blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduzione alla messaggistica Premium del bus di servizio di Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
- [Panoramica dell'architettura del bus di servizio di Azure](fundamentals-service-bus-hybrid-solutions.md)
- [Come usare le code del bus di servizio](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=Sept15_HO3-->