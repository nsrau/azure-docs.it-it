---
title: Caricare file da dispositivi nell'hub IoT di Azure con Python | Microsoft Docs
description: Come caricare file da un dispositivo al cloud usando Azure IoT SDK per dispositivi per Python. I file caricati vengono salvati in un contenitore BLOB di archiviazione di Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 47fb7c615389e24322450ed1785aa7da9ec50db6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759701"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Caricare file dal dispositivo al cloud con IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Questo articolo mostra come usare le [funzionalità di caricamento dei file dell'hub IoT](iot-hub-devguide-file-upload.md) per caricare un file in una risorsa di [archiviazione BLOB di Azure](../storage/index.yml). L'esercitazione illustra come:

* Fornire in modo sicuro un contenitore di archiviazione per il caricamento di un file.

* Usare il client Python per caricare un file tramite l'hub IoT.

La guida introduttiva [Invia dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-python.md) illustra la funzionalità di base di messaggistica da dispositivo a cloud dell'hub IoT.The Send telemetry from a device to an IoT hub quickstart demonstrates the basic device-to-cloud messaging functionality of IoT Hub. Tuttavia in alcuni scenari non è possibile mappare facilmente i dati che i dispositivi inviano in messaggi relativamente ridotti da dispositivo a cloud, che l'hub IoT accetta. Quando è necessario caricare file da un dispositivo, è comunque possibile usare la sicurezza e l'affidabilità dell'hub IoT.

Al termine di questa esercitazione, si esegue l'app console Python:

* **FileUpload.py**, che carica un file nella risorsa di archiviazione tramite Python SDK per dispositivi.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo in questo articolo usa il protocollo MQTT, che comunica tramite la porta 8883.The device sample in this article uses MQTT protocol, which communicates over port 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Caricare un file da un'app per dispositivi

In questa sezione viene creata l'app del dispositivo per caricare un file nell'hub IoT.

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto **azure-iot-device.** Utilizzare questo pacchetto per coordinare il caricamento del file con l'hub IoT.You use this package to coordinate the file upload with your IoT hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Al prompt dei comandi eseguire il comando seguente per installare il pacchetto [**azure.storage.blob.At**](https://pypi.org/project/azure-storage-blob/) your command prompt, run the following command to install the azure.storage.blob package. Utilizzare questo pacchetto per eseguire il caricamento del file.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Creare un file di test da caricare nell'archiviazione BLOB.

1. Con un editor di testo, creare un file **FileUpload.py** nella cartella di lavoro.

1. Aggiungere le variabili e le istruzioni `import` seguenti all'inizio del file **FileUpload.py**.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Nel file sostituire `[Device Connection String]` con la stringa di connessione del dispositivo dell'hub IoT. Sostituire `[Full path to local file]` con il percorso del file di test creato o con qualsiasi file nel dispositivo che si desidera caricare.

1. Creare una funzione per caricare il file nell'archiviazione BLOB:Create a function to upload the file to blob storage:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Questa funzione analizza la *struttura blob_info* passata per creare un URL utilizzato per inizializzare un [azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python). Quindi carica il file nell'archiviazione BLOB di Azure usando questo client.

1. Aggiungere il codice seguente per connettere il client e caricare il file:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Questo codice crea un **IoTHubDeviceClient asincrono** e usa le API seguenti per gestire il caricamento del file con l'hub IoT:This code creates an asynchronous IoTHubDeviceClient and uses the following APIs to manage the file upload with your IoT hub:

    * **get_storage_info_for_blob** ottiene informazioni dall'hub IoT sull'account di archiviazione collegato creato in precedenza. Queste informazioni includono il nome host, il nome del contenitore, il nome del BLOB e un token di firma di accesso condiviso. Le informazioni di archiviazione vengono passate alla funzione **store_blob** (creata nel passaggio precedente), pertanto il BlobClient in tale funzione può eseguire l'autenticazione con l'archiviazione di Azure.The storage info is passed to the store_blob function (created in the previous step), so the **BlobClient** in that function can authenticate with Azure storage. Il **metodo get_storage_info_for_blob** restituisce anche un correlation_id, che viene utilizzato nel metodo **notify_blob_upload_status.** Il correlation_id è il modo in cui l'hub IoT contrassegna il BLOB su cui si sta lavorando.

    * **notify_blob_upload_status** notifica all'hub IoT lo stato dell'operazione di archiviazione BLOB. Si passa il correlation_id ottenuto dal **metodo get_storage_info_for_blob.** Viene usato dall'hub IoT per notificare a qualsiasi servizio che potrebbe essere in ascolto di una notifica sullo stato dell'attività di caricamento file.

1. Salvare e chiudere il file **UploadFile.py**.

## <a name="run-the-application"></a>Eseguire l'applicazione

A questo punto si è pronti per eseguire l'applicazione.

1. Al prompt dei comandi nella cartella di lavoro eseguire il comando seguente:

    ```cmd/sh
    python FileUpload.py
    ```

2. Lo screenshot seguente mostra l'output dell'app **FileUpload**:

    ![Output dell'app simulated-device](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Per visualizzare il file caricato nel contenitore di archiviazione configurato, è possibile usare il portale:

    ![File caricato](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare le funzionalità di caricamento file dell'hub IoT per semplificare i caricamenti di file dai dispositivi. È possibile continuare a esplorare le funzionalità e gli scenari dell'hub IoT vedendo i seguenti articoli:

* [Creare un hub IoT a livello di codice](iot-hub-rm-template-powershell.md)

* [Introduzione a C SDK](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

Altre informazioni sull'archiviazione BLOB di Azure con i collegamenti seguenti:Learn more about Azure Blob Storage with the following links:

* [Documentazione sull'archiviazione BLOB di AzureAzure Blob Storage documentation](https://docs.microsoft.com/azure/storage/blobs/)

* [Documentazione sull'API Azure Blob Storage for Python](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
