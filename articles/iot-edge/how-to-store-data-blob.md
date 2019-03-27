---
title: Archiviare BLOB in blocchi nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Distribuire un modulo di archiviazione BLOB di Azure al dispositivo IoT Edge per archiviare i dati nei dispositivi perimetrali.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3a0df408e70ed61355ffba319f6261f90d8e4348
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499159"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge (anteprima)

L'archiviazione BLOB di Azure in IoT Edge offre una soluzione di archiviazione [BLOB in blocchi](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) nei dispositivi perimetrali. Un modulo di archiviazione BLOB nel dispositivo IoT Edge si comporta come un servizio BLOB in blocchi di Azure, ma i BLOB in blocchi vengono archiviati localmente nel dispositivo IoT Edge. È possibile accedere ai BLOB usando gli stessi metodi di archiviazione di Azure SDK o bloccare le chiamate API BLOB che già utilizzate. 

Questo modulo viene fornito con **suddivisione in livelli automatica** e **Auto-scadenza** funzionalità.

> [!NOTE]
> Attualmente la suddivisione in livelli automatica e le funzionalità di auto-scadenza sono disponibili solo in Linux AMD64 e Linux ARM32.

**La suddivisione in livelli automatica** è una funzionalità configurabile che consente di caricare automaticamente i dati dall'archiviazione di blob locale in Azure con supporto per la connettività internet intermittenti. Consente di:
- Acceso/spento attivare la funzionalità di suddivisione in livelli
- Scegliere l'ordine in cui i dati verranno copiati in Azure, ad esempio NewestFirst o OldestFirst
- Specificare l'account di archiviazione di Azure a cui si vuole che i dati caricati.
- Specificare i contenitori che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione.
- Completa la suddivisione in livelli di blob (tramite `Put Blob` operazione) e bloccare la suddivisione in livelli a livello (usando `Put Block` e `Put Block List` operazioni).

Questo modulo Usa blocchi a livello la suddivisione in livelli, quando il blob è costituito da blocchi. Ecco alcuni degli scenari comuni:
- L'applicazione aggiorna alcuni blocchi di un blob caricato in precedenza, questo modulo verrà caricato solo i blocchi aggiornati e non l'intero blob.
- Il modulo è caricamento blob e la connessione internet viene chiuso, quando la connettività è tornare nuovamente che caricherà solo i rimanenti blocchi e non l'intero blob.

Se si verifica una chiusura imprevista del processo (ad esempio, interruzione dell'alimentazione) durante il caricamento di un blob, tutti i blocchi che sono stati scadenza per il caricamento verranno caricati anche in questo caso, quando il modulo ritorna in linea.

**Auto-scadenza** è una funzionalità configurabile in cui questo modulo Elimina automaticamente i BLOB dalla risorsa di archiviazione locale quando si Time to Live (TTL) scade. Questo è espresso in minuti. Consente di:
- Acceso/spento attivare la funzionalità di auto-scadenza
- Specificare la durata (TTL) in pochi minuti

Gli scenari in cui i dati come video, immagini, i dati finanziari, dati ospedale o tutti i dati devono essere archiviati in locale, in seguito che potrebbero essere elaborati in locale o trasferiti nel cloud sono ottimi esempi per usare questo modulo.

Questo articolo fornisce istruzioni per la distribuzione di un'archiviazione BLOB di Azure nel contenitore di IoT Edge che esegue un servizio BLOB nel dispositivo IoT Edge. 

>[!NOTE]
>L'archiviazione BLOB di Azure in IoT Edge è in versione di [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Guarda il video di introduzione rapida
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer per lo sviluppo o una macchina virtuale come dispositivo perimetrale seguendo la procedura illustrata nella guida introduttiva per dispositivi [Linux](quickstart-linux.md) o [Windows](quickstart.md).
* L'archiviazione BLOB di Azure nel modulo di IoT Edge supporta le configurazioni di dispositivo seguenti:

   | Sistema operativo | Architettura |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (aggiornamento di ottobre) | AMD64 |
   | Windows 10 IoT Enterprise (aggiornamento di ottobre) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Risorse cloud:

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Distribuire l'archiviazione BLOB al dispositivo

Esistono diversi modi per distribuire moduli in un dispositivo IoT Edge e tutti sono idonei all'archiviazione BLOB di Azure nei moduli di IoT Edge. I due metodi più semplici consistono nell'usare i modelli di Visual Studio Code o il portale di Azure. 

### <a name="azure-portal"></a>Portale di Azure

Azure Marketplace offre moduli IoT Edge che possono essere distribuiti direttamente in dispositivi IoT Edge, incluso Azure Blob Storage on IoT Edge. Seguire questa procedura per distribuire il modulo dal portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com) cercare "Azure Blob Storage on IoT Edge". **Selezionare** il risultato della ricerca dal Marketplace.

   ![Creare un modulo dalla ricerca nel Marketplace](./media/how-to-store-data-blob/marketplace-module.png)

2. Scegliere il dispositivo IoT Edge che riceverà il modulo. Nella pagina **Dispositivi di destinazione per il modulo IoT Edge** specificare le informazioni seguenti:

   1. Selezionare la **Sottoscrizione** che contiene l'hub IoT in uso.

   2. Selezionare l'**hub IoT**.

   3. Se si conosce il **nome del dispositivo IoT Edge**, immetterlo nella casella di testo. In alternativa, selezionare **Trova dispositivo** per scegliere da un elenco dei dispositivi IoT Edge nell'hub IoT. 
   
   4. Selezionare **Create**.

   Dopo avere scelto un modulo IoT Edge da Azure Marketplace e un dispositivo IoT Edge che riceverà il modulo, viene visualizzata una procedura guidata in tre passaggi che consente di definire esattamente come verrà distribuito il modulo.

3. Nel passaggio **Aggiungi moduli** della procedura guidata Imposta moduli, si noti che il modulo **AzureBlobStorageonIoTEdge** è già elencato in **Moduli di distribuzione**. 

2. Selezionare il modulo di archiviazione BLOB dall'elenco dei moduli di distribuzione per aprire i dettagli del modulo. 

   ![Selezionare il nome del modulo per aprire i dettagli del modulo](./media/how-to-store-data-blob/open-module-details.png)

3. Nella pagina **Moduli personalizzati di IoT Edge** aggiornare il modulo Azure Blob Storage on IoT Edge seguendo questa procedura:

   1. Specificare un **Nome** per il modulo in minuscolo. È possibile rinominare il modulo o usare `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >Azure IoT Edge distingue tra maiuscole e minuscole quando si effettuano chiamate ai moduli e Storage SDK usa per impostazione predefinita caratteri minuscoli. Per assicurarsi che le connessioni al modulo Azure Blob Storage on IoT Edge non vengano interrotte, assegnargli un nome con caratteri minuscoli. 

   2. Le **Opzioni di creazione del contenitore** predefinite includono le associazioni di porte necessarie per il contenitore, ma è anche necessario aggiungere le informazioni sull'account di archiviazione e un'associazione per la directory di archiviazione nel dispositivo. Sovrascrivere il codice JSON nel portale con il codice JSON seguente:
    
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
   3. Aggiornare il codice JSON copiato con le informazioni seguenti: 

      * Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi di account devono essere compresi tra i 3 e i 24 caratteri e includere lettere minuscole e numeri.
      * Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.
      * Sostituire `<storage directory bind>` a seconda del sistema operativo del contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. L'associazione per la directory di archiviazione corrisponde a una posizione nel dispositivo specificata per una posizione impostata nel modulo. 

         * Contenitori Linux: **\<percorso di archiviazione >:/blobroot**. Ad esempio, /srv/containerdata:/blobroot. O, my-volume:/blobroot. 
         * Contenitori Windows: **\<percorso di archiviazione>:C:/BlobRoot**. Ad esempio, C:/ContainerData:C:/BlobRoot. In alternativa, my-volume: C:/blobroot.
   
      > [!IMPORTANT]
      > Non modificare la seconda metà del valore dell'associazione per la directory di archiviazione, che fa riferimento a una posizione specifica nel modulo. L'associazione per la directory di archiviazione deve sempre terminare con **:/blobroot** per i contenitori Linux e **:C:/BlobRoot** per i contenitori Windows.

      ![Aggiornare le opzioni di creazione del contenitore del modulo - portale](./media/how-to-store-data-blob/edit-module.png)

   4. Impostare [auto-scadenza e la suddivisione in livelli automatica](#configure-auto-tiering-and-auto-expiration-via-azure-portal) nelle proprietà desiderate. Elenco delle regole di [suddivisione in livelli automatica](#auto-tiering-properties) e [auto-scadenza](#auto-expiration-properties) proprietà e i relativi valori possibili. 

   5. Selezionare **Salva**. 

4. Selezionare **Avanti** per continuare con il passaggio successivo della procedura guidata.
5. Nel passaggio **Specifica route** della procedura guidata selezionare **Avanti**.
6. Nel passaggio **Rivedi distribuzione** della procedura guidata selezionare **Invia**.
7. Dopo l'invio della distribuzione, si torna alla pagina **IoT Edge** dell'hub IoT. Selezionare il dispositivo IoT Edge impostato come destinazione della distribuzione per aprirne i dettagli. 
8. Nei dettagli del dispositivo verificare che il modulo di archiviazione BLOB sia elencato sia come **Specificato nella distribuzione** che come **Segnalato dal dispositivo**. Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato. 

### <a name="visual-studio-code-templates"></a>Modelli di Visual Studio Code

Azure IoT Edge fornisce modelli di Visual Studio Code per assistere allo sviluppo di soluzioni per dispositivi perimetrali. Per questi passaggi, è necessario che [Visual Studio Code](https://code.visualstudio.com/) sia installato nel computer di sviluppo e configurato con [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Seguire i passaggi seguenti per creare una nuova soluzione IoT Edge con un modulo di archiviazione BLOB e configurare il manifesto della distribuzione. 

1. Selezionare **Visualizza** > **Riquadro comandi**. 

2. Nel riquadro comandi immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Seguire i prompt nel riquadro comandi per creare la soluzione.

   | Campo | Valore |
   | ----- | ----- |
   | Selezionare la cartella | Nel computer di sviluppo scegliere la posizione in cui Visual Studio Code dovrà creare i file della soluzione. |
   | Provide a solution name (Specificare un nome per la soluzione) | Immettere un nome descrittivo per la soluzione oppure accettare quello predefinito **EdgeSolution**. |
   | Select module template (Selezionare un modello di modulo) | Scegliere **Existing Module (Enter full image URL)** (Modulo esistente - Immettere l'URL completo dell'immagine). |
   | Provide a module name (Specificare un nome per il modulo) | Immettere un nome per il modulo con caratteri tutti minuscoli, ad esempio **archiviazioneblobazure**.<br><br>È importante usare un nome in lettere minuscole per il modulo Azure Blob Storage on IoT Edge. IoT Edge distingue tra maiuscole e minuscole nei riferimenti ai moduli e Storage SDK usa per impostazione predefinita caratteri minuscoli. |
   | Provide Docker image for the module (Specificare l'immagine Docker per il modulo) | Specificare l'URI dell'immagine: **mcr.microsoft.com/azure-blob-storage:latest** |

   VS Code usa le informazioni specificate per creare una soluzione IoT Edge e quindi la carica in una nuova finestra. Il modello di soluzione crea un modello di manifesto della distribuzione che include l'immagine del modulo di archiviazione BLOB, ma è necessario configurare le opzioni per la creazione del modulo. 

3. Aprire **deployment.template.json** nell'area di lavoro della nuova soluzione e trovare la sezione **moduli**. Apportare le modifiche seguenti alla configurazione:

   1. Eliminare il modulo **tempSensor**, perché non necessario per questa distribuzione. 

   2. Copiare e incollare il codice seguente nel campo **createOptions** del modulo di archiviazione BLOB: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aggiornare createOptions per il modulo - VS Code](./media/how-to-store-data-blob/create-options.png)

4. Nelle opzioni di creazione JSON, aggiornare `<storage directory bind>` in base al sistema operativo del contenitore. Specificare il nome di un [volume](https://docs.docker.com/storage/volumes/) o il percorso assoluto in una directory nel dispositivo IoT Edge in cui si desidera che il modulo BLOB archivi i suoi dati. L'associazione per la directory di archiviazione corrisponde a una posizione nel dispositivo specificata per una posizione impostata nel modulo.  

   * Contenitori Linux: **\<percorso di archiviazione >:/blobroot**. Ad esempio, /srv/containerdata:/blobroot. O, my-volume:/blobroot.
   * Contenitori Windows: **\<percorso di archiviazione>:C:/BlobRoot**. Ad esempio, C:/ContainerData:C:/BlobRoot. In alternativa, my-volume: C:/blobroot.
   
   > [!IMPORTANT]
   > Non modificare la seconda metà del valore dell'associazione per la directory di archiviazione, che fa riferimento a una posizione specifica nel modulo. L'associazione per la directory di archiviazione deve sempre terminare con **:/blobroot** per i contenitori Linux e **:C:/BlobRoot** per i contenitori Windows.

5. Configurare [auto-scadenza e la suddivisione in livelli automatica](#configure-auto-tiering-and-auto-expiration-via-vscode). Elenco delle regole di [suddivisione in livelli automatica](#auto-tiering-properties) e [auto-scadenza](#auto-expiration-properties) proprietà

6. Salvare il file **deployment.template.json**.

7. Aprire il file con estensione **env** nell'area di lavoro della soluzione. 

8. Il file con estensione env è configurato per ricevere le credenziali del registro contenitori, ma non è necessario per l'immagine dell'archivio BLOB perché è disponibile pubblicamente. Sostituire invece il file con due nuove variabili di ambiente: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Specificare un valore per `STORAGE_ACCOUNT_NAME`, i nomi di account devono avere una lunghezza compresa fra 3 e 24 caratteri e devono includere lettere minuscole e numeri. Specificare una chiave base64 a 64 byte per `STORAGE_ACCOUNT_KEY`. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli. 

   Non includere spazi o virgolette tra i valori specificati. 

10. Salvare il file con estensione **env**. 

11. Fare clic con il pulsante destro del mouse su **deployment.template.json** e selezionare **Generare manifesto di distribuzione di IoT Edge**. 

12. Visual Studio Code richiede le informazioni fornite in deployment.template.json e con estensione .env e le usa per creare un nuovo file del manifesto di distribuzione. Il manifesto di distribuzione viene creato in una nuova cartella **config** nell'area di lavoro della soluzione. Dopo aver creato tale file, è possibile seguire i passaggi descritti in [Distribuire i moduli di Azure IoT Edge da Visual Studio Code](how-to-deploy-modules-vscode.md) oppure [Distribuire i moduli di Azure IoT Edge con l'interfaccia della riga di comando 2.0 di Azure](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>La suddivisione in livelli automatica e le proprietà di scadenza automatica e configurazione

Usare le proprietà desiderate per impostare le proprietà di scadenza automatica e la suddivisione in livelli automatica. Può essere impostati durante la distribuzione o modificate in un secondo momento modificando il dispositivo gemello del modulo senza la necessità di ridistribuire. È consigliabile controllare "Modulo gemello" per `reported configuration` e `configurationValidation` per assicurarsi che i valori vengano propagati correttamente.

### <a name="auto-tiering-properties"></a>La suddivisione in livelli automatica delle proprietà 
Il nome di questa impostazione è `tieringSettings`

| Campo | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| tieringOn | true, false | Per impostazione predefinita è impostata su `false`, se si desidera attivarla, impostare su `true`|
| backlogPolicy | NewestFirst, OldestFirst | Consente di scegliere l'ordine in cui i dati verranno copiati in Azure. Per impostazione predefinita è impostata su `OldestFirst`. L'ordine è determinato dall'ora dell'ultima modifica del Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` è una stringa di connessione che consente di specificare l'account di archiviazione di Azure a cui si vuole che i dati caricata. Specificare `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Aggiungere appropriato EndpointSuffix di Azure in cui verranno caricati i dati, varia per Azure globale, Azure per enti pubblici e Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Consente di specificare i nome del contenitore che si desidera caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione. Se non si specifica il nome del contenitore di destinazione, verrà assegnato automaticamente il nome del contenitore come `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. È possibile creare stringhe di modello per nome di contenitore di destinazione, vedere la colonna di valori possibili. <br>* %h -> nome dell'Hub IoT (da 3 a 50 caratteri). <br>* %d -> Id (1 per i 129 caratteri) del dispositivo IoT. <br>* %m -> nome del modulo (da 1 a 64 caratteri). <br>* %c -> nome del contenitore di origine (da 3 a 63 caratteri). <br><br>Dimensione massima del nome del contenitore è 63 caratteri, mentre assegnare automaticamente il nome del contenitore di destinazione se la dimensione del contenitore supera 63 caratteri che annullerà ogni sezione (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) e 15 caratteri. |

### <a name="auto-expiration-properties"></a>Proprietà di auto-scadenza
Il nome di questa impostazione è `ttlSettings`

| Campo | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| ttlOn | true, false | Per impostazione predefinita è impostata su `false`, se si desidera attivarla, impostare su `true`|
| timeToLiveInMinutes | `<minutes>` | Specificare la durata (TTL) in pochi minuti. Il modulo eliminerà automaticamente i BLOB dall'archivio locale alla scadenza di TTL |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Configurare la suddivisione in livelli automatica e Auto-scadenza tramite il portale di Azure

Impostare le proprietà desiderate per abilitare la suddivisione in livelli automatica e auto-scadenza, è possibile impostare questi valori:

- **Durante la distribuzione iniziale**: Copiare il codice JSON nel **proprietà desiderate del dispositivo gemello del modulo Set** casella. Configurare ogni proprietà con valore appropriato, salvarlo e continuare con la distribuzione.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

  ![impostare le proprietà di suddivisione in livelli automatica e di auto-scadenza](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **Dopo aver distribuito il modulo tramite la funzionalità "Modulo gemello identità"**: Passare a "Identità del dispositivo gemello del modulo" di questo modulo, copiare il codice JSON nelle proprietà desiderate, configurare ogni proprietà con valore appropriato e salvare. In Json "Modulo gemello identità" assicurarsi che ogni volta che si aggiungono o aggiornano una proprietà, desiderata il `reported configuration` sezione riflette le modifiche e il `configurationValidation` sezione segnala l'esito positivo per ogni proprietà.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![suddivisione in livelli + durata (TTL) module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Configurare la suddivisione in livelli automatica e Auto-scadenza tramite Visual Studio code

- **Durante la distribuzione iniziale**: Aggiungere il codice JSON nel deployment.template.json per definire le proprietà desiderate per questo modulo seguente. Configurare ogni proprietà con valore appropriato e salvarlo.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

Di seguito è riportato un esempio di proprietà desiderate per questo modulo: ![impostare le proprietà desiderate per azureblobstorageoniotedge - Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **Dopo aver distribuito il modulo tramite "Modulo gemello"**: [Modificare il modulo gemello](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) di questo modulo, copiare il codice JSON nelle proprietà desiderate, configurare ogni proprietà con valore appropriato e salvare. In Json "Modulo gemello" assicurarsi che ogni volta che si aggiungono o aggiornano una proprietà, desiderata il `reported configuration` sezione riflette le modifiche e il `configurationValidation` sezione segnala l'esito positivo per ogni proprietà.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```
## <a name="logs"></a>Log

Seguire le istruzioni fornite per [configurare i log di docker per i moduli di IoT Edge](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Connettere il modulo di archiviazione BLOB

È possibile usare il nome dell'account e la chiave dell'account configurati per il modulo per accedere all'archiviazione BLOB nel dispositivo IoT Edge. 

Specificare il dispositivo IoT Edge come endpoint BLOB per qualsiasi richiesta di archiviazione effettuata. È possibile [Creare una stringa di connessione per un endpoint di archiviazione esplicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando le informazioni del dispositivo IoT Edge e il nome dell'account che è stato configurato. 

1. Per i moduli, che vengono distribuiti nello stesso dispositivo edge in cui è in esecuzione "Azure Blob Storage in IoT Edge", l'endpoint blob è: `http://<module name>:11002/<account name>`. 
2. Per i moduli, che vengono distribuiti nel dispositivo edge diversi, più il dispositivo perimetrale in cui è in esecuzione "Azure Blob Storage in IoT Edge", quindi a seconda del programma di installazione all'endpoint blob è: `http://<device IP >:11002/<account name>` o `http://<IoT Edge device hostname>:11002/<account name>` o `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Distribuire più istanze

Se si desidera distribuire più istanze di archiviazione Blob di Azure in dispositivi perimetrali IoT, è necessario specificare il percorso di archiviazione diverso e modificare il HostPort che associa il modulo. I moduli di archiviazione BLOB espongono sempre la porta 11002 nel contenitore, ma è possibile dichiarare a quale porta sono associati nell'host. 

Modificare il modulo creare opzioni per modificare il valore HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Quando ci si connette ai moduli di archiviazione BLOB aggiuntivi, modificare l'endpoint in modo che punti alla porta host aggiornata. 

## <a name="try-it-out"></a>Provare il servizio

### <a name="azure-blob-storage-quickstart-samples"></a>Esempi di avvio rapido di archiviazione Blob di Azure
La documentazione dell'archiviazione BLOB di Azure include guide introduttive che forniscono il codice di esempio in linguaggi diversi. È possibile eseguire questi esempi per testare l'archiviazione BLOB di Azure in IoT Edge, modificando l'endpoint BLOB in modo che punti al modulo di archiviazione BLOB. Seguire i passaggi per [connettere il modulo di archiviazione blob](#connect-to-your-blob-storage-module)

Le seguenti guide introduttive utilizzano linguaggi che sono supportati anche da IoT Edge, in modo che sia possibile distribuirle come moduli di IoT Edge con il modulo di archiviazione BLOB:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Esplora archivi Azure
È anche possibile provare "Azure Storage Explorer" per la connessione all'account di archiviazione locale. Funziona con [Azure Storage Explorer versione 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> Si potrebbero verificare errori durante l'esecuzione di questa procedura, ad esempio l'aggiunta di una connessione a un account di archiviazione locale o la creazione di contenitori nell'account di archiviazione locale. Per ignorare e aggiornare. 

1. Scaricare e installare Azure Storage Explorer
2. Connettersi ad archiviazione di Azure usando una stringa di connessione
3. Specificare una stringa di connessione: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Seguire i passaggi per la connessione.
5. Creare contenitori all'interno dell'account di archiviazione locale
6. Avvio del caricamento del file come BLOB in blocchi.
   > [!NOTE]
   > deselezionare la casella di controllo per caricare il file come BLOB di pagine. Questo modulo non supporta i BLOB di pagine. Verrà visualizzato questo messaggio di richiesta durante il caricamento di file, ad esempio con estensione ISO, file con estensione vhd, vhdx o qualsiasi file di grandi dimensioni.

7. È possibile scegliere di connettere gli account di archiviazione di Azure in cui si siano caricando i dati. Offre una visualizzazione singola per l'account di archiviazione locale e account di archiviazione di Azure

## <a name="supported-storage-operations"></a>Operazioni di archiviazione supportate

Moduli di archiviazione BLOB in IoT Edge usano gli stessi SDK di archiviazione di Azure e sono coerenti con la versione 2017-04-17 dell'API di archiviazione di Azure per gli endpoint di blob di blocco. Le versioni successive dipendono dalle esigenze dei clienti.

Non tutte le operazioni di archiviazione BLOB di Azure sono supportate dall'archiviazione BLOB di Azure in IoT Edge. La sezione seguente elenca le operazioni supportate e non supportate.

### <a name="account"></a>Account

Supportate: 
* Elencare i contenitori

Non supportate: 
* Ottenere e impostare le proprietà del servizio BLOB
* Richiedere un BLOB preliminare
* Ottenere le statistiche del servizio BLOB
* Ottenere le informazioni sull'account

### <a name="containers"></a>Contenitori

Supportate: 
* Creare ed eliminare un contenitore
* Ottenere i metadati ele proprietà del contenitore
* Elencare i BLOB
* Ottenere e impostare il contenitore ACL
* Impostare i metadati del contenitore

Non supportate:
* Lease del contenitore

### <a name="blobs"></a>Blobs

Supportate: 
* Inserire, ottenere ed eliminare BLOB
* Ottenere e impostare le proprietà BLOB
* Ottenere e impostare i metadati BLOB

Non supportate: 
* Lease del BLOB
* Snapshot di BLOB
* Copiare e interrompere la copia di BLOB
* Annullare l'eliminazione di BLOB
* Impostare il livello di BLOB

### <a name="block-blobs"></a>BLOB in blocchi

Supportate: 
* Inserire il blocco
* Inserire e ottenere l'elenco del blocco

Non supportate:
* Inserire il blocco dall'URL

## <a name="feedback"></a>Commenti e suggerimenti:
Commenti e suggerimenti sono molto importanti per noi, per rendere questo modulo e le relative funzionalità utili e facile da usare. Per condividere commenti e suggerimenti e facci sapere come possiamo migliorare.

È possibile contattare Microsoft all'indirizzo absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'[Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md)

