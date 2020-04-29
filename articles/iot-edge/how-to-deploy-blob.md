---
title: "Distribuire l'archiviazione BLOB nel modulo nel dispositivo: Azure IoT Edge"
description: Distribuire un modulo di archiviazione BLOB di Azure al dispositivo IoT Edge per archiviare i dati nei dispositivi perimetrali.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804623"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Distribuire l'Archiviazione BLOB di Azure su un modulo IoT Edge al dispositivo

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per l'archiviazione BLOB di Azure nei moduli IoT Edge. I due metodi più semplici consistono nell'usare i modelli di Visual Studio Code o il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un [Hub](../iot-hub/iot-hub-create-through-portal.md) Internet delle cose nella sottoscrizione di Azure.
- Un [dispositivo IoT Edge](how-to-register-device.md) con il runtime di IoT Edge installato.
- [Visual Studio Code](https://code.visualstudio.com/) e gli [strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) per la distribuzione da Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Eseguire la distribuzione dalla portale di Azure

Il portale di Azure illustra la creazione di un manifesto di distribuzione e il push della distribuzione in un dispositivo IoT Edge.

### <a name="select-your-device"></a>Selezionare il dispositivo

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu.
1. Fare clic sull'ID del dispositivo di destinazione dall'elenco dei dispositivi.
1. Selezionare **Set Modules** (Configura i moduli).

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure dispone di una procedura guidata che illustra la creazione di un manifesto di distribuzione. Include tre passaggi organizzati in schede: **moduli**, **Route**e **Revisione + creazione**.

#### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **moduli IOT Edge** della pagina fare clic sull'elenco a discesa **aggiungi** e selezionare **IOT Edge modulo** per visualizzare la pagina **Aggiungi IOT Edge modulo** .

2. Nella scheda **Impostazioni modulo** immettere un nome per il modulo e quindi specificare l'URI dell'immagine del contenitore:

   Esempi:
  
   - **Nome del modulo IOT Edge**:`azureblobstorageoniotedge`
   - **URI immagine**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Impostazioni del modulo gemello](./media/how-to-deploy-blob/addmodule-tab1.png)

   Non selezionare **Aggiungi** fino a quando non sono stati specificati i valori nelle schede **Impostazioni modulo**, **Opzioni di creazione contenitori**e **modulo gemello** , come descritto in questa procedura.

   > [!IMPORTANT]
   > Azure IoT Edge distingue tra maiuscole e minuscole quando si effettuano chiamate ai moduli e l'SDK di archiviazione usa anche il valore minuscolo. Anche se il nome del modulo in [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) è **AzureBlobStorageonIoTEdge**, la modifica del nome in minuscolo consente di garantire che le connessioni all'archivio BLOB di Azure nel modulo IOT Edge non vengano interrotte.

3. Aprire la scheda **Opzioni di creazione del contenitore** .

   ![Impostazioni del modulo gemello](./media/how-to-deploy-blob/addmodule-tab3.png)

   Copiare e incollare il codice JSON seguente nella casella per fornire le informazioni sull'account di archiviazione e un montaggio per l'archiviazione nel dispositivo.
  
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

4. Aggiornare il file JSON copiato nelle **Opzioni di creazione del contenitore** con le informazioni seguenti:

   - Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account devono avere una lunghezza di 3 e 24 caratteri, con lettere minuscole e numeri. Nessuno spazio.

   - Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

   - Sostituire `<storage mount>` in base al sistema operativo del contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto di una directory esistente nel dispositivo IOT Edge in cui i dati vengono archiviati nel modulo BLOB. Il montaggio di archiviazione esegue il mapping di una posizione nel dispositivo fornita a una posizione impostata nel modulo.

     - Per i contenitori Linux, il formato è ** \<il percorso di archiviazione o il volume>:/blobroot**. Ad esempio:
         - usare il [montaggio del volume](https://docs.docker.com/storage/volumes/):`my-volume:/blobroot`
         - usare [Binding Mount](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot`. Assicurarsi di seguire i passaggi per [concedere l'accesso alla directory all'utente del contenitore](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Per i contenitori di Windows, il formato è ** \<il percorso di archiviazione o il volume>: C:/BlobRoot**. Ad esempio:
         - usare il [montaggio](https://docs.docker.com/storage/volumes/)del `my-volume:C:/BlobRoot`volume:.
         - usare [Binding Mount](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot`.
         - Anziché utilizzare l'unità locale, è possibile eseguire il mapping del percorso di rete SMB. per ulteriori informazioni, vedere [utilizzo della condivisione SMB come archiviazione locale](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Non modificare la seconda metà del valore di montaggio dell'archiviazione, che fa riferimento a una posizione specifica nell'archivio BLOB sul modulo IoT Edge. Il montaggio dell'archiviazione deve sempre terminare con **:/blobroot** per i contenitori Linux e **: C:/blobroot** per i contenitori di Windows.

5. Nella scheda **Impostazioni dispositivi gemelli del modulo** copiare il codice JSON seguente e incollarlo nella casella.

   ![Impostazioni del modulo gemello](./media/how-to-deploy-blob/addmodule-tab4.png)

   Configurare ogni proprietà con un valore appropriato, come indicato dai segnaposto. Se si usa il simulatore di IoT Edge, impostare i valori per le variabili di ambiente correlate per queste proprietà, come descritto in [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

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

   Per informazioni sulla configurazione di deviceToCloudUploadProperties e deviceAutoDeleteProperties dopo la distribuzione del modulo, vedere [modificare il modulo gemello](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Per ulteriori informazioni sulle proprietà desiderate, vedere [definire o aggiornare le proprietà desiderate](module-composition.md#define-or-update-desired-properties).

6. Selezionare **Aggiungi**.

7. Selezionare **Avanti: Route** per passare alla sezione route.

#### <a name="specify-routes"></a>Specificare le route

Mantenere le route predefinite e selezionare **Avanti: rivedere + crea** per passare alla sezione revisione.

#### <a name="review-deployment"></a>Verificare la distribuzione

La sezione relativa alla verifica mostra il manifesto della distribuzione JSON che è stato creato in base alle selezioni nelle due sezioni precedenti. Sono stati dichiarati anche due moduli che non sono stati aggiunti: **$edgeAgent** e **$edgeHub**. Questi due moduli costituiscono il [runtime di IoT Edge](iot-edge-runtime.md) e sono impostazioni predefinite obbligatorie in ogni distribuzione.

Esaminare le informazioni di distribuzione e quindi selezionare **Crea**.

### <a name="verify-your-deployment"></a>Verificare la distribuzione

Dopo aver creato la distribuzione, tornare alla pagina **IOT Edge** dell'hub Internet.

1. Selezionare il dispositivo IoT Edge impostato come destinazione della distribuzione per aprirne i dettagli.
1. Nei dettagli del dispositivo verificare che il modulo di archiviazione BLOB sia elencato sia come **Specificato nella distribuzione** che come **Segnalato dal dispositivo**.

Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="deploy-from-visual-studio-code"></a>Distribuisci da Visual Studio Code

Azure IoT Edge fornisce modelli di Visual Studio Code per assistere allo sviluppo di soluzioni per dispositivi perimetrali. Usare la procedura seguente per creare una nuova soluzione di IoT Edge con un modulo di archiviazione BLOB e per configurare il manifesto della distribuzione.

1. Selezionare **Visualizza** > **Riquadro comandi**.

1. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**.

   ![Eseguire il comando per creare una nuova soluzione IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | valore |
   | ----- | ----- |
   | Selezionare la cartella | Scegliere il percorso nel computer di sviluppo per Visual Studio Code creare i file di soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Existing Module (Enter full image URL)** (Modulo esistente - Immettere l'URL completo dell'immagine). |
   | Provide a module name (Specificare un nome per il modulo) | Immettere un nome in lettere minuscole per il modulo, ad esempio **azureblobstorageoniotedge**.<br/><br/>È importante usare un nome in lettere minuscole per il modulo Azure Blob Storage on IoT Edge. IoT Edge distingue tra maiuscole e minuscole nei riferimenti ai moduli e Storage SDK usa per impostazione predefinita caratteri minuscoli. |
   | Provide Docker image for the module (Specificare l'immagine Docker per il modulo) | Specificare l'URI dell'immagine: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra. Il modello di soluzione crea un modello di manifesto della distribuzione che include l'immagine del modulo di archiviazione BLOB, ma è necessario configurare le opzioni per la creazione del modulo.

1. Aprire *deployment.template.json* nell'area di lavoro della nuova soluzione e trovare la sezione **moduli**. Apportare le modifiche seguenti alla configurazione:

   1. Eliminare il modulo **SimulatedTemperatureSensor** , perché non è necessario per questa distribuzione.

   1. Copiare e incollare il codice seguente nel `createOptions` campo:

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

      ![Aggiornare il modulo createOptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account devono avere una lunghezza di 3 e 24 caratteri, con lettere minuscole e numeri. Nessuno spazio.

1. Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

1. Sostituire `<storage mount>` in base al sistema operativo del contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. Il montaggio di archiviazione esegue il mapping di una posizione nel dispositivo fornita a una posizione impostata nel modulo.  

     - Per i contenitori Linux, il formato è ** \<il percorso di archiviazione o il volume>:/blobroot**. Ad esempio:
         - usare il [montaggio del volume](https://docs.docker.com/storage/volumes/):`my-volume:/blobroot`
         - usare [Binding Mount](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot`. Assicurarsi di seguire i passaggi per [concedere l'accesso alla directory all'utente del contenitore](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Per i contenitori di Windows, il formato è ** \<il percorso di archiviazione o il volume>: C:/BlobRoot**. Ad esempio
         - usare il [montaggio](https://docs.docker.com/storage/volumes/)del `my-volume:C:/BlobRoot`volume:.
         - usare [Binding Mount](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot`.
         - Anziché utilizzare l'unità locale, è possibile eseguire il mapping del percorso di rete SMB. per ulteriori informazioni, vedere [utilizzo della condivisione SMB come archiviazione locale](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Non modificare la seconda metà del valore di montaggio dell'archiviazione, che fa riferimento a una posizione specifica nell'archivio BLOB sul modulo IoT Edge. Il montaggio dell'archiviazione deve sempre terminare con **:/blobroot** per i contenitori Linux e **: C:/blobroot** per i contenitori di Windows.

1. Configurare [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) per il modulo aggiungendo il codice JSON seguente al file *Deployment. template. JSON* . Configurare ogni proprietà con un valore appropriato e salvare il file. Se si usa il simulatore di IoT Edge, impostare i valori per le variabili di ambiente correlate per queste proprietà, reperibili nella sezione spiegazione di [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) e [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![impostare le proprietà desiderate per azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Per informazioni sulla configurazione di deviceToCloudUploadProperties e deviceAutoDeleteProperties dopo la distribuzione del modulo, vedere [modificare il modulo gemello](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Per ulteriori informazioni sulle opzioni di creazione dei contenitori, riavviare i criteri e lo stato desiderato, vedere [EdgeAgent desired Properties](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Salvare il file *Deployment. template. JSON* .

1. Fare clic con il pulsante destro del mouse su **deployment.template.json** e selezionare **Generare manifesto di distribuzione di IoT Edge**.

1. Visual Studio Code acquisisce le informazioni fornite in *Deployment. template. JSON* e le utilizza per creare un nuovo file manifesto di distribuzione. Il manifesto di distribuzione viene creato in una nuova cartella **config** nell'area di lavoro della soluzione. Dopo aver creato tale file, è possibile seguire i passaggi descritti in [Distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md) oppure [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando 2.0 di Azure](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Distribuire più istanze del modulo

Se si desidera distribuire più istanze dell'archiviazione BLOB di Azure in IoT Edge modulo, è necessario specificare un percorso di archiviazione diverso e modificare il `HostPort` valore a cui il modulo è associato. I moduli di archiviazione BLOB espongono sempre la porta 11002 nel contenitore, ma è possibile dichiarare a quale porta sono associati nell'host.

Modificare **le opzioni di creazione del contenitore** (nel portale di Azure) o il campo **createOptions** (nel file *Deployment. template. JSON* in Visual Studio Code) per modificare `HostPort` il valore:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Quando ci si connette ai moduli di archiviazione BLOB aggiuntivi, modificare l'endpoint in modo che punti alla porta host aggiornata.

## <a name="configure-proxy-support"></a>Configurare il supporto del proxy

Se l'organizzazione usa un server proxy, sarà necessario configurare il supporto proxy per i moduli di runtime edgeAgent e edgeHub. Questo processo implica due attività:

- Configurare i daemon di runtime e l'agente di IoT Edge nel dispositivo.
- Impostare la variabile di ambiente HTTPS_PROXY per i moduli nel file JSON del manifesto di distribuzione.

Questo processo è descritto in [configurare un dispositivo IOT Edge per la comunicazione tramite un server proxy](how-to-configure-proxy-support.md).

Inoltre, un modulo di archiviazione BLOB richiede l'impostazione HTTPS_PROXY nel file di distribuzione del manifesto. È possibile modificare direttamente il file manifesto di distribuzione oppure utilizzare il portale di Azure.

1. Passare all'hub Internet delle cose nel portale di Azure e selezionare il **bordo** del menu di scelta rapida nel riquadro sinistro.

1. Selezionare il dispositivo con il modulo da configurare.

1. Selezionare **Set Modules** (Configura i moduli).

1. Nella sezione **moduli IOT Edge** della pagina selezionare il modulo di archiviazione BLOB.

1. Nella pagina **aggiorna IOT Edge modulo** selezionare la scheda **variabili di ambiente** .

1. Aggiungere `HTTPS_PROXY` per il **nome** e l'URL del proxy per il **valore**.

      ![Imposta HTTPS_PROXY variabile di ambiente](./media/how-to-deploy-blob/https-proxy-config.png)

1. Fare clic su **Aggiorna**e quindi su **Verifica + crea**.

1. Si noti che il proxy viene aggiunto al modulo nel manifesto di distribuzione e selezionare **Crea**.

1. Verificare l'impostazione selezionando il modulo dalla pagina dei dettagli del dispositivo e nella parte inferiore della pagina dei **Dettagli dei moduli IOT Edge** selezionare la scheda **variabili di ambiente** .

      ![Imposta HTTPS_PROXY variabile di ambiente](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [archiviazione BLOB di Azure sono disponibili in IOT Edge](how-to-store-data-blob.md).

Per altre informazioni sul funzionamento e sulla modalità di creazione dei manifesti della distribuzione, vedere [Informazioni su come usare, configurare e riusare i moduli IoT Edge](module-composition.md).
