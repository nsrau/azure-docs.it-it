---
title: Cercare un percorso usando i servizi di ricerca di Azure Maps | Mappe Microsoft Azure
description: In questo articolo si apprenderà come cercare un percorso usando il Microsoft Azure Maps servizio di ricerca per la geocodifica e la geocodifica inversa.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335437"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Cercare un percorso usando i servizi di ricerca di Azure Maps

Azure Maps [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) è un set di API RESTful progettate per aiutare gli sviluppatori a eseguire ricerche in indirizzi, luoghi, elenchi aziendali per nome o categoria e altre informazioni geografiche. Oltre a supportare la geocodifica tradizionale, i servizi possono anche invertire gli indirizzi geocodificati e attraversare le strade in base a latitudine e longitudine. I valori di latitudine e Longitudine restituiti dalla ricerca possono essere usati come parametri in altri servizi di Azure Maps come [Route](https://docs.microsoft.com/rest/api/maps/route) e servizi [meteorologici](https://docs.microsoft.com/rest/api/maps/weather) .

In questo articolo si apprenderà come:

* Richiedere le coordinate di latitudine e Longitudine per un indirizzo (geocode address location) usando l' [API di ricerca di indirizzi]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Cercare un indirizzo o un punto di interesse (PDI) usando l' [API di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Cercare un indirizzo insieme alle proprietà e alle coordinate
* Eseguire una [ricerca di indirizzi inversi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per tradurre la posizione delle coordinate in un indirizzo via
* Cercare una traversa usando l' [API di ricerca inversa dell'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, è necessario creare prima di tutto un account Azure Maps e ottenere la chiave di sottoscrizione dell'account Maps. Per ottenere la chiave primaria per l'account, seguire le istruzioni riportate in [creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) per creare una sottoscrizione dell'account Azure Maps e seguire i passaggi in [ottenere](quick-demo-map-app.md#get-the-primary-key-for-your-account) la chiave primaria. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Richiedere Latitudine e Longitudine per un indirizzo (geocodifica)

In questo esempio viene usata l'API per la [ricerca dell'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) di Azure Maps per convertire il numero civico in coordinate di latitudine e longitudine. È possibile passare un indirizzo completo o parziale all'API e ricevere una risposta che include proprietà di indirizzo dettagliate, ad esempio via, codice postale e paese/area geografica, nonché valori posizionali in latitudine e longitudine.

Se è presente un set di indirizzi per la geocodifica, è possibile usare l' [API batch di indirizzi di ricerca post](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) per inviare un batch di query in una singola chiamata API.

1. In post, fare clic su **nuova richiesta**  |  **Get request** e denominarla **Address Search**.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET**, immettere l'URL della richiesta per l'endpoint API e selezionare il protocollo di autorizzazione, se necessario.

![Ricerca di indirizzi](./media/how-to-search-for-address/address_search_url.png)

| Parametro | Valore consigliato |
|---------------|------------------------------------------------| 
| Metodo HTTP | GET |
| URL richiesta | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorizzazione | No Auth (Senza autenticazione) |

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta: 

![Ricerca di indirizzi](./media/how-to-search-for-address/address_search_params.png) 

| Chiave | Valore | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<your Azure Maps key\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 

In questo caso, è stata specificata una query di indirizzo completo ed è stato ricevuto un solo risultato nel corpo della risposta. 

5. In Params (Parametri) modificare la stringa di query nel valore seguente: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**: 

| Chiave | Valore | 
|-----|------------| 
| typeahead | true | 

Il flag **typeahead** indica all'API di ricerca di indirizzi di considerare la query un input parziale e di restituire una matrice di valori predittivi.

## <a name="using-fuzzy-search-api"></a>Uso dell'API di ricerca fuzzy

L'[ API ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Azure Maps è il servizio consigliato da usare quando non si conoscono gli input degli utenti per una query di ricerca. L'API combina la ricerca di punti di interesse (PDI) e la geocodifica in una "ricerca a riga singola" canonica. L'API può ad esempio gestire gli input di qualsiasi combinazione di token di indirizzo e punto di interesse. Può anche essere ponderata con una posizione contestuale (coppia latitudine/longitudine), vincolata completamente da una coordinata e un raggio oppure essere eseguita più genericamente senza alcun punto di ancoraggio di compensazione geografica.

La maggior parte delle query di ricerca restituisce per impostazione predefinita `maxFuzzyLevel=1` per favorire le prestazioni e ridurre i risultati insoliti. Questa impostazione predefinita può essere sostituita nel modo necessario in base alla richiesta passando il parametro di query `maxFuzzyLevel=2` o `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Cercare un indirizzo usando la ricerca fuzzy

1. Aprire l'app Postman, fare clic su New (Nuovo) | Create New (Crea nuovo) e selezionare **GET request** (Richiesta GET). Immettere il nome di richiesta **Fuzzy search** (Ricerca fuzzy), selezionare una raccolta o una cartella in cui salvarla e quindi fare clic su **Save** (Salva).

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.

    ![Ricerca fuzzy](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorizzazione | No Auth (Senza autenticazione) |

    L'attributo **json** nel percorso URL determina il formato della risposta. Questo articolo usa JSON per facilitarne l'uso e la leggibilità. I formati di risposta disponibili sono inclusi nella definizione **Get Search Fuzzy** (Richiesta GET per la ricerca fuzzy) in [Maps Functional API reference](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) (Informazioni di riferimento sull'API funzionale di Mappe).

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:

    ![Ricerca fuzzy](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | query | pizza |

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

    La stringa di query ambigua per "pizza" ha restituito 10 [punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (PDI) nelle categorie "pizza" e "ristorante". Ogni risultato restituisce un indirizzo via, i valori di latitudine e longitudine, la porta di visualizzazione e i punti di ingresso per la posizione.
  
    I risultati sono diversi per questa query e non sono associati ad alcuna località di riferimento. È possibile usare il parametro **countrySet** per specificare solo i paesi o le aree per cui l'applicazione necessita di copertura. Il comportamento predefinito prevede la ricerca nell'intero mondo, restituendo potenzialmente risultati non necessari.

5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |------------------|-------------------------|
    | countrySet | US |
  
    I risultati sono ora limitati dall'indicativo paese e la query restituisce le pizzerie negli Stati Uniti.
  
    Per offrire risultati per una località, è possibile eseguire una query per un punto di interesse e usare i valori di latitudine e longitudine restituiti nella chiamata al servizio di ricerca fuzzy. In questo caso, è stato usato il servizio di ricerca per restituire la località dello Space Needle di Seattle, usando i valori di latitudine /longitudine per orientare la ricerca.
  
6. In Params (Parametri) immettere le coppie chiave/valore seguenti e fare clic su **Send** (Invia):

    ![Ricerca fuzzy](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chiave | Valore |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Cercare un indirizzo tramite la ricerca di indirizzi inversa

L' [API di ricerca dell'indirizzo di ricerca]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) di Azure Maps consente di convertire una coordinata (ad esempio: 37,786505-122,3862) in un indirizzo via comprensibile. Spesso questa operazione è necessaria per tenere traccia delle applicazioni in cui si riceve un feed GPS dal dispositivo o dall'asset e si desidera conoscere l'indirizzo in cui si trova la coordinata.
Se si dispone di un set di percorsi delle coordinate per la geocodifica inversa, è possibile usare l' [API batch inversa dell'indirizzo di ricerca post](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) per inviare un batch di query in una singola chiamata API.


1. In postazione fare clic su **nuova richiesta**  |  **Get request** e denominarla **Reverse Address Search**.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
  
    ![URL di ricerca di indirizzi inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorizzazione | No Auth (Senza autenticazione) |
  
3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
  
    ![Parametri di ricerca di indirizzi inversa](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | query | 47.591180,-122.332700 |
  
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

    La risposta include informazioni sull'indirizzo chiave per Safeco Field.
  
5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | d'acquisto | true |

    Se il parametro di query [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) viene inviato con la richiesta, la risposta può includere il lato della strada (a sinistra o a destra) e anche una posizione di offset per quel numero.
  
6. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando viene impostato il parametro di query [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , la risposta restituisce il limite di velocità inviato.

7. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | returnRoadUse | true |

    Quando è impostato il parametro di query [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), la risposta restituisce la matrice degli usi stradali per codifiche geografiche inverse a livello di via.

8. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |-----|------------|
    | roadUse | true |

    È possibile limitare la query di geocodifica inversa a un tipo specifico di strada usando il parametro di query [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Cercare Cross Street usando la ricerca di indirizzi inversi tra le vie

1. In postazione fare clic su **nuova richiesta**  |  **Get request** e denominarla **inversa Address Cross Street Search**.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
  
    ![Ricerca di strade secondarie inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorizzazione | No Auth (Senza autenticazione) |
  
3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
  
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | query | 47.591180,-122.332700 |
  
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare la [documentazione sull'API REST di Azure Maps servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search).
- Informazioni sulle [mappe di Azure servizio di ricerca procedure consigliate](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) e su come ottimizzare le query.
