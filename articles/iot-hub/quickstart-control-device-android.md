---
title: Controllare un dispositivo dalla guida introduttiva di Hub IoT di Azure (Android) | Microsoft Docs
description: In questa guida introduttiva vengono eseguite due applicazioni Java di esempio. Una è un'applicazione di servizio che può controllare in remoto i dispositivi connessi all'hub. L'altra applicazione viene eseguita su un dispositivo fisico o simulato, connesso all'hub, che può essere controllato in remoto.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- devx-track-java
- devx-track-azurecli
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: a1166874ed743efa599743fa6db8341e94c0fe1f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747660"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Avvio rapido: controllare un dispositivo connesso a un hub IoT (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

In questa guida di avvio rapido si usa un metodo diretto per controllare un dispositivo simulato connesso all'hub IoT di Azure. L'hub IoT è un servizio di Azure che consente di gestire i dispositivi IoT dal cloud e inserire volumi elevati di dati di telemetria dei dispositivi nel cloud per l'archiviazione o l'elaborazione. È possibile usare metodi diretti per modificare in remoto il comportamento di un dispositivo connesso all'hub IoT. Questa guida di avvio rapido usa due applicazioni: un'applicazione del dispositivo simulato che risponde ai metodi diretti chiamati da un'applicazione di servizio back-end e un'applicazione di servizio che chiama il metodo diretto sul dispositivo Android.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Android Studio con Android SDK 27](https://developer.android.com/studio/). Per altre informazioni, vedere le istruzioni per l'[installazione di Android Studio](https://developer.android.com/studio/install).

* [Git](https://git-scm.com/download/).

* [Applicazione Android di esempio dell'SDK per dispositivi](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample), inclusa negli [esempi di Azure IoT (Java)](https://github.com/Azure-Samples/azure-iot-samples-java).

* [Applicazione Android di esempio dell'SDK per servizi](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample), inclusa negli esempi di Azure IoT (Java).

* Porta 8883 aperta nel firewall. L'esempio di dispositivo di questo argomento di avvio rapido usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Aggiungere l'estensione Azure IoT

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici dell'hub IoT, di IoT Edge e del servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Se è stata completata la precedente [Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-android.md), ignorare questo passaggio e usare l'hub IoT già creato.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

Se è stata completata la precedente [Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-android.md), ignorare questo passaggio e usare lo stesso dispositivo registrato nella guida introduttiva precedente.

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa guida introduttiva si usa Azure Cloud Shell per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName** : sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyAndroidDevice** : nome del dispositivo da registrare. È consigliabile usare **MyAndroidDevice** , come illustrato. Se si sceglie un altro nome per il dispositivo, è necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Eseguire il comando seguente in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo appena registrato.

   **YourIoTHubName** : sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyAndroidDevice \
      --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

## <a name="retrieve-the-service-connection-string"></a>Recuperare la stringa di connessione del servizio

È necessaria anche una _stringa di connessione del servizio_ per consentire all'applicazione di servizio di connettersi all'hub IoT per eseguire i metodi e recuperare i messaggi. Il comando seguente recupera la stringa di connessione del servizio per l'hub IoT:

**YourIoTHubName** : sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Annotare la stringa di connessione del servizio, che avrà questo aspetto:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Il valore verrà usato più avanti in questa guida introduttiva. Questa stringa di connessione del servizio è diversa dalla stringa di connessione del dispositivo annotata nel passaggio precedente.

## <a name="listen-for-direct-method-calls"></a>Ascoltare le chiamate dei metodi diretti

Entrambi gli esempi di questa guida di avvio rapido sono inclusi nel repository azure-iot-samples-java in GitHub. Scaricare o clonare il repository [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

L'applicazione di esempio degli SDK per dispositivi può essere eseguita su un dispositivo Android fisico o un emulatore Android. L'esempio si connette a un endpoint specifico del dispositivo nell'hub IoT, invia dati di telemetria simulati e ascolta le chiamate dei metodi diretti dall'hub. In questa guida introduttiva la chiamata dei metodi diretti dall'hub indica al dispositivo di modificare l'intervallo di invio dei dati di telemetria. Il dispositivo simulato invia un acknowledgement all'hub dopo l'esecuzione del metodo diretto.

1. Aprire il progetto Android di esempio di GitHub in Android Studio. Il progetto si trova nella directory seguente della copia clonata o scaricata del repository [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java): *\azure-iot-samples-java\iot-hub\Samples\device\AndroidSample* .

2. In Android Studio aprire *gradle.properties* per il progetto di esempio e sostituire il segnaposto **Device_Connection_String** con la stringa di connessione del dispositivo annotata in precedenza.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. In Android Studio fare clic su **File** > **Sync Project with Gradle Files** . Verificare il completamento della compilazione.

   > [!NOTE]
   > L'esito negativo della sincronizzazione del progetto può essere dovuto a uno dei motivi seguenti:
   >
   > * Le versioni del plug-in Android Gradle e di Gradle a cui si fa riferimento nel progetto non sono aggiornate per la versione di Android Studio in uso. Per fare riferimento e installare le versioni corrette del plug-in e di Gradle per l'installazione, seguire [queste istruzioni](https://developer.android.com/studio/releases/gradle-plugin).
   > * Il contratto di licenza per Android SDK non è stato firmato. Seguire le istruzioni nell'output di compilazione per firmare il contratto di licenza e scaricare l'SDK.

4. Una volta completata la compilazione, fare clic su **Esegui** > **Esegui "app"** . Configurare l'app in modo da eseguirla su un dispositivo Android fisico o un emulatore Android. Per altre informazioni sull'esecuzione di un'app Android in un dispositivo fisico o un emulatore, vedere [Eseguire l'app](https://developer.android.com/training/basics/firstapp/running-app).

5. Una volta caricata l'app, fare clic su **Avvia** per avviare l'invio di dati di telemetria all'hub IoT:

    ![Screenshot di esempio dell'app del dispositivo client Android](media/quickstart-control-device-android/sample-screenshot.png)

Questa app deve essere lasciata in esecuzione in un dispositivo fisico o un emulatore mentre si esegue l'esempio dell'SDK di servizio per aggiornare l'intervallo dei dati di telemetria durante la fase di esecuzione.

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

In questa sezione si eseguirà il monitoraggio dei messaggi inviati dal dispositivo Android usando Azure Cloud Shell con l'[estensione IoT](/cli/azure/ext/azure-iot/iot?view=azure-cli-latest).

1. Tramite Azure Cloud Shell, eseguire il comando seguente per connettersi e leggere i messaggi dall'hub IoT:

   **YourIoTHubName** : sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    La schermata seguente mostra l'output mentre l'hub IoT riceve i dati di telemetria inviati dal dispositivo Android:

      ![Leggere i messaggi del dispositivo tramite l'interfaccia della riga di comando di Azure](media/quickstart-control-device-android/read-data.png)

Per impostazione predefinita l'app di telemetria invia i dati di telemetria dal dispositivo Android ogni 5 secondi. Nella sezione successiva, si userà una chiamata al metodo diretto per aggiornare l'intervallo dei dati di telemetria per il dispositivo IoT Android.

## <a name="call-the-direct-method"></a>Chiamare il metodo diretto

L'applicazione di servizio si connette a un endpoint sul lato servizio nell'hub IoT dell'utente. L'applicazione effettua chiamate dei metodi diretti a un dispositivo tramite l'hub IoT e rimane in ascolto degli acknowledgement.

Eseguire questa app su un dispositivo Android fisico o un emulatore Android.

Un'applicazione di servizio back-end dell'hub IoT in genere viene eseguita nel cloud, dove è più facile attenuare i rischi associati alla stringa di connessione sensibile che controlla tutti i dispositivi in un hub IoT. In questo esempio, viene eseguita come un'app Android unicamente a scopo dimostrativo. Le altre versioni di linguaggio di questo argomento di avvio rapido forniscono esempi che si allineano in modo più preciso con una tipica applicazione di servizio back-end.

1. Aprire il progetto Android di esempio del servizio GitHub in Android Studio. Il progetto si trova nella directory seguente della copia clonata o scaricata del repository [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) *\azure-iot-samples-java\iot-hub\Samples\service\AndroidSample* .

2. In Android Studio aprire *gradle.properties*  per il progetto di esempio. Sostituire il valore delle proprietà **ConnectionString** e **DeviceId** con la stringa di connessione di servizio annotata in precedenza e l'ID del dispositivo Android registrato.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. In Android Studio fare clic su **File** > **Sync Project with Gradle Files** . Verificare il completamento della compilazione.

   > [!NOTE]
   > L'esito negativo della sincronizzazione del progetto può essere dovuto a uno dei motivi seguenti:
   >
   > * Le versioni del plug-in Android Gradle e di Gradle a cui si fa riferimento nel progetto non sono aggiornate per la versione di Android Studio in uso. Per fare riferimento e installare le versioni corrette del plug-in e di Gradle per l'installazione, seguire [queste istruzioni](https://developer.android.com/studio/releases/gradle-plugin).
   > * Il contratto di licenza per Android SDK non è stato firmato. Seguire le istruzioni nell'output di compilazione per firmare il contratto di licenza e scaricare l'SDK.

4. Una volta completata la compilazione, fare clic su **Esegui** > **Esegui "app"** . Configurare l'app in modo da eseguirla su un dispositivo Android fisico o un emulatore Android separato. Per altre informazioni sull'esecuzione di un'app Android in un dispositivo fisico o un emulatore, vedere [Eseguire l'app](https://developer.android.com/training/basics/firstapp/running-app).

5. Una volta caricata l'app, aggiornare il valore **Set Messaging Interval** su **1000** e fare clic su **Invoke** .

    L'intervallo di messaggistica dei dati di telemetria è espresso in millisecondi. L'intervallo dei dati di telemetria predefinito dell'esempio di dispositivo è impostato per 5 secondi. Questa modifica aggiornerà il dispositivo IoT Android in modo che i dati di telemetria vengano inviati ogni secondo.

    ![Immettere l'intervallo dei dati di telemetria](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. L'app riceverà un acknowledgement che indica se il metodo è stato eseguito correttamente o meno.

    ![Acknowledgement del metodo diretto](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato chiamato un metodo diretto su un dispositivo da un'applicazione back-end ed è stata inviata una risposta alla chiamata del metodo diretto in un'applicazione del dispositivo simulato.

Per informazioni su come indirizzare messaggi da dispositivo a cloud a diverse destinazioni nel cloud, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Esercitazione: indirizzare i dati di telemetria a endpoint diversi per l'elaborazione](tutorial-routing.md)
