<properties 
    pageTitle="Disponibilità di Hub di eventi e supporto | Microsoft Azure"
    description="Domande frequenti sulla disponibilità e il supporto di Hub eventi."
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
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# Domande frequenti sulla disponibilità e il supporto di Hub eventi

Hub eventi consente l'acquisizione, la persistenza e l'elaborazione su vasta scala di eventi di dati da origini dati a velocità effettiva elevata e/o milioni di dispositivi. Se usato con argomenti e code del bus di servizio, Hub eventi consente distribuzioni di controllo e comando persistenti per scenari di [Internet delle cose (IoT)](https://azure.microsoft.com/services/iot-hub/).

Questo articolo contiene informazioni sulla disponibilità di Hub eventi e risposte ad alcune domande frequenti:

## Informazioni sui prezzi

Per informazioni complete sui prezzi di Hub eventi, vedere [Prezzi Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

## Come vengono calcolati gli eventi in ingresso di Hub eventi?

Ogni evento inviato a un Hub eventi conta come messaggio fatturabile. Un *evento in ingresso* è definito come un'unità di dati minore o uguale a 64 KB. Qualsiasi evento di dimensioni minori o uguali a 64 KB è considerato un evento fatturabile. Se l'evento è maggiore di 64 KB, il numero di eventi fatturabili viene calcolato in base alla dimensione dell'evento, in multipli di 64 KB. Ad esempio, un evento di 8 KB inviato all'Hub eventi viene fatturato come un solo evento, mentre un messaggio di 96 KB inviato all'Hub eventi viene fatturato come due eventi.

Gli eventi utilizzati da un Hub eventi, nonché le operazioni di gestione e le chiamate di controllo come i checkpoint, non vengono conteggiati come eventi in ingresso fatturabili, ma si accumulano fino alla capacità massima di unità elaborate.

## Cosa sono le unità elaborate in Hub eventi?

Le unità elaborate in Hub eventi vengono selezionate in modo esplicito tramite il portale di Azure o i modelli di Resource Manager in Hub eventi. Le unità elaborate sono applicabili a tutti gli hub eventi in uno spazio dei nomi di Hub eventi e ogni unità elaborata offre allo spazio dei nomi le capacità seguenti:

- Fino a 1 MB al secondo di eventi in ingresso (gli eventi inviati a un Hub eventi), ma non più di 1000 eventi in ingresso, operazioni di gestione o chiamate di controllo API al secondo.

- Fino a 2 MB al secondo di eventi in uscita (gli eventi utilizzati da un Hub di eventi).

- Fino a 84 GB di archiviazione eventi (sufficienti per il periodo di conservazione di 24 ore predefinito).

Le unità elaborate in Hub eventi vengono fatturate con tariffe orarie, in base al numero massimo di unità selezionate in una determinata ora.

## Come vengono applicati i limiti di unità elaborate in Hub eventi

Se la velocità effettiva totale in ingresso o la frequenza degli eventi totali in ingresso fra tutti gli Hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i mittenti verranno limitati e riceveranno errori indicanti che è stata superata la quota di ingresso.

Se la velocità effettiva totale in uscita o la frequenza degli eventi totali in uscita fra tutti gli Hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i destinatari verranno limitati e riceveranno errori indicanti che è stata superata la quota di uscita. Le quote di ingresso e uscita vengono applicate separatamente, in modo che nessun mittente possa causare il rallentamento del consumo e nessun destinatario possa impedire l’invio di eventi a un Hub eventi.

Si noti che la selezione delle unità elaborate è indipendente dal numero di partizioni di Hub eventi. Ogni partizione offre una velocità effettiva massima di 1 MB al secondo in ingresso (con un massimo di 1000 eventi al secondo) e 2 MB al secondo in uscita, non è previsto alcun costo fisso per le partizioni stesse. L'addebito è relativo alle unità elaborate aggregate in tutti gli hub eventi in uno spazio dei nomi di Hub eventi. Con questo modello è possibile creare partizioni sufficienti a supportare il carico massimo previsto per i sistemi senza incorrere in eventuali costi di unità elaborate fino a quando il carico dell'evento nel sistema richiede effettivamente numeri di unità elaborate più alti e senza dover modificare la struttura e l’architettura dei sistemi quando il carico del sistema aumenta.

## Esiste un limite al numero di unità elaborate che possono essere selezionate?

Esiste una quota predefinita di 20 unità elaborate per ogni spazio dei nomi. È possibile richiedere una quota maggiore di unità elaborate creando un ticket di supporto. Oltre il limite di 20 unità elaborate, sono disponibili bundle di 20 e 100 unità elaborate. Si noti che l'utilizzo di più di 20 unità di elaborate elimina la possibilità di modificare il numero di unità elaborate senza creare ticket di supporto.

## È previsto un addebito per conservare gli eventi di Hub eventi per più di 24 ore?

Il livello standard di Hub eventi consente periodi di conservazione dei messaggi superiori alle 24 ore, per un massimo di 30 giorni. Se le dimensioni del numero totale di eventi archiviati supera i limiti di archiviazione per il numero di unità elaborate selezionate, ovvero 84 GB per unità elaborata, alle dimensioni che superano i limiti saranno applicate le tariffe pubblicate per l'archivio BLOB di Azure. La capacità di archiviazione consentita in ogni unità elaborata copre tutti i costi di archiviazione per periodi di conservazione di 24 ore (impostazione predefinita) anche se l'unità elaborata viene utilizzata fino alla massima capacità consentita in ingresso.

## Qual è il periodo di conservazione massimo?

Il livello Standard di Hub eventi supporta attualmente un periodo di conservazione massimo di 7 giorni. Si noti che gli Hub eventi non sono intesi come archivi dati permanenti. Sono previsti periodi di conservazione maggiori di 24 ore per scenari in cui è opportuno riprodurre un flusso di eventi negli stessi sistemi, ad esempio, per la formazione o per verificare un nuovo modello di machine learning sui dati esistenti.

## Come vengono calcolate e addebitate le dimensioni di archiviazione di Hub eventi?

Le dimensioni totali di tutti gli eventi archiviati, incluso il sovraccarico interno per le intestazioni degli eventi o nelle strutture di archiviazione su disco in tutti gli Hub eventi vengono misurate nel corso della giornata. Alla fine della giornata, viene calcolata la dimensione di archiviazione massima. L'archiviazione giornaliera consentita viene calcolata in base al numero minimo di unità elaborate selezionate durante il giorno (ogni unità elaborata fornisce una capacità massima di 84 GB). Se la dimensione totale supera la capacità massima di archiviazione giornaliera calcolata, l'archiviazione in eccesso viene fatturata in base alle tariffe di archiviazione BLOB di Azure (alla tariffa di **archiviazione con ridondanza locale**).

## È possibile usare una singola connessione AMQP per l'invio a e la ricezione da Hub eventi e code/argomenti del bus di servizio?

Sì, purché tutti gli hub eventi, le code e gli argomenti si trovino nello stesso spazio dei nomi. In questo modo è possibile implementare una connettività bidirezionale e negoziata a più dispositivi con latenze inferiori al secondo, in modo economico e altamente scalabile.

## Si applicano costi di connessione negoziata ad Hub eventi?

Per i mittenti, i costi di connessione si applicano solo quando viene utilizzato il protocollo AMQP. Non sono previsti costi di connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di sistemi o dispositivi di invio. Se si prevede di usare AMQP, ad esempio per ottenere un flusso di eventi più efficiente o consentire la comunicazione bidirezionale in scenari di comando e controllo IoT, vedere la pagina delle [informazioni sui prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/) per sapere che cosa si intende per connessione negoziata e come viene misurato il consumo.

## Qual è la differenza tra i livelli Standard e Base di Hub eventi?

Il livello Standard di Hub eventi offre funzionalità superiori a quelle disponibili con il livello Basic e con alcuni sistemi della concorrenza. Le funzionalità includono periodi di conservazione superiori a 24 ore e la possibilità di usare una singola connessione AMQP per inviare comandi a un numero elevato di dispositivi con latenze inferiori al secondo, oltre a inviare dati di telemetria da tali dispositivi a Hub eventi. Per l'elenco delle funzionalità, vedere i [dettagli dei prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

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

Il supporto tecnico per Hub eventi è disponibile tramite i [forum della community](https://social.msdn.microsoft.com/forums/azure/home). Il supporto per fatturazione e gestione delle sottoscrizioni viene fornito gratuitamente.

Per altre informazioni sul Contratto di servizio, visitare la pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

## Passaggi successivi

Per ulteriori informazioni su Hub eventi, vedere i seguenti articoli:

- [Panoramica di Hub eventi][]
- Un'[applicazione di esempio completa che usa Hub eventi][].
- Una [soluzione di messaggistica accodata][] che usa le code di Bus di servizio.

[Panoramica di Hub eventi]: event-hubs-overview.md
[applicazione di esempio completa che usa Hub eventi]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[soluzione di messaggistica accodata]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_0817_2016-->