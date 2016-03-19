<properties
   pageTitle="Connettere un dispositivo utilizzando C in mbed | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in un dispositivo mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="dobett"/>


# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilare ed eseguire la soluzione di esempio C in mbed

Le istruzioni seguenti descrivono i passaggi per connettere un dispositivo [Freescale FRDM-K64F abilitato per mbed][lnk-mbed-home] alla soluzione di monitoraggio remoto.

### Connettere il dispositivo alla rete e al computer desktop

1. Connettere il dispositivo mbed alla rete con un cavo Ethernet. Questo passaggio è necessario perché l'applicazione di esempio richiede l'accesso a Internet.

2. Vedere [Guida introduttiva a mbed][lnk-mbed-getstarted] per connettere il dispositivo mbed al PC desktop.

3. Se il PC desktop esegue Windows, vedere [Configurazione PC][lnk-mbed-pcconnect] per configurare l'accesso alla porta seriale al dispositivo mbed.

### Creare un progetto mbed e importare il codice di esempio

1. Nel Web browser passare al [sito per sviluppatori](https://developer.mbed.org/) mbed.org. Se non si è iscritti, verrà visualizzata un'opzione per creare un nuovo account (gratuito). In caso contrario, accedere con le credenziali dell'account. Fare quindi clic su **Compiler** nell'angolo superiore destro della pagina. Si dovrebbe passare all'interfaccia di Workspace Management.

2. Verificare che la piattaforma hardware usata venga visualizzata nell'angolo superiore destro della finestra oppure fare clic sull'icona nell'angolo destro per selezionare la piattaforma hardware.

3. Fare clic su **Import** nel menu principale. Fare quindi clic sul collegamento **Click here** per importare da URL accanto al logo con il mondo di mbed.

    ![][6]

4. Nella finestra popup immettere il collegamento per il codice di esempio https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ quindi fare clic su **Import**.

    ![][7]

5. Nella finestra del compilatore mbed è possibile osservare che l'importazione del progetto include diverse librerie. Alcune sono fornite e gestite dal team IoT di Azure ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), altre invece sono librerie di terze parti disponibili nel catalogo delle librerie di mbed.

    ![][8]

6. Aprire il file remote\_monitoring\\remote\_monitoring.c e trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Sostituire [Device Id] e [Device Key] con i dati del dispositivo. Usare il nome host dell'hub IoT Hub per sostituire i segnaposto [IoTHub Name] e [IoTHub Suffix, ovvero azure-devices.net]. Ad esempio, se il nome host dell'hub IoT è contoso.azure-devices.net, contoso sarà **hubName** e tutto ciò che segue sarà **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### Compilare ed eseguire il programma

1. Fare clic su **Compila** per compilare il programma. È possibile ignorare eventuali avvisi, ma, se la compilazione genera errori, correggerli prima di continuare.

2. Se la compilazione ha esito positivo, il sito Web del compilatore mbed genera un file .bin con il nome del progetto che viene scaricato nel computer locale. Copiare il file bin sul dispositivo. Il salvataggio del file .bin nel dispositivo causa il riavvio del dispositivo e l'esecuzione del programma contenuto nel file .bin. È possibile riavviare manualmente il programma in qualsiasi momento facendo clic sul pulsante reset sul dispositivo mbed.

3. Connettersi al dispositivo con un'applicazione client SSH, ad esempio PuTTY. È possibile determinare la porta seriale usata dal dispositivo controllando Gestione dispositivi di Windows.

    ![][11]

4. In PuTTY fare clic sul tipo di connessione **Serial**. Poiché il dispositivo si connette in genere a 115200 baud, immettere 115200 nella casella **Velocità**. Fare quindi clic su **Apri**.

5. Inizia l'esecuzione del programma. Potrebbe essere necessario reimpostare la scheda (premere CTRL+INTERR o premere il pulsante reset della scheda) se il programma non si avvia automaticamente quando ci si connette.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

<!---HONumber=AcomDC_0218_2016-->