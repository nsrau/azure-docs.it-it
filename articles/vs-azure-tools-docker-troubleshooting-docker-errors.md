---
title: Risoluzione degli errori dei client Docker in Windows con Visual Studio | Documentazione Microsoft
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
ms.sourcegitcommit: 01c10d04606e15082c8842ad87f617703a00c903
ms.openlocfilehash: cbb376dae88d39e965838de74d90158691b59f90


---

# <a name="troubleshooting-visual-studio-docker-development"></a>Risoluzione dei problemi di sviluppo di Docker in Visual Studio

Quando si usa l'anteprima degli Strumenti di Visual Studio per Docker, si possono verificare alcuni problemi a causa della natura dell'anteprima.
Di seguito vengono riportati alcuni problemi comuni e le soluzioni relative.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Contenitori Linux**

###  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Si verificano errori di compilazione durante il debug di un'applicazione console o Web .NET Core.  

Questo errore può verificarsi quando l'unità in cui si trova il progetto non viene condivisa con Docker per Windows.  È possibile che venga visualizzato un errore simile al seguente:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Per risolverlo, fare clic con il pulsante destro del mouse sull'icona di Docker per Windows nell'area di notifica e scegliere "Impostazioni".  Fare clic sulla scheda "Shared Drives" (Unità condivise) e condividere la lettera di unità in cui si trova il progetto.

### <a name="windows-containers"></a>**Contenitori Windows**

Di seguito sono elencate le possibili soluzioni a errori che possono verificarsi durante il debug di applicazioni console e Web .NET Framework in contenitori Windows.

### <a name="pre-requisites"></a>Prerequisiti

1. Visual Studio 2017 RC (o versione successiva) con .NET Core e il carico di lavoro Docker (anteprima) installato.
2. Windows 10 Anniversary Update con le più recenti patch di aggiornamento di Windows installate.
3. Docker per Windows (Beta) - https://docs.docker.com/docker-for-windows/ (Versione 1.12.2-beta28 7813 o versione successiva).
4. Dall'icona di Docker per Windows nell'area di notifica selezionare "Switch to Windows containers" (Passa ai contenitori Windows).  Dopo aver riavviato il computer, verificare che questa impostazione sia stata mantenuta.

### <a name="clicking-docker-debug-project-results-in-the-error--client-version-122-is-too-old--minimum-supported-api-version-is-124-please-upgrade-your-client-to-a-newer-version"></a>Facendo clic su **Docker: Esegui il debug del progetto** viene visualizzato l'errore "client version 1.22 is too old.  Minimum supported API version is 1.24, please upgrade your client to a newer version" (La versione client 1.22 è troppo vecchia. La versione minima supportata dell'API è 1.24. Aggiornare il client a una versione più recente).

La versione 1.13-RC2-beta31 (9123) o successiva di Docker per Windows richiede l'uso della versione "2.1" di Docker Compose.   Per risolvere questo problema, modificare tutte le occorrenze della versione "2" nei file docker-compose*.yml del progetto. I modelli predefiniti che Visual Studio aggiunge al progetto verranno aggiornati in una futura release degli strumenti. 

### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>L'output della console non viene visualizzato nella finestra di output di Visual Studio durante il debug di un'applicazione console.

Si tratta di un problema noto con il Debugger di Visual Studio (msvsmon.exe), attualmente non progettato per questo scenario.  Questo tipo di supporto verrà fornito in una release futura.  Per visualizzare l'output dell'applicazione console in Visual Studio, usare "Docker: Avvia progetto", che equivale a "Avvia senza eseguire debug".

### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>Il debug di applicazioni Web con la configurazione di rilascio ha esito negativo con l'errore&403; - Accesso negato.

Per risolvere il problema, aprire il file web.release.config nella soluzione ed eliminare o impostare come commento le righe seguenti:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

### <a name="when-switching-to-windows-containers-you-could-potentially-see-an-error-dialog-stating-error-response-from-daemon-io-timeout"></a>Quando si passa ai contenitori Windows, è possibile che venga visualizzata una finestra di dialogo con il messaggio di errore seguente: "Error response from daemon: i/o timeout" (Risposta di errore da daemon: timeout di I/O).

Questo problema di Docker per Windows è analizzato nell'articolo disponibile all'indirizzo: https://github.com/docker/for-win/issues/178.


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contenitori Linux**

### <a name="unable-to-validate-volume-mapping"></a>Non è possibile convalidare il mapping del volume
Il mapping del volume è necessario per condividere il codice sorgente e i file binari dell'applicazione con la cartella dell'app nel contenitore.  Mapping del volume specifici sono contenuti all'interno dei file docker-compose.dev.debug.yml e docker-compose.dev.release.yml. Dal momento che i file vengono modificati nel computer host, i contenitori riflettono queste modifiche in una struttura di cartelle simile.

Per abilitare il mapping del volume, aprire **Settings** (Impostazioni) dall'icona con la balena di Docker per Windows nell'area di notifica e quindi selezionare la scheda **Shared Drives** (Unità condivise).  Assicurarsi che la lettera dell'unità che ospita il progetto e la lettera dell'unità in cui si trova %USERPROFILE% siano condivise selezionandole e quindi facendo clic su **Applica**.

Per verificare il corretto funzionamento del mapping del volume dopo aver condiviso le unità, selezionare Ricompila e premere F5 da Visual Studio oppure provare l'operazione seguente da un prompt dei comandi:

*In un prompt dei comandi di Windows*

> [!Note]
> Questo esempio presuppone che la cartella Utenti si trovi nell'unità "C" e che sia stata condivisa.
> Se è stata condivisa un'unità diversa, aggiornare di conseguenza.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Nel contenitore Linux*

```
/ # ls
```

Verrà visualizzato un elenco di cartelle dalla directory Users/Public.
Se non vengono visualizzati file e la cartella in /c/Users/Public non è vuota, il mapping del volume non è configurato correttamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Passare alla directory wormhole per visualizzare il contenuto della directory `/c/Users/Public` :

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!Note]
> Quando si usano macchine virtuali Linux, il file system del contenitore fa distinzione tra maiuscole e minuscole.

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Compilazione: errore imprevisto dell'attività "PrepareForBuild".

Microsoft.DotNet.Docker.CommandLine.ClientException: si è verificato un errore nel tentativo di connessione:

Verificare che l'host Docker predefinito sia in esecuzione. Aprire un prompt dei comandi ed eseguire questo comando:

```
docker info
```

Se viene restituito un errore, provare ad avviare l'applicazione desktop **Docker per Windows** .  Se l'applicazione desktop è in esecuzione, l'icona della **balena** nell'area di notifica deve essere visibile. Fare clic con il pulsante destro del mouse sull'icona nell'area di notifica e scegliere **Impostazioni**.  Fare clic sulla scheda **Reimposta** e quindi su **Restart Docker** (Riavvia Docker).

##<a name="manually-upgrading-from-version-031-to-040"></a>Aggiornamento manuale dalla versione 0.31 alla versione 0.40


1. Eseguire il backup del progetto
1. Eliminare i file seguenti dal progetto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Chiudere la soluzione e rimuovere le righe seguenti dal file con estensione xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Riaprire la soluzione
1. Rimuovere le righe seguenti dal file Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Rimuovere i file seguenti relativi a Docker da project.json in publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Disinstallare la versione precedente e installare Docker Tools 0.40, quindi selezionare di nuovo **Add->Docker Support** (Aggiungi->Supporto per Docker) dal menu di scelta rapida per l'applicazione console o Web ASP.Net Core. I nuovi elementi Docker richiesti vengono nuovamente aggiunti al progetto.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Viene visualizzato un messaggio di errore quando si tenta di usare **Add->Docker Support** (Aggiungi->Supporto per Docker) o Debug (F5) per un'applicazione ASP.NET Core in un contenitore

In alcuni casi si è osservato che la cache MEF (Managed Extensibility Framework) di Visual Studio può risultare danneggiata dopo la disinstallazione e l'installazione delle estensioni. In questo caso possono essere visualizzate diverse finestre di dialogo di errore quando si aggiunge il supporto per Docker e/o si prova a eseguire l'applicazione ASP.NET Core o il relativo debug (F5). Come soluzione temporanea, eseguire questa procedura per eliminare e rigenerare la cache MEF.

1. Chiudere tutte le istanze di Visual Studio
1. Aprire %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Eliminare le cartelle seguenti
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Aprire Visual Studio.
1. Provare a eseguire di nuovo lo scenario



<!--HONumber=Dec16_HO2-->


