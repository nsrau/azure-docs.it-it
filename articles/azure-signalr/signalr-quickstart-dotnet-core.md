---
title: Guida introduttiva per imparare a usare il servizio Azure SignalR
description: Una guida introduttiva per usare il servizio Azure SignalR per la creazione di una chat room con le app ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: b5fc15815c9843c55bf31efe31e12e2de02d3be3
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874017"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Guida introduttiva: Creare una chat room mediante il servizio SignalR

Il servizio Azure SignalR è un servizio di Azure che consente agli sviluppatori di compilare facilmente applicazioni Web con funzionalità in tempo reale. Questo servizio in origine era basato su [SignalR per ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), ma ora supporta le versioni successive.

Questo articolo illustra come iniziare a usare il servizio Azure SignalR. In questo argomento di avvio rapido verrà creata un'applicazione di chat tramite un'app Web ASP.NET Core MVC. Questa app stabilirà una connessione alla risorsa del servizio Azure SignalR per abilitare gli aggiornamenti dei contenuti in tempo reale. L'applicazione Web sarà ospitata in locale e verrà stabilita la connessione con più client browser. Ogni client sarà in grado di eseguire il push degli aggiornamenti dei contenuti a tutti gli altri client. 

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Una scelta possibile è [Visual Studio Code](https://code.visualstudio.com/), disponibile per le piattaforme Windows, macOS e Linux.

Il codice per questa esercitazione è disponibile per il download nel [repository GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). È inoltre possibile creare le risorse di Azure usate in questo argomento di avvio rapido seguendo le istruzioni riportate nell'argomento relativo allo [script per la creazione di un servizio SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Prerequisiti

* Installare [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Scaricare o clonare il repository GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Creare una risorsa di Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

In questa sezione verrà usata l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) per creare un progetto di app Web ASP.NET Core MVC. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core rispetto a Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux. 

1. Creare una cartella per il progetto. In questo argomento di avvio rapido viene usata la cartella *E:\Testing\chattest*.

2. Nella nuova cartella eseguire il comando seguente per creare il progetto:

    ```dotnetcli
    dotnet new mvc
    ```

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>Aggiungere Secret Manager al progetto

In questa sezione verrà aggiunto lo [strumento Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) al progetto. Lo strumento Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app nel codice sorgente.

1. Aprire il file con estensione *csproj*. Aggiungere un elemento `DotNetCliToolReference` per includere *Microsoft.Extensions.SecretManager.Tools*. Aggiungere anche un elemento `UserSecretsId` come mostrato nel codice seguente per *chattest.csproj* e salvare il file.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Aggiungere Azure SignalR all'app Web

1. Aggiungere un riferimento al pacchetto NuGet `Microsoft.Azure.SignalR` eseguendo il comando seguente:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

    ```dotnetcli
    dotnet restore
    ```

3. Aggiungere un segreto denominato *Azure:SignalR:ConnectionString* a Secret Manager. 

    Questo segreto conterrà la stringa di connessione per accedere alla risorsa del servizio SignalR. *Azure:SignalR:ConnectionString* è la chiave di configurazione predefinita cercata da SignalR per stabilire una connessione. Sostituire il valore nel comando seguente con la stringa di connessione per la propria risorsa del servizio SignalR.

    È necessario eseguire questo comando nella stessa directory del file con estensione *csproj*.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Secret Manager verrà usato solo per il test dell'app Web mentre è ospitata in locale. In un'esercitazione successiva si distribuirà l'app Web di chat in Azure. Dopo la distribuzione dell'app Web in Azure, sarà possibile usare un'impostazione dell'applicazione invece di archiviare la stringa di connessione con Secret Manager.

    È possibile accedere al segreto con l'API di configurazione. Con tale API è possibile usare i due punti (:) nel nome di configurazione in tutte le piattaforme supportate. Vedere la [configurazione in base all'ambiente](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Aprire *Startup.cs* e aggiornare il metodo `ConfigureServices` per usare il servizio Azure SignalR chiamando il metodo `AddSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureSignalR();
    }
    ```

    Non passando un parametro a `AddAzureSignalR()`, questo codice usa la chiave di configurazione predefinita per la stringa di connessione della risorsa del servizio SignalR. La chiave di configurazione predefinita è *Azure:SignalR:ConnectionString*.

5. In *Startup.cs*aggiornare il metodo `Configure` sostituendolo con il codice seguente.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Aggiungere una classe hub

In SignalR un hub è un componente di base che espone un set di metodi che possono essere chiamati dal client. In questa sezione si definisce una classe hub con due metodi:

* `Broadcast`: Questo metodo trasmette un messaggio a tutti i client.
* `Echo`: Questo metodo invia un messaggio al chiamante.

Entrambi i metodi usano l'interfaccia `Clients` fornita da ASP.NET Core SignalR SDK. Questa interfaccia consente di accedere a tutti i client connessi, rendendo possibile il push del contenuto ai client.

1. Nella directory del progetto aggiungere una nuova cartella denominata *Hub*. Aggiungere un nuovo file di codice hub denominato *ChatHub.cs* nella nuova cartella.

2. Aggiungere il codice seguente a *ChatHub.cs* per definire la classe hub e salvare il file.

    Aggiornare lo spazio dei nomi per questa classe se è stato usato un nome di progetto diverso da *SignalR.Mvc*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Aggiungere l'interfaccia client per l'app Web

L'interfaccia utente client per questa app di chat room sarà costituita da HTML e JavaScript in un file denominato *index.html* nella directory *wwwroot*.

Copiare il file *css/site.css* dalla cartella *wwwroot* del [repository degli esempi](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Sostituire il file *css/site.css* del progetto con quello copiato.

Di seguito è riportato il codice principale di *index.html*:

Creare un nuovo file nella directory *wwwroot* denominato *index.html*, quindi copiare e incollare il codice HTML seguente nel file appena creato:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
                document.getElementById('message').addEventListener('keypress', function (event) {
                    if (event.keyCode === 13) {
                        event.preventDefault();
                        document.getElementById('sendmessage').click();
                        return false;
                    }
                });
                document.getElementById('echo').addEventListener('click', function (event) {
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

Il codice in *index.html* chiama `HubConnectionBuilder.build()` per stabilire una connessione HTTP alla risorsa di Azure SignalR.

Se la connessione ha esito positivo, viene passata a `bindConnectionMessage`, che consente di aggiungere i gestori di eventi per i push dei contenuti in ingresso al client. 

`HubConnection.start()` avvia la comunicazione con l'hub. `onConnected()` quindi aggiunge i gestori eventi del pulsante. Questi gestori usano la connessione per consentire a questo client di eseguire il push degli aggiornamenti dei contenuti a tutti i client connessi.

## <a name="add-a-development-runtime-profile"></a>Aggiungere un profilo di runtime di sviluppo

In questa sezione si aggiungerà un ambiente di runtime di sviluppo per ASP.NET Core. Per altre informazioni, vedere [Usare più ambienti in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Creare una cartella denominata *Proprietà* nel progetto.

2. Aggiungere un nuovo file denominato *launchSettings.json* alla cartella, con il contenuto seguente, e salvare il file.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

    ```dotnetcli
    dotnet build
    ```

1. Dopo che la compilazione viene completata correttamente, eseguire questo comando per eseguire l'app Web in locale:

    ```dotnetcli
    dotnet run
    ```

    L'app verrà ospitata in locale sulla porta 5000 come configurato nel profilo di runtime di sviluppo:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Aprire due finestre del browser. In ciascun browser passare a `http://localhost:5000`. Verrà richiesto di immettere il proprio nome. Immettere un nome per entrambi i client e verificare il push del contenuto del messaggio tra i due client usando il pulsante **Invia**.

    ![Esempio di chat di gruppo di Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questo argomento di avvio rapido e riutilizzarle.

Se si è terminato di lavorare con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo argomento di avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e interessa tutte le risorse del gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create in un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dal relativo pannello anziché eliminare il gruppo di risorse.

Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

Nella casella di testo **Filtra per nome** digitare il nome del gruppo di risorse. Per le istruzioni di questa guida introduttiva è stato usato un gruppo di risorse denominato *SignalRTestResources*. Nell'elenco dei risultati del gruppo di risorse selezionare i puntini di sospensione ( **...** ) > **Elimina gruppo di risorse**.

![Selezioni per eliminare un gruppo di risorse](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e selezionare **Elimina**.

Dopo qualche istante, il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una nuova risorsa del servizio Azure SignalR che è stata quindi usata con un'app Web ASP.NET Core per eseguire il push degli aggiornamenti del contenuto in tempo reale a più client connessi. Per altre informazioni sull'uso del servizio Azure SignalR, continuare con l'esercitazione sull'autenticazione.

> [!div class="nextstepaction"]
> [Autenticazione del servizio Azure SignalR](./signalr-concept-authenticate-oauth.md)

[Problemi? Segnalarli](https://aka.ms/asrs/qsnetcore).
