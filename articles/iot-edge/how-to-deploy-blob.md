---
title: Distribuire il modulo di archiviazione Blob di Azure per i dispositivi - Azure IoT Edge | Microsoft Docs
description: Distribuire un modulo di archiviazione BLOB di Azure al dispositivo IoT Edge per archiviare i dati nei dispositivi perimetrali.
author: arduppal
ms.author: arduppal
ms.date: 06/19/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 468e4fca5e67850949e7d5826e4bc88fa504b9d6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295229"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Distribuire l'archiviazione Blob di Azure nel modulo di IoT Edge nel dispositivo

Esistono diversi modi per distribuire moduli in un dispositivo IoT Edge e tutti gli elementi di lavoro per l'archiviazione Blob di Azure nei moduli di IoT Edge. I due metodi più semplici consistono nell'usare i modelli di Visual Studio Code o il portale di Azure.

> [!NOTE]
> L'archiviazione BLOB di Azure in IoT Edge è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure.
- Un [dispositivo IoT Edge](how-to-register-device-portal.md) con il runtime di IoT Edge installato.
- [Visual Studio Code](https://code.visualstudio.com/) e il [strumenti di Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) se la distribuzione da Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Distribuzione dal portale di Azure

Il portale di Azure illustra la creazione di un manifesto di distribuzione e il push della distribuzione in un dispositivo IoT Edge.

### <a name="select-your-device"></a>Selezionare il dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Selezionare **Set Modules** (Configura i moduli).

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure include una procedura guidata che illustra come creare un manifesto della distribuzione, invece di creare manualmente il documento JSON. Sono previsti tre passaggi: **Aggiungi moduli**, **Specifica route** e **Rivedi distribuzione**.

#### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione dei **moduli di distribuzione** della pagina fare clic su **Aggiungi**.

1. Uno dei tipi di moduli nell'elenco a discesa, selezionare **modulo di IoT Edge**.

1. Specificare un nome per il modulo e quindi specificare l'immagine del contenitore:

   - **Nome** -azureblobstorageoniotedge
   - **URI immagine** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge è distinzione maiuscole/minuscole quando si effettuano chiamate ai moduli e il SDK di archiviazione per impostazione predefinita in caratteri minuscoli. Anche se il nome del modulo nel [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) viene **AzureBlobStorageonIoTEdge**, la modifica del nome in lettere minuscole aiuta a garantire che le connessioni all'archiviazione Blob di Azure nel modulo di IoT Edge non vengano interrotte.

1. Il valore predefinito **opzioni di creazione del contenitore** valori definiscono il binding delle porte che il contenitore deve, ma è anche necessario aggiungere le informazioni sull'account di archiviazione e un binding per la directory di archiviazione nel dispositivo. Sostituire il valore predefinito JSON nel portale con il codice JSON seguente:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Aggiornare il codice JSON copiato con le informazioni seguenti:

   - Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account deve essere compreso tra 3 e 24 caratteri, con lettere minuscole e numeri. Senza spazi.

   - Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

   - Sostituire `<storage directory bind>` in base al sistema operativo contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. L'associazione per la directory di archiviazione corrisponde a una posizione nel dispositivo specificata per una posizione impostata nel modulo.

     - Per i contenitori Linux, è il formato  *\<percorso di archiviazione >: / blobroot*. Ad esempio, **/srv/containerdata: / blobroot** oppure **my-volume: / blobroot**.
     - Per i contenitori di Windows, è il formato  *\<percorso di archiviazione >: C: / BlobRoot*. Ad esempio, **c: / ContainerData:C: / BlobRoot** oppure **my-volume: C: / blobroot**.

     > [!IMPORTANT]
     > Non modificare la seconda metà del valore dell'associazione per la directory di archiviazione, che fa riferimento a una posizione specifica nel modulo. L'associazione per la directory di archiviazione deve sempre terminare con **:/blobroot** per i contenitori Linux e **:C:/BlobRoot** per i contenitori Windows.

1. Impostare [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) delle proprietà per il modulo copiando il codice JSON seguente e averla poi incollata nel **desiderate del dispositivo gemello modulo Set proprietà** casella. Configurare ogni proprietà con un valore appropriato, salvarlo e continuare con la distribuzione.

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

   ![contenitore di set di creare proprietà deviceToCloudUploadProperties, deviceAutoDeleteProperties e opzioni](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Per informazioni sulla configurazione deviceToCloudUploadProperties e deviceAutoDeleteProperties dopo la distribuzione di un modulo, vedere [modificare il modulo gemello](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Per altre informazioni sulle proprietà desiderate, vedere [definire o aggiornare le proprietà desiderate](module-composition.md#define-or-update-desired-properties).

1. Selezionare **Salva**.

1. Scegliere **Avanti** per proseguire con la sezione relativa alle route.

#### <a name="specify-routes"></a>Specificare le route

Mantenere le route predefinite e selezionare **successivo** per continuare con la sezione di verifica.

#### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Esistono anche due moduli dichiarate che non sono state aggiunte: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

### <a name="verify-your-deployment"></a>Verifica della distribuzione

Dopo l'invio della distribuzione, si torna alla pagina **IoT Edge** dell'hub IoT.

1. Selezionare il dispositivo IoT Edge impostato come destinazione della distribuzione per aprirne i dettagli.
1. Nei dettagli del dispositivo verificare che il modulo di archiviazione BLOB sia elencato sia come **Specificato nella distribuzione** che come **Segnalato dal dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="deploy-from-visual-studio-code"></a>Distribuire da Visual Studio Code

Azure IoT Edge fornisce modelli di Visual Studio Code per assistere allo sviluppo di soluzioni per dispositivi perimetrali. Usare la procedura seguente per creare una nuova soluzione IoT Edge con un modulo di archiviazione blob e per configurare il manifesto di distribuzione.

1. Selezionare **Visualizza** > **Riquadro comandi**.

1. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**.

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Value |
   | ----- | ----- |
   | Selezionare la cartella | Scegliere il percorso nel computer di sviluppo per Visual Studio Code creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Existing Module (Enter full image URL)** (Modulo esistente - Immettere l'URL completo dell'immagine). |
   | Provide a module name (Specificare un nome per il modulo) | Immettere un nome tutta in minuscolo per il modulo, ad esempio **azureblobstorageoniotedge**.<br /><br />È importante usare un nome in lettere minuscole per il modulo Azure Blob Storage on IoT Edge. IoT Edge distingue tra maiuscole e minuscole nei riferimenti ai moduli e Storage SDK usa per impostazione predefinita caratteri minuscoli. |
   | Provide Docker image for the module (Specificare l'immagine Docker per il modulo) | Specificare l'URI dell'immagine: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra. Il modello di soluzione crea un modello di manifesto della distribuzione che include l'immagine del modulo di archiviazione BLOB, ma è necessario configurare le opzioni per la creazione del modulo.

1. Aprire *deployment.template.json* nell'area di lavoro della nuova soluzione e trovare la sezione **moduli**. Apportare le modifiche seguenti alla configurazione:

   1. Eliminare il modulo **tempSensor**, perché non necessario per questa distribuzione.

   1. Copiare e incollare il codice seguente nel `createOptions` campo:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aggiornamento modulo createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account deve essere compreso tra 3 e 24 caratteri, con lettere minuscole e numeri. Senza spazi.

1. Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

1. Sostituire `<storage directory bind>` in base al sistema operativo contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. L'associazione per la directory di archiviazione corrisponde a una posizione nel dispositivo specificata per una posizione impostata nel modulo.  

      - Per i contenitori Linux, è il formato  *\<percorso di archiviazione >: / blobroot*. Ad esempio, **/srv/containerdata: / blobroot** oppure **my-volume: / blobroot**.
      - Per i contenitori di Windows, è il formato  *\<percorso di archiviazione >: C: / BlobRoot*. Ad esempio, **c: / ContainerData:C: / BlobRoot** oppure **my-volume: C: / blobroot**.

      > [!IMPORTANT]
      > Non modificare la seconda metà del valore dell'associazione per la directory di archiviazione, che fa riferimento a una posizione specifica nel modulo. L'associazione per la directory di archiviazione deve sempre terminare con **:/blobroot** per i contenitori Linux e **:C:/BlobRoot** per i contenitori Windows.

1. Configurare [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) per il modulo aggiungendo il codice JSON seguente per il *deployment.template.json* file. Configurare ogni proprietà con un valore appropriato e salvare il file.

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

   ![impostare le proprietà desiderate per azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Per informazioni sulla configurazione deviceToCloudUploadProperties e deviceAutoDeleteProperties dopo la distribuzione di un modulo, vedere [modificare il modulo gemello](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Per altre informazioni sul contenitore opzioni per la creazione, riavviare criteri e dallo stato previsto, vedere [proprietà desiderate di EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salvare il file *deployment.template.json*.

1. Fare clic con il pulsante destro del mouse su **deployment.template.json** e selezionare **Generare manifesto di distribuzione di IoT Edge**.

1. Visual Studio Code richiede le informazioni fornite in *deployment.template.json* e lo usa per creare un nuovo file manifesto di distribuzione. Il manifesto di distribuzione viene creato in una nuova cartella **config** nell'area di lavoro della soluzione. Dopo aver creato tale file, è possibile seguire i passaggi descritti in [Distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md) oppure [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando 2.0 di Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Distribuire più istanze del modulo

Se si desidera distribuire più istanze di archiviazione Blob di Azure nel modulo di IoT Edge, è necessario fornire un percorso di archiviazione diverso e modificare il `HostPort` valore cui si associa il modulo. I moduli di archiviazione BLOB espongono sempre la porta 11002 nel contenitore, ma è possibile dichiarare a quale porta sono associati nell'host.

Modificare **opzioni di creazione del contenitore** (nel portale di Azure) o il **createOptions** campo (nelle *deployment.template.json* file in Visual Studio Code) per modificare il `HostPort` valore:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando ci si connette ai moduli di archiviazione BLOB aggiuntivi, modificare l'endpoint in modo che punti alla porta host aggiornata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).
