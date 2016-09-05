<properties
   pageTitle="Risoluzione dei problemi client Docker in Windows con Visual Studio | Microsoft Azure"
   description="Risoluzione dei problemi che si verificano quando si usa Visual Studio per creare e distribuire app Web in Docker su Windows mediante Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/18/2016"
   ms.author="allclark" />  

# Risoluzione dei problemi di sviluppo di Docker in Visual Studio

Quando si usa l'anteprima degli Strumenti di Visual Studio per Docker, si possono verificare alcuni problemi a causa della natura dell'anteprima. Di seguito vengono riportati alcuni problemi comuni e le soluzioni relative.

##Configurazione di Program.cs per il supporto di Docker non riuscita

Quando si aggiunge il supporto di Docker, `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS"))` deve essere aggiunto a WebHostBuilder(). Se non è stata trovata la funzione `Main()` o una nuova classe WebHostBuilder in `Program.cs`, viene visualizzato un avviso. `.UseUrls()` è necessario per abilitare Kestrel all'ascolto del traffico in ingresso, oltre il localhost, quando l'esecuzione avviene in un contenitore Docker. Al termine, il codice ha un aspetto simile al seguente:

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() configured the WebHost to listen to incoming URL traffic. [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS) configura la variabile di ambiente in modalità dockerfile.debug/release come indicato di seguito:

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## Mapping del volume non funzionante
Per abilitare la funzionalità di modifica e aggiornamento, il mapping del volume viene configurato per condividere il codice sorgente del progetto nella cartella .app all'interno del contenitore. Dal momento che i file vengono modificati nel computer host, viene usata la stessa directory /app dei contenitori. In docker-compose.debug.yml la configurazione seguente abilita il mapping del volume

```
volumes:
    - ..:/app
```

Per verificare se il mapping del volume funziona, provare a eseguire il comando seguente:

**Da Windows**

```
docker run -it -v /c/Users/Public:/wormhole busybox
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
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

> [AZURE.NOTE] Quando si usano le macchine virtuali Linux, il file system del contenitore fa distinzione tra maiuscole e minuscole.

Se non si riesce a visualizzare il contenuto, procedere come segue:

**Docker per la versione beta di Windows**
- Verificare che Docker per l'app desktop di Windows sia in esecuzione controllando la presenza dell'icona `moby` nell'area di notifica e verificando che sia bianca e attiva.
- Verificare che il mapping del volume sia configurato facendo clic con il pulsante destro del mouse sull'icona `moby` nell'area di notifica, scegliendo le impostazioni e facendo clic su **Manage shared drives** (Gestisci unità condivise).

**Casella degli strumenti di Docker con VirtualBox**

Per impostazione predefinita, VirtualBox condivide `C:\Users` come `c:/Users`. Se possibile, spostare il progetto sotto questa directory. In alternativa, aggiungerlo manualmente alle [cartelle condivise](https://www.virtualbox.org/manual/ch04.html#sharedfolders) di VirtualBox.
	
##Compilazione: non è stato possibile compilare l'immagine. Errore durante il controllo della connessione TLS: l'host non è in esecuzione.

- Verificare che l'host Docker predefinito sia in esecuzione. Vedere l'articolo [Configurare un Host Docker con VirtualBox](./vs-azure-tools-docker-setup.md).

##Uso di Microsoft Edge come browser predefinito.

Se si usa il browser Microsoft Edge, è possibile che il sito non si apra perché Edge considera l'indirizzo IP non sicuro. Per risolvere il problema, eseguire questa procedura:

1. Passare a **Opzioni Internet**.
    - In Windows 10 è possibile digitare `Internet Options` nella casella Esegui di Windows.
    - In Internet Explorer è possibile scegliere **Opzioni Internet** dal menu **Impostazioni**.
1. Selezionare **Opzioni Internet** quando viene visualizzata l'opzione.
1. Selezionare la scheda **Sicurezza**.
1. Selezionare l'area **Intranet locale**.
1. Selezionare **Siti**.
1. Aggiungere l'IP della macchina virtuale (in questo caso, l'host Docker) nell'elenco.
1. Aggiornare la pagina in Edge per visualizzare il sito come operativo.
1. Per altre informazioni su questo problema, vedere il post nel blog di Scott Hanselman [Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx) (Impossibilità di aprire o visualizzare in Microsoft Edge i siti Web ospitati in VirtualBox).

##Risoluzione dei problemi relativi alla versione 0.15 o versioni precedenti


###Quando si esegue l'app, PowerShell si apre, visualizza un errore e poi si chiude. La pagina del browser non si apre.

Il problema di apertura del browser potrebbe essere causato da un errore durante `docker-compose-up`. Per visualizzare l'errore, eseguire questi passaggi:

1. Aprire il file `Properties\launchSettings.json`.
1. Individuare la voce Docker.
1. Individuare la riga che inizia come segue:

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. Aggiungere il parametro `-noexit` in modo che la riga sia simile alla seguente. Questo codice tiene aperto PowerShell ed è possibile visualizzare l'errore.

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0824_2016-->