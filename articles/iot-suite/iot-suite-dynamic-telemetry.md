<properties
	pageTitle="Usare la telemetria dinamica | Microsoft Azure"
	description="Seguire questa esercitazione per imparare a usare la telemetria dinamica con la soluzione preconfigurata per il monitoraggio remoto."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/07/2016"
     ms.author="dobett"/>

# Usare la telemetria dinamica con la soluzione preconfigurata per il monitoraggio remoto

## Introduzione

La telemetria dinamica consente di visualizzare eventuali dati di telemetria inviati alla soluzione preconfigurata per il monitoraggio remoto. I dispositivi simulati distribuiti con la soluzione preconfigurata inviano dati di telemetria su temperatura e umidità visualizzabili nel dashboard. Se si personalizzano i dispositivi simulati esistenti, creano nuovi dispositivi simulati o collegano i dispositivi fisici alla soluzione preconfigurata, è possibile inviare altri valori di telemetria, ad esempio temperatura esterna, RPM o la velocità del vento. È quindi possibile visualizzare questi dati di telemetria aggiuntivi nel dashboard.

Questa esercitazione usa un semplice dispositivo simulato Node.js facilmente modificabile per provare la telemetria dinamica.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure attiva. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].
- [Node.js][lnk-node] 0.12.x o versione successiva.

È possibile completare questa esercitazione su qualsiasi sistema operativo, ad esempio Windows o Linux, in cui si può installare Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Configurare il dispositivo simulato Node.js

1. Nel dashboard di monitoraggio remoto fare clic su **+ Add a device** (+ Aggiungi un dispositivo) e quindi aggiungere un nuovo dispositivo personalizzato. Prendere nota del nome host dell'hub IoT, dell'ID dispositivo e della chiave del dispositivo. Queste informazioni saranno necessarie più avanti nell'esercitazione quando si preparerà l'applicazione client per dispositivi remote\_monitoring.js.

2. Verificare che Node.js 0.12.x o una versione successiva sia installata sul computer di sviluppo. Per verificare la versione, eseguire `node --version` al prompt dei comandi o in una shell. Per informazioni sull'uso di Gestione pacchetti per installare Node.js su Linux, vedere [Installing Node.js via package manager][node-linux] (Installazione di Node.js tramite Gestione pacchetti).

3. Dopo aver installato Node.js, clonare l'ultima versione del repository [azure-iot-sdks][lnk-github-repo] nel computer di sviluppo. È consigliabile usare sempre il ramo **master** per la versione più recente delle librerie e degli esempi.

4. Da una copia locale del repository [azure-iot-sdks][lnk-github-repo], copiare i seguenti due file dalla cartella node/device/samples in una cartella vuota sul computer di sviluppo:

  - packages.json
  - remote\_monitoring.js

5. Aprire il file remote\_monitoring.js e cercare la definizione di variabile seguente:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Sostituire **[stringa di connessione dispositivo Hub IoT]** con la stringa di connessione del dispositivo. Usare i valori per il nome host dell'hub IoT, dell'ID dispositivo e della chiave del dispositivo annotati nel passaggio 1. Una stringa di connessione del dispositivo ha il formato seguente:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se il nome host dell'Hub IoT è **contoso** e l'id dispositivo è **mydevice**, la stringa di connessione sarà simile alla seguente:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Salvare il file. Eseguire i comandi seguenti in una shell o al prompt dei comandi nella cartella che contiene i file per installare i pacchetti necessari e quindi eseguire l'applicazione di esempio:

    ```
    npm install
    node remote_monitoring.js
    ```

## Osservare la telemetria dinamica in azione

Il dashboard mostra dati di telemetria su temperatura e umidità dai dispositivi simulati esistenti:

![Dashboard predefinito][image1]

Se si seleziona il dispositivo simulato Node.js in esecuzione nella sezione precedente, verranno visualizzati i dati di telemetria su temperatura, umidità e temperatura esterna:

![Aggiungere la temperatura esterna al dashboard][image2]

La soluzione di monitoraggio remoto rileva automaticamente il tipo di dati di telemetria aggiuntivo relativo alla temperatura esterna e lo aggiunge al grafico nel dashboard.

## Aggiungere un nuovo tipo di dati di telemetria

Il passaggio successivo consiste nella sostituzione dei dati di telemetria generati dal dispositivo simulato Node.js con un nuovo set di valori:

1. Arrestare il dispositivo simulato Node.js digitando **Ctrl+C** al prompt dei comandi o nella shell.

2. Nel file remote\_monitoring.js è possibile visualizzare i valori dei dati di base per la telemetria su temperatura, umidità e temperatura esterna esistente. Aggiungere un nuovo valore di dati di base per **rpm** come indicato di seguito:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Il dispositivo simulato Node.js genera i dati di telemetria aggiungendo un incremento casuale ai valori di dati di base tramite la funzione **generateRandomIncrement** nel file remote\_monitoring.js. Impostare in modo casuale il valore **rpm** aggiungendo una riga di codice dopo le sequenze casuali esistenti come mostrato di seguito:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Aggiungere il nuovo valore rpm per il payload JSON che il dispositivo invia all'hub IoT:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Avviare l'esecuzione del dispositivo simulato Node.js usando il comando seguente:

    ```
    node remote_monitoring.js
    ```

6. Osservare il nuovo tipo di dati di telemetria RPM visualizzato sul grafico nel dashboard:

![Aggiungere RPM al dashboard][image3]

> [AZURE.NOTE] Per visualizzare la modifica immediatamente, potrebbe essere necessario disabilitare e quindi abilitare il dispositivo Node.js nella pagina **Devices** (Dispositivi) nel dashboard.

## Personalizzare la schermata del dashboard

Il messaggio **Device-Info** può includere i metadati relativi ai dati di telemetria che il dispositivo può inviare all'hub IoT. Questi metadati possono specificare i tipi di dati di telemetria che il dispositivo invia. Modificare il valore **deviceMetaData** nel file remote\_monitoring.js per includere una definizione **Telemetry** dopo la definizione **Commands** come mostrato nel frammento di codice seguente (assicurarsi di aggiungere una `,` dopo la definizione **Commands**):

```
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

> [AZURE.NOTE] La soluzione di monitoraggio remoto non fa distinzione tra maiuscole e minuscole per confrontare la definizione dei metadati con i dati nel flusso di telemetria.

L'aggiunta di una definizione **Telemetry**, come mostrato nell'esempio precedente, non cambia il comportamento del dashboard. Tuttavia, i metadati possono includere anche un attributo **DisplayName** per personalizzare la visualizzazione nel dashboard. Aggiornare la definizione dei metadati **Telemetry** nel modo seguente:

```
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

> [AZURE.NOTE] Per visualizzare la modifica immediatamente, potrebbe essere necessario disabilitare e quindi abilitare il dispositivo Node.js nella pagina **Devices** (Dispositivi) nel dashboard.

## Filtrare i tipi di dati di telemetria

Per impostazione predefinita, il grafico nel dashboard mostra ogni serie di dati nel flusso di telemetria. È possibile usare i metadati **Device-Info** per disattivare la visualizzazione di tipi di dati di telemetria specifici nel grafico.

Per far sì che il grafico mostri solo i dati di telemetria su temperatura e umidità, omettere **ExternalTemperature** dai metadati **Device-Info** **Telemetry** nel modo seguente:

```
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

Si noti che ciò interessa solo la visualizzazione del grafico, i valori **ExternalTemperature** dei dati vengono comunque memorizzati e resi disponibili per qualsiasi elaborazione back-end.

> [AZURE.NOTE] Per visualizzare la modifica immediatamente, potrebbe essere necessario disabilitare e quindi abilitare il dispositivo Node.js nella pagina **Devices** (Dispositivi) nel dashboard.

## Gestire gli errori

Per visualizzare un flusso di dati sul grafico, il relativo attributo **Type** nei metadati **Device-Info** deve corrispondere al tipo di dati dei valori di telemetria. Ad esempio, se i metadati specificano che l'attributo **Type** dei dati sull'umidità è **int** ma nel flusso di dati di telemetria è indicato **double**, i dati di telemetria sull'umidità non saranno visualizzati nel grafico. Tuttavia, i valori **Humidity** vengono comunque memorizzati e resi disponibili per qualsiasi elaborazione back-end.

## Passaggi successivi

Ora che è stata compilata una soluzione preconfigurata funzionante, è possibile passare alle procedure dettagliate seguenti:

-   [Guida alla personalizzazione di soluzioni preconfigurate][lnk-customize]
-   [Panoramica della soluzione preconfigurata di manutenzione predittiva][lnk-predictive]

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0615_2016-->