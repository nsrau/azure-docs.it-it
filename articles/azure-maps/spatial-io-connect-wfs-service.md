---
title: Connessione a un servizio Web Feature Service (WFS) Mappe di Microsoft Azure
description: Informazioni su come connettersi a un servizio WFS, quindi eseguire una query sul servizio WFS usando il Web SDK di Azure Maps e il modulo I/O spaziale.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334212"
---
# <a name="connect-to-a-wfs-service"></a>Connettersi a un servizio WFS

Un Web Feature Service (WFS) è un servizio Web per l'esecuzione di query su dati spaziali con un'API standardizzata definita dall'Open Geospatial Consortium (OGC). La `WfsClient` classe nel modulo I/O spaziale consente agli sviluppatori di connettersi a un servizio WFS ed eseguire query sui dati dal servizio.

La `WfsClient` classe supporta le seguenti funzionalità:

- Versioni supportate: `1.0.0`, `1.1.0`, e`2.0.0`
- Operatori di filtro supportati: confronti binari, logica, matematica, valore e `bbox`.
- Le richieste `HTTP GET` vengono effettuate solo utilizzando.
- Operazioni supportate:

    | | |
    | :-- | :-- |
    | GetCapabilities | Genera un documento di metadati con operazioni e parametri WFS validi |
    | Funzione GetFeature | Restituisce una selezione di feature da un'origine dati |
    | DescribeFeatureType (Tipo di funzionalità) | Restituisce i tipi di funzionalità supportati |

## <a name="using-the-wfs-client"></a>Utilizzo del client WFS

La `atlas.io.ogc.WfsClient` classe nel modulo I/O spaziale semplifica la query di un servizio WFS e la conversione delle risposte in oggetti GeoJSON. Questo oggetto GeoJSON può quindi essere utilizzato per altri scopi di mappatura.

Il codice seguente esegue una query su un servizio WFS ed esegue il rendering delle funzionalità restituite sulla mappa.

<br/>

<iframe height='700' scrolling='no' title='Esempio WFS semplice' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'esempio Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple WFS</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtri supportati

La specifica per lo standard WFS utilizza filtri OGC. I filtri riportati di seguito sono supportati dal client WFS, presupponendo che il servizio chiamato supporti anche questi filtri. Le stringhe di filtro `CustomFilter` personalizzate possono essere passate alla classe.

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

Il codice seguente illustra l'uso di diversi filtri con il client WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Esempi di filtri WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere gli esempi di filtro Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS</a> di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Esplora servizi WFS

Il codice seguente usa il client WFS per esplorare i servizi WFS. Selezionare un layer del tipo di proprietà all'interno del servizio e visualizzare la legenda associata.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Esplora servizi WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS Service Explorer</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Per accedere ai servizi WFS ospitati in endpoint non abilitati per CORS, è possibile passare un servizio proxy abilitato per CORS nell'opzione `proxyService` del client WFS, come illustrato di seguito. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [WfsClient (client di WfsClient)](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Opzioni WfsService](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Sfruttare le operazioni di base](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportati](spatial-io-supported-data-format-details.md)
