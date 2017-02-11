---
title: Domande frequenti sul bus di servizio | Microsoft Docs
description: Risposte ad alcune delle domande frequenti sul bus di servizio di Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm;juconway
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b350bac36f1d46c97da37a2807ead8c9c732d69a


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
### <a name="what-is-azure-service-bus-messaging"></a>Cos'è la messaggistica del bus di servizio di Azure?
La [messaggistica del bus di servizio di Azure](service-bus-messaging-overview.md) è una piattaforma cloud di messaggistica asincrona che consente di scambiare dati tra sistemi disaccoppiati. Microsoft offre questa funzionalità come servizio. Non è pertanto necessario un hardware per poterla usare.

### <a name="what-is-a-service-bus-namespace"></a>Cos'è uno spazio dei nomi del bus di servizio?
Lo [spazio dei nomi](service-bus-create-namespace-portal.md) è un contenitore per le risorse del bus di servizio all'interno dell'applicazione. La creazione di uno spazio dei nomi è necessaria per usare il bus di servizio ed è uno dei primi passaggi delle attività iniziali.

### <a name="what-is-an-azure-service-bus-queue"></a>Cos'è una coda del bus di servizio di Azure?
La [coda del bus di servizio](service-bus-queues-topics-subscriptions.md) è un'entità in cui vengono archiviati i messaggi. Le code sono particolarmente utili in presenza di più applicazioni o più parti di un'applicazione distribuita che devono comunicare tra loro. La coda è simile a un centro di distribuzione perché più prodotti (messaggi) vengono ricevuti e quindi inviati da tale posizione.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Cosa sono gli argomenti e le sottoscrizioni del bus di servizio?
Un argomento può essere visualizzato come coda e quando si usano più sottoscrizioni diventa un modello di messaggistica più completo. Si tratta essenzialmente di uno strumento di comunicazione uno-a-molti. Questo modello di pubblicazione/sottoscrizione, detto anche *Pub/Sub*, consente a un'applicazione che invia un messaggio a un argomento con più sottoscrizioni di garantire la ricezione di tale messaggio da parte di più applicazioni.

### <a name="what-is-a-partitioned-entity"></a>Cos'è un'entità partizionata?
Una coda o un argomento convenzionale è gestito da un singolo broker messaggi e archiviato in un archivio di messaggistica. Le [code o gli argomenti partizionati](service-bus-partitioning.md) vengono gestiti da più broker dei messaggi e salvati in più archivi di messaggistica. Questo significa che la velocità effettiva complessiva di una coda o di un argomento partizionato non è più limitata dalle prestazioni di un singolo broker messaggi o archivio di messaggistica. Inoltre, un'interruzione temporanea dell'alimentazione di un archivio di messaggistica non determina la mancanza di disponibilità di una coda o di un argomento partizionato.

Se si usano entità di partizionamento, l'ordinamento non è garantito. Se una partizione non è disponibile è comunque possibile inviare e ricevere messaggi da altre partizioni.

## <a name="service-bus-best-practices"></a>Procedure consigliate per il bus di servizio
### <a name="what-are-some-azure-service-bus-best-practices"></a>Quali sono alcune procedure consigliate per il bus di servizio di Azure?
* [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio][Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio]: questo articolo descrive come ottimizzare le prestazioni durante lo scambio di messaggi negoziati.

### <a name="what-should-i-know-before-creating-messaging-entities"></a>Cosa è necessario sapere prima di creare entità di messaggistica?
Le proprietà seguenti di code e argomenti non sono modificabili. Tenerne conto quando si effettua il provisioning delle entità perché non è possibile apportare modifiche senza creare una nuova entità sostitutiva.

* Dimensione
* Partizionamento
* Sessioni
* Rilevamento duplicati
* Entità espressa

## <a name="service-bus-pricing"></a>Prezzi per il bus di servizio
In questa sezione vengono fornite le risposte ad alcune delle domande più frequenti sul modello di prezzo del bus di servizio. Per informazioni generali sui prezzi di Microsoft Azure, vedere le [Domande frequenti su Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=185083) . Per informazioni complete sui prezzi del bus di servizio, vedere la pagina relativa ai [prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>Quali sono le modalità di addebito per il bus di servizio?
Per informazioni complete sui prezzi del bus di servizio, vedere i [dettagli sui prezzi del bus di servizio][Panoramica sui prezzi]. Oltre ai prezzi indicati, vengono addebitati i trasferimenti di dati associati in uscita dal data center in cui è stato effettuato il provisioning dell'applicazione.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quale tipo di utilizzo del bus di servizio è soggetto all'addebito per trasferimento di dati e quale non lo è?
Qualsiasi trasferimento di dati all'interno di una specifica area di Azure non è soggetto ad alcun addebito. Per trasferimenti all'esterno di un'area verranno invece addebitati i costi per trasferimento in uscita, alla tariffa di 0,15 USD per GB dalle aree di America del Nord ed Europa e di 0,20 per GB dall'area Asia Pacifico. Per tutti i trasferimenti di dati in ingresso non è previsto alcun addebito.

### <a name="does-service-bus-charge-for-storage"></a>Per il bus di servizio viene addebitato lo spazio di archiviazione?
No, per il bus di servizio non viene addebitato lo spazio di archiviazione. È tuttavia prevista una quota che limita la quantità massima di dati che è possibile salvare in modo permanente per ogni coda e argomento. Vedere la risposta alla domanda successiva.

## <a name="service-bus-quotas"></a>Quote del bus di servizio
Per un elenco dei limiti e delle quote del bus di servizio, vedere [Quote del bus di servizio][Quote del bus di servizio].

### <a name="does-service-bus-have-any-usage-quotas"></a>Sono previste quote di utilizzo per il bus di servizio?
Per impostazione predefinita, per qualsiasi servizio cloud, Microsoft imposta una quota di utilizzo mensile aggregata che viene calcolata su tutte le sottoscrizioni di un cliente. Dal momento che i limiti previsti potrebbero non essere sufficienti, è possibile rivolgersi in qualsiasi momento al servizio clienti, che identificherà le esigenze specifiche e modificherà di conseguenza i limiti. Per il bus di servizio, le quote di utilizzo aggregate sono le seguenti:

* 5 miliardi di messaggi

Anche se Microsoft si riserva il diritto di disabilitare l'account di un cliente che abbia superato le quote di utilizzo previste in un determinato mese, invierà una notifica tramite posta elettronica ed effettuerà diversi tentativi di contattare il cliente prima di intraprendere qualsiasi azione. I clienti che superano tali quote saranno comunque responsabili per gli addebiti delle eccedenze.

Come gli altri servizi in Azure, il bus di servizio applica un set specifico di quote per garantire un utilizzo corretto delle risorse. Le quote di utilizzo applicate dal servizio sono le seguenti:

#### <a name="queuetopic-size"></a>Dimensioni coda/argomento
È possibile specificare le dimensioni massime di una coda o di un argomento al momento della creazione. La quota può avere un valore pari a 1, 2, 3, 4 o 5 GB. Se vengono raggiunte le dimensioni massime, i messaggi successivi verranno rifiutati e il codice chiamante riceverà un'eccezione.

#### <a name="naming-restrictions"></a>Limitazioni relative all'assegnazione dei nomi
La lunghezza del nome di uno spazio dei nomi del bus di servizio deve essere compresa tra 6 e 50 caratteri. Il numero limite di caratteri per ogni coda, argomento e sottoscrizione è compreso tra 1 e 50 caratteri.

#### <a name="number-of-concurrent-connections"></a>Numero di connessioni simultanee
Coda/argomento/sottoscrizione: il numero di connessioni TCP simultanee per una coda, un argomento o una sottoscrizione è limitato a 100. Se viene raggiunta questa quota, le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Per ogni factory di messaggistica, il bus di servizio mantiene una connessione TCP se uno qualsiasi dei client creati da tale factory ha un'operazione attiva in sospeso oppure ha completato un'operazione da meno di 60 secondi. Le operazioni REST non vengono conteggiate ai fini del numero di connessioni TCP simultanee.

#### <a name="number-of-topicsqueues-per-service-namespace"></a>Numero di argomenti/code per spazio dei nomi servizio
Il numero massimo di argomenti o code (entità con archiviazione permanente) per uno spazio dei nomi del servizio è limitato a 10.000. Se viene raggiunta questa quota, le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi del servizio verranno rifiutate. In questo caso, nel portale di Azure classico verrà visualizzato un messaggio di errore oppure al codice client chiamante verrà inviata un'eccezione, a seconda che il tentativo di creazione sia stato eseguito tramite il portale o nel codice client.

### <a name="message-size-quotas"></a>Quote per le dimensioni dei messaggi
#### <a name="queuetopicsubscription"></a>Coda/argomento/sottoscrizione
**Dimensioni dei messaggi** : le dimensioni complessive di ogni messaggio non possono superare 256 KB, incluse le intestazioni.

**Dimensioni delle intestazioni dei messaggi** : per ogni intestazione del messaggio è previsto il limite di 64 KB.

I messaggi che superano queste quote di dimensioni verranno rifiutati e il codice chiamante riceverà un'eccezione.

**Numero di sottoscrizioni per argomento** : il numero massimo di sottoscrizioni per argomento è limitato a 2.000. Se viene raggiunta questa quota, le successive richieste di creazione di altre sottoscrizioni per l'argomento verranno rifiutate. In questo caso, nel portale di Azure classico verrà visualizzato un messaggio di errore oppure al codice client chiamante verrà inviata un'eccezione, a seconda che il tentativo di creazione sia stato eseguito tramite il portale o nel codice client.

**Numero di filtri SQL per argomento** : il numero massimo di filtri SQL per argomento è limitato a 2.000. Se viene raggiunta questa quota, le successive richieste di creazione di altri filtri per l'argomento verranno rifiutate e il codice chiamante riceverà un'eccezione.

**Numero di filtri di correlazione per argomento** : il numero massimo di filtri di correlazione per argomento è limitato a 100.000. Se viene raggiunta questa quota, le successive richieste di creazione di altri filtri per l'argomento verranno rifiutate e il codice chiamante riceverà un'eccezione.

## <a name="subscription-and-namespace-management"></a>Gestione di sottoscrizioni e spazi dei nomi
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Come si esegue la migrazione di uno spazio dei nomi a un'altra sottoscrizione di Azure?
È possibile usare i comandi di PowerShell descritti in [in questo articolo][in questo articolo] per spostare uno spazio dei nomi da una sottoscrizione di Azure a un'altra. Per eseguire l'operazione, lo spazio dei nomi deve essere già attivo. L'utente che esegue i comandi deve anche essere un amministratore delle sottoscrizioni di origine e destinazione.

## <a name="service-bus-troubleshooting"></a>Risoluzione dei problemi relativi al bus di servizio
[Panoramica sulle eccezioni][Panoramica sulle eccezioni]

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-messaging-apis-and-their-suggested-actions"></a>Quali sono alcune delle eccezioni generate dalle API del sistema di messaggistica del bus di servizio di Azure e le azioni consigliate?
Le eccezioni che possono essere generate dalle API del sistema di messaggistica rientrano nelle categorie seguenti:

* Errore di codifica utente
* Errore di installazione/configurazione
* Eccezioni temporanee
* Altre eccezioni

L'articolo [Eccezioni di messaggistica del bus di servizio][Panoramica sulle eccezioni] descrive alcune eccezioni con le azioni consigliate.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Cos'è una firma di accesso condiviso e quali linguaggi supportano la generazione di una firma?
Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. Per informazioni su come generare le proprie firme in Node, PHP, Java e C\#, vedere l'articolo [Firme di accesso condiviso][Firme di accesso condiviso] (Firme di accesso condiviso).

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



<!--HONumber=Nov16_HO3-->


