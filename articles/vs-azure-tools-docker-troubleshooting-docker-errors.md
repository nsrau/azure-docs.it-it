---
title: Risoluzione dei problemi client docker in Windows con Visual Studio | Microsoft Docs
description: Risoluzione dei problemi che si verificano quando si usa Visual Studio per creare e distribuire app Web in Docker su Windows mediante Visual Studio.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 649cc1a78f3a9f343533cb18fb7d763e4f9ea196
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.lasthandoff: 02/04/2017


---

# <a name="troubleshoot-visual-studio-docker-development"></a>Risoluzione dei problemi di sviluppo di Docker in Visual Studio

Quando si usa l'anteprima degli Strumenti di Visual Studio per Docker, si possono verificare alcuni problemi a causa della natura dell'anteprima.
Di seguito vengono riportati alcuni problemi comuni e le soluzioni relative.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Contenitori Linux**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Si verificano errori di compilazione durante il debug di un'applicazione console o Web .NET Core  

Questo errore può verificarsi quando l'unità in cui si trova il progetto non viene condivisa con Docker per Windows.  È possibile che venga visualizzato un errore simile al seguente:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Per risolvere il problema:

1. Fare doppio clic su **Docker per Windows** nell'area di notifica e quindi selezionare **Impostazioni**.  
2. Fare clic su **Unità condivise** e condividere l'unità in cui si trova il progetto.

### <a name="windows-containers"></a>**Contenitori Windows**

Di seguito sono elencati i possibili errori che possono verificarsi durante il debug di applicazioni console e Web .NET Framework in contenitori Windows.

#### <a name="prerequisites"></a>Prerequisiti

1. Visual Studio 2017 RC (o versione successiva) con .NET Core e il carico di lavoro Docker (anteprima) installato.
2. Windows 10 Anniversary Update con le patch di Windows Update più recenti. È in particolare necessario installare [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016). 
3. È necessario installare [Docker per Windows](https://docs.docker.com/docker-for-windows/) (build 1.13.0 o versione successiva).
4. Opzione **Switch to Windows containers** (Passa ai contenitori Windows) selezionata. Fare doppio clic su **Docker per Windows** nell'area di notifica e quindi selezionare **Switch to Windows containers** (Passa ai contenitori Windows). Dopo aver riavviato il computer, verificare che questa impostazione sia stata mantenuta.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>L'output della console non viene visualizzato nella finestra di output di Visual Studio durante il debug di un'applicazione console

Si tratta di un problema noto con il debugger di Visual Studio (msvsmon.exe), attualmente non progettato per questo scenario. Il supporto per questo scenario potrà essere incluso in una versione futura. Per visualizzare l'output dell'applicazione console in Visual Studio, usare **Docker: Avvia progetto**, che equivale a **Avvia senza eseguire debug**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Il debug di applicazioni Web con la configurazione di rilascio ha esito negativo con l'errore&403; - Accesso negato

Per risolvere il problema, aprire web.release.config nella soluzione ed eliminare o impostare come commento le righe seguenti:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contenitori Linux**

#### <a name="unable-to-validate-volume-mapping"></a>Non è possibile convalidare il mapping del volume
Il mapping del volume è necessario per condividere il codice sorgente e i file binari dell'applicazione con la cartella dell'app nel contenitore.  Mapping del volume specifici sono contenuti all'interno dei file docker-compose.dev.debug.yml e docker-compose.dev.release.yml. Dal momento che i file vengono modificati nel computer host, i contenitori riflettono queste modifiche in una struttura di cartelle simile.

Per abilitare il mapping del volume:

1. Fare clic su **Moby** nell'area di notifica e selezionare **Impostazioni**.
2. Selezionare **Unità condivise**.
3. Selezionare l'unità che ospita il progetto e l'unità in cui si trova %USERPROFILE%.
4. Fare clic su **Apply**.

Per verificare il corretto funzionamento del mapping del volume, ricompilare e premere F5 da Visual Studio dopo aver condivisio le unità oppure eseguire il codice seguente da un prompt dei comandi.

> [!NOTE]
> Questo esempio presuppone che la cartella Utenti si trovi nell'unità C e che sia stata condivisa.
> Se è stata condivisa un'unità diversa, aggiornare di conseguenza.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Eseguire il codice seguente nel contenitore di Linux.

```
/ # ls
```

Verrà visualizzato un elenco di cartelle dalla directory Users/Public. Se non vengono visualizzati file e la cartella in /c/Users/Public non è vuota, il mapping del volume non è configurato correttamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passare alla directory wormhole per visualizzare il contenuto della directory `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Quando si usano macchine virtuali Linux, il file system del contenitore fa distinzione tra maiuscole e minuscole.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Build: errore imprevisto dell'attività "PrepareForBuild"

Microsoft.DotNet.Docker.CommandLine.ClientException: si è verificato un errore nel tentativo di connessione.

Verificare che l'host Docker predefinito sia in esecuzione. Aprire un prompt dei comandi ed eseguire questo comando:

```
docker info
```

Se viene restituito un errore, provare ad avviare l'applicazione desktop **Docker per Windows** . Se l'applicazione desktop è in esecuzione, l'icona di **Moby** nell'area di notifica dovrebbe essere visibile. Fare doppio clic su **Moby** e aprire **Impostazioni**. Fare clic su **Reimposta** e quindi riavviare Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Viene visualizzato un messaggio di errore quando si tenta di aggiungere il supporto per Docker o di eseguire il debug (F5) di un'applicazione ASP.NET Core in un contenitore

In alcuni casi si è osservato che la cache MEF (Managed Extensibility Framework) di Visual Studio può risultare danneggiata dopo la disinstallazione e l'installazione delle estensioni. In questo caso possono essere visualizzate diversi messaggi di errore quando si aggiunge il supporto per Docker e/o si prova a eseguire l'applicazione ASP.NET Core o il relativo debug (F5). Come soluzione temporanea, eseguire questa procedura per eliminare e rigenerare la cache MEF.

1. Chiudere tutte le istanze di Visual Studio.
1. Aprire %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Eliminare le cartelle seguenti:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Aprire Visual Studio.
1. Provare a eseguire di nuovo lo scenario.

