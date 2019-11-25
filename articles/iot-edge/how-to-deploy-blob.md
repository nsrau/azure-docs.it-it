---
title: Deploy the Azure Blob Storage module to devices - Azure IoT Edge | Microsoft Docs
description: Distribuire un modulo di archiviazione BLOB di Azure al dispositivo IoT Edge per archiviare i dati nei dispositivi perimetrali.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: e5241e078cd8d36a9e43b4b55a649c1e24c85345
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456874"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Deploy the Azure Blob Storage on IoT Edge module to your device

There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Blob Storage on IoT Edge modules. I due metodi più semplici consistono nell'usare i modelli di Visual Studio Code o il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure.
- Un [dispositivo IoT Edge](how-to-register-device.md) con il runtime di IoT Edge installato.
- [Visual Studio Code](https://code.visualstudio.com/) and the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) if deploying from Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Deploy from the Azure portal

The Azure portal guides you through creating a deployment manifest and pushing the deployment to an IoT Edge device.

### <a name="select-your-device"></a>Selezionare il dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Selezionare **Set Modules** (Configura i moduli).

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. The Azure portal has a wizard that walks you through creating a deployment manifest, instead of building the JSON document manually. Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

#### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione dei **moduli di distribuzione** della pagina fare clic su **Aggiungi**.

1. From the types of modules in the drop-down list, select **IoT Edge Module**.

1. Provide a name for the module and then specify the container image:

   - **Name** - azureblobstorageoniotedge
   - **Image URI** - mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge is case-sensitive when you make calls to modules, and the Storage SDK also defaults to lowercase. Although the name of the module in the [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) is **AzureBlobStorageonIoTEdge**, changing the name to lowercase helps to ensure that your connections to the Azure Blob Storage on IoT Edge module aren't interrupted.

1. The default **Container Create Options** values define the port bindings that your container needs, but you also need to add your storage account information and a mount for the storage on your device. Replace the default JSON in the portal with the JSON below:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Aggiornare il codice JSON copiato con le informazioni seguenti:

   - Sostituire `<your storage account name>` con un nome facile da ricordare. Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

   - Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

   - Replace `<storage mount>` according to your container operating system. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. The storage mount maps a location on your device that you provide to a set location in the module.

     - For Linux containers, the format is *\<storage path or volume>:/blobroot*. Ad esempio
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot** 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Make sure to follow the steps to [grant directory access to the container user](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - For Windows containers, the format is *\<storage path or volume>:C:/BlobRoot*. Ad esempio
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**. 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Instead of using your local drive, you can map your SMB network location, for more information see [using SMB share as your local storage](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Do not change the second half of the storage mount value, which points to a specific location in the module. The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Set [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) properties for your module by copying the following JSON and pasting it into the **Set module twin's desired properties** box. Configure each property with an appropriate value, save it, and continue with the deployment. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section of [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![set container create options, deviceAutoDeleteProperties and deviceToCloudUploadProperties properties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   For information on configuring deviceToCloudUploadProperties and deviceAutoDeleteProperties after your module has been deployed, see [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). For more information about desired properties, see [Define or update desired properties](module-composition.md#define-or-update-desired-properties).

1. Selezionare **Salva**.

1. Scegliere **Avanti** per proseguire con la sezione relativa alle route.

#### <a name="specify-routes"></a>Specificare le route

Keep the default routes, and select **Next** to continue to the review section.

#### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. There are also two modules declared that you didn't add: **$edgeAgent** and **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

### <a name="verify-your-deployment"></a>Verify your deployment

Dopo l'invio della distribuzione, si torna alla pagina **IoT Edge** dell'hub IoT.

1. Selezionare il dispositivo IoT Edge impostato come destinazione della distribuzione per aprirne i dettagli.
1. Nei dettagli del dispositivo verificare che il modulo di archiviazione BLOB sia elencato sia come **Specificato nella distribuzione** che come **Segnalato dal dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="deploy-from-visual-studio-code"></a>Deploy from Visual Studio Code

Azure IoT Edge fornisce modelli di Visual Studio Code per assistere allo sviluppo di soluzioni per dispositivi perimetrali. Use the following steps to create a new IoT Edge solution with a blob storage module and to configure the deployment manifest.

1. Selezionare **Visualizza** > **Riquadro comandi**.

1. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge).

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Value |
   | ----- | ----- |
   | Selezionare la cartella | Choose the location on your development machine for Visual Studio Code to create the solution files. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Existing Module (Enter full image URL)** (Modulo esistente - Immettere l'URL completo dell'immagine). |
   | Provide a module name (Specificare un nome per il modulo) | Enter an all-lowercase name for your module, like **azureblobstorageoniotedge**.<br /><br />È importante usare un nome in lettere minuscole per il modulo Azure Blob Storage on IoT Edge. IoT Edge distingue tra maiuscole e minuscole nei riferimenti ai moduli e Storage SDK usa per impostazione predefinita caratteri minuscoli. |
   | Provide Docker image for the module (Specificare l'immagine Docker per il modulo) | Specificare l'URI dell'immagine: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra. Il modello di soluzione crea un modello di manifesto della distribuzione che include l'immagine del modulo di archiviazione BLOB, ma è necessario configurare le opzioni per la creazione del modulo.

1. Aprire *deployment.template.json* nell'area di lavoro della nuova soluzione e trovare la sezione **moduli**. Apportare le modifiche seguenti alla configurazione:

   1. Delete the **SimulatedTemperatureSensor** module, as it's not necessary for this deployment.

   1. Copy and paste the following code into the `createOptions` field:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Update module createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Sostituire `<your storage account name>` con un nome facile da ricordare. Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

1. Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

1. Replace `<storage mount>` according to your container operating system. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. The storage mount maps a location on your device that you provide to a set location in the module.  

      
     - For Linux containers, the format is *\<storage path or volume>:/blobroot*. Ad esempio
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot** 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Make sure to follow the steps to [grant directory access to the container user](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - For Windows containers, the format is *\<storage path or volume>:C:/BlobRoot*. Ad esempio
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**. 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Instead of using your local drive, you can map your SMB network location, for more information see [using SMB share as your local storage](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Do not change the second half of the storage mount value, which points to a specific location in the module. The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Configure [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) for your module by adding the following JSON to the *deployment.template.json* file. Configure each property with an appropriate value and save the file. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section of [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![set desired properties for azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   For information on configuring deviceToCloudUploadProperties and deviceAutoDeleteProperties after your module has been deployed, see [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). For more information about container create options, restart policy, and desired status, see [EdgeAgent desired properties](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salvare il file *deployment.template.json*.

1. Fare clic con il pulsante destro del mouse su **deployment.template.json** e selezionare **Generare manifesto di distribuzione di IoT Edge**.

1. Visual Studio Code takes the information that you provided in *deployment.template.json* and uses it to create a new deployment manifest file. Il manifesto di distribuzione viene creato in una nuova cartella **config** nell'area di lavoro della soluzione. Dopo aver creato tale file, è possibile seguire i passaggi descritti in [Distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md) oppure [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando 2.0 di Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Deploy multiple module instances

If you want to deploy multiple instances of the Azure Blob Storage on IoT Edge module, you need to provide a different storage path and change the `HostPort` value that the module binds to. I moduli di archiviazione BLOB espongono sempre la porta 11002 nel contenitore, ma è possibile dichiarare a quale porta sono associati nell'host.

Edit **Container Create Options** (in the Azure portal) or the **createOptions** field (in the *deployment.template.json* file in Visual Studio Code) to change the `HostPort` value:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando ci si connette ai moduli di archiviazione BLOB aggiuntivi, modificare l'endpoint in modo che punti alla porta host aggiornata.

## <a name="next-steps"></a>Passaggi successivi
Learn more about [Azure Blob Storage on IoT Edge](how-to-store-data-blob.md)

Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).
