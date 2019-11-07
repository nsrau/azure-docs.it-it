---
title: Guida introduttiva per imparare a usare il servizio Azure SignalR
description: Una guida introduttiva per usare il servizio Azure SignalR per la creazione di una chat room con le app ASP.NET Core MVC.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 022780f2b37c8bed49c81774d443b69bae41e5e7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476749"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Guida introduttiva: Creare una chat room mediante il servizio SignalR


Il servizio Azure SignalR è un servizio di Azure che consente agli sviluppatori di compilare facilmente applicazioni Web con funzionalità in tempo reale. Questo servizio è basato su [SignalR per ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1) ma supporta anche [SignalR per ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0).

Questo articolo illustra come iniziare a usare il servizio Azure SignalR. In questo argomento di avvio rapido verrà creata un'applicazione di chat tramite un'app Web ASP.NET Core MVC. Questa app stabilirà una connessione alla risorsa del servizio Azure SignalR per abilitare gli aggiornamenti dei contenuti in tempo reale. L'applicazione Web sarà ospitata in locale e verrà stabilita la connessione con più client browser. Ogni client sarà in grado di eseguire il push degli aggiornamenti dei contenuti a tutti gli altri client. 

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Una scelta possibile è [Visual Studio Code](https://code.visualstudio.com/), disponibile per le piattaforme Windows, macOS e Linux.

Il codice per questa esercitazione è disponibile per il download nel [repository GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). È inoltre possibile creare le risorse di Azure usate in questo argomento di avvio rapido seguendo le istruzioni riportate nell'argomento relativo allo [script per la creazione di un servizio SignalR](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

* Installare [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Scaricare o clonare il repository GitHub [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Creare una risorsa di Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

In questa sezione verrà usata l'[interfaccia della riga di comando di .NET Core](https://docs.microsoft.com/dotnet/core/tools/) per creare un progetto di app Web ASP.NET Core MVC. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core rispetto a Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux. 

1. Creare una cartella per il progetto. In questo argomento di avvio rapido viene usata la cartella *E:\Testing\chattest*.

2. Nella nuova cartella eseguire il comando seguente per creare il progetto:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Aggiungere Secret Manager al progetto

In questa sezione verrà aggiunto lo [strumento Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) al progetto. Lo strumento Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app nel codice sorgente.

1. Aprire il file con estensione *csproj*. Aggiungere un elemento `DotNetCliToolReference` per includere *Microsoft.Extensions.SecretManager.Tools*. Aggiungere anche un elemento `UserSecretsId` come mostrato nel codice seguente per *chattest.csproj* e salvare il file.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Aggiungere Azure SignalR all'app Web

1. Aggiungere un riferimento al pacchetto NuGet `Microsoft.Azure.SignalR` eseguendo il comando seguente:

        dotnet add package Microsoft.Azure.SignalR

2. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

        dotnet restore

3. Aggiungere un segreto denominato *Azure:SignalR:ConnectionString* a Secret Manager. 

    Questo segreto conterrà la stringa di connessione per accedere alla risorsa del servizio SignalR. *Azure:SignalR:ConnectionString* è la chiave di configurazione predefinita cercata da SignalR per stabilire una connessione. Sostituire il valore nel comando seguente con la stringa di connessione per la propria risorsa del servizio SignalR.

    È necessario eseguire questo comando nella stessa directory del file con estensione *csproj*.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"    
    ```

    Secret Manager verrà usato solo per il test dell'app Web mentre è ospitata in locale. In un'esercitazione successiva si distribuirà l'app Web di chat in Azure. Dopo la distribuzione dell'app Web in Azure, sarà possibile usare un'impostazione dell'applicazione invece di archiviare la stringa di connessione con Secret Manager.

    È possibile accedere al segreto con l'API di configurazione. Con tale API è possibile usare i due punti (:) nel nome di configurazione in tutte le piattaforme supportate. Vedere la [configurazione in base all'ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0). 


4. Aprire *Startup.cs* e aggiornare il metodo `ConfigureServices` per usare il servizio Azure SignalR chiamando il metodo `services.AddSignalR().AddAzureSignalR()` solo per ASP.NET Core 2:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```
    Per ASP.NET Core 3 e versioni successive non è necessaria alcuna modifica per il metodo `ConfigureServices`.

    Non passando un parametro a `AddAzureSignalR()`, questo codice usa la chiave di configurazione predefinita per la stringa di connessione della risorsa del servizio SignalR. La chiave di configurazione predefinita è *Azure:SignalR:ConnectionString*.

5. Sempre in *Startup.cs*, aggiornare il metodo `Configure` sostituendo la chiamata a `app.UseStaticFiles()` con il codice seguente e salvare il file, solo per ASP.NET Core 2.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    Per ASP.NET Core 3 e versioni successive, sostituire il codice precedente con:

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>Aggiungere una classe hub

In SignalR un hub è un componente di base che espone un set di metodi che possono essere chiamati dal client. In questa sezione si definisce una classe hub con due metodi: 

* `Broadcast`: Questo metodo trasmette un messaggio a tutti i client.
* `Echo`: Questo metodo invia un messaggio al chiamante.

Entrambi i metodi usano l'interfaccia `Clients` fornita da ASP.NET Core SignalR SDK. Questa interfaccia consente di accedere a tutti i client connessi, rendendo possibile il push del contenuto ai client.

1. Nella directory del progetto aggiungere una nuova cartella denominata *Hub*. Aggiungere un nuovo file di codice hub denominato *Chat.cs* nella nuova cartella.

2. Aggiungere il codice seguente a *Chat.cs* per definire la classe hub e salvare il file. 

    Aggiornare lo spazio dei nomi per questa classe se è stato usato un nome di progetto diverso da *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Aggiungere l'interfaccia client per l'app Web

L'interfaccia utente client per questa app di chat room sarà costituita da HTML e JavaScript in un file denominato *index.html* nella directory *wwwroot*.

Copiare il file *index.html* e le cartelle *css* e *scripts* dalla cartella *wwwroot* del [repository samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Incollarli nella cartella *wwwroot* del progetto.

Di seguito è riportato il codice principale di *index.html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
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
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

        dotnet build

2. Dopo che la compilazione viene completata correttamente, eseguire questo comando per eseguire l'app Web in locale:

        dotnet run

    L'app verrà ospitata in locale sulla porta 5000 come configurato nel profilo di runtime di sviluppo:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Aprire due finestre del browser. In ciascun browser passare a `http://localhost:5000`. Verrà richiesto di immettere il proprio nome. Immettere un nome per entrambi i client e verificare il push del contenuto del messaggio tra i due client usando il pulsante **Invia**.

    ![Esempio di chat di gruppo di Azure SignalR](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questo argomento di avvio rapido e riutilizzarle.

Se si è terminato di lavorare con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo argomento di avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e interessa tutte le risorse del gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create in un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dal relativo pannello anziché eliminare il gruppo di risorse.
> 
> 

Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

Nella casella di testo **Filtra per nome** digitare il nome del gruppo di risorse. Per le istruzioni di questa guida introduttiva è stato usato un gruppo di risorse denominato *SignalRTestResources*. Nell'elenco dei risultati del gruppo di risorse selezionare i puntini di sospensione ( **...** ) > **Elimina gruppo di risorse**.

   
![Selezioni per eliminare un gruppo di risorse](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e selezionare **Elimina**.
   
Dopo qualche istante, il gruppo di risorse e tutte le risorse che contiene vengono eliminati.



## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una nuova risorsa del servizio Azure SignalR che è stata quindi usata con un'app Web ASP.NET Core per eseguire il push degli aggiornamenti del contenuto in tempo reale a più client connessi. Per altre informazioni sull'uso del servizio Azure SignalR, continuare con l'esercitazione sull'autenticazione.

> [!div class="nextstepaction"]
> [Autenticazione del servizio Azure SignalR](./signalr-concept-authenticate-oauth.md)


