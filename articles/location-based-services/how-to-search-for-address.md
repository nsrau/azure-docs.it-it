---
title: Come cercare un indirizzo usando il servizio di ricerca di Servizi Location Based di Azure (anteprima) | Microsoft Docs
description: Informazioni su come cercare un indirizzo usando il servizio di ricerca di Servizi Location Based di Azure (anteprima) | Microsoft Docs
services: location-based-services
keywords: Non aggiungere o modificare parole chiave senza consultare il proprio champ SEO.
author: philmea
ms.author: philmea
ms.date: 11/29/2017
ms.topic: how-to
ms.service: location-based-services
ms.openlocfilehash: f7337c1c5821016987096da47dda4ac1124d7910
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-find-an-address-using-the-azure-location-based-services-preview-search-service"></a>Come trovare un indirizzo usando il servizio di ricerca di Servizi Location Based di Azure (anteprima)
Il servizio di ricerca è un set RESTful di API progettato per gli sviluppatori che consente di cercare indirizzi, località, punti di interesse, elenchi di aziende e altre informazioni geografiche. Il servizio di ricerca assegna una coppia latitudine/longitudine a un indirizzo, una strada secondaria, una caratteristica geografica o un punto di interesse specifico. I valori di latitudine e longitudine restituiti dalle API del servizio di ricerca possono essere usati come parametri in altre istanze di Servizi Location Based di Azure come le API di itinerario e flusso di traffico.

## <a name="prerequisites"></a>Prerequisiti
Installare l'[app Postman](https://www.getpostman.com/apps).

Account Servizi Location Based di Azure e una chiave di sottoscrizione. Per informazioni sulla creazione di un account e sul recupero di una chiave di sottoscrizione, vedere [How to manage your Azure Location Based Services account and keys](how-to-manage-account-keys.md) (Come gestire l'account Servizi Location Based di Azure e le chiavi). 

## <a name="using-fuzzy-search"></a>Uso della ricerca fuzzy

L'API predefinita per il servizio di ricerca è quella per la ricerca fuzzy, che gestisce gli input di qualsiasi combinazione di token di indirizzo o punto di interesse. Questa API di ricerca corrisponde alla tradizionale "ricerca a riga singola" ed è utile quando non è noto l'input dell'utente come query di ricerca. L'API di ricerca fuzzy è una combinazione di ricerca di punti di interesse e geocodifica. L'API può anche essere ponderata con una posizione contestuale (coppia latitudine/longitudine), vincolata completamente da una coordinata e un raggio oppure può essere eseguita più genericamente senza alcun punto di ancoraggio di compensazione geografica.

La maggior parte delle query di ricerca restituisce per impostazione predefinita "maxFuzzyLevel=1" per favorire le prestazioni e ridurre i risultati insoliti. Questa impostazione predefinita può essere sostituita nel modo necessario passando il parametro di query "maxFuzzyLevel=2" o "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Cercare un indirizzo usando la ricerca fuzzy

1. Aprire l'app Postman, fare clic su New (Nuovo) | Create New (Crea nuovo) e selezionare **GET request** (Richiesta GET). Immettere il nome di richiesta **Fuzzy search** (Ricerca fuzzy), selezionare una raccolta o una cartella in cui salvarla e quindi fare clic su **Save** (Salva).

    Per altre informazioni, vedere la documentazione delle richieste di Postman.

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.

    ![Ricerca fuzzy ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/fuzzy/json? |
    | Authorization | No Auth (Senza autenticazione) |

    L'attributo **json** nel percorso URL determina il formato della risposta. In questo articolo viene usato json per motivi di semplicità d'uso e leggibilità. I formati di risposta disponibili sono inclusi nella definizione **Get Search Fuzzy** (Richiesta GET per la ricerca fuzzy) in [Location Based Services Functional API reference] (Informazioni di riferimento sull'API funzionale di Servizi Location Based) (https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchfuzzy).

3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:

    ![Ricerca fuzzy ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *chiave di sottoscrizione* |
    | query | pizza |

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 

    La stringa di query ambigua "pizza" ha restituito 10 risultati di punti di interesse con categorie legate a "pizza" e "ristorante". Ogni risultato restituisce un indirizzo, valori di latitudine/longitudine, viewport e punti di ingresso per la località.
    
    I risultati sono diversi per questa query e non sono associati ad alcuna località di riferimento. È possibile usare il parametro **countrySet** per specificare solo i paesi per cui l'applicazione richiede copertura, in quanto il comportamento predefinito consiste nel cercare nel mondo intero, con il rischio di restituire risultati non necessari.

5. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,countrySet=US
    ```
    >[!NOTE] 
    >Assicurarsi di usare virgole per separare tra loro i parametri URI aggiuntivi nella stringa di query.
    
    I risultati sono ora limitati dall'indicativo paese e la query restituisce le pizzerie negli Stati Uniti.
    
    Per fornire risultati orientati in una località specifica, è possibile eseguire una query per un punto di interesse e usare i valori di latitudine e longitudine restituiti nella chiamata al servizio di ricerca fuzzy. In questo caso, è stato usato il servizio di ricerca per restituire la località dello Space Needle di Seattle, usando i valori di latitudine /longitudine per orientare la ricerca.
    
4. In Params (Parametri) immettere le coppie chiave/valore seguenti e fare clic su **Send** (Invia):

    ![Ricerca fuzzy ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Chiave | Valore |
    |-----|------------|
    | lat | 47.62039 |
    | lon | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Cercare proprietà e coordinate di indirizzi 

È possibile passare un indirizzo completo o parziale all'API di ricerca di indirizzi e ricevere una risposta che include proprietà dell'indirizzo dettagliate come il comune o il dipartimento, nonché i valori di posizione in latitudine e longitudine.

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Address Search** (Ricerca indirizzi).
2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET**, immettere l'URL della richiesta per l'endpoint API e selezionare il protocollo di autorizzazione, se necessario.

    ![Ricerca di indirizzi ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/address/json? |
    | Authorization | No Auth (Senza autenticazione) |

2. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
    
    ![Ricerca di indirizzi ](./media/how-to-search-for-address/address_search_params.png)
    
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *chiave di sottoscrizione* |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 
    
    In questo caso, è stata specificata una query di indirizzo completo ed è stato ricevuto un solo risultato nel corpo della risposta. 
    
4. In Params (Parametri) modificare la stringa di query nel valore seguente:
    ```
        400 Broad, Seattle
    ```

5. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,typeahead
    ```

    Il flag **typeahead** indica all'API di ricerca di indirizzi di considerare la query un input parziale e di restituire una matrice di valori predittivi.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Cercare un indirizzo tramite la ricerca di indirizzi inversa
1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Reverse Address Search** (Ricerca indirizzi inversa).

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
    
    ![URL di ricerca di indirizzi inversa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/address/reverse/json? |
    | Authorization | No Auth (Senza autenticazione) |
    
2. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
    
    ![Parametri di ricerca di indirizzi inversa ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *chiave di sottoscrizione* |
    | query | 47.59093,-122.33263 |
    
3. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 
    
    La risposta include la voce del punto di interesse per Safeco Field con una categoria di punto di interesse "stadio". 
    
4. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,number
    ```
    Se con la richiesta viene inviato il parametro di query [number](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta può includere il lato della via (sinistro/destro) e anche una posizione di variazione per il numero.
    
5. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,spatialKeys
    ```

    Quando è impostato il parametro di query [spatialKeys](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta contiene informazioni proprietarie relative alla chiave geospaziale per la località specificata.

6. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,returnSpeedLimit
    ```
    
    Quando è impostato il parametro di query [returnSpeedLimit](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta restituisce il limite di velocità pubblicato.

7. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,returnRoadUse
    ```

    Quando è impostato il parametro di query [returnRoadUse](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la risposta restituisce la matrice degli usi stradali per codifiche geografiche inverse a livello di via.

8. Aggiungere il valore seguente alla stringa di query e fare clic su **Send** (Invia):
    ```
        ,roadUse
    ```

    È possibile limitare la query di codifica geografica inversa a un tipo specifico di uso stradale usando il parametro di query [roadUse](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters).
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Cercare una strada secondaria usando la ricerca di strade secondarie inversa

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Reverse Address Cross Street Search** (Ricerca strade secondarie inversa).

2. Nella scheda Builder (Generatore) selezionare il metodo HTTP **GET** e immettere l'URI della richiesta per l'endpoint API.
    
    ![Ricerca di strade secondarie inversa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parametro | Valore consigliato |
    |---------------|------------------------------------------------|
    | Metodo HTTP | GET |
    | URL richiesta | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Authorization | No Auth (Senza autenticazione) |
    
3. Fare clic su **Params** (Parametri) e immettere le coppie chiave/valore seguenti da usare come parametri di query o percorso nell'URL della richiesta:
    
    | Chiave | Valore |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *chiave di sottoscrizione* |
    | query | 47.59093,-122.33263 |
    
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta. 

## <a name="next-steps"></a>Passaggi successivi
- Esplorare la documentazione delle API del [servizio di ricerca di Servizi Location Based di Azure](https://docs.microsoft.com/en-us/rest/api/location-based-services/search) 