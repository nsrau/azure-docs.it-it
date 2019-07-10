---
title: Argomento di avvio rapido per imparare a usare il Servizio Azure SignalR con ASP.NET
description: Argomento di avvio rapido sull'uso del Servizio Azure SignalR per la creazione di una chat room con il framework ASP.NET.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: 2020ee02d236ca13431adb736d9f48171d33b4f3
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147426"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Guida introduttiva: Creare una chat room con ASP.NET e il Servizio SignalR

Il Servizio Azure SignalR si basa su [SignalR per ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction), che **non** è compatibile al 100% con ASP.NET SignalR. Il Servizio Azure SignalR ha reimplementato il protocollo dati ASP.NET SignalR sulla base delle tecnologie ASP.NET Core più recenti. Quando si usa il Servizio Azure SignalR per ASP.NET SignalR, alcune funzionalità di ASP.NET SignalR non sono più supportate. Ad esempio, Azure SignalR non esegue la riproduzione dei messaggi quando si riconnette il client. Inoltre, il trasporto Forever Frame e JSONP non sono supportati. Per il funzionamento dell'applicazione ASP.NET SignalR con il Servizio SignalR sono necessarie alcune modifiche del codice e la versione corretta delle librerie dipendenti. 

Vedere il [documento sulle differenze tra le versioni](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) per un elenco completo delle funzionalità ASP.NET SignalR e ASP.NET Core SignalR messe a confronto.

In questo argomento di avvio rapido verranno fornite informazioni su come iniziare a usare ASP.NET e il Servizio Azure SignalR per un'[applicazione Chat Room](./signalr-quickstart-dotnet-core.md) simile.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com/) con il proprio account Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

La modalità *Serverless* non è supportata per le applicazioni ASP.NET SignalR. Usare sempre la modalità *Predefinita* oppure *Classica* per l'istanza del Servizio Azure SignalR.

È anche possibile creare risorse di Azure usate in questo argomento di avvio rapido con lo script [Creare un Servizio SignalR](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Mentre il servizio è in distribuzione, è possibile passare all'utilizzo del codice. Clonare l'[app di esempio da GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), impostare la stringa di connessione del servizio SignalR ed eseguire localmente l'applicazione.

1. Aprire una finestra del terminale Git. Passare a una cartella in cui si vuole clonare il progetto di esempio.

1. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>Configurare ed eseguire l'app Web Chat Room

1. Avviare Visual Studio e aprire la soluzione nella cartella *aspnet-samples/ChatRoom/* del repository clonato.

1. Nel browser in cui viene aperto il portale di Azure individuare e selezionare l'istanza creata.

1. Selezionare **Chiavi** per visualizzare le stringhe di connessione per l'istanza del servizio SignalR.

1. Selezionare e copiare la stringa di connessione primaria.

1. Impostare ora la stringa di connessione nel file web.config.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. In *Startup.cs* invece di chiamare `MapSignalR()` è necessario chiamare `MapAzureSignalR({your_applicationName})` e passare la stringa di connessione in modo che l'applicazione si connetta al servizio anziché ospitare SignalR autonomamente. Sostituire `{YourApplicationName}` con il nome dell'applicazione. Questo è un nome univoco per distinguere l'applicazione dalle altre applicazioni. È possibile usare `this.GetType().FullName` come valore.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    È anche necessario fare riferimento all'SDK per i servizi prima di usare queste API. Aprire **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti** ed eseguire il comando:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Ad eccezione di queste modifiche, tutto il resto rimane invariato.È comunque possibile usare l'interfaccia hub con cui si ha familiarità per scrivere la logica di business.

    > [!NOTE]
    > Nell'implementazione l'SDK del Servizio Azure SignalR espone un endpoint `/signalr/negotiate` per la negoziazione. Restituisce una risposta di negoziazione speciale quando i client provano a connettersi e li reindirizza all'endpoint di servizio definito nella stringa di connessione.

1. Premere **F5** per eseguire il progetto in modalità di debug. È possibile osservare l'esecuzione dell'applicazione in locale. Anziché ospitare un runtime di SignalR nell'applicazione stessa, ora si connette al Servizio Azure SignalR.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
> 
> 

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Per le istruzioni di questa guida introduttiva è stato usato un gruppo di risorse denominato *SignalRTestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

   
![Delete](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata una nuova risorsa del Servizio Azure SignalR, che è stata usata con un'app Web ASP.NET. Imparare quindi a sviluppare applicazioni in tempo reale usando il Servizio Azure SignalR con ASP.NET Core.

> [!div class="nextstepaction"]
> [Servizio Azure SignalR con ASP.NET Core](./signalr-quickstart-dotnet-core.md)
