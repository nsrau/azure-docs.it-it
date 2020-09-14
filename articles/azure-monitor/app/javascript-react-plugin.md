---
title: Plug-in REACT per Application Insights JavaScript SDK
description: Come installare e usare il plug-in REACT per Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056201"
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

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

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

## <a name="sample-app"></a>App di esempio

Consultare la [demo Application Insights React](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su JavaScript SDK, vedere la [documentazione di Application Insights JavaScript SDK](javascript.md).
- Per informazioni sul linguaggio di query kusto e sull'esecuzione di query sui dati in Log Analytics, vedere [Cenni preliminari sulle query di log](../../azure-monitor/log-query/log-query-overview.md).
