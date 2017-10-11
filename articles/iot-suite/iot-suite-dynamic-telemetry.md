---
title: Usare la telemetria dinamica | Documentazione Microsoft
description: Seguire questa esercitazione per imparare a usare la telemetria dinamica con la soluzione preconfigurata per il monitoraggio remoto di Azure IoT Suite.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 0114f27f9b8ae76e1170d04ddf66e2c4bf20686a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Usare la telemetria dinamica con la soluzione preconfigurata per il monitoraggio remoto

La telemetria dinamica consente di visualizzare eventuali dati di telemetria inviati alla soluzione preconfigurata per il monitoraggio remoto. I dispositivi simulati distribuiti con la soluzione preconfigurata inviano dati di telemetria su temperatura e umidità visualizzabili nel dashboard. Se si personalizzano i dispositivi simulati esistenti, si creano nuovi dispositivi simulati o si collegano i dispositivi fisici alla soluzione preconfigurata, è possibile inviare altri valori di telemetria, ad esempio temperatura esterna, RPM o velocità del vento. È quindi possibile visualizzare questi dati di telemetria aggiuntivi nel dashboard.

Questa esercitazione usa un semplice dispositivo simulato Node.js facilmente modificabile per provare la telemetria dinamica.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure attiva. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].
* [Node.js][lnk-node] 0.12.x o versione successiva.

È possibile completare questa esercitazione su qualsiasi sistema operativo, ad esempio Windows o Linux, in cui si può installare Node.js.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Aggiungere un tipo di dati di telemetria

Il passaggio successivo consiste nella sostituzione dei dati di telemetria generati dal dispositivo simulato Node.js con un nuovo set di valori:

1. Arrestare il dispositivo simulato Node.js digitando **Ctrl+C** al prompt dei comandi o nella shell.
2. Nel file remote_monitoring.js è possibile visualizzare i valori dei dati di base per la telemetria su temperatura, umidità e temperatura esterna esistente. Aggiungere un valore di dati di base per **rpm** come indicato di seguito:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Il dispositivo simulato Node.js usa la funzione **generateRandomIncrement** nel file remote_monitoring.js per aggiungere un incremento casuale ai valori di dati di base. Impostare in modo casuale il valore **rpm** aggiungendo una riga di codice dopo le sequenze casuali esistenti come mostrato di seguito:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Aggiungere il nuovo valore rpm per il payload JSON che il dispositivo invia all'hub IoT:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Avviare l'esecuzione del dispositivo simulato Node.js usando il comando seguente:

    `node remote_monitoring.js`

6. Osservare il nuovo tipo di dati di telemetria RPM visualizzato sul grafico nel dashboard:

![Aggiungere RPM al dashboard][image3]

> [!NOTE]
> Per visualizzare la modifica immediatamente, potrebbe essere necessario disabilitare e quindi abilitare il dispositivo Node.js nella pagina **Devices** (Dispositivi) nel dashboard.

## <a name="customize-the-dashboard-display"></a>Personalizzare la schermata del dashboard

Il messaggio **Device-Info** può includere i metadati relativi ai dati di telemetria che il dispositivo può inviare all'hub IoT. Questi metadati possono specificare i tipi di dati di telemetria che il dispositivo invia. Modificare il valore **deviceMetaData** nel file remote_monitoring.js per includere una definizione **Telemetry** dopo la definizione **Commands**. Il frammento di codice seguente illustra la definizione **Commands**. Assicurarsi di aggiungere una `,` dopo la definizione **Commands**:

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> La soluzione di monitoraggio remoto non fa distinzione tra maiuscole e minuscole per confrontare la definizione dei metadati con i dati nel flusso di telemetria.


L'aggiunta di una definizione **Telemetry** , come mostrato nel frammento di codice precedente, non cambia il comportamento del dashboard. Tuttavia, i metadati possono includere anche un attributo **DisplayName** per personalizzare la visualizzazione nel dashboard. Aggiornare la definizione dei metadati **Telemetry** come illustrato nel codice seguente:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

La schermata seguente illustra in che modo questo cambiamento modifica la legenda del grafico nel dashboard:

![Personalizzare la legenda del grafico][image4]

> [!NOTE]
> Per visualizzare la modifica immediatamente, potrebbe essere necessario disabilitare e quindi abilitare il dispositivo Node.js nella pagina **Devices** (Dispositivi) nel dashboard.

## <a name="filter-the-telemetry-types"></a>Filtrare i tipi di dati di telemetria

Per impostazione predefinita, il grafico nel dashboard mostra ogni serie di dati nel flusso di telemetria. È possibile usare i metadati **Device-Info** per disattivare la visualizzazione di tipi di dati di telemetria specifici nel grafico. 

Per far sì che il grafico mostri solo i dati di telemetria su temperatura e umidità, omettere **ExternalTemperature** dai metadati **Device-Info** **Telemetry** nel modo seguente:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

La **temperatura esterna** non viene più visualizzata sul grafico:

![Filtrare i dati di telemetria nel dashboard][image5]

Questa modifica interessa solo la visualizzazione del grafico. Tuttavia, i valori dei dati **ExternalTemperature** vengono comunque memorizzati e resi disponibili per qualsiasi elaborazione back-end.

> [!NOTE]
> Per visualizzare la modifica immediatamente, potrebbe essere necessario disabilitare e quindi abilitare il dispositivo Node.js nella pagina **Devices** (Dispositivi) nel dashboard.

## <a name="handle-errors"></a>Gestire gli errori

Per visualizzare un flusso di dati sul grafico, il relativo attributo **Type** nei metadati **Device-Info** deve corrispondere al tipo di dati dei valori di telemetria. Se ad esempio i metadati specificano che l'attributo **Type** dei dati sull'umidità è **int** ma nel flusso di dati di telemetria è indicato **double**, i dati di telemetria sull'umidità non saranno visualizzati nel grafico. Tuttavia, i valori **Humidity** vengono comunque memorizzati e resi disponibili per qualsiasi elaborazione back-end.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare la telemetria dinamica, è possibile consultare altre informazioni su come le soluzioni preconfigurate possono usare le informazioni sul dispositivo in [Metadati di informazioni sul dispositivo nella soluzione preconfigurata per il monitoraggio remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
