---
title: Plug-in REACT per Application Insights JavaScript SDK
description: Come installare e usare il plug-in REACT per Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981086"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Plug-in REACT per Application Insights JavaScript SDK

Plug-in REACT per Application Insights JavaScript SDK, Abilita:

- Rilevamento delle modifiche della route
- Statistiche sull'utilizzo dei componenti React

## <a name="getting-started"></a>Introduzione

Installare il pacchetto NPM:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Uso di base

Inizializzare una connessione a Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Eseguire il wrapping del componente con la funzione componente di ordine superiore per abilitare Application Insights:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>Configurazione

| Nome    | Predefinito | Descrizione                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | Null    | Cronologia router React. Per ulteriori informazioni, vedere la [documentazione relativa al pacchetto del router React](https://reactrouter.com/web/api/history). Per informazioni su come accedere all'oggetto cronologia all'esterno dei componenti, vedere [domande frequenti sul router React](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Rilevamento utilizzo componenti React

Per instrumentare diversi componenti React del rilevamento dell'utilizzo, applicare la `withAITracking` funzione componente di ordine superiore.

Il tempo viene misurato dall' `ComponentDidMount` evento attraverso l' `ComponentWillUnmount` evento. Tuttavia, per rendere questa operazione più accurata, viene sottratta l'ora in cui l'utente è inattivo `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Per visualizzare questa metrica nella portale di Azure è necessario passare alla risorsa Application Insights, selezionare la scheda "metriche" e configurare i grafici vuoti per visualizzare il nome della metrica personalizzata "React Component Engaged time (secondi)", Select Aggregation (Sum, AVG e così via) della metrica e Apply Split be "Component Name".

![Screenshot del grafico che visualizza la metrica personalizzata "tempo occupato componente REACT (secondi)" diviso per "nome componente"](./media/javascript-react-plugin/chart.png)

È anche possibile eseguire query personalizzate per dividere i dati Application Insights per generare report e visualizzazioni in base ai requisiti. Nella portale di Azure passare alla risorsa Application Insights selezionare "Analytics" dal menu superiore della scheda Panoramica ed eseguire la query.

![Screenshot dei risultati della query della metrica personalizzata.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Possono essere necessari fino a 10 minuti affinché le nuove metriche personalizzate vengano visualizzate nel portale di Azure.

## <a name="using-react-hooks"></a>Uso di hook React

Gli [hook React](https://reactjs.org/docs/hooks-reference.html) sono un approccio alla gestione dello stato e del ciclo di vita in un'applicazione React senza basarsi su componenti React basati su classi. Il plug-in Application Insights React fornisce un numero di integrazioni di hook che operano in modo analogo all'approccio del componente di ordine superiore.

### <a name="using-react-context"></a>Uso del contesto React

Gli hook React per Application Insights sono progettati per usare il [contesto React](https://reactjs.org/docs/context.html) come un aspetto che lo contiene. Per usare context, inizializzare Application Insights come sopra, quindi importare l'oggetto context:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Questo provider di contesto renderà Application Insights disponibile come `useContext` hook all'interno di tutti i componenti figlio.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

L' `useTrackMetric` hook replica la funzionalità del componente di `withAITracking` ordine superiore, senza aggiungere un componente aggiuntivo alla struttura dei componenti. Il gancio accetta due argomenti, il primo è l'istanza di Application Insights (che può essere ottenuta dall' `useAppInsightsContext` hook) e un identificatore per il componente per il rilevamento, ad esempio il nome.

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Funziona come il componente di ordine superiore, ma risponde ai hook degli eventi del ciclo di vita, anziché al ciclo di vita di un componente. L'hook deve essere fornito in modo esplicito agli eventi utente se è necessario eseguire in particolari interazioni.

### `useTrackEvent`

L' `useTrackEvent` hook viene usato per tenere traccia di qualsiasi evento personalizzato che può essere necessario tenere traccia di un'applicazione, ad esempio un clic su un pulsante o un'altra chiamata API. Accetta due argomenti, il primo è l'istanza Application Insights (che può essere ottenuta dall' `useAppInsightsContext` hook) e un nome per l'evento.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Quando si usa l'hook, è possibile fornire un payload di dati per aggiungere ulteriori dati all'evento quando viene archiviato in Application Insights.

## <a name="react-error-boundaries"></a>Limiti di errore React

I [limiti di errore](https://reactjs.org/docs/error-boundaries.html) consentono di gestire normalmente un'eccezione quando si verifica all'interno di un'applicazione React e, quando si verifica questo errore, è probabile che l'eccezione debba essere registrata. Il plug-in REACT per Application Insights fornisce un componente limite errori che registrerà automaticamente l'errore quando si verifica.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

Per `AppInsightsErrorBoundary` è necessario passare due oggetti Props, l' `ReactPlugin` istanza creata per l'applicazione e il rendering di un componente quando si verifica un errore. Quando si verifica un errore non gestito, `trackException` viene chiamato con le informazioni fornite al limite di errore e `onError` viene visualizzato il componente.

## <a name="sample-app"></a>App di esempio

Consultare la [demo Application Insights React](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su JavaScript SDK, vedere la [documentazione di Application Insights JavaScript SDK](javascript.md).
- Per informazioni sul linguaggio di query kusto e sull'esecuzione di query sui dati in Log Analytics, vedere [Cenni preliminari sulle query di log](../../azure-monitor/log-query/log-query-overview.md).
