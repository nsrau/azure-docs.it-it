## <a name="configure-the-nodejs-simulated-device"></a>Configurare il dispositivo simulato Node.js
1. Nel dashboard di monitoraggio remoto fare clic su **+ Aggiungi un dispositivo** e quindi aggiungere un *dispositivo personalizzato*. Prendere nota del nome host dell'hub IoT, dell'ID dispositivo e della chiave del dispositivo. Queste informazioni saranno necessarie più avanti nell'esercitazione quando si preparerà l'applicazione client per dispositivi remote_monitoring.js.
2. Verificare che Node.js 0.12.x o una versione successiva sia installata sul computer di sviluppo. Per verificare la versione, eseguire `node --version` al prompt dei comandi o in una shell. Per informazioni sull'uso di Gestione pacchetti per installare Node.js su Linux, vedere [Installing Node.js via package manager][node-linux] (Installazione di Node.js tramite Gestione pacchetti).
3. Dopo aver installato Node.js, clonare l'ultima versione del repository [azure-iot-sdk-node][lnk-github-repo] nel computer di sviluppo. Usare sempre il ramo **master** per la versione più recente delle librerie e degli esempi.
4. Da una copia locale del repository [azure-iot-sdk-node][lnk-github-repo], copiare i seguenti due file dalla cartella node/device/samples in una cartella vuota sul computer di sviluppo:
   
   * packages.json
   * remote_monitoring.js
5. Aprire il file remote_monitoring.js e cercare la definizione di variabile seguente:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Sostituire **[stringa di connessione dispositivo Hub IoT]** con la stringa di connessione del dispositivo. Usare i valori per il nome host dell'hub IoT, dell'ID dispositivo e della chiave del dispositivo annotati nel passaggio 1. Una stringa di connessione del dispositivo ha il formato seguente:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Se il nome host dell'Hub IoT è **contoso** e l'ID dispositivo è **mydevice**, la stringa di connessione sarà simile al seguente frammento:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Salvare il file. Eseguire i comandi seguenti in una shell o al prompt dei comandi nella cartella che contiene i file per installare i pacchetti necessari e quindi eseguire l'applicazione di esempio:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Osservare la telemetria dinamica in azione
Il dashboard mostra dati di telemetria su temperatura e umidità dai dispositivi simulati esistenti:

![Dashboard predefinito][image1]

Se si seleziona il dispositivo simulato Node.js in esecuzione nella sezione precedente, verranno visualizzati i dati di telemetria su temperatura, umidità e temperatura esterna:

![Aggiungere la temperatura esterna al dashboard][image2]

La soluzione di monitoraggio remoto rileva automaticamente il tipo di dati di telemetria aggiuntivo relativo alla temperatura esterna e lo aggiunge al grafico nel dashboard.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-send-external-temperature/image1.png
[image2]: media/iot-suite-send-external-temperature/image2.png