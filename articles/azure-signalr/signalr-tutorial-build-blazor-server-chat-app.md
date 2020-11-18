---
title: "Esercitazione: Creare un'app di chat Blazor Server - Azure SignalR"
description: In questa esercitazione si apprenderà come creare e modificare un'app Blazor Server con il servizio Azure SignalR
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 16fd15a5939cc6c268a80e88401f05042a206075
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516816"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Esercitazione: Creare un'app di chat Blazor Server

Questa esercitazione illustra come creare e modificare un'app Blazor Server. Si apprenderà come:

> [!div class="checklist"]
> * Creare una chat room semplice con l'app Blazor Server.
> * Modificare i componenti Razor.
> * Usare la gestione degli eventi e l'associazione di dati nei componenti.
> * Eseguire rapidamente la distribuzione nel servizio app di Azure in Visual Studio.
> * Eseguire la migrazione di SignalR locale al servizio Azure SignalR.

## <a name="prerequisites"></a>Prerequisiti
* Installare [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (Versione >= 3.0.100)
* Installare [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (Versione >= 16.3)
> È possibile usare anche la versione Visual Studio 2019 Preview che viene rilasciata con il modello di app Blazor Server più recente destinato alla versione .NET Core più recente.

[Problemi? Segnalarli](https://aka.ms/asrs/qsblazor).

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Creare una chat room locale nell'app Blazor Server

A partire da Visual Studio 2019 versione 16.2.0, il servizio Azure SignalR è un processo di pubblicazione delle app Web incorporato e la gestione delle dipendenze tra l'app Web e il servizio SignalR sarà molto più pratica. È possibile provare a usare SignalR locale nell'ambiente di sviluppo locale e a usare contemporaneamente il servizio Azure SignalR per il servizio app di Azure senza apportare modifiche al codice.

1. Creare un'app di chat Blazor
   
   In Visual Studio scegliere Crea un nuovo progetto -> App Blazor -> (assegnare un nome all'app e scegliere una cartella)-> App Blazor Server. Assicurarsi di aver già installato .NET Core SDK 3.0+ per consentire a Visual Studio di riconoscere correttamente il framework di destinazione.

   [ ![blazor-chat-create](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Oppure eseguire il comando seguente:
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Aggiungere un file `BlazorChatSampleHub.cs` per implementare un `Hub` per chat.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Aggiungere un endpoint per l'hub in `Startup.Configure()`.
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Installare il pacchetto `Microsoft.AspNetCore.SignalR.Client` per usare il client SignalR.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Creare `ChatRoom.razor` nella cartella `Pages` per implementare il client SignalR. Attenersi alla procedura seguente o semplicemente copiare [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor).

   1. Aggiungere il collegamento alla pagina e il riferimento.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Aggiungere il codice al nuovo client SignalR per inviare e ricevere messaggi.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Aggiungere la parte di rendering prima di `@code` per consentire all'interfaccia utente di interagire con il client SignalR.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Aggiornare `NavMenu.razor` per inserire un voce di menu per la chat room in `NavMenuCssClass` come il resto.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Aggiornare `site.css` per ottimizzare le visualizzazioni a bolle dell'area chat. Aggiungere il codice seguente alla fine.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Premere <kbd>F5</kbd> per eseguire l'app. Sarà possibile avviare una chat come nell'esempio di seguito.

   [ ![blazor-chat](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Problemi? Segnalarli](https://aka.ms/asrs/qsblazor).

## <a name="publish-to-azure"></a>Pubblicazione in Azure

   Finora l'app Blazor è stata usata con SignalR locale ma se viene distribuita nel servizio app Azure, è consigliabile usare il [servizio Azure SignalR](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) che consente di incrementare il numero di connessioni SignalR simultanee di un'app Blazor Server. Inoltre, la copertura globale del servizio SignalR e i data center a prestazioni elevate consentono di ridurre in modo significativo la latenza dovuta alla geografia.

> [!IMPORTANT]
> Nell'app Blazor Server gli stati dell'interfaccia utente vengono mantenuti sul lato server, il che significa che in questo caso è necessario che il server sia permanente. Se è presente un solo server app, il server permanente è garantito per impostazione predefinita. Tuttavia, se sono presenti più server app, è possibile che la negoziazione e la connessione del client siano indirizzate a server diversi e che si verifichino errori dell'interfaccia utente nell'app Blazor. Quindi, è necessario abilitare il server permanente come riportato di seguito in `appsettings.json`:
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere `Publish`.

   * Destinazione: Azure
   * Destinazione specifica: sono supportati tutti i tipi del **servizio app di Azure**.
   * Servizio app: creare un nuovo servizio app o selezionare un servizio app esistente.

   [ ![blazor-chat-profile](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Aggiungere una dipendenza del servizio Azure SignalR

   Dopo aver creato il profilo di pubblicazione, è possibile visualizzare un messaggio consigliato in **Dipendenze del servizio**. Fare clic su **Configura** per selezionare il servizio Azure SignalR esistente nel pannello o per creare un nuovo servizio.

   [ ![blazor-chat-dependency](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   La dipendenza del servizio eseguirà le operazioni seguenti per consentire all'app di passare automaticamente al servizio Azure SignalR quando si usa Azure.

   * Aggiornare [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) per usare il servizio Azure SignalR.
   * Aggiungere il riferimento al pacchetto NuGet del servizio Azure SignalR.
   * Aggiornare le proprietà del profilo per salvare le impostazioni della dipendenza.
   * La configurazione dell'archivio segreti dipende dalla scelta effettuata.
   * Aggiungere la configurazione di `appsettings` per fare in modo che l'app sia la destinazione selezionata del servizio Azure SignalR.

   [ ![blazor-chat-dependency-summary](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Pubblicare l'app

   A questo punto è possibile procedere alla pubblicazione. Verrà automaticamente visualizzata la pagina al termine della pubblicazione. 
   > [!NOTE]
   > È possibile che l'app non funzioni immediatamente la prima volta che si visita la pagina a causa della latenza di avvio della distribuzione del servizio app di Azure e provare ad aggiornare la pagina può contribuire al ritardo.
   > Inoltre, è possibile usare la modalità debugger del browser con <kbd>F12</kbd> per verificare che il traffico sia già stato reindirizzato al servizio Azure SignalR.

   [ ![blazor-chat-azure](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Problemi? Segnalarli](https://aka.ms/asrs/qsblazor).

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Ulteriore argomento: Abilitare il servizio Azure SignalR nello sviluppo locale

1. Aggiungere un riferimento ad Azure SignalR SDK

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Aggiungere una chiamata al servizio Azure SignalR in `Startup.ConfigureServices()`.

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Configurare il servizio Azure SignalR `ConnectionString` in `appsetting.json` o con lo strumento [Secret Manager](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager)

> [!NOTE]
> Il passaggio 2 può essere sostituito usando [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) in SignalR SDK.
> 
> 1. Aggiungere la configurazione per attivare il servizio Azure SignalR in `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Assegnare l'assembly di avvio dell'hosting per usare Azure SignalR SDK. Modificare `launchSettings.json` e aggiungere una configurazione come riportato di seguito all'interno di `environmentVariables`.
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Problemi? Segnalarli](https://aka.ms/asrs/qsblazor).

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create in questa esercitazione, eliminare il gruppo di risorse usando il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una chat room semplice con l'app Blazor Server.
> * Modificare i componenti Razor.
> * Usare la gestione degli eventi e l'associazione di dati nei componenti.
> * Eseguire rapidamente la distribuzione nel servizio app di Azure in Visual Studio.
> * Eseguire la migrazione di SignalR locale al servizio Azure SignalR.

Leggere altre informazioni sulla disponibilità elevata.
> [!div class="nextstepaction"]
> [Resilienza e ripristino di emergenza](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [ASP.NET Core Blazor](/aspnet/core/blazor)
