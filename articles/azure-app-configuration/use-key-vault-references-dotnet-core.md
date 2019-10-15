---
title: Esercitazione per l'uso dei riferimenti a Key Vault di Configurazione app di Azure in un'app ASP.NET Core | Microsoft Docs
description: Questa esercitazione illustra come usare i riferimenti a Key Vault di Configurazione app di Azure da un'app ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035816"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Esercitazione: Usare i riferimenti a Key Vault in un'app ASP.NET Core

Questa esercitazione illustra come usare il servizio Configurazione app di Azure insieme ad Azure Key Vault. Si tratta di servizi complementari che verranno usati affiancati nella maggior parte delle distribuzioni di applicazioni. Per usarli insieme, Configurazione app consente di creare chiavi che fanno riferimento ai valori archiviati in Key Vault. In questo caso, Configurazione app archivia l'URI del valore di Key Vault invece del valore stesso. L'applicazione recupera il valore di questa chiave tramite il provider client di Configurazione app, come per qualsiasi altra chiave archiviata in Configurazione app. Il provider client lo riconosce come riferimento a Key Vault e chiama Key Vault per recuperarlo. L'applicazione è responsabile dell'autenticazione corretta sia per Configurazione app che per Key Vault. I due servizi non comunicano direttamente.

Questa esercitazione mostra come implementare i riferimenti a Key Vault nel codice. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima opzione ed è disponibile per le piattaforme Windows, macOS e Linux.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una chiave di Configurazione app che fa riferimento a un valore archiviato in Key Vault
> * Accedere al valore di questa chiave da un'applicazione Web ASP.NET Core

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Selezionare l'opzione **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure

    ![Output dopo la creazione dell'istanza di Key Vault](./media/quickstarts/search-services.png)
2. Nella casella di ricerca immettere **Key Vault**.
3. Nell'elenco dei risultati scegliere **Key Vault**.
4. Nella sezione Key Vault scegliere **Crea**.
5. Nella pagina **Crea insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    - **Nome**: è necessario un nome univoco. Per questo argomento di avvio rapido si usa **Contoso-vault2**. 
    - **Sottoscrizione** Scegliere una sottoscrizione.
    - In **Gruppo di risorse** scegliere **Crea nuovo** e immettere il nome del gruppo di risorse.
    - Scegliere un percorso nel menu a discesa **Percorso**.
    - Lasciare invariati i valori predefiniti delle altre opzioni.
6. Dopo avere specificato le informazioni, selezionare **Crea**.

A questo punto, l'account Azure è l'unico autorizzato ad accedere a questo nuovo insieme di credenziali.

![Output dopo la creazione dell'istanza di Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti un paio di passaggi aggiuntivi. In questo caso viene aggiunto un messaggio che è possibile usare per testare il recupero da Key Vault. Nel messaggio, denominato **Message**, viene archiviato il valore di **Hello from Key Vault**.

1. Nella pagina delle proprietà di Key Vault selezionare **Segreti**.
1. Fare clic su **Genera/Importa**.
1. Nella schermata **Crea un segreto** selezionare i seguenti valori:
    - **Opzioni di caricamento**: manuale.
    - **Nome**: Message
    - **Value**: Hello from Key Vault
    - Lasciare invariati gli altri valori predefiniti. Fare clic su **Create**(Crea).

## <a name="add-a-key-vault-reference-to-app-config"></a>Aggiungere un riferimento a Key Vault in Configurazione app

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di configurazione app creata nella guida di avvio rapido.

1. Fare clic su **Esplora configurazioni**.

1. Fare clic su **+ Crea** > **Riferimento all'insieme di credenziali delle chiavi** e scegliere i valori seguenti:
    - **Chiave**: TestApp:Settings:KeyVaultMessage
    - **Etichetta**: Lasciare vuoto
    - **Sottoscrizione**, **Gruppo di risorse**, **Insieme di credenziali delle chiavi**: scegliere le opzioni corrispondenti all'istanza di Key Vault creata nella sezione precedente.
    - **Segreto**: selezionare il segreto denominato **Message** creato nella sezione precedente.

## <a name="connect-to-key-vault"></a>Connettersi a Key Vault

1. Per questa esercitazione verrà usata un'entità servizio per l'autenticazione in Key Vault. Per creare questa entità servizio, usare il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Questa operazione restituisce una serie di coppie chiave-valore. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Eseguire il comando seguente per consentire all'entità servizio di accedere all'insieme di credenziali delle chiavi:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Aggiungere i segreti per *clientId* e *clientSecret* in Secrets Manager. Questi comandi devono essere eseguiti nella stessa directory del file con estensione *csproj*.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Queste credenziali di Key Vault vengono usate solo all'interno dell'applicazione. Servono solo a eseguire l'autenticazione dell'applicazione direttamente in Key Vault. Non vengono mai passate al servizio Configurazione app.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aggiornare il codice per usare un riferimento a Key Vault

1. Aprire *Program.cs* e aggiungere i riferimenti ai pacchetti necessari.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Aggiornare il metodo `CreateWebHostBuilder` per usare Configurazione app effettuando una chiamata al metodo `config.AddAzureAppConfiguration()`. Includere l'opzione `UseAzureKeyVault`, che passa un nuovo riferimento a `KeyVaultClient` a Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Dopo aver passato il riferimento a *KeyVaultClient* al metodo `UseAzureKeyVault` durante l'inizializzazione della connessione a Configurazione app, è possibile accedere ai valori dei riferimenti a Key Vault allo stesso modo con cui si accede ai valori delle normali chiavi di Configurazione app. Per vedere questo processo in azione, aprire *Index.cshtml* nella directory Views > Home. Sostituire il contenuto con il codice seguente:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Il valore del riferimento *TestApp:Settings:KeyVaultMessage* a Key Vault è accessibile allo stesso modo del valore di configurazione *TestApp:Settings:Message*

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

        dotnet build

2. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

        dotnet run

3. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiunta un'identità del servizio gestita di Azure per facilitare l'accesso a Configurazione app e migliorare la gestione delle credenziali dell'app. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Esempi dell'interfaccia della riga di comando](./cli-samples.md)
