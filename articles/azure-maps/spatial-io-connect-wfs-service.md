---
title: Connettersi a un servizio Web Feature Service (WFS) | Mappe Microsoft Azure
description: Informazioni su come connettersi a un servizio WFS, quindi eseguire query sul servizio WFS usando Azure Maps Web SDK e il modulo di i/o spaziale.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 75a7da41e4f0d5356ca135eb3cb4cbe5f29e8116
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891450"
---
# <a name="connect-to-a-wfs-service"></a>Connettersi a un servizio WFS

Un servizio funzionalità Web (WFS) è un servizio Web per l'esecuzione di query sui dati spaziali con un'API standardizzata definita dal Open Geospatial Consortium (OGC). La `WfsClient` classe nel modulo io spaziale consente agli sviluppatori di connettersi a un servizio WFS ed eseguire query sui dati dal servizio.

Le funzionalità seguenti sono supportate dalla `WfsClient` classe:

- Versioni supportate: `1.0.0` , `1.1.0` e `2.0.0`
- Operatori di filtro supportati: confronti binari, logica, matematica, valore e `bbox` .
- Le richieste vengono effettuate `HTTP GET` solo utilizzando.
- Operazioni supportate:

    | Operazione | Descrizione |
    | :-- | :-- |
    | GetCapabilities | Genera un documento di metadati con operazioni e parametri WFS validi |
    | GetFeature | Restituisce una selezione di funzionalità da un'origine dati |
    | DescribeFeatureType | Restituisce i tipi di funzionalità supportati |

## <a name="using-the-wfs-client"></a>Uso del client WFS

La `atlas.io.ogc.WfsClient` classe nel modulo di i/o spaziale semplifica l'esecuzione di query su un servizio WFS e la conversione delle risposte in oggetti GeoJSON. Questo oggetto GeoJSON può quindi essere usato per altri scopi di mapping.

Il codice seguente esegue una query su un servizio WFS ed esegue il rendering delle funzionalità restituite sulla mappa.

<br/>

<iframe height='700' scrolling='no' title='Esempio di WFS semplice' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l' <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>esempio Pen Simple WFS</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Filtri supportati

La specifica per lo standard WFS usa i filtri OGC. I filtri seguenti sono supportati dal client WFS, supponendo che il servizio chiamato supporti anche questi filtri. Le stringhe di filtro personalizzate possono essere passate alla `CustomFilter` classe.

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

<iframe height='500' scrolling='no' title= 'Esempi di filtro WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere gli <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>esempi di filtri Pen WFS</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Esplora servizi WFS

Il codice seguente usa il client WFS per esplorare i servizi di WFS. Selezionare un livello del tipo di proprietà all'interno del servizio e visualizzare la legenda associata.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Esplora servizi WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS Service Explorer</a> da Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Per accedere ai servizi WFS ospitati in endpoint non CORS abilitati, è possibile passare un servizio proxy abilitato per CORS nell' `proxyService` opzione del client WFS, come illustrato di seguito. 

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
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Sfruttare le operazioni principali](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportato](spatial-io-supported-data-format-details.md)