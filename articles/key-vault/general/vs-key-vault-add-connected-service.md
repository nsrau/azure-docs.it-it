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
ms.openlocfilehash: 68f8607db25b70ea5e6bfe21a7d6e002aeb127d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429772"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Aggiungere Key Vault all'applicazione Web usando Servizi connessi di Visual Studio

In questa esercitazione si apprenderà come aggiungere facilmente tutto ciò che occorre per iniziare a usare Azure Key Vault per gestire i segreti per i progetti Web in Visual Studio, sia che si usi ASP.NET Core o qualsiasi tipo di progetto ASP.NET. Usando la funzionalità Servizi connessi in Visual Studio, è possibile fare in modo che Visual Studio aggiugli automaticamente tutti i pacchetti NuGet e le impostazioni di configurazione necessari per connettersi all'insieme di credenziali delle chiavi in Azure.By using the Connected Services feature in Visual Studio, you can have Visual Studio automatically add all the NuGet packages and configuration settings you need to connect to Key Vault in Azure.

Per i dettagli sulle modifiche apportate da Servizi connessi al progetto per abilitare Key Vault, vedere [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) (Modifiche apportate al progetto ASP.NET - Servizio connesso Key Vault con Visual Studio) o [Key Vault Connected Service - What happened to my ASP.NET Core project](#how-your-aspnet-core-project-is-modified) (Modifiche apportate al progetto ASP.NET Core - Servizio connesso Key Vault con Visual Studio).

## <a name="prerequisites"></a>Prerequisiti

- **Una sottoscrizione di Azure.** Se non hai un abbonamento, iscriviti per ottenere un [account gratuito.](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2019 versione 16.3** o successiva oppure **Visual Studio 2017 versione 15.7** con il carico di lavoro **di sviluppo Web** installato. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Per ASP.NET (non Core) con Visual Studio 2017, sono necessari gli strumenti di sviluppo di .NET Framework 4.7.1 o versioni successive, che non vengono installati per impostazione predefinita. Per eseguire l'installazione, avviare il programma di installazione di Visual Studio, scegliere **Modifica**, quindi **Singoli componenti**, sul lato destro espandere **Sviluppo web e ASP.NET**, quindi scegliere **Strumenti di sviluppo per .NET Framework 4.7.1**.
- Un ASP.NET 4.7.1 o versione successiva oppure ASP.NET progetto Web Core 2.0 o versione successiva.

## <a name="add-key-vault-support-to-your-project"></a>Aggiungere il supporto di Key Vault al progetto

Prima di iniziare, assicurati di aver effettuato l'accesso a Visual Studio. Accedere con lo stesso account usato per la sottoscrizione di Azure.Sign in with the same account that you use for your Azure subscription. Aprire quindi un progetto di ASP.NET 4.7.1 o versione successiva o ASP.NET progetto Web Core 2.0 ed eseguire le operazioni seguenti:

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto a cui si desidera aggiungere il supporto dell'insieme di chiavi e scegliere **Aggiungi** > **servizio connesso**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.
1. Nel menu dei servizi disponibili scegliere **Proteggi i segreti con Azure Key Vault**.

   ![Scegliere "Proteggi i segreti con Azure Key Vault"](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selezionare la sottoscrizione che si vuole usare e quindi scegliere un insieme di credenziali delle chiavi nuovo o esistente. Se si sceglie il nuovo Key Vault, viene visualizzato un collegamento **Modifica.** Selezionarla per configurare il nuovo archivio chiavi.

   ![Selezionare la propria sottoscrizione](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. In **Modifica insieme di credenziali delle chiavi**di Azure immettere il nome che si vuole usare per l'insieme di credenziali delle chiavi.

1. Selezionare un **gruppo di risorse**esistente o scegliere di crearne uno nuovo con un nome univoco generato automaticamente.  Se si vuole creare un nuovo gruppo con un nome diverso, è possibile usare il portale di [Azure](https://portal.azure.com), quindi chiudere la pagina e riavviare per ricaricare l'elenco dei gruppi di risorse.
1. Scegliere la **posizione** in cui creare l'insieme di credenziali delle chiavi. Se l'applicazione Web è ospitata in Azure, scegliere l'area che ospita l'applicazione Web per ottenere prestazioni ottimali.
1. Scegliere un **piano tariffario**. Per informazioni dettagliate, vedere [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Scegliere **OK** per accettare le opzioni di configurazione.
1. Dopo aver selezionato un insieme di credenziali delle chiavi esistente o aver configurato un nuovo insieme di credenziali delle chiavi, nella scheda **Insieme di credenziali delle** chiavi di Azure di Visual Studio selezionare **Aggiungi** per aggiungere il servizio connesso.
1. Selezionare il collegamento **Gestisci segreti archiviati in questo archivio chiavi** per aprire la pagina **Segreti** per l'insieme di credenziali delle chiavi. Se la pagina o il progetto è stato chiuso, è possibile accedervi nel [portale](https://portal.azure.com) di Azure scegliendo **Tutti i servizi** e, in **Sicurezza**, scegliendo **Key Vault**, quindi scegliere l'insieme di credenziali delle chiavi.
1. Nella sezione Key Vault relativa all'insieme di credenziali delle chiavi creato, scegliere **Secrets**, quindi **Generate/Import**.

   ![Generare/Importare un segreto](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Immettere un segreto, ad esempio MySecret e assegnargli qualsiasi valore stringa come test, quindi selezionare il pulsante **Crea.Enter** a secret, such as *MySecret* and give it any string value as a test, then select the Create button.

   ![Creare un segreto](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (facoltativo) Immettere un altro segreto, ma questa volta inserirlo in una categoria assegnando il nome *Secrets--MySecret*. Questa sintassi specifica una categoria "Secrets" che contiene un segreto "MySecret".

A questo punto è possibile accedere ai segreti nel codice. I passaggi successivi variano a seconda che si usi ASP.NET 4.7.1 o ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Accedere ai segreti nel codice (ASP.NET Core)Access your secrets in code (ASP.NET Core)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e **scegliere Gestisci pacchetti NuGet**. Nella scheda **Sfoglia** individuare e installare questi due pacchetti NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e per .NET Core 2 aggiungere [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) o per .NET Core 3, aggiungere[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. Per .NET Core 2, selezionare la `Program.cs` scheda e modificare la `BuildWebHost` definizione nella classe Program nel modo seguente:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Per .NET Core 3, usare il codice seguente.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Aprire quindi uno dei file di pagina, ad esempio *Index.cshtml.cs* e scrivere il codice seguente:
   1. Includere un `Microsoft.Extensions.Configuration` riferimento a con questa direttiva using:Include a reference to by this using directive:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Aggiungere la variabile di configurazione.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Aggiungere questo costruttore o sostituire il costruttore esistente con il seguente:Add this constructor or replace the existing constructor with this:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aggiornare il metodo `OnGet`. Aggiornare il valore segnaposto mostrato qui con il nome segreto creato nei comandi precedenti.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Per confermare il valore in fase `ViewData["Message"]` di esecuzione, aggiungere il codice da visualizzare nel file *con estensione cshtml* per visualizzare il segreto in un messaggio.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

È possibile eseguire l'app in locale per verificare che il segreto venga ottenuto correttamente dall'insieme di credenziali delle chiavi.

## <a name="access-your-secrets-aspnet"></a>Accedi ai tuoi segreti (ASP.NET)

È possibile impostare la configurazione in modo che il file `appSettings` web.config abbia un valore fittizio nell'elemento sostituito dal valore true in fase di esecuzione. È quindi possibile accedere `ConfigurationManager.AppSettings` a questo tramite la struttura dei dati.

1. Modificare il file web.config.  Trovare il tag appSettings, `configBuilders="AzureKeyVault"`aggiungere un attributo e aggiungere una riga:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Modificare `About` il metodo in *HomeController.cs*, per visualizzare il valore per la conferma.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Eseguire l'app in locale sotto il debugger, passare alla scheda **Informazioni** e verificare che venga visualizzato il valore dell'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questo avvio rapido, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **TYPE THE RESOURCE GROUP NAME:** immettere il nome del gruppo di risorse e selezionare **Delete**.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se l'insieme di credenziali delle chiavi è in esecuzione su un account Microsoft diverso da quello che si è connessi a Visual Studio (ad esempio, l'insieme di credenziali delle chiavi è in esecuzione sull'account di lavoro, ma Visual Studio utilizza l'account privato) si ottiene un errore nel file di Program.cs, visual Studio non può ottenere l'accesso all'insieme di credenziali delle chiavi. Per risolvere il problema:

1. Passare al portale di [Azure](https://portal.azure.com) e aprire l'insieme di credenziali delle chiavi.

1. Scegliere Criteri di **accesso**, quindi Aggiungi criteri di **accesso**e scegliere l'account con cui si è connessi come principale.

1. In Visual Studio scegliere**Impostazioni account** **file.** > 
Seleziona **Aggiungi un account** dalla sezione Tutti gli **account.** Accedere con l'account scelto come Principal dei criteri di accesso.

1. Scegliere**Opzioni** **degli strumenti** > e cercare **Autenticazione servizio di Azure**. Selezionare quindi l'account appena aggiunto a Visual Studio.

A questo punto, quando si esegue il debug dell'applicazione, Visual Studio si connette all'account in cui si trova l'insieme di credenziali delle chiavi.

## <a name="how-your-aspnet-core-project-is-modified"></a>Come viene modificato il progetto ASP.NET Core

Questa sezione identifica le modifiche esatte apportate a un progetto di ASP.NET quando si aggiunge il servizio Key Vault connesso utilizzando Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Aggiunti riferimenti per ASP.NET Core

Influisce sui riferimenti .NET al file di progetto e sui riferimenti al pacchetto NuGet.

| Type | Informazioni di riferimento |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Aggiunti file per ASP.NET Core

- `ConnectedService.json`aggiunta, che registra alcune informazioni sul provider di servizi connessi, versione e un collegamento alla documentazione.

### <a name="project-file-changes-for-aspnet-core"></a>Modifiche ai file di progetto per ASP.NET CoreProject file changes for ASP.NET Core

- Sono stati aggiunti il `ConnectedServices.json` ItemGroup e il file di Servizi connessi.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>Launchsettings.json modifiche per ASP.NET Core

- Aggiungere le seguenti voci di variabili di ambiente sia al profilo IIS Express sia al profilo che corrisponde al nome del progetto Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Modifiche in Azure per ASP.NET CoreChanges on Azure for ASP.NET Core

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Modalità di modifica del progetto ASP.NET Framework

Questa sezione identifica le modifiche esatte apportate a un progetto di ASP.NET quando si aggiunge il servizio Key Vault connesso utilizzando Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Aggiunti riferimenti per ASP.NET Framework

Influisce sul file di `packages.config` progetto riferimenti .NET e (riferimenti NuGet).

| Type | Informazioni di riferimento |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Aggiunti file per ASP.NET Framework

- `ConnectedService.json`aggiunta, che registra alcune informazioni sul provider di servizi connessi, versione e un collegamento alla documentazione.

### <a name="project-file-changes-for-aspnet-framework"></a>Modifiche ai file di progetto per ASP.NET Framework

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Modifiche in Azure per ASP.NET FrameworkChanges on Azure for ASP.NET Framework

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

## <a name="next-steps"></a>Passaggi successivi

Se è stata seguita questa esercitazione, le autorizzazioni dell'insieme di credenziali delle chiavi vengono configurate per l'esecuzione con la propria sottoscrizione di Azure, ma ciò potrebbe non essere auspicabile per uno scenario di produzione. È possibile creare un'identità gestita per gestire l'accesso all'insieme di credenziali delle chiavi per l'app. Consultate Fornire l'autenticazione dell'insieme di credenziali delle [chiavi con un'identità gestita.](/azure/key-vault/managed-identity)

Per ulteriori informazioni sullo sviluppo di Key Vault, leggere la Guida per [gli sviluppatori di Key Vault](developers-guide.md).
