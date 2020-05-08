---
title: Usare l'emulatore di azzurrite per lo sviluppo locale di archiviazione di Azure
description: L'emulatore open source azzurrite (anteprima) fornisce un ambiente locale gratuito per il test delle applicazioni di archiviazione di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/01/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: e20271e381f2e7023dca3c3382c9f329a5149a62
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872611"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Usare l'emulatore di azzurrite per lo sviluppo e il testing di archiviazione di Azure locale (anteprima)

L'emulatore open source azzurrite versione 3,2 (anteprima) fornisce un ambiente locale gratuito per il test delle applicazioni di archiviazione BLOB e di Accodamento di Azure. Quando si è soddisfatti del funzionamento dell'applicazione in locale, passare a usare un account di archiviazione di Azure nel cloud. L'emulatore fornisce supporto multipiattaforma in Windows, Linux e macOS. Azzurrite V3 supporta le API implementate dal servizio BLOB di Azure.

Azzurrite è la piattaforma dell'emulatore di archiviazione futura. Azzurrite sostituisce l' [emulatore di archiviazione di Azure](storage-use-emulator.md). Azzurrite continuerà a essere aggiornato per supportare le versioni più recenti delle API di archiviazione di Azure.

Esistono diversi modi per installare ed eseguire azzurrite nel sistema locale:

  1. [Installare ed eseguire l'estensione azzurrite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installare ed eseguire azzurrite usando NPM](#install-and-run-azurite-by-using-npm)
  1. [Installare ed eseguire l'immagine Docker azzurrite](#install-and-run-the-azurite-docker-image)
  1. [Clonare, compilare ed eseguire azzurrite dal repository GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installare ed eseguire l'estensione azzurrite Visual Studio Code

In Visual Studio Code selezionare il riquadro **estensioni** e cercare *azzurrite* in **Extensions: Marketplace**.

![Marketplace di Visual Studio Code Extensions](media/storage-use-azurite/azurite-vs-code-extension.png)

È anche possibile passare a [Visual Studio Code Market Extension](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) nel browser. Selezionare il pulsante **Install (installa** ) per aprire Visual Studio Code e passare direttamente alla pagina di estensione azzurrite.

È possibile avviare o chiudere rapidamente azzurrite nella barra di stato Visual Studio Code. Fare clic su **[servizio BLOB azzurrite]** o **[servizio di Accodamento azzurrite]**.

L'estensione supporta i comandi di Visual Studio Code seguenti. Per aprire il riquadro comandi, premere F1 in Visual Studio Code. 

   - **Azzurrite: Pulisci** -Reimposta tutti i servizi azzurrite dati persistenza
   - **Azzurrite: Pulisci servizio BLOB** -Pulisci servizio BLOB
   - **Azzurrite: Pulisci servizio di Accodamento** -Pulisci servizio di Accodamento
   - **Azzurrite: chiudere** tutti i servizi azzurrite
   - **Azzurrite: Chiudi servizio BLOB** -Chiudi servizio BLOB
   - **Azzurrite: Chiudi servizio di Accodamento** -Chiudi servizio di Accodamento
   - **Azzurrite: avviare** tutti i servizi azzurrite
   - **Azzurrite: Avvia servizio BLOB** -avvia servizio BLOB
   - **Azzurrite: avvio del servizio di Accodamento** -avvio del servizio di Accodamento

Per configurare azzurrite in Visual Studio Code, selezionare il riquadro estensioni. Selezionare l'icona **Gestisci** (ingranaggio) per **azzurrite**. Selezionare **Impostazioni estensione**.

![Azurites configurare le impostazioni dell'estensione](media/storage-use-azurite/azurite-configure-extension-settings.png)

Sono supportate le impostazioni seguenti:

   - **Azzurrite: host BLOB:** endpoint di ascolto del servizio BLOB. L'impostazione predefinita è 127.0.0.1.
   - **Azzurrite: porta BLOB:** porta di ascolto del servizio BLOB. La porta predefinita è 10000.
   - **Azzurrite: CERT** -percorso di un file di certificato PEM o PFX localmente attendibile per abilitare la modalità HTTPS.
   - **Azzurrite: debug:** consente di restituire il log di debug al canale azzurrite. Il valore predefinito è **false**.
   - **Azzurrite: chiave** -percorso di un file di chiave PEM locale attendibile, obbligatorio quando **azzurrite: CERT** punta a un file PEM.
   - **Azzurrite: location** -percorso dell'area di lavoro. Il valore predefinito è la cartella di lavoro Visual Studio Code.
   - **Azzurrite:** Abilita la modalità Loose, che ignora le intestazioni e i parametri non supportati.
   - **Azzurrite: OAuth** -livello OAuth facoltativo.
   - **Azzurrite: pwd** -password per il file PFX. Obbligatorio quando **azzurrite: CERT** punta a un file PFX.
   - **Azzurrite: host della coda:** l'endpoint di ascolto servizio di Accodamento. L'impostazione predefinita è 127.0.0.1.
   - **Azzurrite: porta di coda** -la porta di attesa servizio di Accodamento. La porta predefinita è 10001.
   - **Azzurrite:** la modalità invisibile all'utente disabilita il log di accesso. Il valore predefinito è **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installare ed eseguire azzurrite usando NPM

Per questo metodo di installazione è necessario che sia installato [node. js versione 8,0 o successiva](https://nodejs.org) . Node Package Manager (NPM) è lo strumento di gestione pacchetti incluso in ogni installazione di node. js. Dopo l'installazione di node. js, eseguire `npm` il comando seguente per installare azzurrite.

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
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**Specificare il percorso dell'area di lavoro**:

Nell'esempio seguente il `-v c:/azurite:/data` parametro specifica *c:/azzurrite* come percorso dati permanente azzurrite. È necessario creare la directory *c:/azzurrite*prima di eseguire il comando docker.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Eseguire solo il servizio BLOB**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

Per ulteriori informazioni sulla configurazione di azzurrite all'avvio, vedere [Opzioni della riga di comando](#command-line-options).

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

## <a name="run-azurite-from-a-command-line"></a>Eseguire azzurrite dalla riga di comando

> [!NOTE]
> Non è possibile eseguire azzurrite dalla riga di comando se è stata installata solo l'estensione Visual Studio Code. Usare invece il Visual Studio Code riquadro comandi. Per ulteriori informazioni, vedere [installare ed eseguire l'estensione azzurrite Visual Studio Code](#install-and-run-the-azurite-visual-studio-code-extension).

Per iniziare immediatamente a usare la riga di comando, creare una directory denominata *c:\azurite*, quindi avviare azzurrite eseguendo il comando seguente:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Questo comando indica a azzurrite di archiviare tutti i dati in una directory specifica, *c:\azurite*. Se l' `--location` opzione viene omessa, verrà utilizzata la directory di lavoro corrente.

## <a name="command-line-options"></a>Opzioni della riga di comando

Questa sezione illustra in dettaglio le opzioni della riga di comando disponibili all'avvio di azzurrite.

### <a name="help"></a>Guida

**Facoltativo** : ottenere la guida della riga di comando usando `-h` l' `--help` opzione o.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Host in ascolto BLOB

**Facoltativo** : per impostazione predefinita, azzurrite resterà in ascolto di 127.0.0.1 come server locale. Usare l' `--blobHost` opzione per impostare l'indirizzo ai propri requisiti.

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

### <a name="blob-listening-port-configuration"></a>Configurazione della porta di ascolto BLOB

**Facoltativo** : per impostazione predefinita, azzurrite resterà in ascolto del servizio BLOB sulla porta 10000. Usare l' `--blobPort` opzione per specificare la porta di ascolto richiesta.

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

### <a name="queue-listening-host"></a>Host in ascolto coda

**Facoltativo** : per impostazione predefinita, azzurrite resterà in ascolto di 127.0.0.1 come server locale. Usare l' `--queueHost` opzione per impostare l'indirizzo ai propri requisiti.

Accetta solo le richieste sul computer locale:

```console
azurite --queueHost 127.0.0.1
```

Consenti richieste remote:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> Consentire le richieste remote può rendere il sistema vulnerabile agli attacchi esterni.

### <a name="queue-listening-port-configuration"></a>Configurazione della porta di ascolto della coda

**Facoltativo** : per impostazione predefinita, azzurrite resterà in ascolto della servizio di Accodamento sulla porta 10001. Usare l' `--queuePort` opzione per specificare la porta di ascolto richiesta.

> [!NOTE]
> Dopo aver usato una porta personalizzata, è necessario aggiornare la stringa di connessione o la configurazione corrispondente negli strumenti o negli SDK di archiviazione di Azure.

Personalizzare la porta di attesa Servizio di accodamento:

```console
azurite --queuePort 8888
```

Consentire al sistema di selezionare automaticamente una porta disponibile:

```console
azurite --queuePort 0
```

La porta in uso viene visualizzata durante l'avvio di azzurrite.

### <a name="workspace-path"></a>Percorso area di lavoro

**Facoltativo** : azzurrite archivia i dati nel disco locale durante l'esecuzione. Utilizzare l' `-l` opzione `--location` o per specificare un percorso come percorso dell'area di lavoro. Per impostazione predefinita, verrà usata la directory di lavoro del processo corrente. Si noti il carattere minuscolo "l".

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>Log di accesso

**Facoltativo** : per impostazione predefinita, il log di accesso viene visualizzato nella finestra della console. Disabilitare la visualizzazione del log di accesso utilizzando l' `-s` opzione o `--silent` .

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>Log di debug

**Facoltativo** : il log di debug include informazioni dettagliate su ogni richiesta e analisi dello stack di eccezioni. Abilitare il log di debug fornendo un percorso di file locale valido per `-d` l' `--debug` opzione o.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>Modalità Loose

**Facoltativo** : per impostazione predefinita, azzurrite applica la modalità Strict per bloccare le intestazioni di richiesta e i parametri non supportati. Disabilitare la modalità Strict utilizzando l' `-L` opzione `--loose` o. Prendere nota del capitale "L".

```console
azurite -L
azurite --loose
```
### <a name="version"></a>Versione

**Facoltativo** : consente di visualizzare il numero di versione di azzurrite `-v` installato `--version` utilizzando l'opzione o.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>Configurazione del certificato (HTTPS)

**Facoltativo** : per impostazione predefinita, azzurrite usa il protocollo http. Abilitare la modalità HTTPS specificando il `--cert` percorso di un file di certificato di privacy Enhanced Mail (con estensione PEM) o di scambio di [informazioni personali (con estensione pfx](https://docs.microsoft.com/windows-hardware/drivers/install/personal-information-exchange---pfx--files) ) al commutatore.

Quando `--cert` viene fornito per un file PEM, è necessario specificare un'opzione `--key` corrispondente.

```console
azurite --cert path/server.pem --key path/key.pem
```

Quando `--cert` viene fornito per un file PFX, è necessario specificare un'opzione `--pwd` corrispondente.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

Per informazioni dettagliate sulla creazione di file PEM e PFX, vedere la pagina relativa all' [installazione di HTTPS](https://github.com/Azure/Azurite/blob/master/README.md#https-setup).

### <a name="oauth-configuration"></a>Configurazione OAuth

**Facoltativo** : abilitare l'autenticazione OAuth per azzurrite usando l' `--oauth` opzione.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth richiede un endpoint HTTPS. Verificare che HTTPS sia abilitato fornendo `--cert` switch insieme all' `--oauth` opzione.

Azzurrite supporta l'autenticazione di base specificando il `basic` parametro `--oauth` per l'opzione. Azzurrite eseguirà l'autenticazione di base, ad esempio convalidando il bearer token in ingresso, controllando l'autorità emittente, il pubblico e la scadenza. Azzurrite non verificherà la firma del token o le autorizzazioni.

## <a name="authorization-for-tools-and-sdks"></a>Autorizzazione per strumenti e SDK

Connettersi a azzurrite da SDK o strumenti di archiviazione di Azure, ad esempio [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), usando qualsiasi strategia di autenticazione. L'autenticazione è obbligatoria. Azzurrite supporta l'autorizzazione con OAuth, chiave condivisa e firme di accesso condiviso (SAS). Azzurrite supporta anche l'accesso anonimo ai contenitori pubblici.

Se si usano gli Azure SDK, avviare azzurrite con le `--oauth basic and --cert --key/--pwd` opzioni disponibili.

### <a name="well-known-storage-account-and-key"></a>Chiave e account di archiviazione ben noti

Azzurrite accetta lo stesso account e la chiave noti usati dall'emulatore di archiviazione di Azure legacy.

- Nome account:`devstoreaccount1`
- Chiave account:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>Chiavi e account di archiviazione personalizzati

Azzurrite supporta i nomi e le chiavi degli account di archiviazione `AZURITE_ACCOUNTS` personalizzati impostando la variabile di ambiente `account1:key1[:key2];account2:key1[:key2];...`nel formato seguente:.

Ad esempio, usare un account di archiviazione personalizzato con una chiave:

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

In alternativa, usare più account di archiviazione con due chiavi:

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azzurrite aggiorna i nomi e le chiavi degli account personalizzati dalla variabile di ambiente ogni minuto per impostazione predefinita. Con questa funzionalità è possibile ruotare dinamicamente la chiave dell'account o aggiungere nuovi account di archiviazione senza riavviare azzurrite.

> [!NOTE]
> L'account `devstoreaccount1` di archiviazione predefinito è disabilitato quando si impostano gli account di archiviazione personalizzati.

### <a name="connection-strings"></a>Stringhe di connessione

Il modo più semplice per connettersi a azzurrite dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento *UseDevelopmentStorage = true*. Di seguito è riportato un esempio di una stringa di connessione in un file *app. config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>Stringhe di connessione HTTP

È possibile passare le seguenti stringhe di connessione agli [SDK](https://aka.ms/azsdk) o agli strumenti di Azure, ad esempio l'interfaccia della riga di comando di Azure 2,0 o Storage Explorer.

La stringa di connessione completa è:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Per connettersi solo al servizio BLOB, la stringa di connessione è:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

Per connettersi solo al servizio di Accodamento, la stringa di connessione è:

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>Stringhe di connessione HTTPS

La stringa di connessione HTTPS completa è:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Per utilizzare solo il servizio BLOB, la stringa di connessione HTTPS è:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

Per utilizzare solo il servizio di Accodamento, la stringa di connessione HTTPS è:

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Se è stato `dotnet dev-certs` usato per generare il certificato autofirmato, usare la stringa di connessione seguente.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

Aggiornare la stringa di connessione quando si usano gli [account di archiviazione e le chiavi personalizzati](#custom-storage-accounts-and-keys).

Per altre informazioni, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](storage-configure-connection-string.md).

### <a name="azure-sdks"></a>Azure SDK

Per usare azzurrite con gli [SDK di Azure](https://aka.ms/azsdk), usare le opzioni OAuth e https:

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

È quindi possibile creare un'istanza di BlobContainerClient, BlobServiceClient o BlobClient.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Archiviazione code di Azure

È anche possibile creare un'istanza di QueueClient o QueueServiceClient.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

È possibile usare Storage Explorer per visualizzare i dati archiviati in azzurrite.

#### <a name="connect-to-azurite-using-http"></a>Connettersi a azzurrite tramite HTTP

In Storage Explorer connettersi a azzurrite attenendosi alla procedura seguente:

 1. Selezionare **l'icona Aggiungi un account**
 1. Selezionare **Connetti a un emulatore locale**
 1. Fare clic su **Avanti**.
 1. Fare di nuovo clic su **Avanti**
 1. Selezionare **Connetti**

#### <a name="connect-to-azurite-using-https"></a>Connettersi a azzurrite tramite HTTPS

Per impostazione predefinita Storage Explorer non apre un endpoint HTTPS che utilizza un certificato autofirmato. Se si esegue azzurrite con HTTPS, è probabile che si stia usando un certificato autofirmato. In Storage Explorer importare i certificati SSL tramite la finestra di dialogo **modifica** -> **certificati SSL** -> **Importa certificati** .

##### <a name="import-certificate-to-storage-explorer"></a>Importa certificato in Storage Explorer

1. Trovare il certificato nel computer locale.
1. In Storage Explorer passare a **modifica** -> **certificati** -> SSL**Importa certificati** e importare il certificato.

Se non si importa un certificato, viene ricevuto un errore:

`unable to verify the first certificate` o `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>Aggiungere azzurrite tramite la stringa di connessione HTTPS

Seguire questa procedura per aggiungere azzurrite HTTPS a Storage Explorer:

1. Selezionare l' **interruttore di esplorazione**
1. Selezione **& locale collegata**
1. Fare clic con il pulsante destro del mouse su **account di archiviazione** e selezionare **Connetti ad archiviazione di Azure**.
1. Selezionare **Usa stringa di connessione**
1. Selezionare **Avanti**.
1. Immettere un valore nel campo **nome visualizzato** .
1. Immettere la [stringa di connessione HTTPS](#https-connection-strings) della sezione precedente di questo documento
1. Fare clic su **Avanti**.
1. Selezionare **Connetti**

## <a name="workspace-structure"></a>Struttura dell'area di lavoro

È possibile creare i file e le cartelle seguenti nel percorso dell'area di lavoro durante l'inizializzazione di azzurrite.

- `__blobstorage__`-Directory contenente i dati binari salvati in un servizio BLOB azzurrite
- `__queuestorage__`-Directory contenente i dati binari salvati in un servizio di Accodamento azzurrite
- `__azurite_db_blob__.json`-File di metadati del servizio BLOB azzurrite
- `__azurite_db_blob_extent__.json`-File di metadati dell'extent del servizio BLOB azzurrite
- `__azurite_db_queue__.json`-Azzurrite file di metadati del servizio di Accodamento
- `__azurite_db_queue_extent__.json`-File di metadati dell'extent del servizio di Accodamento azzurrite

Per pulire azzurrite, eliminare i file e le cartelle precedenti e riavviare l'emulatore.

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

Azzurrite non supporta un numero elevato di client connessi. Non esiste alcuna garanzia sulle prestazioni. Azzurrite è destinato a scopi di sviluppo e test.

### <a name="error-handling"></a>Gestione degli errori

Azzurrite è allineato alla logica di gestione degli errori di archiviazione di Azure, ma esistono differenze. Ad esempio, i messaggi di errore possono essere diversi, mentre i codici di stato dell'errore sono allineati.

### <a name="ra-grs"></a>RA-GRS

Azzurrite supporta la replica con ridondanza geografica e accesso in lettura (RA-GRS). Per le risorse di archiviazione, accedere al percorso secondario aggiungendo `-secondary` al nome dell'account. Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB usando la replica secondaria di sola lettura in azzurrite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azzurrite è open source

I contributi e i suggerimenti per azzurrite sono benvenuti. Visitare la pagina del [progetto GitHub](https://github.com/Azure/Azurite/projects) azzurrite o i [problemi di GitHub](https://github.com/Azure/Azurite/issues) per le attività cardine e gli elementi di lavoro che vengono monitorati per le funzionalità imminenti e le correzioni di bug. Gli elementi di lavoro dettagliati vengono inoltre rilevati in GitHub.

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'emulatore di archiviazione di Azure per lo sviluppo e il test](storage-use-emulator.md) dei documenti nell'emulatore di archiviazione di Azure legacy, che viene sostituito da azzurrite.
- [Configurare le stringhe di connessione di archiviazione di Azure](storage-configure-connection-string.md) illustra come assemblare una stringa di connessione di archiviazione di Azure valida.
