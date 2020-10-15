---
title: Plug-in nativo React per Application Insights JavaScript SDK
description: Come installare e usare il plug-in nativo React per Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 3dac47aa7cf93ca882c4c1d0d191dabf0eb7178c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227433"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Plug-in nativo React per Application Insights JavaScript SDK

Il plug-in nativo React per Application Insights JavaScript SDK raccoglie le informazioni sul dispositivo. per impostazione predefinita, questo plug-in raccoglie automaticamente:

- **ID univoco del dispositivo** (noto anche come ID di installazione).
- **Nome del modello del dispositivo** (ad esempio iPhone X, Samsung Galaxy fold, Huawei P30 Pro e così via)
- **Tipo di dispositivo** (ad esempio, portatile, tablet e così via)

## <a name="requirements"></a>Requisiti

È necessario utilizzare una versione >= 2.0.0 di `@microsoft/applicationinsights-web` . Questo plug-in funziona solo nelle app React-native. Non funzionerà con [le app che usano Expo Framework](https://docs.expo.io/), pertanto non funzionerà con la creazione di un'app React native.

## <a name="getting-started"></a>Guida introduttiva

Installare e collegare il pacchetto [React-Native-Device-Info](https://www.npmjs.com/package/react-native-device-info) . `react-native-device-info`Per raccogliere i nomi dei dispositivi più recenti usando l'app, è necessario aggiornare il pacchetto.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Inizializzazione del plug-in

Per usare questo plug-in, è necessario costruire il plug-in e aggiungerlo come `extension` all'istanza di Application Insights esistente.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su JavaScript SDK, vedere la [documentazione di Application Insights JavaScript SDK](javascript.md).
- Per informazioni sul linguaggio di query kusto e sull'esecuzione di query sui dati in Log Analytics, vedere [Cenni preliminari sulle query di log](../../azure-monitor/log-query/log-query-overview.md).
