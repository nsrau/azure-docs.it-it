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
ms.openlocfilehash: e8c452ffb40b27ed84061e93c1758b3d403052fe
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054425"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Aggiungere Key Vault all'applicazione Web usando Servizi connessi di Visual Studio

In questa esercitazione si apprenderà come aggiungere facilmente tutto ciò che occorre per iniziare a usare Azure Key Vault per gestire i segreti per i progetti Web in Visual Studio, sia che si usi ASP.NET Core o qualsiasi tipo di progetto ASP.NET. Usando la funzionalità Servizi connessi in Visual Studio, Visual Studio aggiunge automaticamente tutti i pacchetti NuGet e le impostazioni di configurazione necessari per connettersi ai Key Vault in Azure.

Per i dettagli sulle modifiche apportate da Servizi connessi al progetto per abilitare Key Vault, vedere [Key Vault Connected Service - What happened to my ASP.NET 4.7.1 project](#how-your-aspnet-framework-project-is-modified) (Modifiche apportate al progetto ASP.NET - Servizio connesso Key Vault con Visual Studio) o [Key Vault Connected Service - What happened to my ASP.NET Core project](#how-your-aspnet-core-project-is-modified) (Modifiche apportate al progetto ASP.NET Core - Servizio connesso Key Vault con Visual Studio).

## <a name="prerequisites"></a>Prerequisiti

- **Una sottoscrizione di Azure**. Se non si ha una sottoscrizione, iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 versione 16,3** o successiva [scaricarlo ora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Aggiungere il supporto di Key Vault al progetto

Prima di iniziare, verificare di avere eseguito l'accesso a Visual Studio. Accedere con lo stesso account usato per la sottoscrizione di Azure. Aprire quindi un progetto Web ASP.NET 4.7.1 o versione successiva o ASP.NET Core 2,0 e seguire questa procedura:

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto a cui si desidera aggiungere il supporto Key Vault e scegliere **Aggiungi**  >  **servizio connesso**  >  **Aggiungi**.
   Nella pagina Servizio connesso visualizzata sono elencati i servizi che è possibile aggiungere al progetto.
1. Nel menu dei servizi disponibili scegliere **Azure Key Vault** e fare clic su **Avanti**.

   ![Scegliere "Azure Key Vault"](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Selezionare la sottoscrizione che si vuole usare, quindi scegliere una Key Vault esistente e fare clic su **fine**. 

   ![Selezionare la propria sottoscrizione](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

A questo punto, viene stabilita la connessione al Key Vault ed è possibile accedere ai segreti nel codice. I passaggi successivi variano a seconda che si usi ASP.NET 4.7.1 o ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Accedere ai segreti nel codice (ASP.NET Core)

1. Aprire uno dei file di paging, ad esempio *index.cshtml.cs* , e scrivere il codice seguente:
   1. Includere un riferimento a `Microsoft.Extensions.Configuration` tramite questa direttiva using:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Aggiungere la variabile di configurazione.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Aggiungere questo costruttore o sostituire il costruttore esistente con questo:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aggiornare il metodo `OnGet`. Aggiornare il valore segnaposto indicato qui con il nome del segreto creato nei comandi precedenti.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Per confermare il valore in fase di esecuzione, aggiungere il codice da visualizzare `ViewData["Message"]` nel file con *estensione cshtml* per visualizzare il segreto in un messaggio.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

È possibile eseguire l'app localmente per verificare che il segreto venga ottenuto correttamente dalla Key Vault.

## <a name="access-your-secrets-aspnet"></a>Accedere ai segreti (ASP.NET)
È possibile configurare la configurazione in modo che il file di web.config disponga di un valore fittizio nell' `appSettings` elemento sostituito dal valore true in fase di esecuzione. È quindi possibile accedere a questo oggetto tramite la `ConfigurationManager.AppSettings` struttura dei dati.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet. Nella scheda Sfoglia individuare e installare [Microsoft.Configuration.ConfigurationBuilders. Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. Aprire il file di web.config e scrivere il codice seguente:
    1. Aggiungi `configSections` e `configBuilders` :
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
    1. Trovare il tag appSettings, aggiungere un attributo `configBuilders="AzureKeyVault"` e aggiungere una riga:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Modificare il `About` metodo in *HomeController.cs*per visualizzare il valore per la conferma.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Eseguire l'app localmente sotto il debugger, passare alla scheda **About** e verificare che venga visualizzato il valore della Key Vault.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se il Key Vault è in esecuzione in un account Microsoft diverso da quello con cui si è connessi a Visual Studio (ad esempio, il Key Vault è in esecuzione nell'account di lavoro, ma Visual Studio usa l'account privato) viene visualizzato un errore nel file Program.cs, che Visual Studio non può accedere al Key Vault. Per risolvere il problema:

1. Passare alla [portale di Azure](https://portal.azure.com) e aprire il Key Vault.

1. Scegliere **criteri di accesso**, quindi **Aggiungi criteri di accesso**e scegliere l'account con cui si è connessi come entità.

1. In Visual Studio scegliere **file**  >  **Impostazioni account**.
Selezionare **Aggiungi un account** dalla sezione **tutti gli account** . Accedere con l'account scelto come principale dei criteri di accesso.

1. Scegliere **strumenti**  >  **Opzioni**e cercare autenticazione dei **servizi di Azure**. Selezionare quindi l'account appena aggiunto a Visual Studio.

A questo punto, quando si esegue il debug dell'applicazione, Visual Studio si connette all'account in cui si trova il Key Vault.

## <a name="how-your-aspnet-core-project-is-modified"></a>Modalità di modifica del progetto ASP.NET Core

Questa sezione identifica le modifiche esatte apportate a un progetto ASP.NET quando si aggiunge il Key Vault servizio connesso usando Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Aggiunta di riferimenti per ASP.NET Core

Influiscono sul file di progetto riferimenti .NET e sui riferimenti ai pacchetti NuGet.

| Type | Informazioni di riferimento |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Aggiunta di file per ASP.NET Core

- `ConnectedService.json`aggiunta, che registra alcune informazioni sul provider di servizi connesso, sulla versione e su un collegamento alla documentazione.

### <a name="project-file-changes-for-aspnet-core"></a>Modifiche al file di progetto per ASP.NET Core

- Aggiunta del Servizi connessi ItemGroup e del `ConnectedServices.json` file.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jsmodifiche per ASP.NET Core

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

Influiscono sul file di progetto .NET References e `packages.config` (riferimenti a NuGet).

| Type | Informazioni di riferimento |
| --- | --- |
| .NET; NuGet | Azure. Identity |
| .NET; NuGet | Azure. Security. Key Vault. Keys |
| .NET; NuGet | Azure. Security. Vault. Secrets |

### <a name="added-files-for-aspnet-framework"></a>Aggiunta di file per ASP.NET Framework

- `ConnectedService.json`aggiunta, che registra alcune informazioni sul provider di servizi connesso, sulla versione e su un collegamento alla documentazione.

### <a name="project-file-changes-for-aspnet-framework"></a>Modifiche al file di progetto per ASP.NET Framework

- Sono stati aggiunti il file ConnectedServices.json e ItemGroup di Servizi connessi.
- Riferimenti agli assembly .NET descritti nella sezione [Riferimenti aggiunti](#added-references-for-aspnet-framework).

## <a name="next-steps"></a>Passaggi successivi

Se è stata seguita questa esercitazione, le autorizzazioni Key Vault sono configurate per l'esecuzione con la propria sottoscrizione di Azure, ma ciò potrebbe non essere auspicabile per uno scenario di produzione. È possibile creare un'identità gestita per gestire l'accesso Key Vault per l'app. Vedere [fornire Key Vault autenticazione con un'identità gestita](/azure/key-vault/managed-identity).

Per ulteriori informazioni sullo sviluppo Key Vault, vedere la guida per gli [sviluppatori di Key Vault](developers-guide.md).
