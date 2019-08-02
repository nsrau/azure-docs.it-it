---
title: Usare l'emulatore open source azzurrite per lo sviluppo e il test dell'archiviazione BLOB (anteprima)
description: L'emulatore open source azzurrite (anteprima) fornisce un ambiente locale gratuito per il test delle applicazioni di archiviazione BLOB di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/12/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: ebecd6cf9af5395e4da2b395ca9b2ff974a75409
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721706"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>Usare l'emulatore open source azzurrite per lo sviluppo e il test dell'archiviazione BLOB (anteprima)

L'emulatore open source azzurrite versione 3 (anteprima) fornisce un ambiente locale gratuito per il test delle applicazioni di archiviazione BLOB di Azure. Quando si è soddisfatti del funzionamento dell'applicazione in locale, passare a usare un account di archiviazione di Azure nel cloud. L'emulatore fornisce supporto multipiattaforma in Windows, Linux e MacOS. Azzurrite V3 supporta le API implementate dal servizio BLOB di Azure.

Azzurrite è la piattaforma dell'emulatore di archiviazione futura. Azzurrite sostituisce l'emulatore di [archiviazione di Azure](storage-use-emulator.md). Azzurrite continuerà a essere aggiornato per supportare le versioni più recenti delle API di archiviazione di Azure.

Esistono diversi modi per installare ed eseguire azzurrite nel sistema locale:

  1. [Installare ed eseguire l'estensione azzurrite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installare ed eseguire azzurrite usando NPM](#install-and-run-azurite-by-using-npm)
  1. [Installare ed eseguire l'immagine Docker azzurrite](#install-and-run-the-azurite-docker-image)
  1. [Clonare, compilare ed eseguire azzurrite dal repository GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installare ed eseguire l'estensione azzurrite Visual Studio Code

In Visual Studio Code selezionare il riquadro **estensioni** e cercare *azzurrite* in **Extensions: Marketplace**.

![Marketplace di Visual Studio Code Extensions](media/storage-use-azurite/azurite-vs-code-extension.png)

In alternativa, passare a [vs code Market Extension](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) nel browser. Selezionare il pulsante **Install (installa** ) per aprire Visual Studio Code e passare direttamente alla pagina di estensione azzurrite.

È possibile avviare o chiudere rapidamente azzurrite facendo clic sul **servizio BLOB azzurrite** nella barra di stato vs code o eseguendo i comandi seguenti nel riquadro dei comandi di vs code. Per aprire il riquadro comandi, premere **F1** in vs code.

L'estensione supporta i comandi di Visual Studio Code seguenti:

   * **Azzurrite Start** : avvia tutti i servizi azzurrite
   * **Azzurrite Chiudi** chiudere tutti i servizi azzurrite
   * **Azzurrite Pulisci** -Reimposta tutti i servizi azzurrite persistenza
   * **Azzurrite Start** -BLOB avvia servizio BLOB
   * **Azzurrite Close** -BLOB Close-servizio BLOB
   * **Azzurrite Clean** -BLOB Pulisci servizio BLOB

Per configurare azzurrite in Visual Studio Code, selezionare il riquadro estensioni e fare clic con il pulsante destro del mouse su **azzurrite**. Selezionare **Configura impostazioni estensioni**.

![Azzurrite configurare le impostazioni dell'estensione](media/storage-use-azurite/azurite-configure-extension-settings.png)

Sono supportate le impostazioni seguenti:

   * **Azzurrite Host** BLOB: endpoint di ascolto del servizio BLOB. L'impostazione predefinita è 127.0.0.1.
   * **Azzurrite Porta** BLOB: porta di ascolto del servizio BLOB. La porta predefinita è 10000.
   * **Azzurrite Debug** : consente di restituire il log di debug al canale azzurrite. Il valore predefinito è **false**.
   * **Azzurrite Location** : percorso dell'area di lavoro. Il valore predefinito è la cartella di lavoro Visual Studio Code.
   * **Azzurrite**  In modalità invisibile all'utente viene disabilitato il log di accesso. Il valore predefinito è **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installare ed eseguire azzurrite usando NPM

Per questo metodo di installazione è necessario che sia installato [node. js versione 8,0 o successiva](https://nodejs.org) . **NPM** è lo strumento di gestione dei pacchetti incluso in ogni installazione di node. js. Dopo l'installazione di node. js, eseguire il comando **NPM** seguente per installare azzurrite.

```console
npm install -g azurite
```

Dopo l'installazione di azzurrite, vedere [eseguire azzurrite da una riga di comando](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Installare ed eseguire l'immagine Docker azzurrite

Usare [DockerHub](https://hub.docker.com/) per eseguire il pull dell' [immagine di azzurrite più recente](https://hub.docker.com/_/microsoft-azure-storage-azurite) usando il comando seguente:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Eseguire l'immagine Docker azzurrite**:

Il comando seguente esegue l'immagine Docker azzurrite. Il `-p 10000:10000` parametro reindirizza le richieste dalla porta 10000 del computer host all'istanza docker.

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**Specificare il percorso dell'area di lavoro**:

Nell'esempio seguente, il `-v c:/azurite:/data` parametro specifica `c:/azurite` il percorso dei dati azzurrite salvati in modo permanente.

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Imposta tutti i parametri di azzurrite**:

Questo esempio illustra come impostare tutti i parametri della riga di comando. Tutti i parametri seguenti devono essere posizionati in una singola riga di comando.

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

Per ulteriori informazioni sulla configurazione di azzurrite all'avvio, vedere [Opzioni della riga di comando](#command-line-options) .

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Clonare, compilare ed eseguire azzurrite dal repository GitHub

Questo metodo di installazione richiede che [git](https://git-scm.com/) sia installato. Clonare il [repository GitHub](https://github.com/azure/azurite) per il progetto azzurrite usando il comando della console seguente.

```console
git clone https://github.com/Azure/Azurite.git
```

Dopo la clonazione del codice sorgente, eseguire i comandi seguenti dalla radice del repository clonato per compilare e installare azzurrite.

```console
npm install
npm run build
npm install -g
```

Dopo l'installazione e la compilazione di azzurrite, vedere [eseguire azzurrite da una riga di comando](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Eseguire azzurrite da una riga di comando

> [!NOTE]
> Non è possibile eseguire azzurrite dalla riga di comando se è stata installata solo l'estensione Visual Studio Code. Usare invece il VS Code riquadro comandi. Per ulteriori informazioni, vedere [installare ed eseguire l'estensione azzurrite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Per iniziare immediatamente a usare la riga di comando, creare una directory denominata **c:\azurite**, quindi avviare azzurrite eseguendo il comando seguente:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Questo comando indica a azzurrite di archiviare tutti i dati in una directory specifica, **c:\azurite**. Se l'opzione **--location** viene omessa, verrà utilizzata la directory di lavoro corrente.

## <a name="command-line-options"></a>Opzioni della riga di comando

Questa sezione illustra in dettaglio le opzioni della riga di comando disponibili all'avvio di azzurrite. Tutte le opzioni della riga di comando sono facoltative.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

L'opzione **-l** è un collegamento per **--location**, **-s** è un collegamento per **--Silent**e **-d** è un collegamento per **--debug**.

### <a name="listening-host"></a>Host in ascolto

**Facoltativo** Per impostazione predefinita, azzurrite resterà in ascolto di 127.0.0.1 come server locale. Usare l'opzione **--blobHost** per impostare l'indirizzo ai propri requisiti.

Accetta solo le richieste sul computer locale:

```console
azurite --blobHost 127.0.0.1
```

Consenti richieste remote:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Consentire le richieste remote può rendere il sistema vulnerabile agli attacchi esterni.

### <a name="listening-port-configuration"></a>Configurazione della porta di ascolto

**Facoltativo** Per impostazione predefinita, azzurrite resterà in ascolto per il servizio BLOB sulla porta 10000. Usare l'opzione **--blobPort** per specificare la porta di ascolto richiesta.

> [!NOTE]
> Dopo aver usato una porta personalizzata, è necessario aggiornare la stringa di connessione o la configurazione corrispondente negli strumenti o negli SDK di archiviazione di Azure.

Personalizzare la porta di ascolto del servizio BLOB:

```console
azurite --blobPort 8888
```

Consentire al sistema di selezionare automaticamente una porta disponibile:

```console
azurite --blobPort 0
```

La porta in uso viene visualizzata durante l'avvio di azzurrite.

### <a name="workspace-path"></a>Percorso area di lavoro

**Facoltativo** Azzurrite archivia i dati nel disco locale durante l'esecuzione. Usare l'opzione **--location** per specificare un percorso come percorso dell'area di lavoro. Per impostazione predefinita, verrà usata la directory di lavoro del processo corrente.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Log di accesso

**Facoltativo** Per impostazione predefinita, il log di accesso viene visualizzato nella finestra della console. Disabilitare la visualizzazione del log di accesso utilizzando l'opzione **--invisibile all'utente** .

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Log di debug

**Facoltativo** Il log di debug include informazioni dettagliate su ogni richiesta e analisi dello stack di eccezioni. Abilitare il log di debug specificando un percorso di file locale valido per l'opzione **--debug** .

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>Autorizzazione per strumenti e SDK

Connettersi a azzurrite da SDK o strumenti di archiviazione di Azure, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), usando qualsiasi strategia di autenticazione. Autenticazione necessaria. Azzurrite supporta l'autorizzazione con la chiave condivisa e le firme di accesso condiviso (SAS). Azzurrite supporta anche l'accesso anonimo ai contenitori pubblici.

### <a name="well-known-storage-account-and-key"></a>Chiave e account di archiviazione ben noti

Con azzurrite è possibile usare il nome e la chiave dell'account seguenti. Si tratta dello stesso account e chiave ben noti usati dall'emulatore di archiviazione di Azure legacy.

* Nome account:`devstoreaccount1`
* Chiave account:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Oltre all'autenticazione SharedKey, azzurrite supporta l'autenticazione della firma di accesso condiviso dell'account e del servizio. L'accesso anonimo è disponibile anche quando un contenitore è impostato per consentire l'accesso pubblico.

### <a name="connection-string"></a>Stringa di connessione

Il modo più semplice per connettersi a azzurrite dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento *UseDevelopmentStorage = true*. Di seguito è riportato un esempio di una stringa di connessione in un file *app. config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Per altre informazioni, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](storage-configure-connection-string.md).

### <a name="storage-explorer"></a>Storage Explorer

In Azure Storage Explorer connettersi a azzurrite facendo clic sull'icona **Aggiungi account** , quindi selezionare Connetti **a un emulatore locale** e fare clic su **Connetti**.

## <a name="differences-between-azurite-and-azure-storage"></a>Differenze tra azzurrite e archiviazione di Azure

Esistono differenze funzionali tra un'istanza locale di azzurrite e un account di archiviazione di Azure nel cloud.

### <a name="endpoint-and-connection-url"></a>Endpoint e URL di connessione

Gli endpoint di servizio per azzurrite sono diversi dagli endpoint di un account di archiviazione di Azure. Il computer locale non esegue la risoluzione dei nomi di dominio, che richiede che gli endpoint azzurrite siano indirizzi locali.

Quando si indirizza una risorsa in un account di archiviazione di Azure, il nome dell'account fa parte del nome host dell'URI. La risorsa che viene risolta fa parte del percorso URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

L'URI seguente è un indirizzo valido per un BLOB in un account di archiviazione di Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Poiché il computer locale non esegue la risoluzione dei nomi di dominio, il nome dell'account fa parte del percorso URI anziché del nome host. Usare il formato URI seguente per una risorsa in azzurrite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Per accedere a un BLOB in azzurrite, è possibile usare l'indirizzo seguente:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Scalabilità e prestazioni

Azzurrite non è un servizio di archiviazione scalabile e non supporta un numero elevato di client simultanei. Non esiste alcuna garanzia sulle prestazioni. Azzurrite è destinato a scopi di sviluppo e test.

### <a name="error-handling"></a>Gestione degli errori

Azzurrite è allineato alla logica di gestione degli errori di archiviazione di Azure, ma esistono differenze. Ad esempio, i messaggi di errore possono essere diversi, mentre i codici di stato dell'errore sono allineati.

### <a name="ra-grs"></a>RA-GRS

Azzurrite supporta la replica con ridondanza geografica e accesso in lettura (RA-GRS). Per le risorse di archiviazione, accedere alla posizione secondaria aggiungendo **-Secondary** al nome dell'account. Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB usando la replica secondaria di sola lettura in azzurrite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azzurrite è open source

I contributi e i suggerimenti per azzurrite sono benvenuti. Visitare la pagina del [progetto GitHub](https://github.com/Azure/Azurite/projects) azzurrite o i [problemi di GitHub](https://github.com/Azure/Azurite/issues) per le attività cardine e gli elementi di lavoro che vengono monitorati per le funzionalità imminenti e le correzioni di bug. Gli elementi di lavoro dettagliati vengono inoltre rilevati in GitHub.

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'emulatore di archiviazione di Azure per lo sviluppo e il test](storage-use-emulator.md) dei documenti nell'emulatore di archiviazione di Azure legacy, che viene sostituito da azzurrite.
* [Configurare le stringhe di connessione di archiviazione di Azure](storage-configure-connection-string.md) illustra come assemblare una stringa di connessione di archiviazione di Azure valida.
