---
title: Esercitazione per C# sull'ordinamento dei risultati - Ricerca di Azure
description: Questa esercitazione si basa sul progetto "Paginazione dei risultati della ricerca - Ricerca di Azure" per aggiungere l'ordinamento dei risultati della ricerca. Vengono fornite informazioni su come ordinare i risultati in base a una proprietà primaria e, per i risultati con la stessa proprietà primaria, come ordinare i risultati in base a una proprietà secondaria. Vengono infine fornite informazioni su come ordinare i risultati in base a un profilo di punteggio.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 684ce33e5ecf587aa2030a817680f2d405225117
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327655"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>Esercitazione per C#: Ordinare i risultati - Ricerca di Azure

Fino a questo punto nella serie di esercitazioni, i risultati vengono restituiti e visualizzati in base a un ordine predefinito. Può trattarsi dell'ordine in cui si trovano i dati o eventualmente di un _profilo di punteggio_ che è stato definito e che verrà usato quando non vengono specificati parametri di ordinamento. In questa esercitazione verrà illustrato come ordinare i risultati in base a una proprietà primaria e quindi, per i risultati con la stessa proprietà primaria, come ordinare tale selezione in base a una proprietà secondaria. In alternativa all'ordinamento basato su valori numerici, nell'esempio finale viene illustrato come ordinare i risultati in base a un profilo di punteggio personalizzato. Verrà anche illustrata in dettaglio la visualizzazione dei _tipi complessi_.

Per poter confrontare facilmente i risultati restituiti, questo progetto si basa sul progetto di scorrimento infinito creato in [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca di Azure](tutorial-csharp-paging.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Ordinare i risultati in base a un'unica proprietà
> * Ordinare i risultati in base a più proprietà
> * Filtrare i risultati in base a una distanza da un punto geografico
> * Ordinare i risultati in base a un profilo di punteggio

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

Disporre della versione con scorrimento infinito del progetto [Esercitazione per C#: Paginazione dei risultati della ricerca - Ricerca di Azure](tutorial-csharp-paging.md) attivo e in esecuzione. Questo progetto può essere una versione personalizzata oppure può essere installato da GitHub: [Creare la prima app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Ordinare i risultati in base a un'unica proprietà

Quando si ordinano i risultati in base a una proprietà, ad esempio la valutazione di un albergo, non si vuole solo che i risultati siano ordinati, ma si vuole anche conferma che l'ordine sia corretto. Se quindi si ordinano i risultati in base alla valutazione, la visualizzazione dovrebbe includere anche la valutazione.

In questa esercitazione si aggiungeranno anche altre informazioni alla visualizzazione dei risultati per ogni albergo, ovvero la tariffa più bassa e quella più alta per camera. Durante l'analisi dell'ordinamento verranno inoltre aggiunti valori per essere certi che la chiave dell'ordinamento venga inclusa nella visualizzazione.

Per abilitare l'ordinamento, non è necessario modificare alcun modello. La visualizzazione e il controller devono essere aggiornati. Per iniziare, aprire il controller home.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Aggiungere la proprietà OrderBy ai parametri di ricerca

1. Per ordinare i risultati in base a una singola proprietà numerica, è sufficiente impostare il parametro **OrderBy** sul nome della proprietà. Nel metodo **Index(SearchData model)** aggiungere la riga seguente ai parametri di ricerca.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > L'ordine predefinito è crescente, anche se è possibile aggiungere **asc** alla proprietà per renderlo più evidente. Per specificare l'ordine decrescente, aggiungere **desc**.

2. A questo punto, eseguire l'app e immettere un qualsiasi termine di ricerca comune. I risultati possono o meno essere visualizzati nell'ordine corretto, dal momento che né lo sviluppatore né l'utente possono verificarli facilmente.

3. Bisogna quindi chiarire che i risultati sono ordinati in base alla valutazione. Sostituire innanzitutto le classi **box1** e **box2** nel file hotels.css con le classi seguenti, completamente nuove e necessarie per questa esercitazione.

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >I browser in genere memorizzano i file CSS nella cache. Può quindi capitare che venga usato un file CSS obsoleto e che le modifiche apportate vengano ignorate. Una soluzione valida per ovviare a questo problema consiste nell'aggiungere al collegamento una stringa di query con un parametro version. Ad esempio:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Aggiornare il numero di versione se si ritiene che il browser usi un file CSS obsoleto.

4. Aggiungere la proprietà **Rating** al parametro **Select** nel metodo **Index(SearchData model)** .

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Aprire la visualizzazione (index.cshtml) e sostituire il ciclo di rendering ( **&lt;!-- Show the hotel data. --&gt;** ) con il codice seguente.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. La valutazione deve essere disponibile sia nella prima pagina visualizzata che nelle pagine successive, che vengono chiamate tramite lo scorrimento infinito. Nella seconda di queste due situazioni, è necessario aggiornare sia l'azione **Next** nel controller che la funzione **scrolled** nella visualizzazione. Iniziando con il controller, modificare il metodo **Next** sostituendolo con il codice seguente. Questo codice consente di creare e comunicare il testo della valutazione.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. A questo punto, aggiornare la funzione **scrolled** nella visualizzazione in modo da visualizzare il testo della valutazione.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Eseguire di nuovo l'app. Cercare un termine comune, ad esempio "wifi", e verificare che i risultati vengano visualizzati in ordine decrescente in base alla valutazione dell'albergo.

    ![Ordinamento basato sulla valutazione](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Si noterà che la valutazione è identica per diversi alberghi, di conseguenza anche in questo caso vengono visualizzati nell'ordine arbitrario in cui sono stati trovati i dati.

    Prima di vedere come aggiungere un secondo livello di ordinamento, è necessario aggiungere il codice per visualizzare le tariffe delle camere. Questo codice viene aggiunto non solo per illustrare l'estrazione dei dati da un _tipo complesso_, ma anche per esaminare l'ordinamento dei risultati in base al prezzo (probabilmente il più economico per primo).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Aggiungere le tariffe delle camere alla visualizzazione

1. Aggiungere proprietà con le tariffe più alte e più basse al modello Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Calcolare le tariffe delle camere alla fine dell'azione **Index(SearchData model)** , nel controller home. Aggiungere i calcoli dopo l'archiviazione dei dati temporanei.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Aggiungere la proprietà **Rooms** al parametro **Select** nel metodo dell'azione **Index(SearchData model)** del controller.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Modificare il ciclo di rendering nella visualizzazione in modo da visualizzare le tariffe per la prima pagina di risultati.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Modificare il metodo **Next** nel controller home per comunicare le tariffe per le pagine di risultati successive.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Aggiornare la funzione **scrolled** nella visualizzazione, per gestire il testo delle tariffe delle camere.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Eseguire l'app e verificare che le tariffe delle camere siano visualizzate.

    ![Visualizzazione delle tariffe delle camere](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

La proprietà **OrderBy** dei parametri di ricerca non accetterà una voce come **Rooms.BaseRate** per fornire la tariffa più bassa, neanche se le camere fossero già ordinate in base alla tariffa. In questo caso, le camere non sono ordinate in base alla tariffa. Per visualizzare gli alberghi nel set di dati di esempio, ordinati in base alla tariffa della camera, è necessario ordinare i risultati nel controller home e inviarli alla visualizzazione nell'ordine desiderato.

## <a name="order-results-based-on-multiple-values"></a>Ordinare i risultati in base a più valori

La questione è ora come distinguere tra alberghi con la stessa valutazione. Una buona soluzione consiste nell'ordinare in base alla data dell'ultima ristrutturazione dell'albergo. In questo modo gli alberghi rinnovati più di recente compariranno per primi nei risultati.

1. Per aggiungere un secondo livello di ordinamento, modificare le proprietà **OrderBy** e **Select** nel metodo **Index(SearchData model)** in modo da includere la proprietà **LastRenovationDate**.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Nell'elenco **OrderBy** è possibile aggiungere un numero qualsiasi di proprietà. Nel caso in cui gli alberghi abbiano la stessa valutazione e siano stati ristrutturati nello stesso periodo, è possibile aggiungere una terza proprietà per distinguerli ulteriormente.

2. Anche in questo caso, per essere sicuri che l'ordinamento sia corretto, è necessario che la data di ristrutturazione sia inclusa nella visualizzazione. Per una ristrutturazione è probabilmente sufficiente l'anno. Modificare il ciclo di rendering nella visualizzazione sostituendolo con il codice seguente.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Modificare il metodo **Next** nel controller home per trasferire il componente dell'anno della data dell'ultima ristrutturazione.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Modificare la funzione **scrolled** nella visualizzazione in modo da visualizzare il testo relativo alla ristrutturazione.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Eseguire l'app. Cercare un termine comune, come "pool" o "view", quindi verificare che gli alberghi con la stessa valutazione siano ora elencati in ordine decrescente in base alla data di ristrutturazione.

    ![Ordinamento in base alla data di ristrutturazione](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrare i risultati in base a una distanza da un punto geografico

Valutazione e data di ristrutturazione sono esempi di proprietà ideali per la visualizzazione in ordine decrescente. Un elenco alfabetico costituisce un esempio di uso corretto dell'ordine crescente. Se, ad esempio, esistesse una sola proprietà **OrderBy** impostata su **HotelName**, i risultati verrebbero visualizzati in ordine alfabetico. Per i dati dell'esempio, però, è più appropriata la distanza da un punto geografico.

Per visualizzare i risultati in base alla distanza geografica, sono necessari diversi passaggi.

1. Filtrare tutti gli alberghi non compresi in un raggio specificato dal punto indicato, immettendo un filtro con i parametri di longitudine, latitudine e raggio. La longitudine viene assegnata inizialmente alla funzione POINT. Il raggio è espresso in chilometri.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Il filtro illustrato in precedenza _non_ consente di ordinare i risultati in base alla distanza, ma rimuove gli outlier. Per ordinare i risultati, immettere un'impostazione **OrderBy** che specifica il metodo geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Anche se i risultati sono stati restituiti da Ricerca di Azure usando un filtro di distanza, la distanza calcolata tra i dati e il punto specificato _non_ viene restituita. Ricalcolare questo valore nella visualizzazione o nel controller se si vuole includerlo nei risultati.

    Il codice seguente consente di calcolare la distanza tra due punti lat/lon.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. A questo punto è necessario unire questi concetti. Questi frammenti di codice sono però utili solo ai fini di questa esercitazione. La creazione di un'app basata su mappa costituisce un esercizio per il lettore. Per approfondire questo esempio, provare a immettere un nome di città con un raggio oppure a localizzare un punto su una mappa e selezionare un raggio. Per saperne di più su queste opzioni, vedere le risorse seguenti:

* [Documentazione di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/)
* [Trovare un indirizzo usando il servizio di ricerca di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Ordinare i risultati in base a un profilo di punteggio

Gli esempi forniti finora nell'esercitazione mostrano come ordinare i risultati in base a valori numerici (valutazione, data di ristrutturazione, distanza geografica), offrendo un processo di ordinamento _esatto_. Tuttavia, con alcune ricerche e alcuni dati non è semplice eseguire un confronto tra due elementi dati. Ricerca di Azure include il concetto di _punteggio_. È possibile specificare _profili di punteggio_ per un set di dati utilizzabile per confronti più complessi e di qualità, caratteristica essenziale quando si confrontano dati basati su testo per decidere quelli da visualizzare per primi.

I profili di punteggio non sono definiti dagli utenti, ma in genere dagli amministratori di un set di dati. Per i dati degli alberghi sono stati configurati diversi profili di punteggio. Verrà ora esaminato come definire un profilo di punteggio e provare a scrivere codice per eseguire ricerche basate sui profili.

### <a name="how-scoring-profiles-are-defined"></a>Modalità di definizione dei profili di punteggio

Esaminare tre esempi di profili di punteggio e considerare in che modo ognuno _deve_ influire sull'ordine dei risultati. I profili non vengono scritti dagli sviluppatori di app, ma dall'amministratore dei dati, tuttavia è utile esaminarne la sintassi.

1. Si tratta del profilo di punteggio predefinito per il set di dati degli alberghi, usato quando non si specifica alcun parametro **OrderBy** o **ScoringProfile**. Questo profilo consente di incrementare il _punteggio_ di un albergo se il testo cercato è presente nel nome, nella descrizione o nell'elenco di tag (servizi) di un albergo. Notare come i pesi del punteggio favoriscono determinati campi. Se il testo da cercare è presente in un altro campo, non elencato di seguito, avrà peso pari a 1. Ovviamente, maggiore è il punteggio, migliore sarà la posizione di un risultato nella visualizzazione.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Il profilo di punteggio seguente consente di incrementare il punteggio in modo significativo se un parametro specificato include uno o più voci dell'elenco di tag ("amenities"). L'aspetto fondamentale di questo profilo è che è _necessario_ specificare un parametro che contiene testo. Se il parametro è vuoto o non viene specificato, verrà generato un errore.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. Nel terzo esempio è il parametro rating a incrementare significativamente il punteggio. Anche la data dell'ultima ristrutturazione contribuisce al punteggio, ma solo se risale a meno di 730 giorni (2 anni) dalla data corrente.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    È il momento di verificare se i profili funzionano come previsto.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Aggiungere codice alla visualizzazione per confrontare i profili

1. Aprire il file index.cshtml e sostituire la sezione &lt;body&gt; con il codice seguente.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Aprire il file SearchData.cs e sostituire la classe **SearchData** con il codice seguente.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Aprire il file hotels.css e aggiungere le classi HTML seguenti.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Aggiungere codice al controller per specificare un profilo di punteggio

1. Aprire il file del controller home. Aggiungere l'istruzione **using** seguente per facilitare la creazione di elenchi.

    ```cs
    using System.Linq;
    ```

2.  Per questo esempio è necessario che la chiamata iniziale a **Index** non si limiti a restituire la visualizzazione iniziale. Il metodo ora cerca fino a 20 servizi da includere nella visualizzazione.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. Sono necessari due metodi privati, uno per salvare i facet nell'archivio temporaneo e l'altro per ripristinarli dall'archivio temporaneo e popolare un modello.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. È necessario impostare i parametri **OrderBy** e **ScoringProfile** secondo necessità. Sostituire il metodo **Index(SearchData model)** esistente con il codice seguente.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Leggere attentamente i commenti relativi a ognuna delle selezioni di **switch**.

5. Non è necessario apportare modifiche all'azione **Next** se è stato completato il codice aggiuntivo per la sezione precedente sull'ordinamento basato su più proprietà.

### <a name="run-and-test-the-app"></a>Eseguire e testare l'app

1. Eseguire l'app. La visualizzazione includerà un set completo di servizi.

2. Per l'ordinamento, se si seleziona "By numerical Rating" verrà applicato l'ordinamento numerico già implementato in questa esercitazione, in cui la data di ristrutturazione consente di decidere tra alberghi con la stessa valutazione.

![Ordinamento di "beach" basato sulla valutazione](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Provare ora il profilo "By amenities". Effettuare varie selezioni di servizi e verificare che gli alberghi con questi servizi occupino una posizione di rilievo nell'elenco risultati.

![Ordinamento di "beach" basato sul profilo](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Provare il profilo "By Renovated date/Rating profile" per verificare se i risultati sono quelli previsti. Solo agli alberghi ristrutturati di recente dovrebbe essere assegnata una priorità _freshness_.

### <a name="resources"></a>Risorse

Per altre informazioni, vedere l'articolo seguente [Aggiungere profili di punteggio a un indice di Ricerca di Azure](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Risultati

Tenere conto delle considerazioni seguenti riguardo a questo progetto:

* Gli utenti si aspettano che i risultati della ricerca siano ordinati e che quelli più pertinenti siano visualizzati per primi.
* I dati devono essere strutturati in modo da semplificare l'ordinamento. Non è stato possibile ordinare facilmente prima in base alla tariffa più bassa perché i dati non sono strutturati per consentire l'ordinamento senza codice aggiuntivo.
* Possono esistere più livelli di ordinamento, per distinguere tra i risultati che hanno lo stesso valore a un livello di ordinamento superiore.
* Per alcuni risultati viene applicato naturalmente l'ordine crescente, ad esempio la distanza da un punto, mentre per altri l'ordine decrescente, ad esempio la valutazione degli ospiti.
* È possibile definire profili di punteggio quando i confronti numerici per un set di dati non sono disponibili o abbastanza efficaci. L'assegnazione di un punteggio a ogni risultato consente di ordinare e visualizzare i risultati in modo intelligente.

## <a name="next-steps"></a>Passaggi successivi

Questa serie di esercitazioni per C# è stata completata. A questo punto si sono acquisite utili informazioni sulle API di Ricerca di Azure.

Per altre informazioni di riferimento ed esercitazioni, provare a esplorare [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) o a eseguire le altre esercitazioni disponibili nella [documentazione di Ricerca di Azure](https://docs.microsoft.com/azure/search/).
