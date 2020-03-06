---
title: Connettersi a un servizio Web Feature Service (WFS) | Mappe Microsoft Azure
description: Informazioni su come connettersi a un servizio WFS, quindi eseguire query sul servizio WFS usando Azure Maps Web SDK e il modulo di i/o spaziale.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0f50fe51f9c1cdef3c3f07c91640f5b9b9616229
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370960"
---
# <a name="connect-to-a-wfs-service"></a>Connettersi a un servizio WFS

Un servizio funzionalità Web (WFS) è un servizio Web per l'esecuzione di query sui dati spaziali con un'API standardizzata, definita dall'Open Geospatial Consortium (OGC). La classe `WfsClient` nel modulo IO spaziale consente agli sviluppatori di connettersi a un servizio WFS ed eseguire query sui dati dal servizio.

Le funzionalità seguenti sono supportate dalla classe `WfsClient`:

- Versioni supportate: `1.0.0`, `1.1.0`e `2.0.0`
- Operatori di filtro supportati: confronti binari, logica, matematica, valore e `bbox`.
- Le richieste vengono effettuate usando solo `HTTP GET`.
- Operazioni supportate:

    | | |
    | :-- | :-- |
    | GetCapabilities | Genera un documento di metadati con operazioni e parametri WFS validi |
    | GetFeature | Restituisce una selezione di funzionalità da un'origine dati |
    | DescribeFeatureType | Restituisce i tipi di funzionalità supportati |

## <a name="using-the-wfs-client"></a>Uso del client WFS

La classe `atlas.io.ogc.WfsClient` nel modulo di i/o spaziale semplifica l'esecuzione di query su un servizio WFS e la conversione delle risposte in oggetti GeoJSON. Questo oggetto GeoJSON può quindi essere usato per altri scopi di mapping.

Il codice seguente esegue una query su un servizio WFS ed esegue il rendering delle funzionalità restituite sulla mappa.

<br/>

<iframe height='700' scrolling='no' title='Esempio di WFS semplice' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l' <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>esempio Pen Simple WFS</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtri supportati

La specifica per lo standard WFS usa i filtri OGC. I filtri seguenti sono supportati dal client WFS, supponendo che il servizio chiamato supporti anche questi filtri. Le stringhe di filtro personalizzate possono essere passate alla classe `CustomFilter`.

**Operatori logici**

- `And`
- `Or`
- `Not`

**Operatori di valore**

- `GmlObjectId`
- `ResourceId`

**Operatori matematici**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operatori di confronto**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Nel codice seguente viene illustrato l'utilizzo di filtri diversi con il client WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Esempi di filtro WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere gli <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>esempi di filtri Pen WFS</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Esplora servizi WFS

Il codice seguente usa il client WFS per esplorare i servizi di WFS. Selezionare un livello del tipo di proprietà all'interno del servizio e visualizzare la legenda associata.

<br/>

<iframe height='700' scrolling='no' title= 'Esplora servizi WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS Service Explorer</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

È anche possibile usare un servizio proxy per caricare le risorse ospitate in domini che non sono abilitati per CORs. È innanzitutto necessario definire una variabile che contenga l'URL del servizio proxy e impostare l'opzione `proxyService` per il client WFS. Per eseguire il rendering di un'opzione del servizio proxy per l'utente, aggiungere un input utente all'interfaccia utente. Caricare l'URL del servizio quando si fa clic sull'input. Nei frammenti di codice seguenti viene illustrato come utilizzare il servizio proxy.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

Il frammento di codice HTML seguente corrisponde al codice JavaScript precedente:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Sfruttare le operazioni principali](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli del formato dati supportati](spatial-io-supported-data-format-details.md)
