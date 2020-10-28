---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 203ba2988d817e3e396fd8aec3db00eba9832041
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92323249"
---
## <a name="download-and-install"></a>Download e installazione

#### <a name="windows-install"></a>[Installazione di Windows](#tab/windowsinstall)

> [!NOTE]
> In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso. La prima volta che si esegue questa installazione può essere necessario riavviare Windows.

Per installare l’interfaccia della riga di comando di Voce su Windows, seguire questa procedura:

1. Scaricare l'[archivio zip](https://aka.ms/speech/spx-zips.zip) dell’interfaccia della riga di comando di Voce, quindi estrarlo.
2. Passare alla directory radice `spx-zips` estratta dal download ed estrarre la sottodirectory necessaria (`spx-net471` per .NET Framework 4.7 o `spx-netcore-win-x64` per .NET Core 3.0 su una CPU x64).

Nel prompt dei comandi, passare alla directory in questo percorso, quindi digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

> [!NOTE]
> In Windows, l'interfaccia della riga di comando di Voce può visualizzare solo i tipi di carattere disponibili per il prompt dei comandi nel computer locale.
> Il [terminale Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) supporta tutti i tipi di carattere prodotti in modo interattivo dall'interfaccia della riga di comando di Voce.
> Se l'output viene restituito in un file, è possibile che anche un editor di testo come Blocco note o un Web browser come Microsoft Edge visualizzi tutti i tipi di carattere.

> [!NOTE]
> PowerShell non controlla la directory locale durante la ricerca di un comando. In PowerShell cambiare directory passando alla posizione di `spx` e chiamare lo strumento immettendo `.\spx`.
> Se si aggiunge questa directory al percorso, PowerShell e il prompt dei comandi di Windows troveranno `spx` da qualsiasi directory senza includere il prefisso `.\`.

#### <a name="linux-install"></a>[Installazione di Linux](#tab/linuxinstall)

Per installare l’interfaccia della riga di comando di Voce su Linux su una CPU x64, seguire questa procedura:

1. Installare [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Scaricare l'[archivio zip](https://aka.ms/speech/spx-zips.zip) dell’interfaccia della riga di comando di Voce, quindi estrarlo.
3. Passare alla directory radice `spx-zips` estratta dal download ed estrarre `spx-netcore-30-linux-x64` in una nuova directory `~/spx`.
4. In un terminale digitare i comandi seguenti:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Digitare `spx` per visualizzare la guida per l'interfaccia della riga di comando di Voce.

#### <a name="docker-install"></a>[Installazione di Docker](#tab/dockerinstall)

> [!NOTE]
> È necessario che sia installato <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop per la piattaforma <span class="docon docon-navigate-external x-hidden-focus"></span></a> in uso.

Seguire questa procedura per installare l'interfaccia della riga di comando di Voce in un contenitore Docker:

1. In un nuovo prompt dei comandi o terminale digitare questo comando: `docker pull msftspeech/spx`
2. Digitare il comando seguente. Dovrebbero essere visualizzate informazioni della Guida per l'interfaccia della riga di comando di Voce: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Montare una directory nel contenitore

Lo strumento interfaccia della riga di comando di Voce salva le impostazioni di configurazione come file e carica tali file durante l'esecuzione di qualsiasi comando, ad eccezione dei comandi della Guida.
Quando si usa l'interfaccia della riga di comando di Voce in un contenitore Docker, è necessario montare una directory locale dal contenitore, in modo che il contenitore possa archiviare o trovare le impostazioni di configurazione e anche per consentire allo strumento di leggere o scrivere eventuali file richiesti dal comando, ad esempio file audio di parlato.

In Windows digitare questo comando per creare una directory locale che può essere usata dall'interfaccia della riga di comando di Voce entro il contenitore:

`mkdir c:\spx-data`

In alternativa, in Linux o Mac digitare questo comando in un terminale per creare una directory e visualizzarne il percorso assoluto:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Il percorso assoluto verrà usato quando si chiama l'interfaccia della riga di comando di Voce.

### <a name="run-speech-cli-in-the-container"></a>Eseguire l'interfaccia della riga di comando di Voce nel contenitore

Questa documentazione mostra il comando `spx` dell'interfaccia della riga di comando di Voce usato nelle installazioni non Docker.
Quando si chiama il comando `spx` in un contenitore Docker, è necessario montare una directory nel contenitore nel file system in cui l'interfaccia della riga di comando di Voce può archiviare e trovare valori di configurazione e leggere e scrivere file.
In Windows i comandi inizieranno come segue:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

In Linux o Mac i comandi inizieranno in modo analogo al seguente:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Sostituire `/ABSOLUTE_PATH` con i percorsi assoluti mostrati dal comando `pwd` nella sezione precedente.

Per usare il comando `spx` installato in un contenitore, immettere sempre il comando completo mostrato sopra, seguito dai parametri della richiesta specifica.
Ad esempio, in Windows questo comando configura la chiave:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Non è possibile usare il microfono o l'altoparlante del computer quando si esegue l'interfaccia della riga di comando di Voce in un contenitore Docker.
> Per usare questi dispositivi, passare file audio da e verso l'interfaccia della riga di comando di Voce per la registrazione/riproduzione all'esterno del contenitore Docker.
> Lo strumento interfaccia della riga di comando di Voce può accedere alla directory locale configurata nella procedura precedente.

***

## <a name="create-subscription-config"></a>Creare la configurazione della sottoscrizione

Per iniziare a usare l'interfaccia della riga di comando di Voce, è necessario innanzitutto immettere le informazioni sull’area e la chiave di sottoscrizione a Voce. Per trovare l'identificatore di area, vedere la pagina del [supporto a livello di area](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk). Dopo aver ottenuto l’identificatore dell’area e della chiave di sottoscrizione (ad esempio, `eastus`, `westus`), eseguire i comandi seguenti.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

L'autenticazione della sottoscrizione è ora archiviata per richieste SPX future. Se è necessario rimuovere uno di questi valori archiviati, eseguire `spx config @region --clear` o `spx config @key --clear`.
