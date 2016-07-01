<properties
   pageTitle="Risoluzione dei problemi client Docker in Windows con Visual Studio | Microsoft Azure"
   description="Risoluzione dei problemi che si verificano quando si usa Visual Studio per creare e distribuire app Web in Docker su Windows mediante Visual Studio."
   services="visual-studio-online"
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
   ms.date="06/08/2016"
   ms.author="allclark" />

# Risoluzione dei problemi di sviluppo di Docker in Visual Studio

Quando si usa l'anteprima degli Strumenti di Visual Studio per Docker, si possono verificare alcuni problemi a causa della natura dell'anteprima. Di seguito vengono riportati alcuni problemi comuni e le soluzioni relative.

##Configurazione di Program.cs per il supporto di Docker non riuscita

Quando si aggiunge il supporto di Docker, `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS"))` deve essere aggiunto a WebHostBuilder(). Se per Program.cs non è stata trovata la funzione Main () o una nuova classe WebHostBuilder, viene visualizzato un avviso. UseUrls() è necessario per abilitare Kestrel ad ascoltare il traffico in ingresso, dopo il localhost, quando l'esecuzione avviene in un contenitore Docker. Al termine, il codice avrà un aspetto simile al seguente:

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() ha configurato WebHost per l’ascolto del traffico URL in ingresso. [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS) configurerà la variabile di ambiente in modalità dockerfile.debug/release come indicato di seguito:

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## Mapping del volume non funzionante
Per abilitare la funzionalità di modifica e aggiornamento, il mapping del volume viene configurato per condividere il codice sorgente del progetto nella cartella .app all'interno del contenitore. Dal momento che i file vengono modificati nel computer host, viene usata la stessa directory per i contenitori e l'app. In docker-compose.debug.yml la configurazione seguente abilita il mapping di volume:

```
    volumes:
      - ..:/app
```

Per verificare se il mapping del volume funziona, provare a eseguire il comando seguente:

**Da Windows**

```
docker run -it -v /c/Users/Public:/wormhole busybox
cd wormhole
/ # ls
```

Verrà visualizzato un elenco di cartelle dalla directory Users/Public. Se non vengono visualizzati file e la cartella in /c/Users/Public non è vuota, il mapping del volume non è configurato correttamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Apportare modifiche nella directory del tunnel spaziale per visualizzare il contenuto della directory `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

**Nota:** *quando si usano le macchine virtuali Linux, il file system del contenitore distingue tra maiuscole e minuscole.*

Se non si riesce a visualizzare il contenuto, procedere come segue:

**Docker per la versione beta di Windows**
- Verificare che Docker per l'applicazione desktop di Windows sia in esecuzione controllando la presenza dell'icona del dispositivo mobile nella barra delle applicazioni e verificare che sia bianca e attiva.
- Verificare che il mapping del volume sia configurato facendo clic con il pulsante destro del mouse sull’icona del dispositivo mobile nella barra delle applicazioni, selezionando le impostazioni e facendo clic su **Gestione unità condivise...**

**Casella degli strumenti di Docker con VirtualBox**

Per impostazione predefinita, VirtualBox condivide `C:\Users` come `c:/Users`. Se possibile, spostare il progetto sotto questa directory. In alternativa, aggiungerlo manualmente alle [cartelle condivise](https://www.virtualbox.org/manual/ch04.html#sharedfolders) di VirtualBox.
	
##Compilazione: non è possibile compilare l'immagine. Errore durante il controllo della connessione TLS: l'host non è in esecuzione.

- Verificare che l'host Docker predefinito sia in esecuzione. Vedere l’articolo [Configurare un host Docker con VirtualBox](./vs-azure-tools-docker-setup.md).

##Uso di Microsoft Edge come browser predefinito.

Se si usa il browser Microsoft Edge, è possibile che il sito non si apra perché Edge considera l'indirizzo IP non sicuro. Per risolvere il problema, eseguire questa procedura:
1. Nella casella Esegui di Windows digitare `Internet Options`.
2. Selezionare **Opzioni Internet** quando viene visualizzato. 
2. Selezionare la scheda **Sicurezza**.
3. Selezionare l'area **Intranet locale**.
4. Selezionare **Siti**. 
5. Aggiungere l'IP della macchina virtuale (in questo caso, l'host Docker) nell'elenco. 
6. Aggiornare la pagina in Edge per visualizzare il sito come operativo. 
7. Per altre informazioni su questo problema, vedere il post nel blog di Scott Hanselman relativo all'[impossibilità di aprire o visualizzare in Microsoft Edge i siti Web ospitati in VirtualBox](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx). 

##Risoluzione dei problemi relativi alla versione 0.15 o versioni precedenti


###Quando si esegue l'app, PowerShell si apre, visualizza un errore e poi si chiude. La pagina del browser non si apre.

Ciò potrebbe essere dovuto a un errore durante l'esecuzione di `docker-compose-up`. Per visualizzare l'errore, eseguire questi passaggi:

1. Aprire il file `Properties\launchSettings.json`
1. Individuare la voce Docker.
1. Individuare la riga che inizia come segue:

    "commandLineArgs": "-ExecutionPolicy RemoteSigned …"
	
1. Aggiungere il parametro `-noexit` in modo che la riga ora sia simile alla seguente. In questo modo PowerShell rimarrà aperto e sarà possibile visualizzare l'errore.

	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …"

<!---HONumber=AcomDC_0608_2016-->
