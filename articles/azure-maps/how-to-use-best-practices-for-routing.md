---
title: Procedure consigliate per il servizio di reindirizzamento delle mappe di Azure Mappe di Microsoft Azure
description: Informazioni su come eseguire il route in modo efficiente usando il servizio Route da Microsoft Azure Maps.Learn how to route efficiently by using Route Service from Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335409"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Procedure consigliate per il servizio Azure Maps RouteBest practices for Azure Maps Route service

Le API Route Directions e Route Matrix nel [servizio Diorientamento](https://docs.microsoft.com/rest/api/maps/route) mappe di Azure possono essere usate per calcolare gli orari di arrivo stimati (ETA) per ogni route richiesta. Le API del percorso considerano fattori quali le informazioni sul traffico in tempo reale e i dati cronologici sul traffico, ad esempio le velocità tipiche della strada nel giorno della settimana e nell'ora del giorno richiesti. Le API restituiscono i percorsi più brevi o più veloci disponibili per più destinazioni in un momento in sequenza o in ordine ottimizzato, in base al tempo o alla distanza. Gli utenti possono anche richiedere percorsi specializzati e dettagli per escursionisti, ciclisti e veicoli commerciali come camion. In questo articolo verranno create le procedure consigliate per chiamare Azure Maps Route Service e verranno fornite le procedure per le procedure:In this article, we'll share the best practices to call Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route), and you'll learn how-to:

* Scegliere tra le API Route Directions e l'API Matrix Routing
* Richiedi tempi di viaggio storici e previsti, basati su dati di traffico storici e in tempo reale
* Richiedi dettagli percorso, come tempo e distanza, per l'intero percorso e ogni tappa del percorso
* Richiedi percorso per un veicolo commerciale, come un camion
* Richiedere informazioni sul traffico lungo un percorso, come inceppamenti e informazioni sul pedaggio
* Richiedere un percorso costituito da una o più fermate (waypoint)
* Ottimizzare un percorso di una o più fermate per ottenere l'ordine migliore per visitare ogni fermata (waypoint)
* Ottimizzare percorsi alternativi utilizzando i punti di supporto. Ad esempio, offrire percorsi alternativi che superano una stazione di ricarica di veicoli elettrici.
* Usare il servizio Route con Azure Maps Web SDKUse the [Route Service](https://docs.microsoft.com/rest/api/maps/route) with the Azure Maps Web SDK

## <a name="prerequisites"></a>Prerequisiti

Per effettuare chiamate alle API di Azure Maps, sono necessari un account di Azure Maps e una chiave. Per ulteriori informazioni, vedere [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) e Ottenere una chiave [primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). La chiave primaria è nota anche come chiave di sottoscrizione primaria o chiave di sottoscrizione.

Per informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe](./how-to-manage-authentication.md)di Azure.For information about authentication in Azure Maps, see Manage authentication in Azure Maps. Per ulteriori informazioni sulla copertura del servizio di instradamento, vedere la [copertura](routing-coverage.md)del routing .

Questo articolo usa [l'app Postman](https://www.postman.com/downloads/) per creare chiamate REST, ma puoi scegliere qualsiasi ambiente di sviluppo API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Scegliere tra Direzioni percorso e Instradamento matrice

Le API Route Directions restituiscono istruzioni, inclusi il tempo di percorrenza e le coordinate per un percorso del percorso. L'API Matrice percorso consente di calcolare il tempo di viaggio e le distanze per un set di percorsi definiti dalle posizioni di origine e di destinazione. Per ogni origine specifica, l'API Matrix calcola il costo (tempo di viaggio e distanza) del routing da tale origine a ogni destinazione specificata. Tutte queste API consentono di specificare parametri quali l'orario di partenza desiderato, gli orari di arrivo e il tipo di veicolo, come l'auto o il camion. Tutti utilizzano i dati sul traffico in tempo reale o predittivi di conseguenza per restituire i percorsi più ottimali.

Considerare la possibilità di chiamare le API Route Directions se lo scenario deve:Consider calling Route Directions APIs if your scenario is to:

* Richiedi il percorso di guida più breve o più veloce tra due o più luoghi conosciuti, per ottenere orari di arrivo precisi per i tuoi veicoli di consegna.
* Richiedere indicazioni dettagliate sul percorso, inclusa la geometria del percorso, per visualizzare i percorsi sulla mappa
* Dato un elenco di sedi cliente, calcolare il percorso più breve possibile per visitare ogni ubicazione del cliente e tornare all'origine. Questo scenario è comunemente noto come il problema del venditore in viaggio. Puoi passare fino a 150 waypoint (interruzioni) in una richiesta.
* Invia batch di query all'API Route Directions Batch usando una sola chiamata API.

Considerare la possibilità di chiamare l'API di routing a matrice se lo scenario deve:Consider calling Matrix Routing API if your scenario is to:

* Calcolare il tempo di viaggio o la distanza tra un insieme di origini e destinazioni. Ad esempio, hai 12 autisti e devi trovare l'autista disponibile più vicino per ritirare la consegna di cibo dal ristorante.
* Ordina i percorsi potenziali in base alla distanza di viaggio o al tempo effettivo. L'API Matrix restituisce solo tempi di percorrenza e distanze per ogni combinazione di origine e destinazione.
* Dati del cluster in base al tempo di percorrenza o alle distanze. Ad esempio, la tua azienda ha 50 dipendenti, trova tutti i dipendenti che vivono entro 20 minuti di tempo di guida dal tuo ufficio.

Ecco un confronto per mostrare alcune funzionalità delle API Route Directions e Matrix:

| API di Mappe di AzureAzure Maps API | Numero massimo di query nella richiesta | Evitare le aree | Percorsi di autocarri e veicoli elettrici | waypoint e ottimizzazione Salesman viaggianti | Punti di supporto |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Get Route Directions | 1 | | X | X | |
| Indicazioni post percorso | 1 | X | X | X | X |
| Batch di direzioni post-ciclo di lavorazione | 700 | | X | X | |
| Post Route Matrix | 700 | | X | | |

Per altre informazioni sulle funzionalità di routing dei veicoli elettrici, vedere l'esercitazione su come [instradare i veicoli elettrici usando i blocchi appunti](tutorial-ev-routing.md)di Azure con Python.

## <a name="request-historic-and-real-time-data"></a>Richiedi dati storici e in tempo reale

Per impostazione predefinita, il servizio Route presuppone che la modalità di viaggio sia un'auto e l'ora di partenza è ora. Restituisce route in base alle condizioni del traffico in tempo reale, a meno che una richiesta di calcolo del percorso non specifichi diversamente. Le restrizioni al traffico dipendenti dal tempo fisse, ad esempio "Le svolte a sinistra non sono consentite tra le 16:00 e le 18:00" vengono acquisite e verranno considerate dal motore di routing. Le chiusure stradali, come i lavori stradali, saranno prese in considerazione a meno che tu non richieda specificamente un percorso che ignori il traffico live corrente. Per ignorare il traffico `traffic` `false` corrente, impostare su nella richiesta API.

Il valore di calcolo della route **travelTimeInSeconds** include il ritardo dovuto al traffico. Viene generato sfruttando i dati del tempo di viaggio attuali e storici, quando l'orario di partenza è impostato su ora. Se l'orario di partenza è impostato in futuro, le API restituiscono i tempi di percorrenza previsti in base ai dati cronologici.

Se nella richiesta si include il parametro **computeTravelTimeFor-all,** l'elemento di riepilogo nella risposta avrà i seguenti campi aggiuntivi, incluse le condizioni del traffico cronologico:

| Elemento | Descrizione|
| :--- | :--- |
| noTrafficTravelTimeInSecondi | Tempo di percorrenza stimato calcolato come se non ci fosse alcun ritardo sul percorso a causa delle condizioni del traffico, ad esempio a causa della congestione |
| historicTrafficTravelTimeSecondi | Tempo di percorrenza stimato calcolato utilizzando dati sul traffico cronologico dipendenti dal tempo |
| LiveTrafficIncidentsTravelTimeInSecondi | Tempo di percorrenza stimato calcolato utilizzando dati di velocità in tempo reale |

Nelle sezioni successive viene illustrato come effettuare chiamate alle API Route usando i parametri descritti.

### <a name="sample-query"></a>Query di esempio

Nel primo esempio sotto l'orario di partenza è impostato sul futuro, al momento della scrittura.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

La risposta contiene un elemento di riepilogo, come quello riportato di seguito. Poiché l'ora di partenza è impostata sul futuro, il valore **trafficDelayInSeconds** è zero. Il valore **travelTimeInSeconds** viene calcolato utilizzando dati cronologici dipendenti dal tempo. Quindi, in questo caso, il valore **di travelTimeInSeconds** è uguale al valore **historicTrafficTravelTimeInSeconds.**

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

Nel secondo esempio riportato di seguito, abbiamo una richiesta di routing in tempo reale, dove l'ora di partenza è ora. Non è specificato in modo esplicito nell'URL perché è il valore predefinito.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

La risposta contiene un riepilogo come illustrato di seguito. A causa delle congestione, il valore **trafficDelaysInSeconds** è maggiore di zero. È anche maggiore di **historicTrafficTravelTimeInSeconds**.

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

## <a name="request-route-and-leg-details"></a>Richiedi i dettagli del percorso e della gamba

Per impostazione predefinita, il servizio Route restituirà una matrice di coordinate. La risposta conterrà le coordinate che costituiscono `points`il percorso in un elenco denominato . La risposta del percorso include anche la distanza dall'inizio del percorso e il tempo trascorso stimato. Questi valori possono essere utilizzati per calcolare la velocità media per l'intero percorso.

L'immagine seguente `points` mostra l'elemento.

<center>

![elenco punti](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Espandere `point` l'elemento per visualizzare l'elenco delle coordinate per il percorso:

<center>

![elenco punti](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

Le API Route Directions supportano diversi formati di istruzioni che possono essere utilizzate specificando il parametro **instructionsType.** Per formattare le istruzioni per un'elaborazione semplice del computer, utilizzare **instructionsType .coded**. Utilizzare **instructionsType: contrassegnato** con tag per visualizzare le istruzioni come testo per l'utente. Inoltre, le istruzioni possono essere formattate come testo in cui alcuni elementi delle istruzioni sono contrassegnati e l'istruzione viene presentata con una formattazione speciale. Per ulteriori informazioni, vedere [l'elenco dei tipi di istruzioni supportati.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)

Quando vengono richieste istruzioni, la risposta `guidance`restituisce un nuovo elemento denominato . L'elemento `guidance` contiene due informazioni: le direzioni di svolta per svolta e le istruzioni riassunte.

<center>

![Tipo di istruzioni](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

L'elemento `instructions` contiene le indicazioni di svolta `instructionGroups` per il viaggio, e le istruzioni sono riepilogate. Ogni riepilogo delle istruzioni copre un segmento del viaggio che potrebbe coprire più strade. Le API possono restituire dettagli per le sezioni di una route. ad esempio, l'intervallo di coordinate di un ingorgo o la velocità corrente del traffico.

<center>

![Istruzioni per turni](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Istruzioni riepilogate](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Richiedi un percorso per un veicolo commerciale

Le API di routing di Azure Maps supportano il routing dei veicoli commerciali, che copre il routing dei camion commerciali. Le API considerano i limiti specificati. Ad esempio, l'altezza e il peso del veicolo, e se il veicolo trasporta merci pericolose. Ad esempio, se un veicolo trasporta infiammabile, il motore di routing evita alcune gallerie vicine ad aree residenziali.

### <a name="sample-query"></a>Query di esempio

La richiesta di esempio riportata di seguito interroga un percorso per un camion commerciale. Il camion trasporta materiale di scarto pericoloso di classe 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

L'API Route restituisce le indicazioni che soddisfano le dimensioni del camion e dei rifiuti pericolosi. È possibile leggere le istruzioni `guidance` del percorso espandendo l'elemento.

<center>

![Camion con hazwaste di classe 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Query di esempio

Se si modifica la classe Hazmat degli Stati Uniti, dalla query precedente, verrà indicato un percorso diverso per adattarsi a questa modifica.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

La risposta qui sotto è per un camion che trasporta un materiale pericoloso di classe 9, che è meno pericoloso di un materiale pericoloso di classe 1. Quando espandi l'elemento `guidance` per leggere le indicazioni stradali, noterai che le direzioni non sono uguali. Ci sono ulteriori istruzioni di percorso per il camion che trasporta il materiale pericoloso di classe 1.

<center>

![Camion con classe 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Richiedere informazioni sul traffico lungo un percorso

Con le API Route Direction di Azure Maps, gli sviluppatori `sectionType` possono richiedere dettagli per ogni tipo di sezione includendo il parametro nella richiesta. Ad esempio, è possibile richiedere le informazioni sulla velocità per ogni segmento di ingorgo. Fare riferimento [all'elenco di valori per la chiave sectionType](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) per informazioni sui vari dettagli che è possibile richiedere.

### <a name="sample-query"></a>Query di esempio

La query seguente `sectionType` `traffic`imposta l'oggetto su . Richiede le sezioni che contengono informazioni sul traffico da Seattle a San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

La risposta contiene le sezioni adatte al traffico lungo le coordinate specificate.

<center>

![sezioni di traffico](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Questa opzione può essere utilizzata per colorare le sezioni durante il rendering della mappa, come nell'immagine seguente: 

<center>

![sezioni di traffico](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Calcolare e ottimizzare un percorso multi-stop

Mappe di Azure offre attualmente due forme di ottimizzazione dei percorsi:Azure Maps currently provides two forms of route optimizations:

* Ottimizzazioni basate sul tipo di percorso richiesto, senza modificare l'ordine dei waypoint. I tipi [di percorso supportati sono](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype) disponibili qui

* Ottimizzazione del venditore in viaggio, che cambia l'ordine dei waypoint per ottenere l'ordine migliore da visitare ogni fermata

Per il routing multi-stop, è possibile specificare fino a 150 waypoint in una singola richiesta di route. Le posizioni delle coordinate iniziale e finale possono essere le stesse, come nel caso di un percorso di andata e ritorno. Ma è necessario fornire almeno un waypoint aggiuntivo per effettuare il calcolo del percorso. I waypoint possono essere aggiunti alla query tra le coordinate di origine e di destinazione.

Se si desidera ottimizzare l'ordine migliore per visitare i waypoint specificati, è necessario specificare **computeBestOrder: true**. Questo scenario è noto anche come il problema di ottimizzazione del venditore in viaggio.

### <a name="sample-query"></a>Query di esempio

La query seguente richiede il percorso per `computeBestOrder` sei `false`waypoint, con il parametro impostato su . È anche il valore predefinito `computeBestOrder` per il parametro.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

La risposta descrive la lunghezza del percorso di 140.851 metri e che ci vorrebbero 9.991 secondi per percorrere quel percorso.

<center>

![Risposta non ottimizzata](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

L'immagine seguente illustra il percorso risultante da questa query. Questo percorso è un possibile percorso. Non è il percorso ottimale in base al tempo o alla distanza.

<center>

![Immagine non ottimizzata](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

L'ordine del waypoint è: 0, 1, 2, 3, 4, 5 e 6.

### <a name="sample-query"></a>Query di esempio

La query seguente richiede il percorso per gli stessi sei waypoint, come nell'esempio precedente. Questa volta, `computeBestOrder` il `true` parametro impostato su (ottimizzazione venditore in viaggio).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

La risposta descrive la lunghezza del percorso di 91.814 metri e che ci vorrebbero 7.797 secondi per percorrere quel percorso. La distanza di viaggio e il tempo di percorrenza sono entrambi inferiori qui perché l'API ha restituito il percorso ottimizzato.

<center>

![Risposta non ottimizzata](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

L'immagine seguente illustra il percorso risultante da questa query.

<center>

![Immagine non ottimizzata](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

Il percorso ottimale ha il seguente ordine waypoint: 0, 5, 1, 2, 4, 3 e 6.

>[!TIP]
> Le informazioni ottimizzate sull'ordine dei waypoint del servizio di routing forniscono un set di indici. Questi escludono l'origine e gli indici di destinazione. È necessario incrementare questi valori di 1 per tenere conto dell'origine. Quindi, aggiungi la tua destinazione alla fine per ottenere l'elenco completo dei waypoint ordinati.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Calcolare e biasire percorsi alternativi utilizzando i punti di supporto

Potrebbero verificarsi situazioni in cui si desidera ricostruire un percorso per calcolare zero o più percorsi alternativi per un percorso di riferimento. Ad esempio, è possibile mostrare ai clienti percorsi alternativi che passano il punto vendita al dettaglio. In questo caso, è necessario inclinare una posizione utilizzando i punti di supporto. Di seguito sono riportati i passaggi per disperdere una posizione:

1. Calcolare un percorso così com'è e ottenere il percorso dalla risposta del percorso
2. Utilizzare il percorso per trovare le posizioni desiderate lungo o vicino al percorso del percorso. Ad esempio, è possibile usare [l'API Point of Interest di](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) Azure Maps o eseguire query sui propri dati nel database.  
3. Ordinare le posizioni in base alla distanza dall'inizio del percorso
4. Aggiungere queste posizioni come punti di supporto in una nuova richiesta di percorso [all'API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Per ulteriori informazioni sui punti di supporto, vedere la [documentazione dell'API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints). 

Quando si chiama [l'API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections), è possibile impostare il tempo di deviazione minimo o i vincoli di distanza, insieme ai punti di supporto. Utilizzare questi parametri se si desidera offrire percorsi alternativi, ma si desidera anche limitare il tempo di percorrenza. Quando vengono utilizzati questi vincoli, i percorsi alternativi seguiranno il percorso di riferimento dal punto di origine per il tempo o la distanza specificati. In altre parole, le altre route si discostano dalla route di riferimento in base ai vincoli specificati.

L'immagine seguente è un esempio di rendering di percorsi alternativi con limiti di deviazione specificati per il tempo e la distanza.

<center>

![Percorsi alternativi](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Usare il servizio routing in un'app Web

Azure Maps Web SDK fornisce un [modulo di servizio](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest). Questo modulo è una libreria helper che semplifica l'uso delle API REST di Azure Maps nelle applicazioni Web o Node.js, usando JavaScript o TypeScript.This module is a helper library that makes it easy to use the Azure Maps REST APIs in web or Node.js applications, using JavaScript or TypeScript. Il modulo Servizio può essere utilizzato per eseguire il rendering dei percorsi restituiti sulla mappa. Il modulo determina automaticamente l'API da utilizzare con le richieste GET e POST.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, si prega di consultare:

> [!div class="nextstepaction"]
> [Servizio Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Come utilizzare il modulo Servizio](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Mostra percorso sulla mappa](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure Maps NPM Package](https://www.npmjs.com/package/azure-maps-rest  )
