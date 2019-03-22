---
title: Aggiungere il supporto di Key Vault al progetto ASP.NET usando Visual Studio - Azure Key Vault | Microsoft Docs
description: Usare questa esercitazione per apprendere come aggiungere il supporto di Key Vault a un'applicazione Web ASP.NET o ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: d95bd114be712953b79ef5afbb0915173f6de26c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339279"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Aggiungere Key Vault all'applicazione Web usando Servizi connessi di Visual Studio

In questa esercitazione si apprenderà come aggiungere facilmente tutto ciò che occorre per iniziare a usare Azure Key Vault per gestire i segreti per i progetti Web in Visual Studio, sia che si usi ASP.NET Core o qualsiasi tipo di progetto ASP.NET. Usando la funzionalità servizi connessi in Visual Studio, è possibile impostare Visual Studio aggiunge automaticamente tutti i pacchetti NuGet e le impostazioni di configurazione che è necessario connettersi a Key Vault in Azure. 

Per i dettagli sulle modifiche apportate da Servizi connessi al progetto per abilitare Key Vault, vedere [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) (Modifiche apportate al progetto ASP.NET - Servizio connesso Key Vault con Visual Studio) o [Key Vault Connected Service - What happened to my ASP.NET Core project](#how-your-aspnet-core-project-is-modified) (Modifiche apportate al progetto ASP.NET Core - Servizio connesso Key Vault con Visual Studio).

## <a name="prerequisites"></a>Prerequisiti

- **Una sottoscrizione di Azure**. Se non si ha una sottoscrizione, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019** oppure **Visual Studio 2017 versione 15.7** con il **lo sviluppo Web** installato il carico di lavoro. [Scaricarla qui](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Per ASP.NET (non Core) con Visual Studio 2017, è necessario il .NET Framework 4.7.1 o versioni successive Development Tools, che non sono installati per impostazione predefinita. Per eseguire l'installazione, avviare il programma di installazione di Visual Studio, scegliere **Modifica**, quindi **Singoli componenti**, sul lato destro espandere **Sviluppo web e ASP.NET**, quindi scegliere **Strumenti di sviluppo per .NET Framework 4.7.1**.
- Un ASP.NET 4.7.1 o versione successiva, o aprire un progetto web ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Aggiungere il supporto di Key Vault al progetto

1. In **Esplora soluzioni** scegliere **Aggiungi** > **Servizio connesso**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.
1. Nel menu dei servizi disponibili scegliere **Proteggi i segreti con Azure Key Vault**.

   ![Scegliere "Proteggi i segreti con Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Se è stato effettuato l'accesso a Visual Studio e al proprio account è associata una sottoscrizione di Azure, viene visualizzata una pagina contenente un elenco a discesa con le sottoscrizioni. Assicurarsi di aver eseguito l'accesso a Visual Studio e verificare che l'account di accesso sia lo stesso usato per la sottoscrizione di Azure.

1. Selezionare la sottoscrizione da usare e quindi scegliere un Key Vault nuovo o esistente oppure scegliere il collegamento per la modifica per cambiare il nome generato automaticamente.

   ![Selezionare la propria sottoscrizione](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Digitare il nome da usare per il Key Vault.

   ![Rinominare il Key Vault e scegliere un gruppo di risorse](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Selezionare un gruppo di risorse esistente oppure scegliere di crearne uno nuovo con un nome univoco generato automaticamente.  Se si vuole creare un nuovo gruppo con un nome diverso, è possibile usare il [portale di Azure](https://portal.azure.com), quindi chiudere la pagina e riavviare per ricaricare l'elenco di gruppi di risorse.
1. Scegliere l'area in cui creare il Key Vault. Se l'applicazione Web è ospitata in Azure, scegliere l'area che ospita l'applicazione Web per ottenere prestazioni ottimali.
1. Scegliere un modello di prezzi. Per informazioni dettagliate, vedere [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Scegliere OK per accettare le opzioni di configurazione.
1. Scegliere **Aggiungi** per creare il Key Vault. Se si sceglie un nome già usato, il processo di creazione potrebbe non riuscire.  In tal caso usare il collegamento per la **modifica** per rinominare il Key Vault, quindi riprovare.

   ![Aggiunta del servizio connesso al progetto](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. A questo punto aggiungere un segreto nel Key Vault in Azure. Per accedere alla posizione corretta nel portale, fare clic sul collegamento Gestisci i segreti archiviati in questo Key Vault. Se si è chiuso il progetto o la pagina, è possibile accedere a questa posizione dal [portale di Azure](https://portal.azure.com). Per eseguire questa operazione, scegliere **Tutti i servizi**, nell'area **Sicurezza** selezionare **Insiemi di credenziali delle chiavi** e quindi selezionare l'insieme di credenziali delle chiavi appena creato.

   ![Accesso al portale](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Nella sezione Insieme di credenziali delle chiavi per l'insieme di credenziali delle chiavi appena creato scegliere **Segreti**, quindi **Genera/Importa**.

   ![Generare/Importare un segreto](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Immettere un segreto, ad esempio "MySecret", e assegnargli un valore stringa a scopo di test, quindi scegliere il pulsante **Crea**.

   ![Creare un segreto](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (facoltativo) Immettere un altro segreto, ma questa volta inserirlo in una categoria assegnando il nome "Secrets--MySecret". Questa sintassi specifica una categoria "Secrets" che contiene un segreto "MySecret".
 
A questo punto è possibile accedere ai segreti nel codice. I passaggi successivi variano a seconda che si usi ASP.NET 4.7.1 o ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Accedere ai segreti nel codice

1. Installare questi due pacchetti NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Aprire il file Program.cs e aggiornare il codice con il codice seguente: 
   ```
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

3. Aprire quindi About.cshtml.cs file e scrivere il codice seguente:
   1. Includere un riferimento a Microsoft.Extensions.Configuration da questa istruzione using:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Aggiungere questo costruttore:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aggiornare il metodo OnGet Aggiornare il valore del segnaposto mostrato qui con il nome del segreto che è stato creato nei comandi precedenti.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Eseguire localmente l'app, passare alla pagina About. Verrà visualizzato il valore del segreto recuperato.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse. In questo modo vengono eliminati l'insieme di credenziali delle chiavi e le risorse correlate. Per eliminare il gruppo di risorse tramite il portale:

1. Immettere il nome del gruppo di risorse nella casella di ricerca nella parte superiore del portale. Quando nei risultati della ricerca viene visualizzato il gruppo di risorse usato in questa guida introduttiva, selezionarlo.
2. Selezionare **Elimina gruppo di risorse**.
3. Nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE:** digitare il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Come viene modificato il progetto ASP.NET Core

Questa sezione identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge l'insieme di credenziali chiave di servizio con Visual Studio connesso.

### <a name="added-references"></a>Riferimenti aggiunti

Interessa il .NET riferimenti a file di progetto e i riferimenti ai pacchetti NuGet.

| Type | Riferimenti |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>File aggiunti

- È stato aggiunto il file ConnectedService.json che registra alcune informazioni su provider del servizio connesso, versione e un collegamento alla documentazione.

### <a name="project-file-changes"></a>Modifiche al file di progetto

- Sono stati aggiunti il file ConnectedServices.json e ItemGroup di Servizi connessi.

### <a name="launchsettingsjson-changes"></a>Modifiche a launchsettings.JSON

- Aggiungere le seguenti voci di variabili di ambiente sia al profilo IIS Express sia al profilo che corrisponde al nome del progetto Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Modifiche in Azure

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Come viene modificato il progetto di Framework di ASP.NET

Questa sezione identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge l'insieme di credenziali chiave di servizio con Visual Studio connesso.

### <a name="added-references"></a>Riferimenti aggiunti

Interessa i riferimenti .NET al file di progetto e `packages.config` (riferimenti NuGet).

| Type | Riferimenti |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>File aggiunti

- È stato aggiunto il file ConnectedService.json che registra alcune informazioni su provider del servizio connesso, versione e un collegamento alla documentazione.

### <a name="project-file-changes"></a>Modifiche al file di progetto

- Sono stati aggiunti il file ConnectedServices.json e ItemGroup di Servizi connessi.
- Riferimenti agli assembly .NET descritti nella sezione [Riferimenti aggiunti](#added-references).

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

### <a name="changes-on-azure"></a>Modifiche in Azure

- È stato creato un gruppo di risorse o ne è stato usato uno esistente.
- È stato creato un insieme di credenziali nel gruppo di risorse specificato.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Key Vault leggere [Guida per gli sviluppatori dell'insieme di credenziali delle chiavi di Azure](key-vault-developers-guide.md)