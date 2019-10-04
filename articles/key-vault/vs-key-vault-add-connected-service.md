---
title: Aggiungere il supporto di Key Vault al progetto ASP.NET usando Visual Studio - Azure Key Vault | Microsoft Docs
description: Usare questa esercitazione per apprendere come aggiungere il supporto di Key Vault a un'applicazione Web ASP.NET o ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 27c21171c2a53cb739215dcae070b94c8610a490
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880867"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Aggiungere Key Vault all'applicazione Web usando Servizi connessi di Visual Studio

In questa esercitazione si apprenderà come aggiungere facilmente tutto ciò che occorre per iniziare a usare Azure Key Vault per gestire i segreti per i progetti Web in Visual Studio, sia che si usi ASP.NET Core o qualsiasi tipo di progetto ASP.NET. Usando la funzionalità Servizi connessi in Visual Studio, Visual Studio aggiunge automaticamente tutti i pacchetti NuGet e le impostazioni di configurazione necessari per connettersi ai Key Vault in Azure.

Per i dettagli sulle modifiche apportate da Servizi connessi al progetto per abilitare Key Vault, vedere [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) (Modifiche apportate al progetto ASP.NET - Servizio connesso Key Vault con Visual Studio) o [Key Vault Connected Service - What happened to my ASP.NET Core project](#how-your-aspnet-core-project-is-modified) (Modifiche apportate al progetto ASP.NET Core - Servizio connesso Key Vault con Visual Studio).

## <a name="prerequisites"></a>Prerequisiti

- **Una sottoscrizione di Azure**. Se non si ha una sottoscrizione, iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 versione 16,3 Preview 1** o successiva o **Visual studio 2017 versione 15,7** con il carico di lavoro **sviluppo Web** installato. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Per ASP.NET (non core) con Visual Studio 2017, sono necessari gli strumenti di sviluppo .NET Framework 4.7.1 o versioni successive, che non sono installati per impostazione predefinita. Per eseguire l'installazione, avviare il programma di installazione di Visual Studio, scegliere **Modifica**, quindi **Singoli componenti**, sul lato destro espandere **Sviluppo web e ASP.NET**, quindi scegliere **Strumenti di sviluppo per .NET Framework 4.7.1**.
- Un progetto Web ASP.NET 4.7.1 o versione successiva o ASP.NET Core 2,0 aperto.

## <a name="add-key-vault-support-to-your-project"></a>Aggiungere il supporto di Key Vault al progetto

Prima di iniziare, verificare di avere eseguito l'accesso a Visual Studio. Accedere con lo stesso account usato per la sottoscrizione di Azure. Aprire quindi un progetto Web ASP.NET 4.7.1 o versione successiva o ASP.NET Core 2,0 e seguire questa procedura:

1. In **Esplora soluzioni** scegliere **Aggiungi** > **Servizio connesso**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.
1. Nel menu dei servizi disponibili scegliere **Proteggi i segreti con Azure Key Vault**.

   ![Scegliere "Proteggi i segreti con Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selezionare la sottoscrizione che si vuole usare e quindi scegliere una Key Vault nuova o esistente. Se si sceglie la nuova Key Vault, viene visualizzato un collegamento **modifica** . Selezionarlo per configurare la nuova Key Vault.

   ![Selezionare la propria sottoscrizione](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. In **modifica Azure Key Vault**immettere il nome che si desidera utilizzare per il Key Vault.

1. Selezionare un **gruppo di risorse**esistente o scegliere di crearne uno nuovo con un nome univoco generato automaticamente.  Se si vuole creare un nuovo gruppo con un nome diverso, è possibile usare la [portale di Azure](https://portal.azure.com), quindi chiudere la pagina e riavviare per ricaricare l'elenco di gruppi di risorse.
1. Scegliere la **posizione** in cui creare il Key Vault. Se l'applicazione Web è ospitata in Azure, scegliere l'area che ospita l'applicazione Web per ottenere prestazioni ottimali.
1. Scegliere unpiano tariffario. Per informazioni dettagliate, vedere [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Scegliere **OK** per accettare le scelte di configurazione.
1. Dopo aver selezionato un Key Vault esistente o aver configurato una nuova Key Vault, nella scheda **Azure Key Vault** di Visual Studio selezionare **Aggiungi** per aggiungere il servizio connesso.
1. Selezionare il collegamento Gestisci i **segreti archiviati in questo Key Vault** per aprire la pagina dei **segreti** per il Key Vault. Se la pagina o il progetto è stato chiuso, è possibile accedervi nella [portale di Azure](https://portal.azure.com) scegliendo **tutti i servizi** e, in **sicurezza**, scegliendo **Key Vault**, quindi scegliere il Key Vault.
1. Nella sezione Insieme di credenziali delle chiavi per l'insieme di credenziali delle chiavi appena creato scegliere **Segreti**, quindi **Genera/Importa**.

   ![Generare/Importare un segreto](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Immettere un segreto, ad esempio *segreto* , assegnargli qualsiasi valore stringa come test, quindi selezionare il pulsante **Crea** .

   ![Crea un segreto](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (facoltativo) Immettere un altro segreto, ma questa volta inserirlo in una categoria assegnando il nome *Secrets--MySecret*. Questa sintassi specifica una categoria "Secrets" che contiene un segreto "My Secret".

A questo punto è possibile accedere ai segreti nel codice. I passaggi successivi variano a seconda che si usi ASP.NET 4.7.1 o ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Accedere ai segreti nel codice

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Nella scheda **Sfoglia** individuare e installare questi due pacchetti NuGet: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft. Azure. Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. Selezionare la `Program.cs` scheda e sostituire la classe Program con il codice seguente:

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. Aprire `About.cshtml.cs` quindi il file e scrivere il codice seguente:
   1. Includere un riferimento a `Microsoft.Extensions.Configuration` tramite questa istruzione using:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Aggiungere il costruttore seguente:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aggiornare il `OnGet` metodo. Aggiornare il valore segnaposto indicato qui con il nome del segreto creato nei comandi precedenti.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Eseguire l'app in locale passando alla pagina About (informazioni). Verrà visualizzato il valore del segreto recuperato.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **digitare il nome del gruppo di risorse:** immettere il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Modalità di modifica del progetto ASP.NET Core

Questa sezione identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge il Key Vault servizio connesso usando Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Aggiunta di riferimenti per ASP.NET Core

Influiscono sul file di progetto riferimenti .NET e sui riferimenti ai pacchetti NuGet.

| Type | Riferimenti |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Aggiunta di file per ASP.NET Core

- `ConnectedService.json`aggiunta, che registra alcune informazioni sul provider di servizi connesso, sulla versione e su un collegamento alla documentazione.

### <a name="project-file-changes-for-aspnet-core"></a>Modifiche al file di progetto per ASP.NET Core

- Aggiunta del servizi connessi ItemGroup e `ConnectedServices.json` del file.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>modifiche di launchsettings. JSON per ASP.NET Core

- Aggiungere le seguenti voci di variabili di ambiente sia al profilo IIS Express sia al profilo che corrisponde al nome del progetto Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Modifiche in Azure per ASP.NET Core

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Modalità di modifica del progetto ASP.NET Framework

Questa sezione identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge il Key Vault servizio connesso usando Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Aggiunta di riferimenti per ASP.NET Framework

Influiscono sul file di progetto .NET `packages.config` References e (riferimenti a NuGet).

| Type | Riferimenti |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Aggiunta di file per ASP.NET Framework

- `ConnectedService.json`aggiunta, che registra alcune informazioni sul provider di servizi connesso, sulla versione e su un collegamento alla documentazione.

### <a name="project-file-changes-for-aspnet-framework"></a>Modifiche al file di progetto per ASP.NET Framework

- Sono stati aggiunti il file ConnectedServices.json e ItemGroup di Servizi connessi.
- Riferimenti agli assembly .NET descritti nella sezione [Riferimenti aggiunti](#added-references-for-aspnet-framework).

### <a name="webconfig-or-appconfig-changes"></a>modifiche apportate al file web.config o app.config

- Sono state aggiunte le seguenti voci di configurazione:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Modifiche in Azure per ASP.NET Framework

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo sviluppo Key Vault, vedere la guida per gli [sviluppatori di Key Vault](key-vault-developers-guide.md).
