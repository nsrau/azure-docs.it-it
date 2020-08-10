---
title: Cercare un percorso usando i servizi di ricerca di Azure Maps
description: Informazioni sul servizio ricerca di Azure maps. Vedere come usare questo set di API per la geocodifica, la geocodifica inversa, le ricerche fuzzy e le ricerche inverse tra le strade.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 48dd0168f878a16e2eabe47151d0b09993d9f5f9
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037780"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Cercare un percorso usando i servizi di ricerca di Azure Maps

[Azure Maps servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) è un set di API RESTful progettate per aiutare gli sviluppatori a ricercare indirizzi, luoghi e elenchi aziendali per nome, categoria e altre informazioni geografiche. Oltre a supportare la geocodifica tradizionale, i servizi possono anche invertire gli indirizzi geocodificati e attraversare le strade in base a latitudine e longitudine. I valori di latitudine e Longitudine restituiti dalla ricerca possono essere usati come parametri in altri servizi di Azure Maps, ad esempio [Route](https://docs.microsoft.com/rest/api/maps/route) e servizi [meteorologici](https://docs.microsoft.com/rest/api/maps/weather) .

In questo articolo si apprenderà come:

* Richiedere le coordinate di latitudine e Longitudine per un indirizzo (geocode address location) usando l' [API di ricerca dell'indirizzo]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).
* Cercare un indirizzo o un punto di interesse (PDI) usando l' [API di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).
* Eseguire una [ricerca di indirizzi inversi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per tradurre la posizione delle coordinate in un indirizzo via.
* Tradurre la posizione delle coordinate in una strada incrociata umana comprensibile usando l' [API di ricerca inversa degli indirizzi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet).  In genere, questa operazione è necessaria per tenere traccia delle applicazioni che ricevono un feed GPS da un dispositivo o un asset e desiderano individuare la posizione della coordinata.

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un account Mappe di Azure](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Ottenere una chiave di sottoscrizione primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account), nota anche come chiave primaria o chiave di sottoscrizione

Questa esercitazione usa l'applicazione [Postman](https://www.postman.com/), ma è possibile scegliere un ambiente di sviluppo API diverso.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Richiedere Latitudine e Longitudine per un indirizzo (geocodifica)

In questo esempio si userà l' [API di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) di Azure Maps Get per convertire un indirizzo in coordinate di latitudine e longitudine. Questo processo viene definito anche *geocodifica*. Oltre a restituire le coordinate, la risposta restituirà anche le proprietà di indirizzo dettagliate, ad esempio via, CAP, municipalità e informazioni sul paese/area geografica.

>[!TIP]
>Se è presente un set di indirizzi per la geocodifica, è possibile usare l' [API batch di indirizzi di ricerca post](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) per inviare un batch di query in una singola chiamata API.

1. Aprire l'app Postman. Nella parte superiore dell'app Postman selezionare **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Collection** (Raccolta).  Assegnare un nome alla raccolta e selezionare **Create** (Crea). Questa raccolta verrà usata per il resto degli esempi in questo documento.

2. Per creare la richiesta, selezionare nuovamente **New** (Nuovo). Nella finestra **Create New** (Crea nuovo) selezionare **Request** (Richiesta). Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare la raccolta creata nel passaggio precedente e fare clic su **Save** (Salva).

3. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. In questa richiesta, è in corso la ricerca di un indirizzo specifico: `400 Braod St, Seattle, WA 98109` .

    Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Fare clic sul pulsante di **invio** blu. Il corpo della risposta conterrà i dati per una singola posizione.

5. A questo punto, si cercherà un indirizzo con più percorsi possibili. Nella sezione **params** impostare la `query` chiave su `400 Broad, Seattle` . Fare clic sul pulsante di **invio** blu.

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Cerca indirizzo":::

6. Successivamente, provare a impostare la `query` chiave su `400 Broa` .

7. Fare clic sul pulsante **Send** (Invia).  È ora possibile osservare che la risposta include risposte da più paesi. Per geopolarizzare i risultati nell'area pertinente per gli utenti, aggiungere sempre il maggior numero possibile di dettagli sulla località alla richiesta.

## <a name="using-fuzzy-search-api"></a>Uso dell'API di ricerca fuzzy

L' [API ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Azure Maps supporta le ricerche standard a riga singola e in formato libero. Si consiglia di usare l'API fuzzy di ricerca Maps di Azure quando non si conosce il tipo di input utente per una richiesta di ricerca.  L'input della query può essere un indirizzo completo o parziale. Può anche essere un token di punto di interesse, ad esempio il nome del punto di interesse, la categoria del punto di interesse o il nome del marchio. Inoltre, per migliorare la pertinenza dei risultati della ricerca, i risultati della query possono essere vincolati da una posizione delle coordinate e da un raggio oppure definendo un rettangolo di delimitazione.

>[!TIP]
>La maggior parte delle query di ricerca viene impostata per impostazione predefinita su maxFuzzyLevel = 1 per ottenere prestazioni e ridurre i risultati anomali È possibile modificare i livelli di confusione usando `maxFuzzyLevel` i `minFuzzyLevel` parametri o. Per ulteriori informazioni su `maxFuzzyLevel` e un elenco completo di tutti i parametri facoltativi, vedere [parametri URI di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy#uri-parameters) .

### <a name="search-for-an-address-using-fuzzy-search"></a>Cercare un indirizzo usando la ricerca fuzzy

In questo esempio verrà usata la ricerca fuzzy per cercare nell'intero mondo `pizza` .  Verrà quindi illustrato come eseguire una ricerca nell'ambito di un paese specifico. Infine, verrà illustrato come usare una posizione delle coordinate e un raggio per definire l'ambito di una ricerca in un'area specifica e limitare il numero di risultati restituiti.

>[!IMPORTANT]
>Per geopolarizzare i risultati nell'area pertinente per gli utenti, aggiungere sempre il maggior numero possibile di dettagli sulla località. Per altre informazioni, vedere [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Aprire l'app post, fare clic su **nuovo**e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella sezione precedente o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria. La richiesta deve essere simile all'URL seguente:

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >L'attributo _json_ nel percorso URL determina il formato della risposta. Questo articolo usa JSON per facilitarne l'uso e la leggibilità. Per trovare altri formati di risposta supportati, vedere la `format` definizione del parametro nella documentazione di riferimento per il [parametro URI](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

    La stringa di query ambigua per "pizza" ha restituito 10 [punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (PDI) nelle categorie "pizza" e "ristorante". Ogni risultato include dettagli quali l'indirizzo via, i valori di latitudine e longitudine, la porta di visualizzazione e i punti di ingresso per la posizione. I risultati sono ora diversi per questa query e non sono associati ad alcun percorso di riferimento.
  
    Nel passaggio successivo verrà usato il `countrySet` parametro per specificare solo i paesi o le aree per cui l'applicazione necessita di copertura. Per un elenco completo di paesi/aree geografiche supportate, vedere il [code coverage di ricerca](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

4. Il comportamento predefinito prevede la ricerca nell'intero mondo, restituendo potenzialmente risultati non necessari. Successivamente, si cercherà solo la Stati Uniti. Aggiungere la `countrySet` chiave alla sezione **params** e impostarne il valore su `US` . Impostando la `countrySet` chiave su `US` , i risultati vengono associati al Stati Uniti.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Cerca pizza nella Stati Uniti":::

    I risultati sono ora limitati dall'indicativo paese e la query restituisce le pizzerie negli Stati Uniti.

5. Per ottenere una ricerca ancora più mirata, è possibile eseguire ricerche nell'ambito di un Lat./Lon. coppia di coordinate. In questo esempio si userà Lat./Lon. dell'ago dello spazio di Seattle. Poiché si desidera restituire i risultati solo entro un raggio di 400 metri, verrà aggiunto il `radius` parametro. Inoltre, verrà aggiunto il `limit` parametro per limitare i risultati alle cinque posizioni più vicine.

    Nella sezione **params** aggiungere le coppie chiave/valore seguenti:

     | Chiave | Valore |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |
    | radius | 400 |
    | limit | 5|

6. Fare clic su **Send**. La risposta include i risultati per i ristoranti della pizza in prossimità dell'ago dello spazio di Seattle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Cercare un indirizzo tramite la ricerca di indirizzi inversa

L'API di [ricerca dell'indirizzo di ricerca]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per le mappe di Azure converte le coordinate in indirizzi leggibili. Questa API viene spesso usata per le applicazioni che utilizzano i feed GPS e vogliono individuare gli indirizzi in punti di coordinate specifici.

>[!IMPORTANT]
>Per geopolarizzare i risultati nell'area pertinente per gli utenti, aggiungere sempre il maggior numero possibile di dettagli sulla località. Per altre informazioni, vedere [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Se si dispone di un set di percorsi delle coordinate per la geocodifica inversa, è possibile usare l' [API batch inversa dell'indirizzo di ricerca post](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) per inviare un batch di query in una singola chiamata API.

In questo esempio verranno eseguite ricerche inverse utilizzando alcuni dei parametri facoltativi disponibili. Per l'elenco completo dei parametri facoltativi, vedere [parametri di ricerca inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. Nell'app post, fare clic su **nuovo**e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella prima sezione o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria. La richiesta deve essere simile all'URL seguente:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Fare clic su **Send**ed esaminare il corpo della risposta. Verrà visualizzato un risultato della query. La risposta include informazioni sull'indirizzo chiave per Safeco Field.
  
4. A questo punto, si aggiungeranno le coppie chiave/valore seguenti alla sezione **params** :

    | Chiave | Valore | Restituisce
    |-----|------------|------|
    | Numero | 1 |La risposta può includere il lato della strada (sinistra/destra) e anche una posizione di offset per il numero.|
    | returnSpeedLimit | true | Restituisce il limite di velocità in corrispondenza dell'indirizzo.|
    | returnRoadUse | true | Restituisce i tipi di utilizzo delle strade in corrispondenza dell'indirizzo. Per tutti i possibili tipi di utilizzo stradali, vedere [tipi di utilizzo delle strade](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Restituisce il tipo di corrispondenza. Per tutti i valori possibili, vedere [Risultati della ricerca di indirizzi inversi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Cerca in senso inverso.":::

5. Fare clic su **Send**ed esaminare il corpo della risposta.

6. Successivamente, si aggiungerà la `entityType` chiave e si imposterà il relativo valore su `Municipality` . La `entityType` chiave sostituirà la `returnMatchType` chiave nel passaggio precedente. Sarà inoltre necessario rimuovere `returnSpeedLimit` e `returnRoadUse` poiché vengono richieste informazioni sul comune.  Per tutti i tipi di entità possibili, vedere [tipi di entità](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Cerca entityType inverso.":::

7. Fare clic su **Send**. Confrontare i risultati con i risultati restituiti nel passaggio 5.  Poiché il tipo di entità richiesto è ora `municipality` , la risposta non include le informazioni sull'indirizzo via. Inoltre, l'oggetto restituito `geometryId` può essere usato per richiedere il poligono di limite tramite le mappe di Azure Get [API poligono di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

>[!TIP]
>Per ottenere altre informazioni su questi parametri e per altre informazioni su altri, vedere la [sezione parametri di ricerca inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Cercare Cross Street usando la ricerca di indirizzi inversi tra le vie

In questo esempio, si cercherà una strada incrociata in base alle coordinate di un indirizzo.

1. Nell'app post, fare clic su **nuovo**e selezionare **Richiedi**. Immettere un **Request Name** (Nome richiesta) per la richiesta. Selezionare l'insieme creato nella prima sezione o crearne uno nuovo, quindi selezionare **Salva**.

2. Selezionare il metodo **Get** http nella scheda generatore e immettere l'URL seguente. Per questa richiesta, e per altre indicate in questo articolo, sostituire `{Azure-Maps-Primary-Subscription-key}` con la chiave di sottoscrizione primaria. La richiesta deve essere simile all'URL seguente:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Cerca tra le strade.":::
  
3. Fare clic su **Send**ed esaminare il corpo della risposta. Si noterà che la risposta contiene un `crossStreet` valore di `Occidental Avenue South` .

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Azure Maps servizio di ricerca API REST](https://docs.microsoft.com/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Mappe di Azure servizio di ricerca procedure consigliate](how-to-use-best-practices-for-search.md)
