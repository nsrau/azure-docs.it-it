---
title: Esercitazione per l'uso della configurazione dinamica di Configurazione app di Azure in un'app di Funzioni di Azure | Microsoft Docs
description: Questa esercitazione illustra come aggiornare dinamicamente i dati di configurazione per le app di Funzioni di Azure
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187295"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Esercitazione: Usare la configurazione dinamica in un'app di Funzioni di Azure

Il provider di configurazione .NET Standard di Configurazione app supporta la memorizzazione nella cache e l'aggiornamento della configurazione dinamicamente in base all'attività dell'applicazione. Questa esercitazione mostra come è possibile implementare aggiornamenti dinamici della configurazione nel codice. Si basa sull'app di Funzioni di Azure presentata negli argomenti di avvio rapido. Prima di continuare, completare le procedure descritte in [Creare un'app di Funzioni di Azure con Configurazione app](./quickstart-azure-functions-csharp.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare l'app di Funzioni di Azure per aggiornarne la configurazione in risposta alle modifiche in un archivio di Configurazione app.
> * Inserire la configurazione più recente nelle chiamate di Funzioni di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con il carico di lavoro **Sviluppo di Azure**
- [Strumenti di Funzioni di Azure](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Completare l'argomento di avvio rapido [Creare un'app di Funzioni di Azure con Configurazione app di Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Ricaricare i dati di Configurazione app

1. Aprire *Function1.cs*. Oltre alla proprietà `static` `Configuration`, aggiungere una nuova proprietà `static` `ConfigurationRefresher` per tenere un'istanza singleton di `IConfigurationRefresher` che verrà usata in seguito per segnalare gli aggiornamenti della configurazione durante le chiamate di Funzioni.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Aggiornare il costruttore e usare il metodo `ConfigureRefresh` per specificare l'impostazione da aggiornare dall'archivio di Configurazione app. Viene recuperata un'istanza di `IConfigurationRefresher` usando il metodo `GetRefresher`. Facoltativamente, è anche possibile cambiare l'impostazione predefinita di 30 secondi della finestra temporale di scadenza della cache impostandola su 1 minuto .

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Aggiornare il metodo `Run` e segnalare di aggiornare la configurazione usando il metodo `Refresh` all'inizio della chiamata di Funzioni. Questa operazione non verrà eseguita se non viene raggiunta la finestra temporale di scadenza della cache. Rimuovere l'operatore `await` se si preferisce che la configurazione venga aggiornata senza blocchi.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testare la funzione in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se si usa Windows PowerShell, eseguire il comando seguente:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se si usa macOS o Linux, eseguire il comando seguente:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Per testare la funzione premere F5. Se viene visualizzata, accettare la richiesta di Visual Studio di scaricare e installare gli strumenti dell'**interfaccia della riga di comando Azure Functions Core Tools**. Potrebbe essere necessario anche abilitare un'eccezione del firewall per consentire agli strumenti di gestire le richieste HTTP.

3. Copiare l'URL della funzione dall'output di runtime di Funzioni di Azure.

    ![Guida introduttiva: debug di funzioni in VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Incollare l'URL per la richiesta HTTP nella barra degli indirizzi del browser. L'immagine seguente mostra la risposta nel browser alla richiesta GET locale restituita dalla funzione.

    ![Guida introduttiva: avvio della funzione in locale](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

6. Selezionare **Configuration Explorer** e aggiornare i valori delle chiavi seguenti:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure - Aggiornati |

7. Aggiornare alcune volte il browser. Quando l'impostazione memorizzata nella cache scade dopo un minuto, nella pagina viene visualizzata la risposta della chiamata di Funzioni con un valore aggiornato.

    ![Avvio rapido: aggiornamento della funzione in locale](./media/quickstarts/dotnet-core-function-refresh-local.png)

Il codice di esempio usato in questa esercitazione può essere scaricato dal [repository GitHub di Configurazione app](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app di Funzioni di Azure per aggiornare dinamicamente le impostazioni di configurazione di Configurazione app. Per informazioni su come usare un'identità gestita di Azure per semplificare l'accesso a Configurazione app, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
