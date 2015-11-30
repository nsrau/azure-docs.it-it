<properties
   pageTitle="Connettere un dispositivo tramite C in Windows | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in Windows."
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilare ed eseguire la soluzione di esempio C in Windows

1. Per clonare l’archivio GitHub *SDK di Microsoft Azure IoT* e installare l’*SDK per dispositivi Microsoft Azure IoT per C* nel proprio ambiente desktop di Windows, seguire le istruzioni per la [Configurazione di un ambiente di sviluppo Windows][lnk-setup-windows].

2. In Visual Studio 2015, aprire la soluzione **remote\_monitoring.sln** nella cartella **c\\serializer\\samples\\remote\_monitoring\\windows** nella copia locale del repository.

3. In **Esplora soluzioni**, nel progetto **remote\_monitoring** aprire il file **remote\_monitoring.c**.

4. Trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. Sostituire **[Id dispositivo]** e **[Chiave dispositivo]** con i valori per il dispositivo dal dashboard della soluzione di monitoraggio remoto.

6. Utilizzare il **Nome host hub IoT** dal dashboard per sostituire **[Nome IoTHub]** e **[Suffisso IoTHub, ad esempio azure-devices.net]**. Ad esempio, se il **Nome host hub IoT** è **contoso.azure devices.net**, sostituire **[Nome IoTHub]** con **contoso** e sostituire **[Suffisso IoTHub, ad esempio azure-devices.net]** con **azure devices.net** come illustrato di seguito:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **remote\_monitoring**, scegliere **Debug** e quindi fare clic su **Avvia nuova istanza** per compilare ed eseguire l'esempio. La console visualizza i messaggi quando l'applicazione invia all'hub IoT un esempio di telemetria.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=Nov15_HO4-->