
<properties
   pageTitle="Azure Service Fabric Actors - Modello di progettazione Composizione dei servizi con stato"
   description="Modello di progettazione di Service Fabric Actors che usa attori con stato per mantenere lo stato tra più chiamate al servizio e memorizzare nella cache i risultati dei servizi precedenti. Lo stato, inoltre, può essere salvato in modo permanente o temporaneo."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/01/2015"
   ms.author="claudioc"/>

# Modello di progettazione di Service Fabric Actors: composizione dei servizi con stato
Gli sviluppatori hanno trascorso gli ultimi quindici anni cercando di creare servizi senza stato a N livelli. Hanno realizzato servizi basati su database, compilato servizi di ordine superiore basati su altri servizi e sviluppato motori di orchestrazione e middleware orientato ai messaggi per coordinare questi servizi. Con l'evolversi dei carichi di lavoro utente, e il conseguente aumento della domanda di interattività e scalabilità, un'architettura basata su servizi senza stato ha iniziato a rivelarsi inadeguata.

## Il passato: servizi SOA
Se da un lato i servizi SOA (Service-Oriented Architecture) possono essere facilmente scalati orizzontalmente grazie alla loro natura senza stato, dall'altro creano un collo di bottiglia nel livello di archiviazione, in termini sia di simultaneità sia di velocità effettiva. L'accesso all'archiviazione, quindi, diventava sempre più costoso. Come prassi comune, gli sviluppatori introducevano il caching nelle proprie soluzioni per ridurre la domanda di archiviazione, ma anche questa soluzione non era priva di svantaggi: un ulteriore livello da gestire, accesso simultaneo alla cache, modifiche e restrizioni semantiche e scarsa coerenza. Come dimostrato in precedenza per il modello Smart Cache, il modello Actor virtuale offre una soluzione perfetta per questo tipo di esigenza.

Alcuni sviluppatori avevano tentato di risolvere il problema replicando il livello di archiviazione. Questo approccio, tuttavia, non garantiva una corretta scalabilità e raggiungeva rapidamente i confini del punto di accesso client. Il secondo problema riguardava l'evoluzione delle esigenze: sia le aziende sia gli utenti finali richiedevano servizi interattivi, ovvero una risposta alle richieste in termini di millisecondi anziché di secondi. Per soddisfare questa esigenza, gli sviluppatori hanno iniziato a mettere a punto servizi facciata basati su altri servizi, in alcuni casi su decine di servizi, allo scopo di creare servizi orientati agli utenti. La combinazione di più servizi downstream, tuttavia, ha rapidamente dimostrato problemi di latenza.

Ancora una volta gli sviluppatori si sono affidati ad archivi di oggetti in memoria e nelle cache, in alcuni casi ricorrendo a implementazioni diverse per soddisfare vari requisiti di prestazioni. Hanno iniziato a creare processi di lavoro back-end per compilare la cache in modo periodico e ridurre al minimo la costosa procedura di popolamento su richiesta della cache. Hanno iniziato, infine, a smontare i propri carichi di lavoro per isolare le operazioni asincrone da quelle sincrone e ottenere più spazio per le operazioni interattive e per poter reagire più rapidamente alle modifiche di stato, obiettivo particolarmente difficile nelle architetture basate sui servizi.

Hanno quindi introdotto livelli aggiuntivi, ad esempio code e processi di lavoro, rendendo le proprie soluzioni ancora più complesse. Essenzialmente, gli sviluppatori hanno iniziato a cercare un modo per creare "servizi con stato", ovvero per collocare lo "stato" e il "comportamento del servizio" in modo da ottenere esperienze interattive e orientate agli utenti. È a questo punto che entra in gioco Azure Service Fabric Actors come livello di composizione dei servizi, e non come sostituito di tali servizi.

Il diagramma seguente illustra questo punto:

![][1]

## Soluzione migliore con gli attori
Ai fini della composizione dei servizi, gli attori possono essere con o senza stato.

* Gli attori senza stato possono essere usati come proxy dei servizi sottostanti. Possono essere scalati in modo dinamico nel cluster di Azure Service Fabric e possono memorizzare nella cache determinate informazioni relative al servizio, ad esempio l'endpoint non appena viene rilevato.
* Gli attori con stato, invece, possono mantenere lo stato tra più chiamate al servizio, nonché memorizzare nella cache i risultati dei servizi precedenti. Lo stato, inoltre, può essere salvato in modo permanente o temporaneo.

Questo modello può essere applicato a molti tipi di scenario. Nella maggior parte dei casi, tuttavia, gli attori devono effettuare chiamate esterne per richiamare un'operazione su un particolare servizio. Questo modello viene illustrato di seguito con un esempio basato su moderne applicazioni di e-commerce. Queste applicazioni sono basate su servizi che offrono varie funzionalità, tra cui: gestione del profilo utente, raccomandazioni, gestione del carrello, gestione della lista dei desideri, acquisti e molto altro.

La maggior parte degli sviluppatori desidera adottare un approccio all'architettura orientato all'utente, simile a quelli usati per lo sviluppo di esperienze social, poiché anche le esperienze di e-commerce ruotano essenzialmente attorno a utenti e prodotti. A questo scopo, in genere, si distribuisce una facciata di servizi solitamente supportati da una cache per motivi di prestazioni.

In un approccio basato sugli attori, un attore utente può rappresentare sia il comportamento dell'utente (la consultazione di un catalogo, l'apprezzamento di un prodotto, l'aggiunta di un elemento al carrello, la raccomandazione di un prodotto a un amico) sia il relativo stato composto (il profilo personale, gli elementi presenti nel carrello, l'elenco degli elementi consigliati dagli amici, la cronologia degli acquisti, la posizione geografica corrente e così via).

## Uso di attori con stato
Si consideri un esempio in cui l'attore utente deve popolare il proprio stato recuperando i dati da più servizi. Per questo scenario non si fornirà un esempio di codice, poiché è valido quanto descritto per il modello Smart Cache. È possibile attivare l'attore utente al momento dell'accesso e popolarlo con dati sufficienti recuperati dai servizi back-end. Naturalmente, come già emerso più volte in questo articolo, lo stato parziale o intero può essere prepopolato su richiesta o in base a un timer (o in entrambi i modi) e memorizzato nella cache dell'attore. Per questo esempio sono illustrati di seguito il profilo e la lista dei desideri:

![][2]

È possibile, ad esempio, prepopolare lo stato degli utenti frequenti e renderlo disponibile al momento dell'accesso oppure popolarlo al momento dell'accesso per gli utenti che visitano il servizio a cadenza mensile. Questi modelli sono stati illustrazioni nella sezione dedicata al modello Smart Cache.

Quando l'utente 23 esegue l'accesso, l'attore utente (23) viene attivato (se non già attivato) e recupera dai servizi back-end le informazioni disponibili sul profilo utente e sulla lista dei desideri. L'attore utente, inoltre, memorizza le informazioni nella cache per le chiamate successive. E se, ad esempio, è necessario aggiungere un elemento alla lista dei desideri, è possibile eseguire operazioni di write-behind o write-through, come indicato in precedenza. Si consideri ora un esempio in cui l'utente fa clic sul pulsante "Mi piace" per esprimere il proprio apprezzamento per un prodotto. Questa operazione può richiedere più chiamate a più servizi, come illustrato di seguito: inviare un "Mi piace" al servizio di catalogo, attivare il successivo set di raccomandazioni e, forse, pubblicare un aggiornamento in un social network.

L'operazione è illustrata di seguito:

![][3]

## Vantaggi offerti dalla composizione degli attori e dalla comunicazione asincrona
In realtà, Azure Service Fabric Actor si esprime al meglio quando si desidera comporre operazioni di tipo richiesta/risposta insieme a operazioni asincrone. Ad esempio, mentre il clic sul pulsante "Mi piace" inserisce immediatamente l'elemento nella lista dei desideri dell'utente, la pubblicazione di un post nei social network e l'attivazione del successivo set di raccomandazioni possono essere operazioni asincrone che usano buffer e timer.

Un altro importante vantaggio offerto dall'uso combinato di un attore utente con i servizi è la capacità degli attori di rappresentare una posizione naturale per lo stato memorizzato nella cache e, soprattutto, di reagire alle modifiche allo stato in modo asincrono. Questi obiettivi risultavano particolarmente difficili da raggiungere usando servizi senza stato. Si supponga, ad esempio, che un utente esegua una serie di azioni nell'ambito di un "viaggio utente". Questi eventi possono essere acquisiti in tempo reale nell'attore, in modo da poter assemblare un flusso da interrogare al momento di un evento, o in modo asincrono con un timer per modificare il comportamento dell'attore.

I puristi delle architetture basate sui servizi, a questo punto, avranno sicuramente notato che questi non sono servizi nel senso di attori considerati come endpoint esposti tramite un protocollo indipendente dalla lingua. Azure Service Fabric Actors non è né un componente di interoperabilità né una piattaforma per l'interoperabilità dei servizi. Tuttavia, niente impedisce di prendere come riferimento la granularità dei servizi SOA quando si modellano gli attori o si predispone la separazione delle problematiche. Questi servizi sono noti come "microservizi". Analogamente, niente impedisce di inserire un endpoint REST o un endpoint SOAP come livello di interoperabilità davanti ad Azure Service Fabric Actors.

La composizione dei servizi con stato si applica anche ai flussi di lavoro e non solo agli scenari transazionali come l'e-commerce. Azure Service Fabric è progettato come un motore di orchestrazione/flusso di lavoro e, quindi, può essere usato per modellare i flussi di lavoro che prevedono interazioni tra i servizi e gestire lo stato di queste interazioni.

I punti deboli dei servizi senza stato appaiono evidenti nella creazione dei servizi scalabili necessari per consentire esperienze dinamiche. Combinando stato e comportamento, Azure Service Fabric Actors consente agli sviluppatori di creare esperienze interattive e scalabili basate sugli investimenti esistenti.


## Passaggi successivi
[Modello: Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modello: Reti distribuite e grafici](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modello: Governance delle risorse](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modello: Internet delle cose](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modello: Calcolo distribuito](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alcuni anti-modelli](service-fabric-reliable-actors-anti-patterns.md)

[Introduzione a Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png
 

<!---HONumber=July15_HO4-->