<properties 
   pageTitle="Domande frequenti sulla disponibilità e il supporto di Hub eventi"
   description="Prezzi e domande frequenti su Hub eventi."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="06/09/2015"
   ms.author="sethm" />

# Domande frequenti sulla disponibilità e il supporto di Hub eventi

Hub eventi fornisce acquisizione, persistenza ed elaborazione su vasta scala di eventi di dati da origini dati ad alta velocità e/o milioni di dispositivi. Quando viene utilizzato con argomenti e code di Bus di servizio, Hub eventi consente distribuzioni di controllo e comando persistente per gli scenari Internet delle cose (Internet of Things).

In questa sezione vengono fornite informazioni sulla disponibilità e le risposte ad alcune domande frequenti.

## Informazioni sui prezzi

Per informazioni sui prezzi di Hub eventi, vedere [Prezzi Hub eventi](http://azure.microsoft.com/pricing/details/event-hubs/).

## Come vengono calcolati gli eventi in ingresso di Hub eventi?

Ogni evento inviato a un Hub eventi conta come messaggio fatturabile. Un *evento in ingresso* è definito come un'unità di dati minore o uguale a 64 KB. Qualsiasi evento di dimensioni inferiori o uguali a 64 KB è considerato un evento fatturabile. Se l'evento è maggiore di 64 KB, il numero di eventi fatturabili viene calcolato in base alla dimensione dell'evento, in multipli di 64 KB. Ad esempio, un evento di 8 KB inviato all'Hub eventi viene fatturato come un solo evento, mentre un messaggio di 96 KB inviato all'Hub eventi viene fatturato come due eventi.

Gli eventi utilizzati da un Hub eventi, nonché le operazioni di gestione e le chiamate di controllo come i checkpoint, non vengono conteggiati come eventi in ingresso fatturabili, ma si accumulano fino alla capacità massima di unità elaborate.

## Cosa sono le unità elaborate in Hub eventi?

Le unità elaborate in Hub eventi vengono selezionate esplicitamente dall'utente, tramite il portale di Azure o l'API di gestione di Hub eventi. Le unità elaborate si applicano a tutti gli Hub eventi in uno spazio dei nomi del Bus di servizio e ogni unità elaborata autorizza lo spazio dei nomi per le funzionalità seguenti:

- Fino a 1 MB al secondo di eventi in ingresso (gli eventi inviati a un Hub eventi), ma non più di 1000 eventi in ingresso, operazioni di gestione o chiamate di controllo API al secondo.

- Fino a 2 MB al secondo di eventi in uscita (gli eventi utilizzati da un Hub di eventi).

- Fino a 84 GB di archiviazione eventi (sufficienti per il periodo di conservazione di 24 ore predefinito).

Le unità elaborate in Hub eventi vengono fatturate con tariffe orarie, in base al numero massimo di unità selezionate in una determinata ora.

## Come vengono applicati i limiti di unità elaborate in Hub eventi

Se la velocità effettiva totale in ingresso o la frequenza degli eventi totali in ingresso fra tutti gli Hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i mittenti verranno limitati e riceveranno errori indicanti che è stata superata la quota di ingresso.

Se la velocità effettiva totale in uscita o la frequenza degli eventi totali in uscita fra tutti gli Hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i destinatari verranno limitati e riceveranno errori indicanti che è stata superata la quota di uscita. Le quote di ingresso e uscita vengono applicate separatamente, in modo che nessun mittente possa causare il rallentamento del consumo e nessun destinatario possa impedire l’invio di eventi a un Hub eventi.

Si noti che la selezione delle unità elaborate è indipendente dal numero di partizioni di Hub eventi. Ogni partizione offre una velocità effettiva massima di 1 MB al secondo in ingresso (con un massimo di 1000 eventi al secondo) e 2 MB al secondo in uscita, non è previsto alcun costo fisso per le partizioni stesse. L'addebito si applica alle unità elaborate aggregate su tutti gli Hub eventi in uno spazio dei nomi del Bus di servizio. Con questo modello è possibile creare partizioni sufficienti a supportare il carico massimo previsto per i sistemi senza incorrere in eventuali costi di unità elaborate fino a quando il carico dell'evento nel sistema richiede effettivamente numeri di unità elaborate più alti e senza dover modificare la struttura e l’architettura dei sistemi quando il carico del sistema aumenta.

## Esiste un limite al numero di unità elaborate che possono essere selezionate?

Esiste una quota predefinita di 20 unità elaborate per ogni spazio dei nomi. È possibile richiedere una quota maggiore di unità elaborate creando un ticket di supporto. Oltre il limite di 20 unità elaborate, sono disponibili bundle di 20 e 100 unità elaborate. Si noti che l'utilizzo di più di 20 unità di elaborate elimina la possibilità di modificare il numero di unità elaborate senza creare ticket di supporto.

## È previsto un addebito per conservare gli eventi di Hub eventi per più di 24 ore?

Il livello standard di Hub eventi consente periodi di conservazione dei messaggi superiori alle 24 ore, per un massimo di 30 giorni. Se la dimensione della quantità totale di eventi archiviati supera la capacità di archiviazione consentita per il numero di unità elaborate selezionate (84 GB per unità elaborata), la dimensione che supera la capacità consentita viene addebitata alla tariffa di archiviazione BLOB di Azure. La capacità di archiviazione consentita in ogni unità elaborata copre tutti i costi di archiviazione per periodi di conservazione di 24 ore (impostazione predefinita) anche se l'unità elaborata viene utilizzata fino alla massima capacità consentita in ingresso.

## Qual è il periodo di conservazione massimo?

Il livello Standard di Hub eventi supporta attualmente un periodo di conservazione massimo di 7 giorni. Si noti che gli Hub eventi non sono intesi come archivi dati permanenti. Sono previsti periodi di conservazione maggiori di 24 ore per scenari in cui è opportuno riprodurre un flusso di eventi negli stessi sistemi, ad esempio, per la formazione o per verificare un nuovo modello di machine learning sui dati esistenti.

## Come vengono calcolate e addebitate le dimensioni di archiviazione di Hub eventi?

Le dimensioni totali di tutti gli eventi archiviati, incluso il sovraccarico interno per le intestazioni degli eventi o nelle strutture di archiviazione su disco in tutti gli Hub eventi vengono misurate nel corso della giornata. Alla fine della giornata, viene calcolata la dimensione di archiviazione massima. L'archiviazione giornaliera consentita viene calcolata in base al numero minimo di unità elaborate selezionate durante il giorno (ogni unità elaborata fornisce una capacità massima di 84 GB). Se la dimensione totale supera la capacità massima di archiviazione giornaliera calcolata, l'archiviazione in eccesso viene fatturata in base alle tariffe di archiviazione BLOB di Azure (alla tariffa di **archiviazione con ridondanza locale**).

## È possibile utilizzare una singola connessione AMQP per inviare e ricevere da Hub eventi e code/argomenti di Bus di servizio?

Sì, purché tutti gli Hub eventi, le code e gli argomenti siano nello stesso spazio dei nomi di Bus di servizio. Di conseguenza, è possibile implementare una connettività bidirezionale e negoziata a più dispositivi con latenze in frazioni di secondo, in modo economico e altamente scalabile.

## Si applicano costi di connessione negoziata ad Hub eventi?

Per i mittenti, i costi di connessione si applicano solo quando viene utilizzato il protocollo AMQP. Non sono previsti costi di connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di sistemi o dispositivi di invio. Se si prevede di utilizzare AMQP (ad esempio, per ottenere il flusso di eventi più efficiente o per consentire la comunicazione bidirezionale in scenari di comando e controllo Internet delle cose), consultare la pagina [Bus di servizio Prezzi](http://azure.microsoft.com/pricing/details/service-bus/) per informazioni su cosa si intende per connessione negoziata e su come viene misurato il consumo.

## Qual è la differenza tra i livelli Standard e Base di Hub eventi?

Il livello standard di Hub eventi fornisce funzionalità maggiori di quelle disponibili con il livello base o con alcuni sistemi della concorrenza. Queste funzionalità includono periodi di conservazione superiori a 24 ore e la possibilità di utilizzare una singola connessione AMQP per inviare comandi a un numero elevato di dispositivi con latenze in frazioni di secondo, nonché per inviare dati di telemetria dai dispositivi a Hub eventi. Fare riferimento a [dettagli sui prezzi] ([Hub eventi Prezzi](http://azure.microsoft.com/pricing/details/event-hubs/)) per l'elenco delle funzionalità.

## Disponibilità a livello geografico

Hub eventi è disponibile nelle seguenti aree:

|Area geografica|Regioni|
|---|---|
|Stati Uniti|Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-meridionali, Stati Uniti occidentali|
|Europa|Europa settentrionale, Europa occidentale|
|Asia/Pacifico|Asia orientale, Asia sudorientale|
|Giappone|Giappone orientale, Giappone occidentale|
|Brasile|Brasile meridionale|
|Australia|Australia orientale, Australia sudorientale|

## Contratto di servizio e supporto

Il supporto tecnico per Hub eventi è disponibile tramite i [forum della community](https://social.msdn.microsoft.com/forums/azure/home). Il supporto per la gestione della fatturazione e delle sottoscrizioni viene fornito gratuitamente.

Per ulteriori informazioni al riguardo, visitare la pagina [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).

## Passaggi successivi

Per ulteriori informazioni su Hub eventi, visitare i seguenti argomenti:

- [Panoramica di Hub eventi]
- Un'[applicazione di esempio completa che usa Hub eventi].
- Una [soluzione di messaggistica accodata] che usa le code di Bus di servizio.

[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[soluzione di messaggistica accodata]: ../cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=62-->