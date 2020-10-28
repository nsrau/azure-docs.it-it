---
title: Procedure consigliate per le mappe di Azure servizio di pianificazione percorso in Microsoft Azure Maps
description: Informazioni su come instradare i veicoli usando servizio di pianificazione percorso da Microsoft Azure maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 743710ea0d40eb31375236d4e59b0b138a217518
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895546"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Procedure consigliate per il servizio Route di Azure Maps

È possibile usare le direzioni Route e le API della matrice di route in Azure Maps [servizio di pianificazione percorso](/rest/api/maps/route) per calcolare i tempi di arrivo stimati (ETAs) per ogni route richiesta. Le API Route considerano fattori quali le informazioni sul traffico in tempo reale e i dati cronologici del traffico, ad esempio le velocità tipiche della strada, il giorno della settimana e l'ora del giorno richiesti. Le API restituiscono i percorsi più brevi o più veloci disponibili per più destinazioni alla volta in sequenza o in ordine ottimizzato, in base al tempo o alla distanza. Gli utenti possono anche richiedere Route specializzate e dettagli per le escursioni, i ciclisti e i veicoli commerciali, ad esempio i furgoni. In questo articolo verranno illustrate le procedure consigliate per chiamare Azure Maps [servizio di pianificazione percorso](/rest/api/maps/route)e si apprenderà come:

 * Scegliere tra le API Route directions e l'API routing Matrix
 * Richiedere tempi di viaggio stimati e cronologici, in base ai dati di traffico in tempo reale e storici
 * Dettagli della route della richiesta, come ora e distanza, per l'intera Route e per ogni parte della route
 * Richiedere la route per un veicolo commerciale, ad esempio un camion
 * Richiedere informazioni sul traffico lungo una route, ad esempio confetture e informazioni sui pedaggi
 * Richiedere una route costituita da una o più interruzioni (waypoint)
 * Ottimizzare una route di una o più interruzioni per ottenere l'ordine migliore per visitare ogni punto di interruzione (waypoint)
 * Ottimizzare le route alternative usando punti di supporto. Ad esempio, offrire route alternative che passano una stazione di ricarica del veicolo elettrico.
 * Usare il [servizio di pianificazione percorso](/rest/api/maps/route) con Azure Maps Web SDK

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione

Per ulteriori informazioni sulla copertura del servizio di pianificazione percorso, vedere la pagina relativa al [code coverage di routing](routing-coverage.md).

Questo articolo usa l' [app post](https://www.postman.com/downloads/) per compilare chiamate REST, ma è possibile scegliere qualsiasi ambiente di sviluppo API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Scegliere tra direzioni Route e routing matrice

Le API Direction Route restituiscono istruzioni che includono il tempo di viaggio e le coordinate per un percorso di route. L'API della matrice di route consente di calcolare il tempo di viaggio e le distanze per un set di route definite da percorsi di origine e di destinazione. Per ogni origine specificata, l'API Matrix calcola il costo (tempo di viaggio e distanza) del routing da tale origine a ogni destinazione specificata. Tutte queste API consentono di specificare parametri quali l'ora di partenza desiderata, i tempi di arrivo e il tipo di veicolo, ad esempio auto o camion. Tutti usano i dati di traffico in tempo reale o predittivo per restituire le route più ottimali.

Si consiglia di chiamare le API Route directions se lo scenario è:

* Richiedere il percorso di guida più breve o più veloce tra due o più posizioni note, per ottenere orari di arrivo precisi per i veicoli di consegna.
* Richiedere istruzioni dettagliate sulla Route, inclusa la geometria della route, per visualizzare le route sulla mappa
* Dato un elenco di percorsi dei clienti, calcolare la route più breve possibile per visitare ogni località del cliente e tornare all'origine. Questo scenario è comunemente noto come problema del commesso viaggiatore. È possibile passare fino a 150 waypoint (arresti) in un'unica richiesta.
* Inviare batch di query all'API batch directions Route usando solo una singola chiamata API.

Si consiglia di chiamare l'API di routing Matrix se lo scenario è:

* Calcolare il tempo di viaggio o la distanza tra un set di origini e destinazioni. Ad esempio, si dispone di 12 driver ed è necessario trovare il driver più vicino disponibile per scegliere la consegna del cibo dal ristorante.
* Ordinare le route potenziali in base alla distanza o al tempo di viaggio effettivo. L'API Matrix restituisce solo i tempi di viaggio e le distanze per ogni combinazione di origine e destinazione.
* Dati del cluster in base al tempo o alle distanze di viaggio. Ad esempio, l'azienda ha 50 dipendenti, trova tutti i dipendenti che risiedono entro 20 minuti di tempo dall'ufficio.

Di seguito è riportato un confronto per illustrare alcune funzionalità delle direzioni delle route e delle API della matrice:

| API Maps di Azure | Numero massimo di query nella richiesta | Evitare le aree | Routing di camion e veicoli elettrici | Waypoint e ottimizzazione del commesso viaggiatore | Punti di supporto |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Get Route Directions | 1 | | X | X | |
| Direzioni post Route | 1 | X | X | X | X |
| Batch istruzioni post Route | 700 | | X | X | |
| Post Route Matrix | 700 | | X | | |

Per altre informazioni sulle funzionalità di routing di veicoli elettrici, vedere l'esercitazione su come [instradare i veicoli elettrici usando Azure notebooks con Python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Richiedi dati cronologici e in tempo reale

Per impostazione predefinita, il servizio Route presuppone che la modalità di viaggio sia un'auto e l'ora di partenza è ora. Restituisce route in base alle condizioni di traffico in tempo reale, a meno che una richiesta di calcolo della route non specifichi diversamente. Le restrizioni di traffico dipendenti dal tempo fisse, ad esempio ' non sono consentite le deviazioni sinistre tra 4:00 e 6:00 PM, verranno acquisite e verranno considerate dal motore di routing. Verranno prese in considerazione le chiusure stradali, ad esempio i lavori stradali, a meno che non si richieda specificamente una route che ignori il traffico attivo corrente. Per ignorare il traffico corrente, impostare `traffic` su `false` nella richiesta dell'API.

Il valore **travelTimeInSeconds** per il calcolo della route include il ritardo dovuto al traffico. Viene generato sfruttando i dati correnti e cronologici di viaggio, quando l'ora di partenza è impostata su Now. Se il tempo di partenza è impostato in futuro, le API restituiscono i tempi di viaggio stimati in base ai dati cronologici.

Se si include il parametro **computeTravelTimeFor = all** nella richiesta, l'elemento Summary nella risposta avrà i campi aggiuntivi seguenti, incluse le condizioni di traffico cronologico:

| Elemento | Descrizione|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Tempo stimato di viaggio calcolato come se non ci siano ritardi nella route a causa di condizioni di traffico, ad esempio, a causa della congestione |
| historicTrafficTravelTimeInSeconds | Tempo stimato di viaggio calcolato usando i dati cronologici dipendenti dal tempo |
| liveTrafficIncidentsTravelTimeInSeconds | Tempo stimato di viaggio calcolato con i dati della velocità in tempo reale |

Le sezioni successive illustrano come effettuare chiamate alle API di route usando i parametri descritti.

### <a name="sample-query"></a>Query di esempio

Nel primo esempio sotto l'ora di partenza è impostato sul futuro, al momento della stesura di questo articolo.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

La risposta contiene un elemento Summary, come quello riportato di seguito. Poiché l'ora di partenza è impostata sul futuro, il valore **trafficDelayInSeconds** è zero. Il valore **travelTimeInSeconds** viene calcolato utilizzando i dati cronologici dipendenti dal tempo. Quindi, in questo caso, il valore di **travelTimeInSeconds** è uguale al valore di **historicTrafficTravelTimeInSeconds** .

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Query di esempio

Nel secondo esempio, abbiamo una richiesta di routing in tempo reale, in cui ora di partenza è ora. Non viene specificato in modo esplicito nell'URL perché è il valore predefinito.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

La risposta contiene un riepilogo, come illustrato di seguito. A causa delle congestioni, il valore di **trafficDelaysInSeconds** è maggiore di zero. È anche maggiore di **historicTrafficTravelTimeInSeconds** .

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Dettagli della route e delle tappe della richiesta

Per impostazione predefinita, il servizio Route restituirà una matrice di coordinate. La risposta conterrà le coordinate che compongono il percorso in un elenco denominato `points` . La risposta di route include anche la distanza dall'inizio della route e il tempo trascorso stimato. Questi valori possono essere usati per calcolare la velocità media per l'intera route.

Nell'immagine seguente viene illustrato l' `points` elemento.

![Elemento Points](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Espandere l' `point` elemento per visualizzare l'elenco di coordinate per il percorso:

![Elemento punti espanso](media/how-to-use-best-practices-for-routing/points-list-img.png)

Le API Route directions supportano formati diversi di istruzioni che possono essere usate specificando il parametro **instructionsType** . Per formattare le istruzioni per l'elaborazione di Easy computer, usare **instructionsType = coded** . Usare **instructionsType = Tagged** per visualizzare le istruzioni come testo per l'utente. Inoltre, le istruzioni possono essere formattate come testo in cui alcuni elementi delle istruzioni sono contrassegnati e l'istruzione viene visualizzata con una formattazione speciale. Per ulteriori informazioni, vedere l' [elenco dei tipi di istruzioni supportati](/rest/api/maps/route/postroutedirections#routeinstructionstype).

Quando vengono richieste istruzioni, la risposta restituisce un nuovo elemento denominato `guidance` . L' `guidance` elemento contiene due tipi di informazioni: direzioni per turni e istruzioni riepilogate.

![Tipo di istruzioni](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

L' `instructions` elemento contiene le direzioni di rotazione per il viaggio e `instructionGroups` contiene istruzioni riepilogate. Ogni riepilogo delle istruzioni riguarda un segmento del viaggio che può coprire più strade. Le API possono restituire dettagli per le sezioni di una route. ad esempio, l'intervallo di coordinate di un ingorgo del traffico o la velocità di traffico corrente.

![Trasforma istruzioni](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Istruzioni riepilogate](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Richiedere una route per un veicolo commerciale

Le API di routing di Azure Maps supportano il routing di veicoli commerciali, coprendo il routing di veicoli commerciali. Le API considerano i limiti specificati. Ad esempio, l'altezza e il peso del veicolo e se il veicolo trasporta un carico pericoloso. Ad esempio, se un veicolo è infiammabile, il motore di routing evita determinati tunnel vicini alle aree residenziali.

### <a name="sample-query"></a>Query di esempio

La richiesta di esempio seguente esegue una query su una route per un camion commerciale. Il veicolo trasporta materiale di spreco pericoloso di classe 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

L'API route restituisce le direzioni che soddisfano le dimensioni del camion e gli sprechi pericolosi. È possibile leggere le istruzioni della route espandendo l' `guidance` elemento.

![Truck con classe 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Query di esempio

La modifica della classe di materiali indesiderati degli Stati Uniti, dalla query precedente, comporterà la modifica di un'altra route.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

La risposta riportata di seguito è destinata a un camion che trasporta un materiale pericoloso di classe 9, che è meno pericoloso rispetto a un materiale pericoloso di classe 1. Quando si espande l' `guidance` elemento per leggere le direzioni, si noterà che le direzioni non sono uguali. Sono disponibili altre istruzioni per la route per il veicolo che trasporta materiali pericolosi di classe 1.



![Truck con classe 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Richiedere informazioni sul traffico lungo una route

Con le API Direction route di Azure Maps, gli sviluppatori possono richiedere dettagli per ogni tipo di sezione includendo il `sectionType` parametro nella richiesta. Ad esempio, è possibile richiedere le informazioni sulla velocità per ogni segmento di ingorgo del traffico. Per informazioni sui vari dettagli che è possibile richiedere, vedere l' [elenco di valori per la chiave sectionType](/rest/api/maps/route/getroutedirections#sectiontype) .

### <a name="sample-query"></a>Query di esempio

La query seguente imposta `sectionType` su `traffic` . Richiede le sezioni che contengono informazioni sul traffico da Seattle a San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

La risposta contiene le sezioni adatte per il traffico lungo le coordinate specificate.

![Sezioni relative al traffico](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Questa opzione può essere usata per colorare le sezioni durante il rendering della mappa, come nell'immagine seguente: 

![Sezioni colorate visualizzate sulla mappa](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Calcolare e ottimizzare un percorso con più fermate

Azure Maps fornisce attualmente due forme di ottimizzazione delle route:

* Ottimizzazioni basate sul tipo di route richiesto, senza modificare l'ordine dei waypoint. Qui è possibile trovare i [tipi di route supportati](/rest/api/maps/route/postroutedirections#routetype)

* Ottimizzazione del commesso viaggiatore, che modifica l'ordine dei waypoint per ottenere l'ordine migliore per visitare ogni punto di interruzione

Per il routing con più interruzioni, è possibile specificare fino a 150 waypoint in una singola richiesta di route. I percorsi delle coordinate iniziali e finali possono essere uguali, come nel caso di un round trip. Tuttavia è necessario fornire almeno un waypoint aggiuntivo per eseguire il calcolo della route. I waypoint possono essere aggiunti alla query tra le coordinate di origine e di destinazione.

Se si vuole ottimizzare l'ordine migliore per visitare i waypoint specificati, è necessario specificare **computeBestOrder = true** . Questo scenario è noto anche come problema di ottimizzazione del commesso viaggiatore.

### <a name="sample-query"></a>Query di esempio

La query seguente richiede il percorso per sei waypoint, con il `computeBestOrder` parametro impostato su `false` . È anche il valore predefinito per il `computeBestOrder` parametro.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

La risposta descrive la lunghezza del percorso che deve essere di 140.851 metri e che richiederà 9.991 secondi per il viaggio.

![Risposta non ottimizzata](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

L'immagine seguente illustra il percorso risultante da questa query. Questo percorso è una possibile route. Non si tratta del percorso ottimale in base all'ora o alla distanza.

![Immagine non ottimizzata](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Questo ordine del waypoint di route è: 0, 1, 2, 3, 4, 5 e 6.

### <a name="sample-query"></a>Query di esempio

La query seguente richiede il percorso per gli stessi sei waypoint, come nell'esempio precedente. Questa volta, il `computeBestOrder` parametro impostato su `true` (l'ottimizzazione del commesso viaggiatore).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

La risposta descrive la lunghezza del percorso che deve essere di 91.814 metri e che richiederà 7.797 secondi per il viaggio. La distanza di viaggio e il tempo di viaggio sono entrambi inferiori perché l'API ha restituito la route ottimizzata.

![Risposta ottimizzata](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

L'immagine seguente illustra il percorso risultante da questa query.

![Immagine ottimizzata](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

La route ottimale presenta l'ordine dei waypoint seguente: 0, 5, 1, 2, 4, 3 e 6.

>[!TIP]
> Le informazioni relative all'ordine del waypoint ottimizzato dal servizio di routing forniscono un set di indici. Che escludono gli indici di origine e di destinazione. È necessario incrementare questi valori di 1 per tenere conto dell'origine. Aggiungere quindi la destinazione alla fine per ottenere l'elenco completo dei waypoint ordinati.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Calcolare e polarizzare route alternative usando punti di supporto

Potrebbero essere presenti situazioni in cui si vuole ricostruire una route per calcolare zero o più route alternative per una route di riferimento. È possibile, ad esempio, mostrare ai clienti route alternative che passano il negozio al dettaglio. In questo caso, è necessario predisporre un percorso usando punti di supporto. Ecco i passaggi per compensare un percorso:

1. Calcolare una route così com'è e ottenere il percorso dalla risposta Route
2. Usare il percorso della route per individuare le località desiderate insieme o vicino al percorso della route. Ad esempio, è possibile usare Azure Maps [API Point of interest](/rest/api/maps/search/getsearchpoi) o eseguire query sui dati nel database.  
3. Ordinare i percorsi in base alla distanza dall'inizio della route
4. Aggiungere questi percorsi come punti di supporto in una nuova richiesta di route all'API per le [direzioni post Route](/rest/api/maps/route/postroutedirections). Per ulteriori informazioni sui punti di supporto, vedere la [documentazione dell'API directions Route](/rest/api/maps/route/postroutedirections#supportingpoints). 

Quando si chiama l' [API delle direzioni post Route](/rest/api/maps/route/postroutedirections), è possibile impostare il tempo minimo di deviazione o i vincoli di distanza, insieme ai punti di supporto. Usare questi parametri se si vogliono offrire route alternative, ma si vuole anche limitare il tempo di viaggio. Quando si usano questi vincoli, le route alternative seguiranno la route di riferimento dal punto di origine per il tempo o la distanza specificata. In altre parole, le altre route divergeranno dalla route di riferimento in base ai vincoli specificati.

L'immagine seguente è un esempio di rendering di route alternative con limiti di deviazione specificati per l'ora e la distanza.

![Route alternative](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Usare il servizio di routing in un'app Web

Azure Maps Web SDK fornisce un [modulo del servizio](/javascript/api/azure-maps-rest/). Questo modulo è una libreria helper che semplifica l'uso delle API REST di Maps di Azure nelle applicazioni Web o Node.js, usando JavaScript o TypeScript. Il modulo del servizio può essere usato per eseguire il rendering delle route restituite sulla mappa. Il modulo determina automaticamente l'API da usare con le richieste GET e POST.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere:

> [!div class="nextstepaction"]
> [Servizio di pianificazione percorso di Mappe di Azure](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Come usare il modulo del servizio](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Mostra route sulla mappa](./map-route.md)

> [!div class="nextstepaction"]
> [Pacchetto NPM di Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )