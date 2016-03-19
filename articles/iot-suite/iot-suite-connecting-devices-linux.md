<properties
   pageTitle="Connettere un dispositivo utilizzando C in Linux | Microsoft Azure"
   description="Descrive come connettere un dispositivo alla soluzione di monitoraggio remoto preconfigurata Azure IoT Suite con un’applicazione scritta in C in esecuzione in Linux."
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
   ms.date="02/04/2016"
   ms.author="dobett"/>


# Connettere il dispositivo alla soluzione preconfigurata per il monitoraggio remoto IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilare ed eseguire la soluzione di esempio C in Linux

1. Per clonare l’archivio GitHub *SDK di Microsoft Azure IoT* e installare l’*SDK per dispositivi Microsoft Azure IoT per C* nel proprio ambiente desktop di Linux, seguire le istruzioni per la [Configurazione di un ambiente di sviluppo Linux][lnk-setup-linux].

2. Aprire il file **c/serializer/samples/remote\_monitoring/remote\_monitoring.c** in un editor di testo.

3. Trovare il codice seguente nel file:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. Sostituire **[Id dispositivo]** e **[Chiave dispositivo]** con i valori per il dispositivo dal dashboard della soluzione di monitoraggio remoto.

5. Utilizzare il **Nome host hub IoT** dal dashboard per sostituire **[Nome IoTHub]** e **[Suffisso IoTHub, ad esempio azure-devices.net]**. Ad esempio, se il **Nome host hub IoT** è **contoso.azure devices.net**, sostituire **[Nome IoTHub]** con **contoso** e sostituire **[Suffisso IoTHub, ad esempio azure-devices.net]** con **azure devices.net** come illustrato di seguito:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Salvare le modifiche e compilare gli esempi. Per compilare l'esempio, è possibile eseguire lo script build.sh nella directory **c/build\_all/linux**. Lo script di compilazione crea una cartella **cmake** per archiviare i programmi di esempio compilati.

7. Eseguire l'applicazione di esempio **cmake/serializer/samples/remote\_monitoring/remote\_monitoring**.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0211_2016-->