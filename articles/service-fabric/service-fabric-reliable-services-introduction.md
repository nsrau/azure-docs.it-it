<properties
   pageTitle="Panoramica del modello di programmazione Reliable Services di Service Fabric"
   description="Informazioni sul modello di programmazione Reliable Services di Service Fabric e su come sviluppare servizi personalizzati."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="masnider;jesseb"/>

# Panoramica di Reliable Services
Service Fabric semplifica la scrittura e la gestione di servizi affidabili con e senza stato. In questa guida verranno affrontati i seguenti argomenti:

1. Il modello di programmazione Reliable Services per servizi con e senza stato
2. Le diverse opzioni disponibili durante lo sviluppo di un servizio affidabile
3. Alcuni dei differenti scenari ed esempi dell'uso di Reliable Services e della relativa modalità di sviluppo

Reliable Services è uno dei modelli di programmazione disponibili in Service Fabric. Per altre informazioni sul modello di programmazione Reliable Actors, consultare l'[introduzione](../service-fabric/service-fabric-reliable-actors-introduction.md).

In Service Fabric un servizio è costituito da configurazione, codice dell'applicazione e stato \(facoltativo\).

Service Fabric gestisce il ciclo di vita dei servizi, dalle fasi di provisioning e sviluppo fino all'aggiornamento e all'eliminazione, mediante gli strumenti di [gestione delle applicazioni di Service Fabric](../service-fabric/service-fabric-deploy-remove-applications.md).

## Che cos'è Reliable Services?
Reliable Services offre un modello di programmazione di alto livello, semplice e potente, per specificare gli elementi importanti per l'applicazione. Il modello di programmazione Reliable Services offre quanto indicato di seguito:

1. Per i servizi con stato, il modello di programmazione Reliable Services consente di archiviare lo stato in modo coerente e affidabile all'interno del servizio usando le raccolte Reliable Collections, un semplice insieme di classi a disponibilità elevata ben conosciuto da chiunque abbia familiarità con le raccolte C\#. In passato, per la gestione dello stato affidabile i servizi dovevano contare su sistemi esterni. Le raccolte Reliable Collections consentono di archiviare lo stato insieme ai calcoli effettuati con gli stessi livelli di disponibilità e affidabilità garantiti dagli archivi esterni a disponibilità elevata.

2. Un modello semplice per l'esecuzione di codice simile ai modelli di programmazione di uso comune: il codice dispone di un punto di ingresso ben definito e di un ciclo di vita di facile gestione.

3. Un modello di comunicazione facilmente integrabile. È possibile usare il trasporto preferito, ad esempio HTTP con [Web API](../service-fabric/service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolli TCP personalizzati e così via. Reliable Services fornisce alcune interessanti opzioni predefinite, ma consente anche di realizzare funzionalità personalizzate.

## Quali sono le caratteristiche distintive di Reliable Services?
Reliable Services in Service Fabric è diverso da eventuali servizi sviluppati in precedenza. Service Fabric consente di assicurare affidabilità, disponibilità, coerenza e scalabilità.

+ <u>Affidabilità</u>: il servizio rimarrà operativo anche se nell'ambiente si verificano eventi negativi, ad esempio errori delle macchine o problemi di rete.

+ <u>Disponibilità</u>: il servizio sarà effettivamente raggiungibile e reattivo \(è possibile che siano attivi servizi non individuabili o raggiungibili\).

+ <u>Scalabilità</u>: i servizi vengono separati da componenti hardware specifici e possono essere aumentati o ridotti secondo necessità mediante l'aggiunta o la rimozione di risorse hardware o virtuali. I servizi, in particolare quelli con stato, possono essere partizionati facilmente. In questo modo, le porzioni indipendenti del servizio possono essere scalate e, in caso di errore, possono rispondere in modo indipendente. Service Fabric, infine, permette di ottenere servizi "leggeri" consentendo il provisioning di migliaia di servizi con un unico processo, evitando così di richiedere o dedicare intere istanze del sistema operativo a una singola istanza di un particolare carico di lavoro.

+ <u>Coerenza</u>: significa che è garantita la coerenza di tutte le informazioni archiviate nel servizio. Questo vale soltanto per i servizi con stato, descritti in maggiore dettaglio più avanti in questo articolo.

## Ciclo di vita del servizio
Reliable Services fornisce un ciclo di vita semplice che consente di attivare rapidamente il codice e iniziarne l'uso, sia per i servizi con stato sia per quelli senza stato. Per garantire l'operatività del servizio, è necessario implementare solo uno o due metodi.

+ CreateCommunicationListener: con questo metodo il servizio definisce lo stack di comunicazione da usare. Lo stack di comunicazione, ad esempio [Web API](../service-fabric/service-fabric-reliable-services-communication-webapi.md), definisce gli endpoint di ascolto per il servizio, ovvero il modo in cui i client potranno raggiungerlo, nonché il modo in cui i messaggi che vengono visualizzati cessano l'interazione con il resto del codice del servizio.

+ RunAsync: con questo metodo il codice può effettivamente eseguire operazioni. Il token di annullamento fornito indica quando l'operazione deve essere interrotta. Si supponga, ad esempio, di avere un servizio che deve effettuare costantemente il pull dei messaggi all'esterno di un oggetto ReliableQueue per elaborarli. RunAsync è il metodo con cui vengono eseguite tali attività.

I principali eventi del ciclo di vita di un servizio Reliable Services sono i seguenti: 1. Viene costruito l'oggetto Service, ovvero l'oggetto che deriva da StatelessService o StatefulService. 2. Viene chiamato il metodo CreateCommunicationListener, così da consentire al servizio di restituire un CommunicationListener di propria scelta. + Si noti che questa operazione è facoltativa, anche se la maggior parte dei servizi esporrà alcuni endpoint direttamente. 3. Dopo la creazione, il CommunicationListener viene aperto. + I CommunicationListener dispongono di un metodo denominato Open\(\) che viene chiamato a questo punto e restituisce l'indirizzo di ascolto per il servizio. Se il servizio Reliable Services usa una delle interfacce ICommunicationListener incorporate, l'operazione viene eseguita automaticamente. 4. Dopo l'esecuzione del metodo Open\(\) su CommunicationListener, viene eseguita una chiamata RunAsync\(\) sul servizio principale. + Si noti che RunAsync è facoltativo. Se il servizio effettua direttamente le operazioni solo in risposta alle chiamate dell'utente, non è necessario implementare RunAsync\(\).

Quando il servizio viene arrestato, ovvero quando viene eliminato o semplicemente spostato da una particolare posizione, l'ordine delle chiamate rimane inalterato: prima viene chiamato Close\(\) sul CommunicationListener, quindi viene eliminato il token di annullamento che era stato passato a RunAsync\(\).

## Servizi di esempio
Per mostrare il funzionamento del modello di programmazione descritto, di seguito sono illustrati due differenti servizi.

### Reliable Services senza stato
Un servizio senza stato è un servizio al cui interno non viene letteralmente gestito alcuno stato. In alternativa, lo stato presente è interamente eliminabile e non richiede sincronizzazione, replica, persistenza o disponibilità elevata.

Si prenda come esempio un servizio Calculator non dotato di memoria e che riceve in un'unica soluzione tutti i termini e le operazioni da eseguire.

In questo caso il metodo RunAsync\(\) del servizio può essere vuoto, dal momento che il servizio non deve eseguire alcuna elaborazione di attività in background. Quando viene creato, il servizio Calculator restituisce un CommunicationListener, ad esempio [Web API](../service-fabric/service-fabric-reliable-services-communication-webapi.md), che apre un endpoint di ascolto su una porta. L'endpoint di ascolto si collegherà ai differenti metodi, ad esempio "Add\(n1, n2\)", che definiscono l'API pubblica del servizio Calculator.

Quando viene effettuata una chiamata da un client, viene richiamato il metodo appropriato. Il servizio Calculator esegue le operazioni sui dati forniti e restituisce il risultato, senza archiviare alcuno stato.

La mancata archiviazione di qualsiasi stato interno rende il servizio Calculator di esempio davvero semplice. La maggior parte dei servizi, tuttavia, non è realmente senza stato. Al contrario, i servizi esternalizzano il proprio stato su qualche altro archivio. Ad esempio, un'app Web che mantiene lo stato delle sessioni in una cache o in un archivio di backup non è completamente senza stato.

Un esempio comune di come i servizi senza stato vengono usati in Service Fabric è un servizio front-end che espone l'API pubblica di un'applicazione Web. Il servizio front-end richiede quindi ai servizi con stato di completare la richiesta di un utente. In questo caso, le chiamate provenienti dai client vengono indirizzate a una porta conosciuta, ad esempio la porta 80, dove è in ascolto il servizio senza stato. Il servizio senza stato riceve la chiamata, determina se quest'ultima proviene da una parte attendibile e identifica il servizio a cui è destinata. Il servizio senza stato inoltra quindi la chiamata alla partizione corretta del servizio con stato e attende una risposta. Quando riceve una risposta, risponde al client originale.

### Reliable Services con stato
Un servizio con stato è un servizio che, per funzionare in modo corretto, deve avere una qualche porzione dello stato coerente e presente. Si consideri, ad esempio, un servizio che deve calcolare costantemente la media mobile di particolari valori in base agli aggiornamenti ricevuti. Per eseguire questa operazione, il servizio deve avere a disposizione sia l'insieme corrente di richieste in ingresso sia la media corrente. Qualsiasi servizio che recupera, elabora e archivia informazioni in un archivio esterno, ad esempio un archivio tabelle o un BLOB di Azure, è un servizio con stato, in quanto conserva il proprio stato in un archivio di stati esterno.

Oggi la maggior parte dei servizi archivia il proprio stato esternamente, poiché gli archivi esterni assicurano affidabilità, disponibilità, scalabilità e coerenza. In Service Fabric per i servizi con stato non è necessario archiviare lo stato esternamente. Service Fabric si occupa infatti dell'archiviazione sia del codice sia dello stato del servizio.

Si supponga di voler sviluppare un servizio che accetta sia richieste relative a una serie di conversioni da eseguire su un'immagine sia l'immagine che deve essere convertita. A questo scopo, il servizio restituisce un CommunicationListener, ad esempio WebAPI, che apre una porta di comunicazione e consente gli invii tramite un'API come `ConvertImage(Image i, IList<Conversion> conversions)`. In questa API il servizio può accettare le informazioni e archiviare la richiesta in un oggetto ReliableQueue. Può restituire quindi alcuni token al client in modo che questo possa tenere traccia della richiesta, dal momento che quest'ultima potrebbe richiedere un po' di tempo.

In questo servizio il metodo RunAsync potrebbe essere più complesso. All'interno del metodo RunAsync del servizio potrebbe essere presente un ciclo che effettua il pull delle richieste all'esterno dell'oggetto ReliableQueue, esegue le conversioni elencate e archivia i risultati un oggetto ReliableDictionary. In questo modo, quando il client richiede le immagini convertite, il servizio è in grado di fornirle. Per garantire che anche in caso di problemi l'immagine non vada perduta, questo servizio Reliable Services effettua il pull all'esterno della coda, esegue le conversioni e archivia il risultato in una transazione, in modo che il messaggio venga effettivamente rimosso dalla coda e che i risultati vengano archiviati nel dizionario dei risultati solo dopo il completamento delle conversioni. Se uno dei passaggi non viene eseguito completamente, ad esempio si verifica un errore nella macchina in cui è in esecuzione l'istanza della coda, la richiesta rimane in coda in attesa di essere elaborata nuovamente.

Una delle caratteristiche di questo servizio è che ha l'aspetto di un normale servizio .NET. L'unica differenza consiste nel fatto che le strutture dati usate, gli oggetti ReliableQueue e ReliableDictionary, sono fornite da Service Fabric e offrono di conseguenza elevati livelli di affidabilità, disponibilità e coerenza.

## Quando usare le API di Reliable Services
È consigliabile usare le API di Reliable Services se il servizio dell'applicazione presenta uno o più dei requisiti seguenti:

- È necessario estendere il comportamento dell'applicazione a più unità di stato, ad esempio elementi di tipo Ordine e Riga ordine.

- Lo stato dell'applicazione può essere modellato naturalmente sotto forma di dizionari e code affidabili

- Lo stato deve essere a disponibilità elevata con accesso a bassa latenza

- L'applicazione deve controllare la concorrenza o la granularità di operazioni transazionali su una o più raccolte affidabili

- Si desidera gestire le comunicazioni o controllare lo schema di partizionamento del servizio

- Il codice necessita di un ambiente di runtime a thread libero

- L'applicazione deve creare o distruggere dinamicamente dizionari o code affidabili in fase di esecuzione

- È necessario controllare a livello di codice i backup forniti da Service Fabric e ripristinare le funzionalità per lo stato del servizio\*

- L'applicazione deve gestire la cronologia delle modifiche per le unità di stato\*

- Si desidera utilizzare provider di stato di terze parti o svilupparne di personalizzati\*

> [AZURE.NOTE]*La disponibilità di queste funzionalità è legata alla disponibilità generale di SDK


## Passaggi successivi
+ [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](../service-fabric/service-fabric-reliable-services-quick-start.md)
+ [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)
+ [Modello di programmazione Reliable Actors](../service-fabric/service-fabric-reliable-actors-introduction.md)
 

<!---HONumber=58_postMigration-->