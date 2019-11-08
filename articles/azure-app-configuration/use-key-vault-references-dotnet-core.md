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
ms.openlocfilehash: 992cface653bf3fe52afc7efa3f17573fcf91399
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469654"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Esercitazione: Usare i riferimenti a Key Vault in un'app ASP.NET Core

Questa esercitazione illustra come usare il servizio Configurazione app di Azure insieme ad Azure Key Vault. Configurazione app e Key Vault sono servizi complementari usati side-by-side nella maggior parte delle distribuzioni di applicazioni.

Configurazione app consente di usare insieme i servizi creando chiavi che fanno riferimento a valori archiviati in Key Vault. Quando crea tali chiavi, Configurazione app archivia gli URI dei valori di Key Vault piuttosto che i valori stessi.

L'applicazione usa il provider client di Configurazione app per recuperare i riferimenti a Key Vault, così come per qualsiasi altra chiave archiviata in Configurazione app. In questo caso, i valori archiviati in Configurazione app sono URI che fanno riferimento ai valori contenuti in Key Vault. Non si tratta dei valori o delle credenziali di Key Vault. Poiché riconosce le chiavi come riferimenti a Key Vault, il provider client usa Key Vault per recuperare i relativi valori.

L'applicazione è responsabile dell'autenticazione corretta sia per Configurazione app che per Key Vault. I due servizi non comunicano direttamente.

Questa esercitazione illustra come implementare i riferimenti a Key Vault nel codice. Si basa sull'app Web presentata nelle guide introduttive. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. Ad esempio, [Visual Studio Code](https://code.visualstudio.com/) è un editor di codice multipiattaforma disponibile per i sistemi operativi Windows, macOS e Linux.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una chiave di Configurazione app che faccia riferimento a un valore archiviato in Key Vault.
> * Accedere al valore di questa chiave da un'applicazione Web ASP.NET Core.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, installare [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Creare un insieme di credenziali

1. Selezionare l'opzione **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure:

    ![Output dopo la creazione dell'insieme di credenziali delle chiavi](./media/quickstarts/search-services.png)
1. Nella casella di ricerca immettere **Insieme di credenziali delle chiavi**.
1. Nell'elenco dei risultati scegliere **Insiemi di credenziali delle chiavi** a sinistra.
1. In **Insiemi di credenziali delle chiavi** selezionare **Aggiungi**.
1. Alla destra di **Crea un insieme di credenziali delle chiavi** specificare le informazioni seguenti:
    - Selezionare **Sottoscrizione** per scegliere una sottoscrizione.
    - In **Gruppo di risorse** selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse.
    - In **Nome dell'insieme di credenziali delle chiavi** è necessario un nome univoco. Per questa esercitazione immettere **Contoso-vault2**.
    - Nell'elenco a discesa **Area** scegliere una località.
1. Lasciare invariati i valori predefiniti delle altre opzioni di **Crea un insieme di credenziali delle chiavi**.
1. Selezionare **Create** (Crea).

A questo punto, l'account Azure è l'unico autorizzato ad accedere a questo nuovo insieme di credenziali.

![Output dopo la creazione dell'insieme di credenziali delle chiavi](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Aggiungere un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti alcuni passaggi aggiuntivi. In questo caso aggiungere un messaggio da usare per testare il recupero da Key Vault. Nel messaggio, denominato **Message**, viene archiviato il valore "Hello from Key Vault".

1. Nella pagina delle proprietà di Key Vault selezionare **Segreti**.
1. Selezionare **Genera/Importa**.
1. Nel riquadro **Crea un segreto** immettere i valori seguenti:
    - **Opzioni di caricamento**: immettere **Manual**.
    - **Nome**: immettere **Message**.
    - **Value**: immettere **Hello from Key Vault**.
1. Lasciare invariati i valori predefiniti delle altre proprietà di **Crea un segreto**.
1. Selezionare **Create** (Crea).

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Aggiungere a Configurazione app un riferimento a Key Vault

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Esplora configurazioni**.

1. Selezionare **+ Crea** > **Riferimento all'insieme di credenziali delle chiavi** e quindi specificare i valori seguenti:
    - **Chiave**: selezionare **TestApp:Settings:KeyVaultMessage**.
    - **Etichetta**: lasciare vuoto questo valore.
    - **Sottoscrizione**, **Gruppo di risorse** e **Insieme di credenziali delle chiavi**: immettere i valori corrispondenti ai valori dell'insieme di credenziali delle chiavi creati nella sezione precedente.
    - **Segreto**: selezionare il segreto denominato **Message** creato nella sezione precedente.

## <a name="connect-to-key-vault"></a>Connettersi a Key Vault

1. Per questa esercitazione viene usata un'entità servizio per l'autenticazione in Key Vault. Per creare questa entità servizio, usare il comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) dell'interfaccia della riga di comando di Azure:

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Questa operazione restituisce una serie di coppie chiave-valore:

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

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Aggiungere segreti per *clientId* e *clientSecret* a Secrets Manager, lo strumento per l'archiviazione di dati sensibili aggiunto al file *csproj* in [Avvio rapido: Creare un'app ASP.NET Core con Configurazione app di Azure](./quickstart-aspnet-core-app.md). Questi comandi devono essere eseguiti nella stessa directory del file con estensione *csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Queste credenziali di Key Vault vengono usate solo all'interno dell'applicazione. Servono solo a eseguire l'autenticazione dell'applicazione direttamente in Key Vault. Non vengono mai passate al servizio Configurazione app.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aggiornare il codice per usare un riferimento a Key Vault

1. Aprire *Program.cs* e aggiungere i riferimenti ai pacchetti richiesti seguenti:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Aggiornare il metodo `CreateWebHostBuilder` per usare Configurazione app effettuando una chiamata al metodo `config.AddAzureAppConfiguration`. Includere l'opzione `UseAzureKeyVault`, che passa un nuovo riferimento `KeyVaultClient` a Key Vault.

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

1. Quando è stata inizializzata la connessione a Configurazione app, è stato passato il riferimento `KeyVaultClient` al metodo `UseAzureKeyVault`. Dopo l'inizializzazione, è possibile accedere ai valori dei riferimenti a Key Vault nello stesso modo in cui si accede ai valori delle normali chiavi di Configurazione app.

    Per vedere questo processo in azione, aprire *Index.cshtml* nella cartella **Views** > **Home**. Sostituire il contenuto con il codice seguente:

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

    Il valore del riferimento **TestApp:Settings:KeyVaultMessage** a Key Vault è accessibile allo stesso modo del valore di configurazione **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

    ```
    dotnet build
    ```

1. Al termine della compilazione, usare questo comando per eseguire l'app Web in locale:

    ```
    dotnet run
    ```

1. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Avvio rapido: avvio dell'app in locale](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una chiave di Configurazione app che fa riferimento a un valore archiviato in Key Vault. Per informazioni su come aggiungere un'identità del servizio gestito di Azure che semplifica l'accesso a Configurazione app e a Key Vault, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
