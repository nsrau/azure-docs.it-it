---
title: Usare l'emulatore Azurite per lo sviluppo di Archiviazione di Azure localeUse Azurite emulator for local Azure Storage development
description: L'emulatore open source di Azurite (anteprima) fornisce un ambiente locale gratuito per testare le applicazioni di archiviazione di Azure.The Azurite open-source emulator (preview) provides a free local environment for testing your Azure storage applications.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029931"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Usare l'emulatore Azurite per lo sviluppo e il test di Archiviazione di Azure locale (anteprima)Use the Azurite emulator for local Azure Storage development and testing (preview)

L'emulatore open source (anteprima) di Azurite versione 3.2 fornisce un ambiente locale gratuito per il test delle applicazioni di archiviazione blob e code di Azure.The Azurite version 3.2 open-source emulator (preview) provides a free local environment for testing your Azure blob and queue storage applications. Quando si è soddisfatti del funzionamento locale dell'applicazione, passare all'uso di un account di Archiviazione di Azure nel cloud. L'emulatore fornisce supporto multipiattaforma su Windows, Linux e MacOS. Azurite v3 supporta le API implementate dal servizio BLOB di Azure.Azurite v3 supports APIs implemented by the Azure Blob service.

Azurite è la futura piattaforma di emulatori di storage. Azurite sostituisce [l'emulatore](storage-use-emulator.md)di archiviazione di Azure. Azurite will continue to be updated to support the latest versions of Azure Storage APIs.

Esistono diversi modi per installare ed eseguire Azurite nel sistema locale:

  1. [Installare ed eseguire l'estensione Azurite Visual Studio CodeInstall and run the Azurite Visual Studio Code extension](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installare ed eseguire Azurite tramite NPM](#install-and-run-azurite-by-using-npm)
  1. [Installare ed eseguire l'immagine della finestra mobile Azurite](#install-and-run-the-azurite-docker-image)
  1. [Clonare, compilare ed eseguire Azurite dal repository GitHubClone, build, and run Azurite from the GitHub repository](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installare ed eseguire l'estensione Azurite Visual Studio CodeInstall and run the Azurite Visual Studio Code extension

All'interno di Visual Studio Code, selezionare il riquadro **EXTENSIONS** e cercare *Azurite* in **EXTENSIONS:MARKETPLACE**.

![Marketplace delle estensioni di codice di Visual StudioVisual Studio Code extensions marketplace](media/storage-use-azurite/azurite-vs-code-extension.png)

In alternativa, passare al [mercato delle estensioni del codice VS](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) nel browser. Selezionare il pulsante **Installa** per aprire Visual Studio Code e passare direttamente alla pagina di estensione Azurite.

È possibile avviare o chiudere rapidamente Azurite facendo clic su **[Azurite Blob Service]** o **[Azurite Queue Service]** nella barra di stato del codice VS o eseguendo i comandi seguenti nella tavolozza dei comandi del codice VS. Per aprire la tavolozza dei comandi, premere **F1** nel codice VS.

L'estensione supporta i seguenti comandi Visual Studio Code:

   * **Azurite: Start** - Avviare tutti i servizi Azurite
   * **Azurite: Chiudi** - Chiudi tutti i servizi Azurite
   * **Azurite: Clean** - Reimposta tutti i dati di persistenza dei servizi Azurite
   * **Azurite: Avviare il servizio BLOB** - Avviare il servizio BLOBAzurite: Start Blob Service - Start blob service
   * **Azurite: Chiudere il servizio BLOB** - chiudere il servizio BLOB
   * **Azurite: Clean Blob Service** - Pulire il servizio BLOB
   * **Azurite: Servizio di coda di avvio** - Avvia servizio di coda
   * **Azurite: Chiudi servizio di coda** - Chiudi servizio di coda
   * **Azurite: Pulire il servizio di coda** - Pulire il servizio di coda

Per configurare Azurite all'interno di Visual Studio Code, selezionare il riquadro delle estensioni. Selezionare l'icona **Gestisci** (ingranaggio) per **Azurite**. Selezionare **Configura impostazioni estensione**.

![Azurite configurare le impostazioni dell'estensione](media/storage-use-azurite/azurite-configure-extension-settings.png)

Sono supportate le seguenti impostazioni:

   * **Azurite: Blob Host** - Endpoint di ascolto del servizio BLOB. L'impostazione predefinita è 127.0.0.1.The default setting is 127.0.0.1.
   * **Azurite: Blob Port** - La porta di ascolto del servizio BLOB. La porta predefinita è 10000.
   * **Azurite: Debug** - Output del log di debug nel canale Azurite. Il valore predefinito è **false**.
   * **Azurite: Posizione** - Percorso del percorso dell'area di lavoro. Il valore predefinito è la cartella di lavoro del codice di Visual Studio.The default is the Visual Studio Code working folder.
   * **Azurite: Host coda** - Endpoint di ascolto del servizio di coda. L'impostazione predefinita è 127.0.0.1.The default setting is 127.0.0.1.
   * **Azurite: Porta coda** - La porta di attesa del servizio di coda. La porta predefinita è 10001.
   * **Azurite: Silent** - Silent mode disabilita il registro di accesso. Il valore predefinito è **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installare ed eseguire Azurite tramite NPM

Questo metodo di installazione richiede che sia installato [Node.js versione 8.0 o successiva.](https://nodejs.org) **npm** è lo strumento di gestione dei pacchetti incluso in ogni installazione di Node.js. Dopo aver installato Node.js, eseguire il seguente comando **npm** per installare Azurite.

```console
npm install -g azurite
```

Dopo aver installato Azurite, vedere [Eseguire Azurite da una riga di comando](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Installare ed eseguire l'immagine della finestra mobile Azurite

Usare [DockerHub](https://hub.docker.com/) per estrarre [l'immagine Azurite più recente](https://hub.docker.com/_/microsoft-azure-storage-azurite) utilizzando il comando seguente:

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Eseguire l'immagine docker Azurite**:

Il comando seguente esegue l'immagine Azurite Docker. Il `-p 10000:10000` parametro reindirizza le richieste dalla porta 10000 del computer host all'istanza Docker.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Specificare il percorso dell'area di lavoro**:

Nell'esempio seguente, `-v c:/azurite:/data` il parametro specifica *c:/azurite* come percorso dei dati persistenti di Azurite. La *directory, c:/azurite*, deve essere creata prima di eseguire il comando Docker .

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Eseguire solo il servizio BLOBRun just the blob service**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Impostare tutti i parametri Azurite**:

In questo esempio viene illustrato come impostare tutti i parametri della riga di comando. Tutti i parametri riportati di seguito devono essere posizionati in un'unica riga di comando.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Vedere [Opzioni della riga](#command-line-options) di comando per ulteriori informazioni sulla configurazione di Azurite all'avvio.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Clonare, compilare ed eseguire Azurite dal repository GitHubClone, build, and run Azurite from the GitHub repository

Questo metodo di installazione richiede l'installazione di [Git.This](https://git-scm.com/) installation method requires that you have Git installed. Clonare il [repository GitHub](https://github.com/azure/azurite) per il progetto Azurite utilizzando il comando console seguente.

```console
git clone https://github.com/Azure/Azurite.git
```

Dopo aver clonato il codice sorgente, eseguire i comandi seguenti dalla radice del repository clonato per compilare e installare Azurite.

```console
npm install
npm run build
npm install -g
```

Dopo l'installazione e la creazione di Azurite, vedere [Eseguire Azurite da una riga di comando](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Eseguire Azurite da una riga di comandoRun Azurite from a command-line

> [!NOTE]
> Azurite non può essere eseguito dalla riga di comando se è stata installata solo l'estensione visual Studio Code. Utilizzare invece la tavolozza dei comandi del codice VS. Per ulteriori informazioni, vedere [Installare ed eseguire l'estensione Azurite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Per iniziare immediatamente con la riga di comando, creare una directory denominata **c:,** quindi avviare Azurite eseguendo il comando seguente:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Questo comando indica ad Azurite di archiviare tutti i dati in una determinata directory, **c:.** Se l'opzione **--location** viene omessa, verrà utilizzata la directory di lavoro corrente.

## <a name="command-line-options"></a>Opzioni della riga di comando

In questa sezione vengono descritte in dettaglio le opzioni della riga di comando disponibili all'avvio di Azurite.This section details the command-line switches available when launching Azurite. Tutte le opzioni della riga di comando sono facoltative.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** è un collegamento per **--debug**, **-l** switch è un collegamento per **--location**, **-s** è una scelta rapida per **--silent**, e **-h** è un collegamento per **--help**.

### <a name="blob-listening-host"></a>Host di ascolto BLOB

**Facoltativo** Per impostazione predefinita, Azurite ascolterà 127.0.0.1 come server locale. Usare l'opzione **--blobHost** per impostare l'indirizzo in base alle proprie esigenze.

Accettare le richieste solo nel computer locale:Accept requests on the local machine only:

```console
azurite --blobHost 127.0.0.1
```

Consenti richieste remote:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> Consentire richieste remote può rendere il sistema vulnerabile ad attacchi esterni.

### <a name="blob-listening-port-configuration"></a>Configurazione della porta di ascolto BLOBBlob listening port configuration

**Facoltativo** Per impostazione predefinita, Azurite attenderà il servizio BLOB sulla porta 10000.By default, Azurite will listen for the Blob service on port 10000. Usare l'opzione **--blobPort** per specificare la porta di attesa necessaria.

> [!NOTE]
> Dopo aver usato una porta personalizzata, è necessario aggiornare la stringa di connessione o la configurazione corrispondente negli strumenti di Archiviazione di Azure o negli SDK.

Personalizzare la porta di ascolto del servizio BLOB:Customize the Blob service listening port:

```console
azurite --blobPort 8888
```

Consentire al sistema di selezionare automaticamente una porta disponibile:

```console
azurite --blobPort 0
```

La porta in uso viene visualizzata durante l'avvio di Azurite.

### <a name="queue-listening-host"></a>Host di ascolto coda

**Facoltativo** Per impostazione predefinita, Azurite ascolterà 127.0.0.1 come server locale. Utilizzare l'opzione **--queueHost** per impostare l'indirizzo in base alle proprie esigenze.

Accettare le richieste solo nel computer locale:Accept requests on the local machine only:

```console
azurite --queueHost 127.0.0.1
```

Consenti richieste remote:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Consentire richieste remote può rendere il sistema vulnerabile ad attacchi esterni.

### <a name="queue-listening-port-configuration"></a>Configurazione della porta di attesa della coda

**Facoltativo** Per impostazione predefinita, Azurite attenderà il servizio di coda sulla porta 10001. Utilizzare l'opzione **--queuePort** per specificare la porta di attesa richiesta.

> [!NOTE]
> Dopo aver usato una porta personalizzata, è necessario aggiornare la stringa di connessione o la configurazione corrispondente negli strumenti di Archiviazione di Azure o negli SDK.

Personalizzare la porta di attesa del servizio di coda:Customize the Queue service listening port:

```console
azurite --queuePort 8888
```

Consentire al sistema di selezionare automaticamente una porta disponibile:

```console
azurite --queuePort 0
```

La porta in uso viene visualizzata durante l'avvio di Azurite.

### <a name="workspace-path"></a>Percorso dell'area di lavoro

**Facoltativo** Azurite archivia i dati sul disco locale durante l'esecuzione. Utilizzare l'opzione **--location** per specificare un percorso come posizione dell'area di lavoro. Per impostazione predefinita, verrà utilizzata la directory di lavoro del processo corrente.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Log di accesso

**Facoltativo** Per impostazione predefinita, il registro di accesso viene visualizzato nella finestra della console. Disabilitare la visualizzazione del registro di accesso utilizzando l'opzione **--silent.**

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Registro di debug

**Facoltativo** Il log di debug include informazioni dettagliate su ogni richiesta e analisi dello stack di eccezioni. Abilitare il registro di debug specificando un percorso di file locale valido per l'opzione **--debug.**

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Modalità Allentata

**Facoltativo** Per impostazione predefinita, Azurite applica la modalità strict per bloccare i parametri e le intestazioni di richiesta non supportati. Disattivare la modalità rigorosa utilizzando l'opzione **--loose.**

```console
azurite --loose
```

Si noti l'interruttore di scelta rapida 'L' maiuscola:

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autorizzazione per strumenti e SDK

Connettersi ad Azurite da SDK o strumenti di Archiviazione di Azure, ad esempio [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)usando qualsiasi strategia di autenticazione. L'autenticazione è obbligatoria. Azurite supporta l'autorizzazione con chiave condivisa e firme di accesso condiviso (SAS). Azurite supporta anche l'accesso anonimo ai contenitori pubblici.

### <a name="well-known-storage-account-and-key"></a>Account e chiave di archiviazione noti

È possibile utilizzare il nome account e la chiave seguenti con Azurite.You can use the following account name and key with Azurite. Si tratta dello stesso account noto e della stessa chiave usata dall'emulatore di archiviazione di Azure legacy.

* Nome account:`devstoreaccount1`
* Chiave account:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> Oltre all'autenticazione SharedKey, Azurite supporta l'autenticazione della rete di accesso condiviso dell'account e del servizio. L'accesso anonimo è disponibile anche quando un contenitore è impostato per consentire l'accesso pubblico.

### <a name="connection-string"></a>Stringa di connessione

Il modo più semplice per connettersi ad Azurite dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento *UseDevelopmentStorage .* Ecco un esempio di stringa di connessione in un file app.config:Here's an example of a connection string in an *app.config* file:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Per altre informazioni, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Account e chiavi di archiviazione personalizzati

Azurite supporta chiavi e nomi di `AZURITE_ACCOUNTS` account di archiviazione personalizzati `account1:key1[:key2];account2:key1[:key2];...`impostando la variabile di ambiente nel formato seguente: .

Ad esempio, usare un account di archiviazione personalizzato con una chiave:For example, use a custom storage account that has one key:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

In alternativa, usare più account di archiviazione con due chiavi ciascuno:Or use multiple storage accounts with 2 keys each:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite aggiorna i nomi account e le chiavi personalizzati dalla variabile di ambiente ogni minuto per impostazione predefinita. Con questa funzionalità, è possibile ruotare dinamicamente la chiave dell'account o aggiungere nuovi account di archiviazione senza riavviare Azurite.With this feature, you can dynamically rotate the account key, or add new storage accounts without restarting Azurite.

> [!NOTE]
> L'account di archiviazione predefinito `devstoreaccount1` è disabilitato quando si impostano account di archiviazione personalizzati.

> [!NOTE]
> Aggiornare la stringa di connessione di conseguenza quando si utilizzano chiavi e nomi di account personalizzati.

> [!NOTE]
> Usare `export` la parola chiave per impostare `set` le variabili di ambiente in un ambiente Linux, usare Windows.Use the keyword to set environment variables in a Linux environment, use in Windows.

### <a name="storage-explorer"></a>Storage Explorer

In Esplora archivi di Azure connettersi ad Azurite facendo clic sull'icona **Aggiungi account,** quindi selezionare **Connetti a un emulatore locale** e fare clic su **Connetti**.

## <a name="differences-between-azurite-and-azure-storage"></a>Differenze tra Azurite e Archiviazione di AzureDifferences between Azurite and Azure Storage

Esistono differenze funzionali tra un'istanza locale di Azurite e un account di archiviazione di Azure nel cloud.

### <a name="endpoint-and-connection-url"></a>Endpoint e URL di connessione

Gli endpoint del servizio per Azurite sono diversi dagli endpoint di un account di archiviazione di Azure.The service endpoints for Azurite are different from the endpoints of an Azure Storage account. Il computer locale non esegui la risoluzione dei nomi di dominio, richiedendo che gli endpoint Azurite siano indirizzi locali.

Quando si indirizza una risorsa in un account di archiviazione di Azure, il nome dell'account fa parte del nome host URI. La risorsa a cui viene affrontata fa parte del percorso URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

L'URI seguente è un indirizzo valido per un BLOB in un account di archiviazione di Azure:The following URI is a valid address for a blob in an Azure Storage account:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Poiché il computer locale non esegue la risoluzione dei nomi di dominio, il nome dell'account fa parte del percorso URI anziché del nome host. Usare il formato URI seguente per una risorsa in Azurite:Use the following URI format for a resource in Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

L'indirizzo seguente può essere usato per accedere a un BLOB in Azurite:The following address might be used for accessing a blob in Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Scalabilità e prestazioni

Azurite non è un servizio di archiviazione scalabile e non supporta un numero elevato di client simultanei. Non c'è garanzia di prestazioni. Azurite è destinato allo sviluppo e al test.

### <a name="error-handling"></a>Gestione degli errori

Azurite è allineato con la logica di gestione degli errori di Archiviazione di Azure, ma esistono differenze. Ad esempio, i messaggi di errore possono essere diversi, mentre i codici di stato di errore vengono allineati.

### <a name="ra-grs"></a>RA-GRS

Azurite supporta la replica con ridondanza geografica di accesso in lettura (RA-GRS). Per le risorse di archiviazione, accedere alla posizione secondaria aggiungendo **-secondary** al nome dell'account. For example, the following address might be used for accessing a blob using the read-only secondary in Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite è open-source

Contributi e suggerimenti per Azurite sono i benvenuti. Vai alla pagina del progetto Azurite [GitHub](https://github.com/Azure/Azurite/projects) o [ai problemi](https://github.com/Azure/Azurite/issues) di GitHub per le attività cardine e gli elementi di lavoro che stiamo monitorando per le funzionalità imminenti e le correzioni di bug. Gli elementi di lavoro dettagliati vengono registrati anche in GitHub.Detailed work items are also tracked in GitHub.

## <a name="next-steps"></a>Passaggi successivi

* [Usare l'emulatore](storage-use-emulator.md) di archiviazione di Azure per lo sviluppo e il test dei documenti dell'emulatore di archiviazione di Azure legacy, che viene sostituito da Azurite.Use the Azure storage emulator for development and testing documents the legacy Azure storage emulator, which is being superseded by Azurite.
* [Configurare le stringhe](storage-configure-connection-string.md) di connessione di Archiviazione di Azure illustra come assemblare una stringa di connessione di Azure STorage valida.
