---
title: Cercare una posizione usando i servizi di Ricerca di Azure Maps . Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come cercare una posizione usando il servizio di ricerca di Microsoft Azure Maps per la geocodifica e la geocodifica inversa.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335437"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Cercare una posizione usando i servizi di Ricerca di Mappe di AzureSearch for a location using Azure Maps Search services

Il [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure è un set di API RESTful progettato per aiutare gli sviluppatori a cercare indirizzi, luoghi, elenchi di attività per nome o categoria e altre informazioni geografiche. Oltre a supportare la geocodifica tradizionale, i servizi possono anche invertire gli indirizzi di geocodifica e le strade incrociate in base alle latitudini e alle longitudini. I valori di latitudine e longitudine restituiti dalla ricerca possono essere usati come parametri in altri servizi di Azure Maps come i servizi [Route](https://docs.microsoft.com/rest/api/maps/route) e [Weather.](https://docs.microsoft.com/rest/api/maps/weather)

In questo articolo imparerai come:

* Richiedere le coordinate di latitudine e longitudine per un indirizzo (posizione dell'indirizzo di geocodifica) tramite [l'API Dell'indirizzo di ricerca]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Cercare un indirizzo o un punto di interesse (POI) utilizzando [l'API di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Cercare un indirizzo insieme a proprietà e coordinate
* Effettuare una [ricerca di indirizzi inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per convertire la posizione delle coordinate in indirizzo
* Cercare una strada trasversale utilizzando [l'API Ricerca indirizzo inverso trasversale](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, è innanzitutto necessario creare un account di Azure Maps e ottenere la chiave di sottoscrizione dell'account di mapping. Seguire le istruzioni in [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) per creare una sottoscrizione di account di Azure Maps e seguire i passaggi descritti in Ottenere la chiave [primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account) per ottenere la chiave primaria per l'account. Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Richiedere latitudine e longitudine per un indirizzo (geocodifica)

In questo esempio viene usata [l'API Get Search Address di](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) Azure Maps per convertire l'indirizzo in coordinate di latitudine e longitudine. È possibile passare un indirizzo completo o parziale all'API e ricevere una risposta che include proprietà dettagliate dell'indirizzo, ad esempio via, codice postale e paese/area geografica, nonché valori posizionali in latitudine e longitudine.

Se si dispone di un set di indirizzi per geocodificare, è possibile utilizzare [post-search Address Batch API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) per inviare un batch di query in una singola chiamata API.

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Ricerca **indirizzo**.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET**, immettere l'URL della richiesta per l'endpoint API e selezionare il protocollo di autorizzazione, se necessario.

![Ricerca di indirizzi](./media/how-to-search-for-address/address_search_url.png)

| Parametro | Valore consigliato |
|---------------|------------------------------------------------| 
| Metodo HTTP | GET |
| URL richiesta | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorizzazione | No Auth (Senza autenticazione) |

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta: 

![Ricerca di indirizzi](./media/how-to-search-for-address/address_search_params.png) 

| Chiave | valore | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<la chiave di Mappe di Azure\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 

In questo caso, è stata specificata una query di indirizzo completo ed è stato ricevuto un solo risultato nel corpo della risposta. 

5. In Params (Parametri) modificare la stringa di query nel valore seguente: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**: 

| Chiave | valore | 
|-----|------------| 
| typeahead | true | 

Il flag **typeahead** indica all'API di ricerca di indirizzi di considerare la query un input parziale e di restituire una matrice di valori predittivi.

## <a name="using-fuzzy-search-api"></a>Utilizzo dell'API di ricerca fuzzyUsing Fuzzy Search API

[L'API di ricerca fuzzy di](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Azure Maps è consigliato dal servizio da usare quando non si conoscono gli input dell'utente per una query di ricerca. L'API combina la ricerca e la geocodifica point of interest (POI) in una "ricerca a riga singola" canonica. L'API può ad esempio gestire gli input di qualsiasi combinazione di token di indirizzo e punto di interesse. Può anche essere ponderata con una posizione contestuale (coppia latitudine/longitudine), vincolata completamente da una coordinata e un raggio oppure essere eseguita più genericamente senza alcun punto di ancoraggio di compensazione geografica.

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

    L'attributo **json** nel percorso URL determina il formato della risposta. Questo articolo usa json per facilità d'uso e leggibilità. I formati di risposta disponibili sono inclusi nella definizione **Get Search Fuzzy** (Richiesta GET per la ricerca fuzzy) in [Maps Functional API reference](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) (Informazioni di riferimento sull'API funzionale di Mappe).

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:

    ![Ricerca fuzzy](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chiave | valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | pizza |

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

    La stringa di query ambigua per "pizza" ha restituito 10 [punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) nelle categorie "pizza" e "ristorante". Ogni risultato restituisce un indirizzo, valori di latitudine e longitudine, una porta di visualizzazione e punti di ingresso per la posizione.
  
    I risultati sono diversi per questa query e non sono associati ad alcuna località di riferimento. È possibile utilizzare il parametro **countrySet** per specificare solo i paesi per i quali l'applicazione necessita di copertura. Il comportamento predefinito consiste nel cercare in tutto il mondo, potenzialmente restituendo risultati non necessari.

5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | valore |
    |------------------|-------------------------|
    | countrySet | Stati Uniti |
  
    I risultati sono ora limitati dall'indicativo paese e la query restituisce le pizzerie negli Stati Uniti.
  
    Per offrire risultati per una località, è possibile eseguire una query per un punto di interesse e usare i valori di latitudine e longitudine restituiti nella chiamata al servizio di ricerca fuzzy. In questo caso, è stato usato il servizio di ricerca per restituire la località dello Space Needle di Seattle, usando i valori di latitudine /longitudine per orientare la ricerca.
  
6. In Params (Parametri) immettere le coppie chiave/valore seguenti e fare clic su **Send** (Invia):

    ![Ricerca fuzzy](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chiave | valore |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Cercare un indirizzo tramite la ricerca di indirizzi inversa

Azure Maps [Get Search Address Reverse API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) aiuta a tradurre una coordinata (ad esempio: 37.786505, -122.3862) in un indirizzo umano comprensibile. Molto spesso questo è necessario nel monitoraggio delle applicazioni in cui si riceve un feed GPS dal dispositivo o dall'asset e si desidera sapere quale indirizzo si trova la coordinata.
Se si dispone di un set di posizioni delle coordinate per invertire il geocodifica, è possibile usare [l'API Batch inverso dell'indirizzo post-ricerca](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) per inviare un batch di query in una singola chiamata API.


1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Reverse Address **Search**.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
  
    ![URL di ricerca di indirizzi inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorizzazione | No Auth (Senza autenticazione) |
  
3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
  
    ![Parametri di ricerca di indirizzi inversa](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Chiave | valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | 47.591180,-122.332700 |
  
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

    La risposta include informazioni sull'indirizzo chiave per Safeco Field.
  
5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | valore |
    |-----|------------|
    | d'acquisto | true |

    Se il parametro di query [numero](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) viene inviato con la richiesta, la risposta può includere il lato della strada (Sinistra o Destra) e anche una posizione di offset per tale numero.
  
6. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | valore |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Quando il parametro di query [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) è impostato, la risposta restituisce il limite di velocità registrato.

7. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | valore |
    |-----|------------|
    | returnRoadUse | true |

    Quando è impostato il parametro di query [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), la risposta restituisce la matrice degli usi stradali per codifiche geografiche inverse a livello di via.

8. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | valore |
    |-----|------------|
    | roadUse | true |

    È possibile limitare la query di geocodifica inversa a un tipo specifico di strada utilizzando il parametro di query [roadUse.](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Cercare strade trasversali utilizzando Reverse Address Cross Street Search

1. In Postman fare clic su **Nuova** | **richiesta GET richiesta** e denominarla Reverse Address Cross Street **Search**.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
  
    ![Ricerca di strade secondarie inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorizzazione | No Auth (Senza autenticazione) |
  
3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
  
    | Chiave | valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | 47.591180,-122.332700 |
  
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare la [documentazione dell'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search)di Azure Maps.Explore the Azure Maps Search Service REST API documentation .
- Informazioni sulle procedure consigliate del [servizio di ricerca](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) di Azure Maps e su come ottimizzare le query.
