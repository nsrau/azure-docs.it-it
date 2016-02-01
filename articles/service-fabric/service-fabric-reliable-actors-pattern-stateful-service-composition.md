
<properties
   pageTitle="Modello di composizione del servizio con stato | Microsoft Azure"
   description="Modello di progettazione Service Fabric Reliable Actors che usa attori con stato per mantenere lo stato tra più chiamate al servizio e memorizzare nella cache i risultati dei servizi precedenti."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Modello di progettazione Reliable Actors: composizione dei servizi con stato

Gli sviluppatori hanno trascorso gli ultimi quindici anni cercando di creare servizi senza stato a N livelli. Hanno realizzato servizi basati su database, compilato servizi di ordine superiore basati su altri servizi e sviluppato motori di orchestrazione e middleware orientato ai messaggi per coordinare questi servizi. Con l'evolversi dei carichi di lavoro utente, finalizzati a soddisfare crescenti esigenze di interattività o scalabilità, un'architettura basata su servizi senza stato ha iniziato a rivelarsi inadeguata.

## Il passato: servizi SOA

Se da un lato i servizi SOA (Service-Oriented Architecture) possono essere facilmente scalati orizzontalmente grazie alla loro natura senza stato, dall'altro creano un collo di bottiglia nel livello di archiviazione, in termini sia di simultaneità sia di velocità effettiva. L'accesso all'archiviazione, quindi, diventava sempre più costoso. Come prassi comune, gli sviluppatori introducevano il caching nelle proprie soluzioni per ridurre la domanda di archiviazione, ma anche questa soluzione non era priva di svantaggi: un ulteriore livello da gestire, accesso simultaneo alla cache, modifiche e restrizioni semantiche e scarsa coerenza. Come dimostrato [nel modello Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md), il modello degli attori virtuali offre una soluzione perfetta per questo tipo di esigenza. Alcuni sviluppatori tentano di risolvere i problemi inerenti ai servizi SOA replicando il livello di archiviazione. Questo approccio, tuttavia, non garantisce una corretta scalabilità e raggiunge rapidamente i confini del protocollo di avviso comune (CAP, Common Alerting Protocol).

Il secondo problema riguarda l'evoluzione delle esigenze: sia le aziende sia gli utenti finali richiedono servizi interattivi, ovvero una risposta alle richieste in termini di millisecondi anziché di secondi. Per soddisfare questa esigenza, gli sviluppatori hanno iniziato a mettere a punto servizi facciata basati su altri servizi. In alcuni casi, sono state persino sviluppate decine di servizi facciata per creare servizi orientati agli utenti. L'integrazione di più servizi downstream, tuttavia, ha rapidamente creato problemi di latenza.

Gli sviluppatori si sono quindi affidati ad archivi di oggetti in memoria e nelle cache, in alcuni casi ricorrendo a implementazioni diverse per soddisfare vari requisiti di prestazioni. Gli sviluppatori che adottano questo approccio, in genere, creano processi di lavoro back-end per compilare la cache in modo periodico e ridurre così la costosa procedura di popolamento su richiesta della cache. Smontano quindi i propri carichi di lavoro per isolare le operazioni asincrone da quelle sincrone e ottenere così più spazio per le operazioni interattive e per poter reagire più rapidamente alle modifiche di stato, obiettivo particolarmente difficile nelle architetture basate sui servizi.

In molti casi, introducono anche livelli aggiuntivi, ad esempio code e processi di lavoro, rendendo le proprie soluzioni ancora più complesse.

Essenzialmente, gli sviluppatori cercano un modo per creare "servizi con stato", ovvero per collocare lo "stato" e il "comportamento del servizio" in modo da ottenere esperienze interattive e orientate agli utenti. È a questo punto che entra in gioco in modello Azure Service Fabric Reliable Actors come livello di composizione dei servizi, e non come sostituito di tali servizi.

Il diagramma seguente illustra questo punto:

![Reliable Actors, composizione dei servizi e persistenza dello stato][1]

## Implementare soluzioni migliori con gli attori

Ai fini della composizione dei servizi, gli attori possono essere con o senza stato.

* Gli attori senza stato possono essere usati come proxy dei servizi sottostanti. Possono essere scalati in modo dinamico nel cluster di Service Fabric e possono memorizzare nella cache determinate informazioni relative al servizio, ad esempio l'endpoint non appena viene rilevato.
* Gli attori con stato, invece, possono mantenere lo stato tra più chiamate al servizio, nonché memorizzare nella cache i risultati dei servizi precedenti. Lo stato, inoltre, può essere salvato in modo permanente o temporaneo.

Questo modello può essere applicato a molti tipi di scenario. Nella maggior parte dei casi, tuttavia, un attore deve eseguire una chiamata esterna per richiamare un'operazione su un particolare servizio. Questo modello viene illustrato di seguito con un esempio basato su una moderna applicazione di e-commerce. Queste applicazioni sono basate su servizi che offrono una gamma completa di funzionalità, tra cui: gestione del profilo utente, raccomandazioni, gestione del carrello, gestione della lista dei desideri e acquisti.

La maggior parte degli sviluppatori di e-commerce tenta di adottare un approccio all'architettura orientato all'utente, simile a quelli usati per lo sviluppo di esperienze social, poiché anche le esperienze di e-commerce ruotano essenzialmente attorno a utenti e prodotti. In genere, le soluzioni per sviluppatori vengono realizzate distribuendo una facciata di servizi che, per motivi di prestazioni, sono spesso supportati da una cache.

In un approccio basato sugli attori, invece, un attore utente può rappresentare sia il comportamento dell'utente (la consultazione di un catalogo, l'apprezzamento di un prodotto, l'aggiunta di un elemento al carrello o la raccomandazione di un prodotto a un amico) sia il relativo stato composto (il profilo personale, gli elementi presenti nel carrello, l'elenco degli elementi consigliati dagli amici, la cronologia degli acquisti e la posizione geografica corrente).

## Popolare lo stato usando attori con stato

Si consideri un esempio in cui un attore utente deve popolare il proprio stato recuperando i dati da più servizi. Per questo scenario non si fornirà un esempio di codice, poiché è valido quanto descritto [per il modello Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md). Un attore utente può essere attivato al momento dell'accesso e popolato con una quantità sufficiente di dati recuperati dai servizi back-end. Lo stato parziale o intero può essere anche prepopolato su richiesta o in base a un timer (o in entrambi i modi) e memorizzato nella cache dell'attore. Per questo esempio sono illustrati di seguito i servizi **Profilo** e **Lista dei desideri**.

![Servizi Profilo e Lista dei desideri][2]

Per gli utenti frequenti, gli sviluppatori possono prepopolare lo stato e renderlo disponibile al momento dell'accesso. Per gli utenti che visitano il servizio a cadenza mensile, invece, possono popolare lo stato al momento dell'accesso. Questi modelli sono stati illustrati anche nella sezione dedicata al modello Smart Cache.

Quando l'utente 23 (come illustrato nell'immagine precedente) esegue l'accesso, l'attore utente (23) viene attivato, se necessario, e recupera dai servizi back-end informazioni sul profilo utente e sulla lista dei desideri. L'attore utente, inoltre, memorizza le informazioni nella cache per le chiamate successive. Se, ad esempio, è necessario aggiungere un elemento alla lista dei desideri, è possibile soddisfare questa richiesta mediante operazioni di write-behind o write-through, come indicato in precedenza.

Si consideri ora un esempio in cui l'utente fa clic sul pulsante **Mi piace** per esprimere il proprio apprezzamento per un prodotto. Questa operazione può richiedere più chiamate a più servizi, ad esempio inviare un "Mi piace" al servizio di catalogo, attivare il successivo set di raccomandazioni e pubblicare un aggiornamento in un social network.

L'operazione è illustrata di seguito:

![Apprezzamento di un prodotto e servizi Lista dei desideri, Profilo e Catalogo][3]

## Usare gli attori nella composizione nella comunicazione asincrona
Il modello di programmazione Service Fabric Reliable Actors si esprime al meglio quando uno sviluppatore vuole comporre operazioni di tipo richiesta/risposta insieme a operazioni asincrone. Ad esempio, mentre il clic sul pulsante "Mi piace" aggiunge immediatamente l'elemento alla lista dei desideri dell'utente, la pubblicazione di un post nei social network e l'attivazione del successivo set di raccomandazioni possono essere operazioni asincrone che usano buffer e timer.

Un altro importante vantaggio offerto dall'uso di un attore per i servizi è la capacità degli attori di rappresentare una posizione naturale per lo stato memorizzato nella cache e, soprattutto, di reagire alle modifiche allo stato in modo asincrono. Questi obiettivi risultavano particolarmente difficili da raggiungere usando servizi senza stato. Si supponga, ad esempio, che un utente esegua una serie di azioni nell'ambito di un "viaggio utente" e che questi eventi vengano acquisiti in tempo reale in un attore. In questo caso, è possibile assemblare un flusso da interrogare al momento di un evento o in modo asincrono con un timer per modificare il comportamento dell'attore.

I puristi delle architetture basate sui servizi, a questo punto, avranno sicuramente notato che questi non sono servizi nel senso di attori considerati come endpoint esposti tramite un protocollo indipendente dalla lingua. Il modello Service Fabric Reliable Actors non è né un componente di interoperabilità né una piattaforma per l'interoperabilità dei servizi. Tuttavia, niente impedisce agli sviluppatori di prendere come riferimento la granularità dei servizi SOA quando modellano gli attori o predispongono la separazione delle problematiche. Questi servizi sono noti come "microservizi". Ma non solo: niente impedisce agli sviluppatori di inserire un endpoint REST o SOAP come livello di interoperabilità davanti a Service Fabric Reliable Actors.

La composizione dei servizi con stato, inoltre, si applica anche ai flussi di lavoro e non solo agli scenari transazionali come l'e-commerce. Service Fabric è progettato come un motore di orchestrazione/flusso di lavoro e può essere usato per modellare i flussi di lavoro che prevedono interazioni tra i servizi e per gestire lo stato di queste interazioni.

I punti deboli dei "servizi senza stato" appaiono evidenti nella creazione dei servizi scalabili necessari per consentire esperienze dinamiche. Combinando stato e comportamento, il modello di programmazione Service Fabric Reliable Actors consente agli sviluppatori di creare esperienze interattive e scalabili basate sugli investimenti esistenti.


## Passaggi successivi

[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=AcomDC_0121_2016-->