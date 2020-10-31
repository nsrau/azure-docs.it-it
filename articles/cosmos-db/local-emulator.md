---
title: Installare e sviluppare localmente con Azure Cosmos DB Emulator
description: Informazioni su come installare e usare l'emulatore di Azure Cosmos DB negli ambienti Windows, Linux, macOS e Windows docker. Usando l'emulatore è possibile sviluppare e testare l'applicazione localmente gratuitamente, senza creare una sottoscrizione di Azure.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperfq1
ms.openlocfilehash: b1a0382b6bb650b6761897f4a16f988e5ce00c1e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088744"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Installare e usare l'emulatore di Azure Cosmos per lo sviluppo e il test locali
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos Emulator fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. Con Azure Cosmos Emulator è possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure o sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore di Azure Cosmos, è possibile passare all'uso di un account Azure Cosmos nel cloud. Questo articolo descrive come installare e usare l'emulatore in ambienti Windows, Linux, macOS e Windows docker.

## <a name="download-the-emulator"></a>Scaricare l'emulatore

Per iniziare, scaricare e installare la versione più recente di Azure Cosmos Emulator nel computer locale. Nell'articolo relativo alle [Note sulla versione dell'emulatore](local-emulator-release-notes.md) sono elencate tutte le versioni disponibili e gli aggiornamenti delle funzionalità eseguiti in ogni versione.

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[Scaricare l'emulatore di Azure Cosmos](https://aka.ms/cosmosdb-emulator)**

È possibile sviluppare applicazioni usando Azure Cosmos Emulator con gli account [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)e API [Table](local-emulator.md#table-api) . Attualmente Esplora dati nell'emulatore supporta completamente la visualizzazione dei dati SQL; i dati creati usando le applicazioni client MongoDB, Gremlin/Graph e Cassandra non sono visualizzabili al momento. Per altre informazioni, vedere [come connettersi all'endpoint dell'emulatore](#connect-with-emulator-apis) da diverse API.

## <a name="how-does-the-emulator-work"></a>Funzionamento dell'emulatore

Azure Cosmos Emulator offre un'emulazione altamente fedele del servizio Azure Cosmos DB. Supporta funzionalità equivalenti come il Azure Cosmos DB, che include la creazione di dati, l'esecuzione di query sui dati, il provisioning e il ridimensionamento di contenitori e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore di Azure Cosmos e distribuirle in Azure su scala globale aggiornando l'endpoint della connessione Azure Cosmos DB.

Anche se l'emulazione del servizio Azure Cosmos DB è fedele, l'implementazione dell'emulatore è diversa da quella del servizio. Ad esempio, l'emulatore usa i componenti del sistema operativo standard, come il file system locale per la persistenza e lo stack di protocolli HTTPS per la connettività. Le funzionalità che si basano sull'infrastruttura di Azure, ad esempio la replica globale, la latenza di millisecondi a una sola cifra per letture/scritture e i livelli di coerenza ottimizzabili non sono applicabili quando si usa l'emulatore

È possibile eseguire la migrazione dei dati tra l'emulatore di Azure Cosmos e il servizio Azure Cosmos DB usando lo [strumento di migrazione dei dati Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Differenze tra l'emulatore e il servizio cloud

Poiché l'emulatore di Azure Cosmos fornisce un ambiente emulato eseguito nella workstation di sviluppo locale, esistono alcune differenze di funzionalità tra l'emulatore e un account Azure Cosmos nel cloud:

* Attualmente il riquadro **Esplora dati** nell'emulatore supporta completamente solo i client dell'API SQL. La visualizzazione **Esplora dati** e le operazioni per Azure Cosmos DB API, ad esempio le API MongoDB, Table, Graph e Cassandra, non sono completamente supportate.

* L'emulatore supporta solo un singolo account fisso e una chiave primaria nota. Non è possibile rigenerare la chiave quando si usa l'emulatore di Azure Cosmos, tuttavia è possibile modificare la chiave predefinita usando l'opzione della [riga di comando](emulator-command-line-parameters.md) .

* Con l'emulatore, è possibile creare un account Azure Cosmos solo in modalità di [velocità effettiva con provisioning](set-throughput.md) ; Attualmente non supporta la modalità senza [Server](serverless.md) .

* L'emulatore non è un servizio scalabile e non supporta un numero elevato di contenitori. Quando si usa l'emulatore di Azure Cosmos, per impostazione predefinita è possibile creare fino a 25 contenitori a dimensione fissa a 400 ur/sec (supportati solo con Azure Cosmos DB SDK) o 5 contenitori illimitati. Per ulteriori informazioni su come modificare questo valore, vedere l'articolo relativo all' [impostazione del valore partitionCount](emulator-command-line-parameters.md#set-partitioncount) .

* L'emulatore non offre diversi [livelli di coerenza Azure Cosmos DB](consistency-levels.md) come il servizio cloud.

* L'emulatore non offre la replica in più [aree](distribute-data-globally.md).

* Poiché la copia dell'emulatore di Azure Cosmos potrebbe non essere sempre aggiornata con le ultime modifiche apportate al servizio Azure Cosmos DB, è sempre necessario fare riferimento a [Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md) per stimare accuratamente le esigenze di velocità effettiva (UR) dell'applicazione.

* L'emulatore supporta una dimensione massima della proprietà ID di 254 caratteri.

## <a name="install-the-emulator"></a>Installare l'emulatore

Prima di installare l'emulatore, verificare che siano presenti i requisiti hardware e software seguenti:

* Requisiti software:
  * Attualmente sono supportati Windows Server 2012 R2, Windows Server 2016, 2019 o Windows 8, 10 sistemi operativi host. Il sistema operativo host con Active Directory abilitato non è al momento supportato.
  * Sistema operativo a 64 bit

* Requisiti hardware minimi:
  * 2 GB DI RAM
  * 10 GB di spazio disponibile su disco rigido

* Per installare, configurare ed eseguire l'emulatore di Azure Cosmos, è necessario disporre dei privilegi amministrativi nel computer. L'emulatore aggiunge un certificato e imposta anche le regole del firewall per eseguire i relativi servizi. Per consentire all'emulatore di eseguire tali operazioni, sono pertanto necessari i diritti di amministratore.

Per iniziare, scaricare e installare la versione più recente di [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator) nel computer locale. Se si verificano problemi durante l'installazione dell'emulatore, vedere l'articolo sulla [risoluzione dei problemi dell'emulatore](troubleshoot-local-emulator.md) per eseguire il debug.

A seconda dei requisiti di sistema, è possibile eseguire l'emulatore in [Windows](#run-on-windows), [Docker per Windows](#run-on-windows-docker), [Linux o MacOS](#run-on-linux-macos) , come descritto nelle sezioni successive di questo articolo.

## <a name="check-for-emulator-updates"></a>Verifica gli aggiornamenti dell'emulatore

Ogni versione dell'emulatore viene fornita con un set di aggiornamenti delle funzionalità o correzioni di bug. Per visualizzare le versioni disponibili, leggere l'articolo relativo alle [Note sulla versione dell'emulatore](local-emulator-release-notes.md) .

Dopo l'installazione, se sono state usate le impostazioni predefinite, i dati corrispondenti all'emulatore vengono salvati nel percorso%LOCALAPPDATA%\CosmosDBEmulator. È possibile configurare un percorso diverso usando le impostazioni del percorso dati facoltativo. ovvero `/DataPath=PREFERRED_LOCATION` come [parametro della riga di comando](emulator-command-line-parameters.md). Non è garantito che i dati creati in una versione dell'emulatore di Azure Cosmos siano accessibili quando si usa una versione diversa. Se è necessario rendere permanente i dati a lungo termine, è consigliabile archiviare i dati in un account Azure Cosmos anziché nell'emulatore di Azure Cosmos.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Usare l'emulatore in Windows

Per impostazione predefinita, Azure Cosmos Emulator è installato in `C:\Program Files\Azure Cosmos DB Emulator` location. Per avviare l'emulatore di Azure Cosmos in Windows, selezionare il pulsante **Start** o premere il tasto Windows. Iniziare a digitare **Azure Cosmos Emulator** e selezionare l'emulatore dall'elenco di applicazioni.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Selezionare il pulsante Start o premere il tasto Windows, iniziare a digitare Azure Cosmos Emulator e selezionare l'emulatore nell'elenco di applicazioni":::

Quando l'emulatore è stato avviato, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows. Apre automaticamente Esplora dati di Azure Cosmos nel browser in corrispondenza di questo `https://localhost:8081/_explorer/index.html` URL URL.

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Selezionare il pulsante Start o premere il tasto Windows, iniziare a digitare Azure Cosmos Emulator e selezionare l'emulatore nell'elenco di applicazioni":::

È anche possibile avviare e arrestare l'emulatore dalla riga di comando o dai comandi di PowerShell. Per ulteriori informazioni, vedere l'articolo di [riferimento dello strumento da riga di comando](emulator-command-line-parameters.md) .

Per impostazione predefinita, l'emulatore di Azure Cosmos viene eseguito sul computer locale ("localhost") in ascolto sulla porta 8081. L'indirizzo viene visualizzato come `https://localhost:8081/_explorer/index.html`. Se si chiude la finestra di esplorazione e si vuole riaprirla in un secondo momento, è possibile aprire l'URL nel browser o avviarlo dall'emulatore di Azure Cosmos nell'icona della barra di Windows, come illustrato di seguito.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Selezionare il pulsante Start o premere il tasto Windows, iniziare a digitare Azure Cosmos Emulator e selezionare l'emulatore nell'elenco di applicazioni":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Usare l'emulatore in Docker per Windows

È possibile eseguire l'emulatore di Azure Cosmos nel contenitore Docker di Windows. Per ulteriori informazioni, vedere l' [Hub Docker](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) per il comando Docker pull e [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) `Dockerfile` . Attualmente l'emulatore non funziona in Docker per Oracle Linux. Usare le istruzioni seguenti per eseguire l'emulatore in Docker per Windows:

1. Dopo aver [Docker per Windows](https://www.docker.com/docker-windows) installato, passare a contenitori Windows facendo clic con il pulsante destro del mouse sull'icona Docker sulla barra degli strumenti e scegliendo **passa a contenitori Windows** .

1. Eseguire quindi il pull dell'immagine dell'emulatore dall'hub Docker eseguendo il comando seguente dalla shell preferita.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Per avviare l'immagine, eseguire i comandi seguenti a seconda della riga di comando o dell'ambiente di PowerShell:

   # <a name="command-line"></a>[Riga di comando](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Le immagini Docker basate su Windows potrebbero non essere generalmente compatibili con ogni sistema operativo host Windows. Ad esempio, l'immagine dell'emulatore di Azure Cosmos predefinita è compatibile solo con Windows 10 e Windows Server 2016. Se è necessaria un'immagine compatibile con Windows Server 2019, eseguire invece il comando seguente:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   La risposta sarà simile a quanto riportato di seguito:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Quando si esegue il `docker run` comando, se viene visualizzato un errore di conflitto di porta, ovvero se la porta specificata è già in uso, passare una porta personalizzata modificando i numeri di porta. Ad esempio, è possibile modificare il parametro "-p 8081:8081" in "-p 443:8081"

1. A questo punto, usare l'endpoint dell'emulatore e la chiave primaria dalla risposta e importare il certificato TLS/SSL nell'host. Per importare il certificato TLS/SSL, eseguire i passaggi seguenti da un prompt dei comandi di amministratore:

   # <a name="command-line"></a>[Riga di comando](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Se si chiude la shell interattiva dopo l'avvio dell'emulatore, il contenitore dell'emulatore verrà arrestato. Per riaprire Esplora dati, passare all'URL seguente nel browser. L'endpoint dell'emulatore è specificato nel messaggio di risposta illustrato in precedenza.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Se si dispone di un'applicazione client .NET in esecuzione in un contenitore Docker Linux e se si esegue Azure Cosmos Emulator in un computer host, usare le istruzioni nella sezione successiva per importare il certificato nel contenitore Docker di Linux.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Rigenerare i certificati dell'emulatore durante l'esecuzione in un contenitore Docker

Quando si esegue l'emulatore in un contenitore Docker, i certificati associati all'emulatore vengono rigenerati ogni volta che si arresta e si riavvia il rispettivo contenitore. Per questo motivo, è necessario importare nuovamente i certificati dopo l'avvio di ogni contenitore. Per ovviare a questa limitazione, è possibile usare un file Docker compose per associare il contenitore Docker a un particolare indirizzo IP e un'immagine del contenitore.

Ad esempio, è possibile usare la configurazione seguente all'interno del file Docker compose, quindi assicurarsi di formattarlo in base ai requisiti: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Usare l'emulatore in Linux o macOS

Attualmente l'emulatore di Azure Cosmos può essere eseguito solo in Windows. Se si usa Linux o macOS, è possibile eseguire l'emulatore in una macchina virtuale Windows ospitata in un hypervisor, ad esempio Parallels o VirtualBox.

> [!NOTE]
> Ogni volta che si riavvia la macchina virtuale Windows ospitata in un hypervisor, è necessario reimportare il certificato perché l'indirizzo IP della macchina virtuale cambia. L'importazione del certificato non è necessaria nel caso in cui la macchina virtuale sia stata configurata per mantenere l'indirizzo IP.

Usare la procedura seguente per usare l'emulatore in ambienti Linux o macOS:

1. Eseguire il comando seguente dalla macchina virtuale Windows e prendere nota dell'indirizzo IPv4:

   ```bash
   ipconfig.exe
   ```

1. All'interno dell'applicazione, modificare l'URL dell'endpoint per utilizzare l'indirizzo IPv4 restituito da `ipconfig.exe` anziché `localhost` .

1. Dalla macchina virtuale Windows avviare l'emulatore di Azure Cosmos dalla riga di comando usando le opzioni seguenti. Per informazioni dettagliate sui parametri supportati dalla riga di comando, vedere le informazioni di [riferimento sullo strumento da riga di comando dell'emulatore](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Infine, è necessario risolvere il processo di attendibilità del certificato tra l'applicazione in esecuzione nell'ambiente Linux o Mac e l'emulatore. Per risolvere il certificato, è possibile usare una delle due opzioni seguenti:

   1. [Importare il certificato TLS/SSL dell'emulatore nell'ambiente Linux o Mac](#import-certificate) oppure
   2. [Disabilitare la convalida TLS/SSL nell'applicazione](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Opzione 1: importare il certificato TLS/SSL dell'emulatore

Le sezioni seguenti illustrano come importare il certificato TLS/SSL dell'emulatore in ambienti Linux e macOS.

#### <a name="linux-environment"></a>Ambiente Linux

Se si usa Linux, .NET si basa su OpenSSL per eseguire la convalida:

1. [Esportare il certificato in formato pfx](local-emulator-export-ssl-certificates.md#export-emulator-certificate). L'opzione PFX è disponibile quando si sceglie di esportare la chiave privata.

1. Copiare il file PFX nell'ambiente Linux.

1. Convertire il file PFX in un file CRT

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Copiare il file CRT nella cartella che contiene i certificati personalizzati nella distribuzione Linux. In genere, nelle distribuzioni Debian si trova in `/usr/local/share/ca-certificates/`.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Aggiornare i certificati TLS/SSL, in modo da aggiornare la `/etc/ssl/certs/` cartella.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>ambiente macOS

Se si usa Mac, seguire questa procedura:

1. [Esportare il certificato in formato pfx](local-emulator-export-ssl-certificates.md#export-emulator-certificate). L'opzione PFX è disponibile quando si sceglie di esportare la chiave privata.

1. Copiare il file PFX nell'ambiente Mac.

1. Aprire l'applicazione *Keychain Access* (Accesso keychain) e importare il file PFX.

1. Aprire l'elenco dei certificati e identificare quello con il nome `localhost`.

1. Aprire il menu di scelta rapida per quell'elemento specifico, scegliere *Get Item* (Ottieni elemento) e nell'opzione *Trust* > *When using this certificate* (Attendibilità > Quando si usa questo certificato) selezionare *Always Trust* (Considera sempre attendibile). 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Selezionare il pulsante Start o premere il tasto Windows, iniziare a digitare Azure Cosmos Emulator e selezionare l'emulatore nell'elenco di applicazioni":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Opzione 2: disabilitare la convalida SSL nell'applicazione

La disabilitazione della convalida SSL è consigliata solo a scopo di sviluppo e non deve essere eseguita in un ambiente di produzione. Gli esempi seguenti illustrano come disabilitare la convalida SSL per le applicazioni .NET e Node.js.

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

Per tutte le applicazioni in esecuzione in un Framework compatibile con .NET Standard 2,1 o versione successiva, è possibile utilizzare `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

Per tutte le applicazioni in esecuzione in un Framework compatibile con .NET Standard 2,0, è possibile utilizzare `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

Per Node.js applicazioni, è possibile modificare il `package.json` file per impostare il `NODE_TLS_REJECT_UNAUTHORIZED` durante l'avvio dell'applicazione:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Abilitare l'accesso all'emulatore in una rete locale

Se sono presenti più computer che usano una singola rete e se si configura l'emulatore in un computer e si vuole accedervi da un altro computer. In tal caso, è necessario abilitare l'accesso all'emulatore in una rete locale.

È possibile eseguire l'emulatore in una rete locale. Per abilitare l'accesso tramite rete, specificare l'opzione `/AllowNetworkAccess` nella [riga di comando](emulator-command-line-parameters.md), che richiede di specificare anche `/Key=key_string` o `/KeyFile=file_name`. È possibile usare `/GenKeyFile=file_name` per generare un file con una chiave casuale sin dall'inizio, per poi passarli a `/KeyFile=file_name` o `/Key=contents_of_file`.

Per abilitare l'accesso alla rete per la prima volta, l'utente deve arrestare l'emulatore ed eliminare la directory dei dati dell'emulatore *%LocalAppData%\CosmosDBEmulator* .

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Autenticare le connessioni quando si usa l'emulatore

Come per Azure Cosmos DB nel cloud, è necessario autenticare tutte le richieste apportate all'emulatore di Azure Cosmos. Azure Cosmos Emulator supporta solo le comunicazioni protette tramite TLS. Azure Cosmos Emulator supporta un singolo account fisso e una chiave di autenticazione nota per l'autenticazione della chiave primaria. Questo account e questa chiave sono le uniche credenziali consentite per l'uso con l'emulatore Azure Cosmos. ovvero:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> La chiave primaria supportata dall'emulatore di Azure Cosmos è destinata all'uso solo con l'emulatore. Non è possibile usare l'account Azure Cosmos DB di produzione e la chiave con l'emulatore Azure Cosmos.

> [!NOTE]
> Se l'emulatore è stato avviato con l'opzione/Key, usare la chiave generata anziché la chiave predefinita `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Per altre informazioni sull'opzione /Key, vedere [Informazioni di riferimento sullo strumento da riga di comando](emulator-command-line-parameters.md).

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Connettersi a API diverse con l'emulatore

### <a name="sql-api"></a>API SQL

Dopo aver eseguito l'emulatore di Azure Cosmos sul desktop, è possibile usare qualsiasi [SDK di Azure Cosmos DB](sql-api-sdk-dotnet-standard.md) supportato o l' [API REST di Azure Cosmos DB](/rest/api/cosmos-db/) per interagire con l'emulatore. Azure Cosmos Emulator include anche una finestra di esplorazione dei dati incorporata che consente di creare contenitori per l'API SQL o Azure Cosmos DB per l'API di Mongo DB. Con Esplora dati è possibile visualizzare e modificare gli elementi senza scrivere alcun codice.

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API Azure Cosmos DB per MongoDB

Dopo aver eseguito l'emulatore di Azure Cosmos sul desktop, è possibile usare l' [API Azure Cosmos DB per MongoDB](mongodb-introduction.md) per interagire con l'emulatore. Avviare l'emulatore dal [prompt dei comandi](emulator-command-line-parameters.md) come amministratore con "/EnableMongoDbEndpoint". Usare quindi la stringa di connessione seguente per connettersi all'account dell'API MongoDB:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>API di tabella

Dopo aver eseguito l'emulatore di Azure Cosmos sul desktop, è possibile usare il [Azure Cosmos DB API tabella SDK](./tutorial-develop-table-dotnet.md) per interagire con l'emulatore. Avviare l'emulatore dal [prompt dei comandi](emulator-command-line-parameters.md) come amministratore con "/EnableTableEndpoint". quindi eseguire il codice seguente per connettersi all'account dell'API Tabella:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>API Cassandra

Avviare l'emulatore da un [prompt dei comandi](emulator-command-line-parameters.md) dell'amministratore con "/EnableCassandraEndpoint". In alternativa, è anche possibile impostare la variabile di ambiente `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

1. [Installare Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [Installare Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. Eseguire i comandi seguenti in una normale finestra del prompt dei comandi:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. Nella shell CQLSH eseguire i comandi seguenti per connettersi all'endpoint Cassandra:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>API Gremlin

Avviare l'emulatore da un [prompt dei comandi](emulator-command-line-parameters.md)dell'amministratore con "/EnableGremlinEndpoint". In alternativa, è anche possibile impostare la variabile di ambiente `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [Installare apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. Da Esplora dati dell'emulatore creare un database "DB1" e una raccolta "coll1"; per la chiave di partizione, scegliere "/Name".

1. Eseguire i comandi seguenti in una normale finestra del prompt dei comandi:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. Nella shell Gremlin eseguire i comandi seguenti per connettersi all'endpoint Gremlin:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Disinstallare l'emulatore locale

Per disinstallare l'emulatore, attenersi alla procedura seguente:

1. Uscire da tutte le istanze aperte dell'emulatore locale facendo clic con il pulsante destro del mouse sull'icona dell' **emulatore di Azure Cosmos** sulla barra delle applicazioni e quindi scegliere **Esci** . La chiusura di tutte le istanze può richiedere qualche minuto.

1. Nella casella di ricerca di Windows digitare **app & funzionalità** e selezionare **app & le funzionalità (impostazioni di sistema)** .

1. Nell'elenco di app scorrere fino all' **emulatore Azure Cosmos DB** , selezionarlo, fare clic su **Disinstalla** , quindi confermare e selezionare di nuovo **Disinstalla** .

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare l'emulatore locale per lo sviluppo locale gratuito. È ora possibile passare agli articoli successivi:

* [Esportare i certificati dell'emulatore di Azure Cosmos per l'uso con app Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Usare i parametri della riga di comando e i comandi di PowerShell per controllare l'emulatore](emulator-command-line-parameters.md)
* [Problemi di debug con l'emulatore](troubleshoot-local-emulator.md)