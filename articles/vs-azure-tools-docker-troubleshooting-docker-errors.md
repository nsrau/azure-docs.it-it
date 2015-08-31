<properties
   pageTitle="Risoluzione degli errori Docker | Microsoft Azure"
   description="Risoluzione degli errori che si verificano quando si utilizza Visual Studio per creare e distribuire applicazioni web in Docker."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/17/2015"
   ms.author="kempb" />

# Risoluzione dei problemi del Docker

Dopo aver configurato tutte le impostazioni per il contenitore del Docker dell'applicazione, verificare che le impostazioni e i percorsi siano corretti. Visual Studio fornisce un pulsante Convalida nella finestra di dialogo Pubblica per questo scopo.

Questo argomento contiene informazioni per eseguire la diagnosi e la correzione o risolvere i problemi più comuni che si incontrano quando si ospita un'applicazione di Visual Studio nel Docker. A questo argomento verranno aggiunti ulteriori problemi quando si verificheranno.

## Viene visualizzato un messaggio di errore quando si tenta di convalidare la connessione all'host del Docker nella finestra di dialogo Pubblica sito Web

Ecco alcune possibili soluzioni al problema.

- Nella scheda Connessione della finestra di dialogo Pubblica, verificare che l'Url del Server sia corretto e che il carattere finale:<port_number> nell'URL del Server sia la porta su cui il daemon Docker è in ascolto.

- Nella scheda Connessione della finestra di dialogo Pubblica, espandere la sezione Opzioni avanzate Docker e assicurarsi che vengano specificate le opzioni di autenticazione corrette.
  - Se il daemon Docker sul server è configurato per utilizzare la protezione TLS, l'interfaccia della riga di comando del Docker (docker.exe) di Windows cercherà la chiave (key.pem) e il certificato (cert.pem) del client per impostazione predefinita nella cartella <%userprofile%>.docker. Se questi elementi non sono presenti, dovranno essere generati tramite OpenSSL. Per ulteriori informazioni sulla configurazione del Docker per TLS, vedere Protezione del Socket del daemon Docker con HTTPS.

	Per assicurarsi che il Docker esegua correttamente l'autenticazione dal client di Windows al server Linux, è possibile copiare il contenuto della casella di testo Anteprima in una nuova finestra di comando e modificare <command> in "info", come illustrato di seguito:

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    In alternativa alla copia dei file della chiave e del certificato del client nella cartella .docker, è possibile modificare le opzioni di autenticazione aggiungendo i seguenti parametri:

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Verificare che il daemon Docker sul computer host del Docker sia della versione 1.6 o successiva.

## Errore di timeout quando si utilizzano i propri certificati senza certificato client nella cartella Docker

Se si sceglie di utilizzare i propri certificati durante la creazione dell'host Docker in Visual Studio (ovvero si deseleziona la casella di controllo Genera automaticamente certificati Docker nella finestra di dialogo Crea la macchina virtuale in Microsoft Azure), è necessario copiare i file del certificato e della chiave del client (cert.pem e key.pem) nella cartella Docker (<%userprofile%>.docker). In caso contrario, quando si pubblica il progetto, si otterrà un errore di timeout in un'ora e l'operazione di pubblicazione avrà esito negativo.

## PowerShell 3.0 è necessario per la pubblicazione nei contenitori Docker

Se il sistema operativo è Windows 7 o Windows Server 2008, è necessario installare PowerShell 3.0 prima di poter effettuare la pubblicazione nei contenitori Docker. PowerShell 3.0 è incluso in [Windows Management Framework 3.0](https://www.microsoft.com/it-it/download/details.aspx?id=34595). È necessario riavviare il sistema dopo l'installazione.

Come soluzione alternativa, è possibile eseguire l’aggiornamento a Windows 8.1 o Windows 10, che dispone già di PowerShell 3.0.

## La finestra di PowerShell non si chiude automaticamente

Dopo aver creato una VM, talvolta la finestra di PowerShell non si chiude automaticamente. La chiusura di questa finestra chiude anche Visual Studio. Poiché la finestra non influisce sulle funzionalità degli strumenti di Visual Studio o Docker, lasciarla aperta fino al termine del lavoro.

## Domande frequenti

D: come è possibile creare un nuovo computer Linux che supporta Docker in Azure utilizzando gli strumenti di Visual Studio?

R: vedere [Hosting di applicazioni Web in Docker](vs-azure-tools-docker-hosting-web-apps-in-docker) per informazioni su come eseguire questa operazione.

D: quali modelli di progetto Visual Studio sono supportati per la pubblicazione in un contenitore Docker Linux?

R: Visual Studio attualmente supporta l'applicazione Console C# (pacchetto) e i modelli Web di anteprima ASP.NET 5 C#, tra cui:

- Vuota

- API Web

- Applicazione Web.

D: come è possibile pubblicare il progetto Web o console di ASP.NET 5 in Docker utilizzando MSBUILD dalla riga di comando?

R: utilizzare il seguente comando MSBuild:

    msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>

D: come è possibile pubblicare il progetto Web o console di ASP.NET 5 in Docker tramite PowerShell dalla riga di comando?

R: utilizzare il seguente comando PowerShell:

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

D: possiedo un server Linux con Docker installato, come è possibile specificarlo nella finestra di dialogo **Pubblicazione Web**?

R: vedere la sezione **Fornire un host Docker personalizzato** nell'argomento [Hosting di applicazioni Web in Docker](vs-azure-tools-docker-hosting-web-apps-in-docker).

D: utilizzo il mio server Linux personale con Docker installato. Come è possibile generare chiavi e certificati per configurare l'autenticazione utilizzando TLS?

R: un modo consiste nell'utilizzare OpenSSL sul server per generare i certificati e le chiavi necessari per autorità di certificazione, server e client. È possibile quindi utilizzare software di terze parti per stabilire una connessione SSH/SFTP e quindi copiare i certificati nel computer di sviluppo di Windows locale. Per impostazione predefinita, Docker (CLI) tenterà di utilizzare i certificati presenti nella cartella <userprofile>.docker.

Un'altra opzione consiste nello scaricare OpenSSL per Windows e generare i certificati e le chiavi necessari e quindi caricare autorità di certificazione, certificati server e chiavi per il computer Linux. Per ulteriori informazioni su come realizzare una connessione sicura a Docker, vedere [Protezione del socket del daemon Docker con HTTPS](https://docs.docker.com/articles/https/).

<!---HONumber=August15_HO8-->