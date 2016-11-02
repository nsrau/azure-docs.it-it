<properties
   pageTitle="Panoramica del modello di programmazione Reliable Services di Service Fabric | Microsoft Azure"
   description="Informazioni sul modello di programmazione Reliable Services di Service Fabric e su come sviluppare servizi personalizzati."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="vturecek; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="masnider;vturecek"/>

# Panoramica di Reliable Services
Azure Service Fabric semplifica la scrittura e la gestione di Reliable Services con e senza stato. In questo documento verranno illustrate:

- Il modello di programmazione Reliable Services per servizi con stato e senza stato.
- Le opzioni disponibili durante lo sviluppo di un servizio Reliable Services.
- Alcuni scenari ed esempi dell'uso di Reliable Services e della relativa modalità di sviluppo.

Reliable Services è uno dei modelli di programmazione disponibili in Service Fabric. Per altre informazioni sul modello di programmazione Reliable Actors, vedere [Introduzione a Reliable Actors in Service Fabric](service-fabric-reliable-actors-introduction.md).

In Service Fabric un servizio è costituito da configurazione, codice dell'applicazione e stato (facoltativo).

Service Fabric gestisce il ciclo di vita dei servizi, dalle fasi di provisioning e distribuzione fino all'aggiornamento e all'eliminazione, usando gli strumenti di [gestione delle applicazioni di Service Fabric](service-fabric-deploy-remove-applications.md).

## Informazioni su Reliable Services
Reliable Services offre un modello di programmazione di alto livello, semplice e potente, per specificare gli elementi importanti per l'applicazione. Il modello di programmazione Reliable Services offre quanto indicato di seguito:

- Per i servizi con stato, il modello di programmazione Reliable Services consente di archiviare lo stato in modo coerente e affidabile all'interno del servizio usando raccolte Reliable Collections. È un semplice set di classi di raccolte a disponibilità elevata che risulterà familiare a chiunque abbia usato raccolte C#. In passato, per la gestione di uno stato affidabile i servizi dovevano contare su sistemi esterni. Le raccolte Reliable Collections consentono di archiviare lo stato insieme ai calcoli effettuati con gli stessi livelli di disponibilità elevata e affidabilità garantiti dagli archivi esterni a disponibilità elevata e con i miglioramenti aggiuntivi a livello di latenza forniti dalla condivisione di risorse di calcolo e stato.

- Un modello semplice per l'esecuzione di codice personalizzato simile ai modelli di programmazione di uso comune. Il codice ha un punto di ingresso ben definito e un ciclo di vita facile da gestire.

- Un modello di comunicazione modulare. È possibile usare il trasporto preferito, ad esempio HTTP con [API Web](service-fabric-reliable-services-communication-webapi.md), WebSocket, protocolli TCP personalizzati e così via. Reliable Services fornisce alcune interessanti opzioni predefinite, ma consente anche di usarne di personalizzate.

## Caratteristiche distintive di Reliable Services
Reliable Services in Service Fabric è diverso dai servizi eventualmente sviluppati in precedenza. Service Fabric offre affidabilità, disponibilità, coerenza e scalabilità.

- **Affidabilità**: il servizio rimane attivo anche in ambienti non affidabili dove i computer possono non funzionare o avere problemi di rete.

- **Disponibilità**: il servizio è raggiungibile e reattivo. Ciò non significa che non si possono avere servizi che non è possibile trovare o raggiungere dall'esterno.

- **Scalabilità**: i servizi vengono disacoppiati da componenti hardware specifici e possono essere aumentati o ridotti secondo necessità con l'aggiunta o la rimozione di risorse hardware o virtuali. I servizi, in particolare quelli con stato, possono essere partizionati facilmente. In questo modo, le porzioni indipendenti del servizio possono essere scalate e, in caso di errore, possono rispondere in modo indipendente. Service Fabric, infine, permette di ottenere servizi "leggeri" consentendo il provisioning di migliaia di servizi con un unico processo, evitando così di richiedere o dedicare intere istanze del sistema operativo a una singola istanza di un particolare carico di lavoro.

- **Coerenza**: è garantita la coerenza di tutte le informazioni archiviate nel servizio. Questo vale soltanto per i servizi con stato, descritti in maggiore dettaglio più avanti.

## Ciclo di vita del servizio
Reliable Services fornisce un ciclo di vita semplice che consente di attivare rapidamente il codice e iniziare a usarlo. Per garantire l'operatività del servizio, è necessario implementare solo uno o due metodi.

- **CreateServiceReplicaListeners/CreateServiceInstanceListeners**: è qui che il servizio definisce gli stack di comunicazione da usare. Lo stack di comunicazione, ad esempio [API Web](service-fabric-reliable-services-communication-webapi.md), definisce l'endpoint di ascolto o gli endpoint per il servizio, ovvero il modo in cui i client potranno raggiungerlo. Definisce anche in che modo i messaggi visualizzati interagiscono con il resto del codice del servizio.

- **RunAsync**: è l'area in cui il servizio esegue la logica di business. Il token di annullamento fornito indica quando l'operazione deve essere interrotta. Si supponga, ad esempio, di avere un servizio che deve effettuare costantemente il pull dei messaggi da un oggetto ReliableQueue per elaborarli. Questa è l'area in cui vengono eseguite tali attività.

### Avvio del servizio

I principali eventi del ciclo di vita di un servizio Reliable Services sono i seguenti:

1. Viene costruito l'oggetto Service, ovvero l'oggetto che deriva da StatelessService o StatefulService.

2. Viene chiamato il metodo `CreateServiceReplicaListeners`/`CreateServiceInstanceListeners` per consentire al servizio di restituire uno o più listener di comunicazione a propria scelta.
  - Si noti che questa operazione è facoltativa anche se la maggior parte dei servizi espone alcuni endpoint direttamente.

3. Una volta creati i listener di comunicazione, vengono aperti.
  - I listener di comunicazione includono un metodo denominato `OpenAsync()`, che viene chiamato a questo punto e che restituisce l'indirizzo di ascolto per il servizio. Se il servizio Reliable Services usa una delle interfacce ICommunicationListener predefinite, l'operazione viene eseguita automaticamente.

4. Una volta che il listener di comunicazione è aperto, viene chiamato il metodo `RunAsync()` nel servizio principale.
  - Si noti che `RunAsync()` è facoltativo. Se il servizio esegue tutte le attività direttamente in risposta solo alle chiamate dell'utente, non è necessario implementare `RunAsync()`

### Arresto del servizio

Quando il servizio viene arrestato, per l'eliminazione, l'aggiornamento o lo spostamento, viene eseguito il mirroring dell'ordine delle chiamate: prima viene eliminato il token di annullamento mantenuto da `RunAsync()`, quindi viene chiamato `CloseAsync()` sui listener di comunicazione.

Esistono alcuni aspetti importanti da considerare riguardo al processo di arresto per i servizi con stato:

- Service Fabric non promuoverà un'altra replica del servizio allo stato primario fino al completamento di `CloseAsync` e `RunAsync`. Se si usa un listener di comunicazione predefinito, il metodo `CloseAsync` viene gestito automaticamente.

- Mentre non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere nelle raccolte Reliable Collections e quindi non è possibile completare alcuna operazione effettiva. È consigliabile completare al più presto la richiesta di annullamento ricevuta.

## Servizi di esempio
Per mostrare il funzionamento del modello di programmazione descritto, di seguito sono illustrati due differenti servizi.

### Reliable Services senza stato
Un servizio senza stato è un servizio al cui interno non viene letteralmente gestito alcuno stato oppure lo stato presente è interamente eliminabile e non richiede sincronizzazione, replica, persistenza o disponibilità elevata.

Si consideri ad esempio un servizio Calculator privo di memoria che riceve contemporaneamente tutti i termini e le operazioni da eseguire.

In questo caso il metodo RunAsync() del servizio può essere vuoto, dal momento che il servizio non deve eseguire alcuna elaborazione di attività in background. Quando viene creato, il servizio Calculator restituisce un ICommunicationListener, ad esempio [Web API](service-fabric-reliable-services-communication-webapi.md), che apre un endpoint di ascolto su una porta. L'endpoint di ascolto si collegherà ai diversi metodi, ad esempio "Add(n1, n2)", che definiscono l'API pubblica del servizio Calculator.

Quando viene effettuata una chiamata da un client, viene richiamato il metodo appropriato. Il servizio Calculator esegue le operazioni sui dati forniti e restituisce il risultato, senza archiviare alcuno stato.

La mancata archiviazione di qualsiasi stato interno rende il servizio Calculator di esempio molto semplice. La maggior parte dei servizi non è però realmente senza stato. Al contrario, esternalizzano il proprio stato in un altro archivio. Ad esempio, un'app Web che mantiene lo stato delle sessioni in una cache o in un archivio di backup non è completamente senza stato.

Un esempio comune di come i servizi senza stato vengono usati in Service Fabric è un servizio front-end che espone l'API pubblica di un'applicazione Web. Il servizio front-end richiede quindi ai servizi con stato di completare la richiesta di un utente. In questo caso, le chiamate provenienti dai client vengono indirizzate a una porta conosciuta, ad esempio la porta 80, dove è in ascolto il servizio senza stato. Il servizio senza stato riceve la chiamata, determina se quest'ultima proviene da una parte attendibile e identifica il servizio a cui è destinata. Il servizio senza stato inoltra quindi la chiamata alla partizione corretta del servizio con stato e attende una risposta. Quando il servizio senza stato riceve una risposta, risponde al client originale.

### Reliable Services con stato
Un servizio con stato è un servizio che, per funzionare in modo corretto, deve avere una qualche porzione dello stato coerente e presente. Si consideri, ad esempio, un servizio che deve calcolare costantemente la media mobile di alcuni valori in base agli aggiornamenti ricevuti. Per eseguire questa operazione, deve avere il set corrente di richieste in ingresso, oltre alla media corrente. Qualsiasi servizio che recupera, elabora e archivia informazioni in un archivio esterno, ad esempio un archivio tabelle o un BLOB di Azure, è un servizio con stato. Conserva il proprio stato nell'archivio stati esterno.

Attualmente la maggior parte dei servizi archivia il proprio stato esternamente, perché gli archivi esterni assicurano affidabilità, disponibilità, scalabilità e coerenza dello stato. In Service Fabric non è richiesto ai servizi con stato di archiviare esternamente il proprio stato. Service Fabric si occupa infatti di questi requisiti sia per il codice che per lo stato del servizio.

Si supponga di voler sviluppare un servizio che accetta sia richieste relative a una serie di conversioni da eseguire su un'immagine, sia l'immagine che deve essere convertita. A questo scopo, il servizio restituisce un listener di comunicazione, ad esempio API Web, che apre una porta di comunicazione e consente invii tramite un'API come `ConvertImage(Image i, IList<Conversion> conversions)`. In questa API il servizio può accettare le informazioni e archiviare la richiesta in un oggetto ReliableQueue. Può quindi restituire alcuni token al client in modo che questo possa tenere traccia della richiesta, dal momento che quest'ultima potrebbe richiedere tempo.

In questo servizio RunAsync potrebbe essere più complesso. All'interno del metodo RunAsync del servizio può essere presente un ciclo che effettua il pull delle richieste dall'oggetto IReliableQueue, esegue le conversioni elencate e archivia i risultati un oggetto IReliableDictionary. In questo modo, quando il client richiede le immagini convertite, il servizio è in grado di fornirle. Per garantire che anche in caso di problemi l'immagine non vada persa, questo servizio Reliable Services effettua il pull dalla coda, esegue le conversioni e archivia il risultato in una transazione. In questo modo il messaggio viene effettivamente rimosso dalla coda e i risultati vengono archiviati nel dizionario dei risultati solo dopo il completamento delle conversioni. Se uno dei passaggi non viene eseguito completamente, ad esempio si verifica un errore nel computer in cui è in esecuzione l'istanza della coda, la richiesta rimane in coda in attesa di essere elaborata di nuovo.

Una delle caratteristiche di questo servizio è che ha l'aspetto di un normale servizio .NET. L'unica differenza consiste nel fatto che le strutture dati usate, ovvero gli oggetti IReliableQueue e IReliableDictionary, sono fornite da Service Fabric e quindi offrono livelli di affidabilità, disponibilità e coerenza elevati.

## Quando usare le API Reliable Services
È consigliabile considerare l'uso delle API Reliable Services se il servizio dell'applicazione presenta uno dei requisiti seguenti:

- È necessario estendere il comportamento dell'applicazione a più unità di stato, ad esempio elementi di tipo Ordine e Riga ordine.

- Lo stato dell'applicazione può essere modellato naturalmente sotto forma di oggetti ReliableDictionary e ReliableQueue.

- Lo stato deve essere a disponibilità elevata con accesso a bassa latenza.

- L'applicazione deve controllare la concorrenza o la granularità di operazioni transazionali su una o più raccolte Reliable Collections.

- Si vogliono gestire le comunicazioni o controllare lo schema di partizionamento del servizio.

- Il codice necessita di un ambiente di runtime a thread libero.

- L'applicazione deve creare o eliminare definitivamente in modo dinamico gli oggetti ReliableDictionary e ReliableQueue in fase di esecuzione.

- È necessario controllare a livello di codice i backup forniti da Service Fabric e ripristinare le funzionalità per lo stato del servizio*.

- L'applicazione deve gestire la cronologia delle modifiche per le unità di stato*.

- Si vogliono utilizzare provider di stato di terze parti o svilupparne di personalizzati*.

> [AZURE.NOTE] *Funzionalità disponibili con la disponibilità generale dell'SDK.


## Passaggi successivi
+ [Guida introduttiva a Reliable Services di Microsoft Azure Service Fabric](service-fabric-reliable-services-quick-start.md)
+ [Uso avanzato del modello di programmazione Reliable Services](service-fabric-reliable-services-advanced-usage.md)
+ [Modello di programmazione Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!---HONumber=AcomDC_0406_2016-->