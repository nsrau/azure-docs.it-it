---
title: Guida di avvio rapido di Configurazione app di Azure con Funzioni di Azure | Microsoft Docs
description: In questa guida di avvio rapido viene creata un'app di Funzioni di Azure con Configurazione app di Azure e C#. Creare e connettersi a un archivio di Configurazione app. Testare la funzione in locale.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: b62bf900e3e7859437b10b7b45801ee0dc575282
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767779"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Avvio rapido: Creare un'app Funzioni di Azure con Configurazione app di Azure

In questa guida di avvio rapido si incorpora il servizio Configurazione app di Azure in un'app Funzioni di Azure per centralizzare l'archiviazione e la gestione di tutte le impostazioni dell'applicazione separatamente dal codice.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con il carico di lavoro **Sviluppo di Azure**.
- [Strumenti di Funzioni di Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selezionare **Esplora configurazioni** >  **+ Crea** > **Coppia chiave-valore** per aggiungere le chiavi chiave-valore seguenti:

    | Chiave | valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

8. Selezionare **Applica**.

## <a name="create-a-functions-app"></a>Creare un'app Funzioni

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Nella scheda **Sfoglia** cercare e aggiungere il pacchetto NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` al progetto. Se non è possibile trovarlo, selezionare la casella di controllo **Includi versione preliminare**.

2. Aprire *Function1.cs* e aggiungere gli spazi dei nomi della configurazione .NET Core al provider di configurazione di Configurazione app.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Aggiungere una proprietà `static` denominata `Configuration` per creare un'istanza singleton di `IConfiguration`. Aggiungere quindi un costruttore `static` per connettersi a Configurazione app chiamando `AddAzureAppConfiguration()`. Questa operazione consentirà di caricare la configurazione una sola volta all'avvio dell'applicazione. La stessa istanza di configurazione verrà usata per tutte le chiamate di Funzioni eseguite successivamente.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Aggiornare il metodo `Run` in modo da leggere i valori dalla configurazione.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testare la funzione in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di Configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Premere F5 per testare la funzione. Se viene visualizzata, accettare la richiesta di Visual Studio di scaricare e installare gli strumenti dell'**interfaccia della riga di comando Azure Functions Core Tools**. Potrebbe essere necessario anche abilitare un'eccezione del firewall per consentire agli strumenti di gestire le richieste HTTP.

3. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Guida introduttiva: debug di funzioni in VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. L'immagine seguente mostra la risposta nel browser alla richiesta GET locale restituita dalla funzione.

    ![Guida introduttiva: avvio della funzione in locale](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato con un'app Funzioni di Azure con il [provider di Configurazione app](https://go.microsoft.com/fwlink/?linkid=2074664). Per informazioni su come configurare l'app Funzioni di Azure per aggiornare in modo dinamico le impostazioni di configurazione, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare la configurazione dinamica](./enable-dynamic-configuration-azure-functions-csharp.md)
