---
title: Distribuire l'archiviazione BLOB nel modulo nel dispositivo - Azure IoT EdgeDeploy blob storage on module to your device - Azure IoT Edge
description: Distribuire un modulo di archiviazione BLOB di Azure al dispositivo IoT Edge per archiviare i dati nei dispositivi perimetrali.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133316"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Distribuire l'Archiviazione BLOB di Azure su un modulo IoT Edge al dispositivo

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per Archiviazione BLOB di Azure nei moduli IoT Edge.There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Blob Storage on IoT Edge modules. I due metodi più semplici consistono nell'usare i modelli di Visual Studio Code o il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un hub IoT nella sottoscrizione di Azure.An [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.
- Un [dispositivo IoT Edge](how-to-register-device.md) con il runtime di IoT Edge installato.
- [Codice](https://code.visualstudio.com/) di Visual Studio e gli strumenti IoT di Azure se si esegue la distribuzione da Visual Studio Code.Visual Studio Code and the Azure [IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) if deploying from Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Distribuire dal portale di AzureDeploy from the Azure portal

Il portale di Azure guida l'utente attraverso la creazione di un manifesto di distribuzione e il push della distribuzione in un dispositivo perimetrale IoT.The Azure portal guides you through creating a deployment manifest and pushing the deployment to an IoT Edge device.

### <a name="select-your-device"></a>Selezionare il dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.
1. Selezionare **Set Modules** (Configura i moduli).

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure include una procedura guidata che illustra come creare un manifesto di distribuzione. Ha tre passaggi organizzati in schede: **Moduli**, **Itinerari**, e Revisione **, Crea**.

#### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli perimetrali IoT** della pagina, fare clic sull'elenco a discesa **Aggiungi** e selezionare **Modulo Edge IoT** per visualizzare la pagina Aggiungi modulo Edge **IoT.**

2. Nella scheda **Impostazioni modulo** specificare un nome per il modulo e quindi specificare l'URI dell'immagine del contenitore:

   Esempi:
  
   - **Nome modulo bordo IoT**:`azureblobstorageoniotedge`
   - **URI immagine**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Impostazioni modulo Twin](./media/how-to-deploy-blob/addmodule-tab1.png)

   Non selezionare **Aggiungi** fino a quando non sono stati specificati i valori nelle schede **Impostazioni modulo**, Opzioni di creazione **contenitore**e **Impostazioni twin del modulo,** come descritto in questa procedura.

   > [!IMPORTANT]
   > Azure IoT Edge fa distinzione tra maiuscole e minuscole quando si effettuano chiamate ai moduli e l'SDK di archiviazione viene impostato anche come minuscolo. Anche se il nome del modulo in [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) è **AzureBlobStorageonIoTEdge**, la modifica del nome in minuscolo consente di garantire che le connessioni all'archiviazione BLOB di Azure nel modulo Edge IoT non vengano interrotte.

3. Aprire la scheda **Opzioni di creazione contenitore.**

   ![Impostazioni modulo Twin](./media/how-to-deploy-blob/addmodule-tab3.png)

   Copiare e incollare il codice JSON seguente nella casella per fornire informazioni sull'account di archiviazione e un montaggio per l'archiviazione nel dispositivo.
  
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

4. Aggiornare il codice JSON copiato in **Contenitore delle opzioni** di creazione con le informazioni seguenti:Update the JSON that you copied into Container Create Options with the following information:

   - Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account devono avere una lunghezza da 3 a 24 caratteri, con lettere minuscole e numeri. Nessuno spazio.

   - Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

   - Sostituire `<storage mount>` in base al sistema operativo del contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto di una directory esistente nel dispositivo IoT Edge in cui il modulo BLOB archivierà i dati. Il supporto di archiviazione esegue il mapping di una posizione sul dispositivo fornita a una posizione impostata nel modulo.

     - Per i contenitori Linux, il formato è * \<percorso di archiviazione o volume>:/blobroot*. Ad esempio:
         - usare [il montaggio](https://docs.docker.com/storage/volumes/)del volume : **my-volume:/blobroot**
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Assicurarsi di seguire la procedura per [concedere l'accesso alla directory all'utente contenitore](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Per i contenitori Windows, il formato è * \<percorso di archiviazione o volume>:C:/BlobRoot*. Ad esempio:
         - utilizzare [il montaggio](https://docs.docker.com/storage/volumes/)del volume : **my-volume:C:/blobroot**.
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Invece di usare l'unità locale, è possibile mappare il percorso di rete SMB, per ulteriori informazioni, vedere [Utilizzo della condivisione SMB come risorsa di archiviazione locale](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Non modificare la seconda metà del valore di montaggio dell'archiviazione, che punta a una posizione specifica nel modulo. Il mount di archiviazione deve terminare sempre con :/blobroot per i contenitori Linux e :C:/BlobRoot per i contenitori Windows.The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

5. Nella scheda **Impostazioni modulo gemello** copiare il codice JSON seguente e incollarlo nella casella.

   ![Impostazioni modulo Twin](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configurare ogni proprietà con un valore appropriato, come indicato dai segnaposto. Se si utilizza il simulatore IoT Edge, impostare i valori sulle variabili di ambiente correlate per queste proprietà come descritto da [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Per informazioni sulla configurazione di deviceToCloudUploadProperties e deviceAutoDeleteProperties dopo la distribuzione del modulo, vedere [Modificare il modulo Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Per ulteriori informazioni sulle proprietà desiderate, consultate [Definire o aggiornare le proprietà desiderate.](module-composition.md#define-or-update-desired-properties)

6. Selezionare **Aggiungi**.

7. Selezionare **Avanti: Percorsi** per continuare alla sezione percorsi.

#### <a name="specify-routes"></a>Specificare le route

Mantenere i percorsi predefiniti e selezionare **Avanti: Revisione e creazione** per continuare con la sezione di revisione.

#### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Ci sono anche due moduli dichiarati che non hai aggiunto: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Esaminare le informazioni di distribuzione, quindi selezionare **Crea**.

### <a name="verify-your-deployment"></a>Verificare la distribuzione

Dopo aver creato la distribuzione, si torna alla pagina **IoT Edge** dell'hub IoT.

1. Selezionare il dispositivo IoT Edge impostato come destinazione della distribuzione per aprirne i dettagli.
1. Nei dettagli del dispositivo verificare che il modulo di archiviazione BLOB sia elencato sia come **Specificato nella distribuzione** che come **Segnalato dal dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="deploy-from-visual-studio-code"></a>Distribuire da codice di Visual StudioDeploy from Visual Studio Code

Azure IoT Edge fornisce modelli di Visual Studio Code per assistere allo sviluppo di soluzioni per dispositivi perimetrali. Usare la procedura seguente per creare una nuova soluzione IoT Edge con un modulo di archiviazione BLOB e configurare il manifesto di distribuzione.

1. Selezionare **Visualizza** > **tavolozza comandi**.

1. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nuova soluzione IoT Edge).

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | valore |
   | ----- | ----- |
   | Selezionare la cartella | Scegliere il percorso nel computer di sviluppo per Visual Studio Code per creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Existing Module (Enter full image URL)** (Modulo esistente - Immettere l'URL completo dell'immagine). |
   | Provide a module name (Specificare un nome per il modulo) | Immettere un nome tutto minuscolo per il modulo, ad esempio **azureblobstorageoniotedge**.<br/><br/>È importante usare un nome in lettere minuscole per il modulo Azure Blob Storage on IoT Edge. IoT Edge distingue tra maiuscole e minuscole nei riferimenti ai moduli e Storage SDK usa per impostazione predefinita caratteri minuscoli. |
   | Provide Docker image for the module (Specificare l'immagine Docker per il modulo) | Specificare l'URI dell'immagine: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra. Il modello di soluzione crea un modello di manifesto della distribuzione che include l'immagine del modulo di archiviazione BLOB, ma è necessario configurare le opzioni per la creazione del modulo.

1. Aprire *deployment.template.json* nell'area di lavoro della nuova soluzione e trovare la sezione **moduli**. Apportare le modifiche seguenti alla configurazione:

   1. Eliminare il modulo **SimulatedTemperatureSensor,** in quanto non è necessario per questa distribuzione.

   1. Copiare e incollare `createOptions` il codice seguente nel campo:

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

      ![Modulo di aggiornamento createOptions - Codice di Visual StudioUpdate module createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account devono avere una lunghezza da 3 a 24 caratteri, con lettere minuscole e numeri. Nessuno spazio.

1. Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

1. Sostituire `<storage mount>` in base al sistema operativo del contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. Il supporto di archiviazione esegue il mapping di una posizione sul dispositivo fornita a una posizione impostata nel modulo.  

     - Per i contenitori Linux, il formato è * \<percorso di archiviazione o volume>:/blobroot*. Ad esempio:
         - usare [il montaggio](https://docs.docker.com/storage/volumes/)del volume : **my-volume:/blobroot**
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Assicurarsi di seguire la procedura per [concedere l'accesso alla directory all'utente contenitore](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Per i contenitori Windows, il formato è * \<percorso di archiviazione o volume>:C:/BlobRoot*. Ad esempio:
         - utilizzare [il montaggio](https://docs.docker.com/storage/volumes/)del volume : **my-volume:C:/blobroot**.
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Invece di usare l'unità locale, è possibile mappare il percorso di rete SMB, per ulteriori informazioni vedere [Utilizzo della condivisione SMB come](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Non modificare la seconda metà del valore di montaggio dell'archiviazione, che punta a una posizione specifica nel modulo. Il mount di archiviazione deve terminare sempre con :/blobroot per i contenitori Linux e :C:/BlobRoot per i contenitori Windows.The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Configurare [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) per il modulo aggiungendo il codice JSON seguente al file *deployment.template.json.* Configurare ogni proprietà con un valore appropriato e salvare il file. Se si utilizza il simulatore IoT Edge, impostare i valori sulle variabili di ambiente correlate per queste proprietà, disponibili nella sezione di spiegazione di [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   Per informazioni sulla configurazione di deviceToCloudUploadProperties e deviceAutoDeleteProperties dopo la distribuzione del modulo, vedere [Modificare il modulo Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Per ulteriori informazioni sulle opzioni di creazione del contenitore, sui criteri di riavvio e sullo stato desiderato, vedere [Proprietà desiderate](module-edgeagent-edgehub.md#edgeagent-desired-properties)da EdgeAgent .

1. Salvare il file *deployment.template.json.*

1. Fare clic con il pulsante destro del mouse su **deployment.template.json** e selezionare **Generare manifesto di distribuzione di IoT Edge**.

1. Visual Studio Code accetta le informazioni fornite in *deployment.template.json* e le usa per creare un nuovo file manifesto di distribuzione. Il manifesto di distribuzione viene creato in una nuova cartella **config** nell'area di lavoro della soluzione. Dopo aver creato tale file, è possibile seguire i passaggi descritti in [Distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md) oppure [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando 2.0 di Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Distribuire più istanze del moduloDeploy multiple module instances

Se si desidera distribuire più istanze dell'archiviazione BLOB di Azure nel modulo Edge IoT, è necessario fornire un percorso di archiviazione diverso e modificare il `HostPort` valore a cui è associato il modulo. I moduli di archiviazione BLOB espongono sempre la porta 11002 nel contenitore, ma è possibile dichiarare a quale porta sono associati nell'host.

Modificare le opzioni di **creazione del contenitore** (nel portale di Azure) o il campo createOptions (nel file deployment.template.json in Visual Studio Code) per modificare il valore:Edit Container Create Options (in the Azure portal) or the **createOptions** field (in the *deployment.template.json* file in Visual Studio Code) to change the `HostPort` value:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando ci si connette ai moduli di archiviazione BLOB aggiuntivi, modificare l'endpoint in modo che punti alla porta host aggiornata.

## <a name="configure-proxy-support"></a>Configurare il supporto del proxy

Se l'organizzazione utilizza un server proxy, sarà necessario configurare il supporto proxy per i moduli di runtime edgeAgent e edgeHub. Questo processo prevede due attività:

- Configurare i daemon di runtime e l'agente IoT Edge nel dispositivo.
- Impostare la variabile di ambiente HTTPS_PROXY per i moduli nel file JSON del manifesto di distribuzione.

Questo processo è descritto in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy.](how-to-configure-proxy-support.md)

Inoltre, un modulo di archiviazione BLOB richiede anche l'impostazione HTTPS_PROXY nel file di distribuzione del manifesto. È possibile modificare direttamente il file manifesto di distribuzione o usare il portale di Azure.You can directly edit the deployment manifest file, or use the Azure portal.

1. Passare all'hub Iot nel portale di Azure e selezionare **Iot Edge** dal menu del riquadro sinistro.

1. Selezionare il dispositivo con il modulo da configurare.

1. Selezionare **Set Modules** (Configura i moduli).

1. Nella sezione **Moduli perimetrali IoT** della pagina selezionare il modulo di archiviazione BLOB.

1. Nella pagina **Aggiorna modulo Edge IoT** selezionare la scheda **Variabili di ambiente.**

1. Aggiungere `HTTPS_PROXY` per il **nome** e l'URL del proxy per il **valore**.

      ![Impostare HTTPS_PROXY variabile di ambienteSet a environment variable](./media/how-to-deploy-blob/https-proxy-config.png)

1. Fare clic su **Aggiorna**, quindi **su Revisione e creazione**.

1. Si noti che il proxy viene aggiunto al modulo nel manifesto di distribuzione e selezionare **Crea**.

1. Verificare l'impostazione selezionando il modulo dalla pagina dei dettagli del dispositivo e nella parte inferiore della pagina **Dettagli moduli Edge IoT** selezionare la scheda **Variabili di ambiente.**

      ![Impostare HTTPS_PROXY variabile di ambienteSet a environment variable](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Archiviazione BLOB di Azure in Edge Edge .Learn more about [Azure Blob Storage on IoT Edge](how-to-store-data-blob.md).

Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).
