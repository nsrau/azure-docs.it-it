---
title: Caricare file da dispositivi nell'hub IoT di Azure con Python | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Python. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: kgremban
ms.openlocfilehash: 6195c37780acaf8c8f432fa09c5ac01a75363c04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441305"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Caricare file da un dispositivo al cloud con l'hub IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questo articolo mostra come usare le [funzionalità di caricamento dei file dell'hub IoT](iot-hub-devguide-file-upload.md) per caricare un file in una risorsa di [archiviazione BLOB di Azure](../storage/index.yml). L'esercitazione illustra come:

* Fornire in modo sicuro un contenitore di archiviazione per il caricamento di un file.

* Usare il client Python per caricare un file tramite l'hub IoT.

L'argomento di avvio rapido [Inviare dati di telemetria all'hub IoT](quickstart-send-telemetry-python.md) illustra le funzionalità di messaggistica di base da dispositivo a cloud dell'hub IoT. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

> [!NOTE]
> Python SDK per hub IoT attualmente supporta solo il caricamento dei file basati sui caratteri come i file **.txt**.

Al termine di questa esercitazione eseguire l'app console Python:

* **FileUpload.py**, che carica un file nella risorsa di archiviazione tramite Python SDK per dispositivi.

> [!NOTE]
> L'hub IoT supporta molte piattaforme e linguaggi, tra cui C, .NET, Javascript, Python e Java, tramite gli Azure IoT SDK per dispositivi. Vedere il [Centro per sviluppatori di IoT di Azure](https://azure.microsoft.com/develop/iot) per istruzioni dettagliate su come connettere il dispositivo all'Hub IoT di Azure.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Python 2.x o 3.x](https://www.python.org/downloads/). Assicurarsi di usare le installazioni a 32 bit o 64 bit, come richiesto dalla configurazione. Quando richiesto durante l'installazione, assicurarsi di aggiungere Python alla variabile di ambiente specifica per la piattaforma. Se si usa Python 2.x, potrebbe essere necessario [installare o aggiornare *pip*, il sistema di gestione pacchetti Python](https://pip.pypa.io/en/stable/installing/).

* Se si usa il sistema operativo Windows, usare il [pacchetto ridistribuibile di Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) per consentire l'uso di DLL native da Python.

* Un account Azure attivo. Se non si ha un account, è possibile creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) in pochi minuti.

* Un hub IoT nel proprio account Azure, con un'identità di dispositivo per testare la funzionalità di caricamento dei file. 

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione viene creata l'app del dispositivo per caricare un file nell'hub IoT.

1. Al prompt dei comandi, eseguire il comando seguente per installare il pacchetto **azure-iothub-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Con un editor di testo, creare un file di test da caricare nella risorsa di archiviazione BLOB.

    > [!NOTE]
    > Python SDK per hub IoT attualmente supporta solo il caricamento dei file basati sui caratteri come i file **.txt**.

3. Con un editor di testo, creare un file **FileUpload.py** nella cartella di lavoro.

4. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **FileUpload.py**. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Nel file sostituire `[Device Connection String]` con la stringa di connessione del dispositivo dell'hub IoT. Sostituire `[Full path to file]` con il percorso del file di test creato o di qualsiasi file nel dispositivo che si vuole caricare. Sostituire `[File name for storage]` con il nome che si vuole assegnare al file dopo che è stato caricato nella risorsa di archiviazione BLOB. 

6. Creare un callback per la funzione **upload_blob**:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Aggiungere il codice seguente per connettere il client e caricare il file. Includere anche la routine `main`:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. Salvare e chiudere il file **UploadFile.py**.

## <a name="run-the-application"></a>Eseguire l'applicazione

A questo punto è possibile eseguire l'applicazione.

1. Al prompt dei comandi nella cartella di lavoro eseguire il comando seguente:

    ```cmd/sh
    python FileUpload.py
    ```

2. Lo screenshot seguente mostra l'output dell'app **FileUpload**:

    ![Output dell'app simulated-device](./media/iot-hub-python-python-file-upload/1.png)

3. Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

    ![File caricato](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)