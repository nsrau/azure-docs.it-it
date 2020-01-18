---
title: Percorsi di ricerca usando le mappe di Azure servizio di ricerca | Mappe Microsoft Azure
description: In questo articolo si apprenderà come cercare un percorso usando il servizio di ricerca Microsoft Azure maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 53856b4157afa5976947c451952fc26eefcdd0ea
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264187"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Trovare un indirizzo usando il servizio di ricerca di Mappe di Azure

Il servizio di ricerca Maps è un set di API RESTful progettate per gli sviluppatori. Il servizio può cercare indirizzi, luoghi, punti di interesse, elenchi aziendali e altre informazioni geografiche. Ognuno dei seguenti ha un valore di latitudine e Longitudine: un indirizzo specifico, una traversa, una funzionalità geografica o un punto di interesse (POI). È possibile utilizzare i valori di latitudine e Longitudine restituiti da una query come parametri in altri servizi della mappa. Ad esempio, i valori restituiti possono diventare parametri per il servizio Route o per il servizio flusso di traffico. 

Si apprenderà come:

* Cercare un indirizzo usando l' [API di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Cercare un indirizzo insieme alle proprietà e alle coordinate
* Eseguire una [ricerca di indirizzi inversi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per cercare un indirizzo via
* Cercare una traversa usando l' [API di ricerca inversa dell'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire chiamate alle API del servizio Maps, sono necessari un account Maps e una chiave. Per creare un account per Azure Maps, seguire le istruzioni riportate in [creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps). Per ottenere assistenza per ottenere la chiave primaria, seguire i passaggi in [ottenere la chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). Per altre informazioni sull'autenticazione in mappe di Azure, vedere [gestire l'autenticazione in mappe di Azure](./how-to-manage-authentication.md).

Questo articolo usa l'[app Postman](https://www.getpostman.com/apps) per compilare le chiamate REST. È possibile usare qualsiasi ambiente di sviluppo API preferito.

## <a name="using-fuzzy-search"></a>Uso della ricerca fuzzy

L'API predefinita per il servizio di ricerca è [ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Questo servizio è utile quando non si è certi del formato di input dell'utente in una query di ricerca. L'API combina la ricerca di punti di interesse e geocodifica in una tradizionale "ricerca a riga singola". L'API può ad esempio gestire gli input di qualsiasi combinazione di token di indirizzo e punto di interesse. Inoltre, può essere ponderato con una posizione contestuale (lat./Lon. latitudine/longitudine), vincolata completamente da una coordinata e un raggio oppure essere eseguita più genericamente senza alcun punto di ancoraggio di compensazione geografica.

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
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | pizza |

4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

    La stringa di query ambigua per "pizza" ha restituito 10 [punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (PDI) nelle categorie "pizza" e "ristorante". Ogni risultato restituisce un indirizzo via, i valori di latitudine e longitudine, la porta di visualizzazione e i punti di ingresso per la posizione.
  
    I risultati sono diversi per questa query e non sono associati ad alcuna località di riferimento. È possibile usare il parametro **countrySet** per specificare solo i paesi o le aree per cui l'applicazione necessita di copertura. Il comportamento predefinito prevede la ricerca nell'intero mondo, restituendo potenzialmente risultati non necessari.

5. Aggiungere la coppia chiave/valore seguente alla sezione **Parametri** e fare clic su **Invia**:

    | Chiave | Valore |
    |------------------|-------------------------|
    | countrySet | Stati Uniti |
  
    I risultati sono ora limitati dall'indicativo paese e la query restituisce le pizzerie negli Stati Uniti.
  
    Per offrire risultati per una località, è possibile eseguire una query per un punto di interesse e usare i valori di latitudine e longitudine restituiti nella chiamata al servizio di ricerca fuzzy. In questo caso, è stato usato il servizio di ricerca per restituire la località dello Space Needle di Seattle, usando i valori di latitudine /longitudine per orientare la ricerca.
  
6. In Params (Parametri) immettere le coppie chiave/valore seguenti e fare clic su **Send** (Invia):

    ![Ricerca fuzzy](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Chiave | Valore |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Cercare proprietà e coordinate di indirizzi

È possibile passare un indirizzo completo o parziale all'API degli indirizzi di ricerca. Si riceve comunque una risposta che include proprietà dell'indirizzo dettagliate. Le proprietà di indirizzo dettagliate sono valori quali i valori posizionali in Altitude e longitudine, municipalità o suddivisione.

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Address Search** (Ricerca indirizzi).
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
    | subscription-key | \<la chiave di Mappe di Azure\> |
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

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Cercare un indirizzo tramite la ricerca di indirizzi inversa

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Reverse Address Search** (Ricerca indirizzi inversa).

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
    | subscription-key | \<la chiave di Mappe di Azure\> |
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

    È possibile limitare la query di geocodifica inversa a un tipo specifico di Road, usando il parametro di query [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) .
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Cercare una strada secondaria usando la ricerca di strade secondarie inversa

1. In Postman fare clic su **New Request** (Nuova richiesta)  | **GET request** (Richiesta GET) e quindi denominare la richiesta **Reverse Address Cross Street Search** (Ricerca strade secondarie inversa).

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
    | subscription-key | \<la chiave di Mappe di Azure\> |
    | query | 47.591180,-122.332700 |
  
4. Fare clic su **Send** (Invia) e quindi esaminare il corpo della risposta.

## <a name="next-steps"></a>Passaggi successivi

- Esplorare la documentazione dell'API del [servizio di ricerca di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search)
