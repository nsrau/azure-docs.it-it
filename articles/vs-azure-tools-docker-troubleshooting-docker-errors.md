<properties
   pageTitle="Risoluzione dei problemi client Docker in Windows con Visual Studio | Microsoft Azure"
   description="Risoluzione dei problemi che si verificano quando si usa Visual Studio per creare e distribuire app Web in Docker su Windows mediante Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />  
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />  

# Risoluzione dei problemi di sviluppo di Docker in Visual Studio

Quando si usa l'anteprima degli Strumenti di Visual Studio per Docker, si possono verificare alcuni problemi a causa della natura dell'anteprima. Di seguito vengono riportati alcuni problemi comuni e le soluzioni relative.


## Non è possibile convalidare il mapping del volume
Il mapping del volume è necessario per condividere il codice sorgente e i file binari dell'applicazione con la cartella dell'app nel contenitore. Mapping del volume specifici sono contenuti all'interno dei file docker-compose.dev.debug.yml e docker-compose.dev.release.yml. Dal momento che i file vengono modificati nel computer host, i contenitori riflettono queste modifiche in una struttura di cartelle simile.

Per abilitare il mapping del volume, aprire **Settings** (Impostazioni) dall'icona con la balena di Docker per Windows nell'area di notifica e quindi selezionare la scheda **Shared Drives** (Unità condivise). Assicurarsi che la lettera dell'unità che ospita il progetto e la lettera dell'unità in cui si trova %USERPROFILE% siano condivise selezionandole e quindi facendo clic su **Apply** (Applica).

Per verificare il corretto funzionamento del mapping del volume dopo aver condiviso le unità, selezionare Ricompila e premere F5 da Visual Studio oppure provare l'operazione seguente da un prompt dei comandi:

*In un prompt dei comandi di Windows*

*[Nota: presuppone che la cartella Utenti si trovi nell'unità "C" e che sia stata condivisa. Se è stata condivisa un'unità diversa, aggiornare in base alle esigenze]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Nel contenitore Linux*

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

**Nota:** *quando si usano le macchine virtuali Linux, il file system del contenitore fa distinzione tra maiuscole e minuscole.*

##Compilazione: errore imprevisto dell'attività "PrepareForBuild".

Microsoft.DotNet.Docker.CommandLine.ClientException: si è verificato un errore nel tentativo di connessione:

Verificare che l'host Docker predefinito sia in esecuzione. Aprire un prompt dei comandi ed eseguire questo comando:

```
docker info
```

Se viene restituito un errore, provare ad avviare l'applicazione desktop **Docker per Windows**. Se l'applicazione desktop è in esecuzione, l'icona della **balena** nell'area di notifica dovrebbe essere visibile. Fare clic con il pulsante destro del mouse sull'icona nell'area di notifica e scegliere **Settings** (Impostazioni). Fare clic sulla scheda **Reset** (Reimposta) e quindi su **Restart Docker** (Riavvia Docker).

##Aggiornamento manuale dalla versione 0.31 alla versione 0.40


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
1. Rimuovere le righe seguenti dal file Properties\\launchSettings.json:

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

1. Disinstallare la versione precedente e installare Docker Tools 0.40, quindi selezionare di nuovo **Add->Docker Support** (Aggiungi->Supporto per Docker) dal menu di scelta rapida per l'applicazione console o Web ASP.Net Core. I nuovi elementi Docker richiesti verranno aggiunti nuovamente al progetto.

## Verrà visualizzata una finestra di dialogo di errore quando si seleziona **Add->Docker Support** o si prova a eseguire il debug (F5) per un'applicazione ASP.NET Core in un contenitore

In alcuni casi si è osservato che la cache MEF (Managed Extensibility Framework) di Visual Studio può risultare danneggiata dopo la disinstallazione e l'installazione delle estensioni. In questo caso possono essere visualizzate diverse finestre di dialogo di errore quando si aggiunge il supporto per Docker e/o si prova a eseguire l'applicazione ASP.NET Core o il relativo debug (F5). Come soluzione temporanea, eseguire questa procedura per eliminare e rigenerare la cache MEF.

1. Chiudere tutte le istanze di Visual Studio
1. Aprire %USERPROFILE%\\AppData\\Local\\Microsoft\\VisualStudio\\14.0\\
1. Eliminare le cartelle seguenti
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Aprire Visual Studio.
1. Provare a eseguire di nuovo lo scenario

<!---HONumber=AcomDC_0921_2016-->