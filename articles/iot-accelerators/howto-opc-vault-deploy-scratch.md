---
title: Come distribuire insieme di credenziali di OPC modulo da zero - Azure | Microsoft Docs
description: Come distribuire OPC dell'insieme di credenziali da zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 990b13d362b796f5e71f1bd41eb35fce9720fff4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835158"
---
# <a name="deploy-opc-vault-from-scratch"></a>Distribuire OPC Vault da zero

OPC Vault è un microservizio che consente di configurare, registrare e gestire il ciclo di vita dei certificati per le applicazioni server e client OPC UA nel cloud. Questo articolo illustra come distribuire OPC dell'insieme di credenziali da zero.

## <a name="configuration-and-environment-variables"></a>Configurazione e variabili di ambiente

La configurazione del servizio verrà archiviata utilizzando schede configurazione di ASP.NET Core, in appsettings.ini. Il formato INI consente di archiviare i valori in un formato leggibile, con commenti.
L'applicazione supporta anche l'inserimento delle variabili di ambiente, ad esempio le credenziali e i dettagli sulla rete. (Questa sezione viene originariamente denominata configurazione TODO e variabili di ambiente).

Alcune variabili di ambiente che è necessario creare almeno una volta fa riferimento il file di configurazione nel repository. A seconda del sistema operativo e dell'IDE, esistono diversi modi per gestire le variabili di ambiente:

- Per gli utenti di Windows, lo script env-var-Setup. cmd deve essere preparata ed eseguita una sola volta. Quando viene eseguita, le impostazioni verranno mantenute tra i riavvii e le sessioni di terminal.

- Per ambienti Linux e OSX, lo script env-var-programma di installazione deve essere eseguita ogni volta che viene aperta una nuova console. A seconda del sistema operativo e terminal, esistono modi per rendere persistente a livello globale, i valori per queste pagine dovrebbero aiutare altre informazioni:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Le variabili di ambiente possono essere impostati anche da Visual Studio, nelle proprietà del progetto, nel riquadro sinistro selezionare "Proprietà di configurazione" e "Environment" per ottenere una sezione in cui è possibile aggiungere più variabili.

- Rider IntelliJ: Le variabili di ambiente possono essere impostate in ogni configurazione di esecuzione, in modo analogo per IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Esecuzione e il debug con Visual Studio

Visual Studio consente di aprire rapidamente l'applicazione senza usare un prompt dei comandi, senza configurare alcuna operazione all'esterno dell'IDE.

Per eseguire e il debug dell'applicazione tramite Visual Studio:

1. Aprire la soluzione tramite il `iot-opc-gds-service.sln` file.

1. Quando viene caricata la soluzione, fare clic sui `WebService` del progetto, selezionare e passare al `Debug` sezione.

1. Nella stessa sezione, definire le variabili di ambiente necessarie.

1. Premere **F5**, o il **eseguire** icona. Visual Studio viene aperto il browser che mostra lo stato del servizio in formato JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Esecuzione e il debug con IntelliJ Rider

1. Aprire la soluzione tramite il `iot-opc-gds-service.sln` file.

1. Quando viene caricata la soluzione, passare a `Run > Edit Configurations` e creare un nuovo `.NET Project` configurazione.

1. Nella configurazione, selezionare il progetto servizio Web.

1. Salvare le impostazioni ed eseguire la configurazione creata nella barra degli strumenti dell'IDE.

1. Verrà visualizzato il servizio bootstrap di messaggi nella finestra Esegui IntelliJ con i dettagli, ad esempio l'URL in cui il servizio web è in esecuzione oltre il servizio di log.

## <a name="build-and-run-from-the-command-line"></a>Compilare ed eseguire dalla riga di comando

La cartella script contiene alcuni script per le attività frequenti:

- `build`: Compilare tutti i progetti ed eseguire i test.

- `compile`: Compilare tutti i progetti.

- `run`: Compilare i progetti ed eseguire il servizio, che richiede privilegi elevati in Windows per eseguire il servizio web.

Gli script di verificare la configurazione di variabili di ambiente. È possibile impostare le variabili di ambiente a livello globale del sistema operativo o usare lo script "env-var-installazione" nella cartella scripts.

### <a name="sandbox"></a>Sandbox

Gli script presuppongono che sia configurato l'ambiente di sviluppo con .NET Core e Docker. È possibile evitare l'installazione di .NET Core e installare solo Docker e usare il parametro della riga di comando `--in-sandbox` (o la versione abbreviata `-s`), ad esempio:

- `build --in-sandbox`: Esegue l'attività di compilazione all'interno di un contenitore Docker (forma breve `build -s`).

- `compile --in-sandbox`: Esegue l'attività di compilazione all'interno di un contenitore Docker (forma breve `compile -s`).

- `run --in-sandbox`: Avvia il servizio all'interno di un contenitore Docker (forma breve `run -s`).

Le immagini Docker usate per la sandbox sono ospitate in Docker Hub [qui](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Pacchetto dell'applicazione in un'immagine Docker

Il `scripts` cartella include una sottocartella di docker con i file necessari per creare un pacchetto del servizio in un'immagine Docker:

- `Dockerfile`: Specifiche di immagini docker.
- `build`: Creare un contenitore Docker ed archiviare l'immagine nel Registro di sistema locale.
- `run`: Eseguire il contenitore Docker dall'immagine archiviate nel Registro di sistema locale.
- `content`: Una cartella con i file copiati nell'immagine, inclusi script del punto di ingresso.

## <a name="azure-iot-hub-setup"></a>Configurazione dell'IoT Hub di Azure

Per usare il microservizio, configurare l'IoT Hub di Azure per i test di integrazione e sviluppo.

Il progetto include alcuni script Bash per facilitare il programma di installazione:

- Creare nuovo IoT Hub: `./scripts/iothub/create-hub.sh`

- Elencare gli hub esistenti: `./scripts/iothub/list-hubs.sh`

- Visualizzare i dettagli (ad esempio, chiavi) dell'IoT Hub: `./scripts/iothub/show-hub.sh`

E, in caso di più sottoscrizioni di Azure:

- Visualizzare un elenco delle sottoscrizioni: `./scripts/iothub/list-subscriptions.sh`

- Modificare la sottoscrizione corrente: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Configurazione di sviluppo

### <a name="net-setup"></a>Programma di installazione di .NET

Il flusso di lavoro del progetto è gestito tramite [.NET Core](https://dotnet.github.io) 1.x, è necessario installare nel proprio ambiente, in modo che è possibile eseguire tutti gli script e assicurarsi che l'IDE funzioni come previsto.

Forniamo inoltre un' [versione di Java](https://github.com/Azure/iot-opc-gds-service-dotnet) del progetto e di altri componenti di Azure IoT PC.

### <a name="ide"></a>IDE

Ecco alcuni degli ambienti IDE che è possibile usare per lavorare nei PC di Azure IoT:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Programma di installazione di GIT

Il progetto include un hook di Git, per automatizzare alcuni controlli prima di accettare una modifica del codice. È possibile eseguire i test manualmente o consentire alla piattaforma di integrazione continua per eseguire i test. Utilizziamo l'hook Git seguente per eseguire tutti i test prima di inviare modifiche al codice in GitHub e velocizzare il flusso di lavoro di sviluppo automaticamente.

Se in qualsiasi momento si desidera rimuovere l'hook, è sufficiente eliminare il file installato sotto `.git/hooks`. È anche possibile ignorare l'hook di pre-commit usando il `--no-verify` opzione.

#### <a name="pre-commit-hook-with-sandbox"></a>Hook di pre-commit con sandbox

Per configurare gli hook inclusi, aprire una console di Windows/Linux/MacOS ed eseguire:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Con questa configurazione, all'archiviazione dei file, Git consente di verificare che l'applicazione passa tutti i test, eseguiti la compilazione e i test all'interno di un contenitore Docker che è configurato con tutti i requisiti di sviluppo.

#### <a name="pre-commit-hook-without-sandbox"></a>Hook di pre-commit senza sandbox

> [!NOTE] 
> Richiede l'hook senza sandbox [.NET Core](https://dotnet.github.io) nel percorso di sistema.

Per configurare gli hook inclusi, aprire una console di Windows/Linux/MacOS ed eseguire:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Con questa configurazione, all'archiviazione dei file, Git consente di verificare che l'applicazione passa tutti i test, Esegui la compilazione e test in workstation usando gli strumenti installati nel sistema operativo.

Linee guida per lo stile di codice di progetto:

- Laddove possibile, lunghezza delle righe è limitato a 80 caratteri massime per le revisioni del codice e gli editor della riga di comando.

- I blocchi di codice il rientro quattro spazi. È consigliabile evitare il carattere tab.

- File di testo utilizzano l'istruzione end Unix del formato di riga (LF).

- Inserimento delle dipendenze viene gestito con [Autofac](https://autofac.org).

- I campi di API del servizio Web sono CamelCased ad eccezione dei metadati.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire OPC dell'insieme di credenziali da zero, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Distribuzione dei dispositivi gemelli OPC da zero](howto-opc-twin-deploy-modules.md)