---
title: Salvataggio sicuro delle impostazioni applicazione segrete per un'applicazione Web - Azure Key Vault | Microsoft Docs
description: Come salvare in modo sicuro le impostazioni applicazione segrete, come le credenziali di Azure o le chiavi API di terze parti, usando il Key Vault Provider di ASP.NET Core, User Secret o i generatori di configurazione di .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.custom: devx-track-csharp
ms.openlocfilehash: 57cda5104551f8b62d157e443a42c5e3c75e4ddf
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792413"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Salvataggio sicuro delle impostazioni applicazione segrete per un'applicazione Web

## <a name="overview"></a>Panoramica
Questo articolo descrive come salvare in modo sicuro le impostazioni di configurazione dell'applicazione segrete per le applicazioni Azure.

Tradizionalmente tutte le impostazioni di configurazione dell'applicazione Web vengono salvate nei file di configurazione, ad esempio Web.config. Questa procedura consente di archiviare le impostazioni segrete, ad esempio le credenziali cloud, per sistemi di controllo del codice sorgente pubblici come GitHub. Nel frattempo potrebbe essere difficile seguire una procedura consigliata di sicurezza a causa dell'overhead necessario per modificare il codice sorgente e riconfigurare le impostazioni di sviluppo.

Per assicurarsi che il processo di sviluppo sia sicuro, gli strumenti e le librerie del Framework vengono creati per salvare le impostazioni del segreto dell'applicazione in modo sicuro con una modifica minima o senza codice sorgente.

## <a name="aspnet-and-net-core-applications"></a>Applicazioni ASP.NET e .NET Core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Salvare le impostazioni segrete nell'archivio User Secret che si trova fuori dalla cartella di controllo del codice sorgente
Se si sta eseguendo un prototipo rapido o non si ha accesso a Internet, iniziare spostando le impostazioni segrete fuori dalla cartella di controllo del codice sorgente all'archivio User Secret. L'archivio User Secret è un file salvato nella cartella del profilo utente, in modo che i segreti non vengano archiviati nel controllo del codice sorgente. Il diagramma seguente dimostra come funziona [User Secret](/aspnet/core/security/app-secrets?tabs=visual-studio).

![User Secret mantiene le impostazioni segrete fuori dal controllo del codice sorgente](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Se si esegue l'applicazione console .NET Core, usare Key Vault per salvare il segreto in modo sicuro.

### <a name="save-secret-settings-in-azure-key-vault"></a>Salvare le impostazioni segrete in Azure Key Vault
Se si sviluppa un progetto ed è necessario condividere il codice sorgente in modo sicuro, usare [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Creare un Key Vault nella sottoscrizione di Azure. Compilare tutti i campi obbligatori nell'interfaccia utente e fare clic su *Crea* nella parte inferiore del pannello

    ![Creare un Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Concedere l'accesso al Key Vault a tutti i membri del team. Se il team è grande, è possibile creare un [gruppo di Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) e aggiungere l'accesso di quel gruppo di sicurezza al Key Vault. Nel menu a discesa *Autorizzazioni segrete* selezionare *Ottieni* ed *Elenca* sotto *Operazioni di gestione dei segreti* .
Se l'app Web è già stata creata, concedere all'app Web l'accesso all'Key Vault in modo che possa accedere all'insieme di credenziali delle chiavi senza archiviare la configurazione segreta nelle impostazioni o nei file dell'app. Cercare l'app Web in base al nome e aggiungerla nello stesso modo in cui si concede l'accesso agli utenti.

    ![Aggiungere criteri di accesso del Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Aggiungere il segreto per Key Vault nel portale di Azure. Per le impostazioni di configurazione annidate, sostituire ":" con "-" in modo che il nome del segreto del Key Vault sia valido. Il segno ":" non è ammesso nel nome di un segreto del Key Vault.

    ![Aggiungere il segreto del Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Prima di Visual Studio 2017 V 15.6 è stato usato per consigliare l'installazione dell'estensione di autenticazione dei servizi di Azure per Visual Studio. Questa funzionalità è tuttavia deprecata perché la funzionalità è integrata in Visual Studio. Di conseguenza, se si usa una versione precedente di Visual Studio 2017, è consigliabile eseguire l'aggiornamento ad almeno 2017 15,6 o versione successiva per poter usare questa funzionalità in modo nativo e accedere all'insieme di credenziali delle chiavi usando l'identità di accesso di Visual Studio.
    >

4. Accedere ad Azure tramite l'interfaccia della riga di comando. è possibile digitare:

    ```azurecli
    az login
    ```

5. Aggiungere i pacchetti NuGet seguenti al progetto:

    ```
    Azure.Identity
    Azure.Extensions.AspNetCore.Configuration.Secrets
    ```
6. Aggiungere il codice seguente al file Program.cs:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        builder.AddAzureKeyVault(new Uri(keyVaultEndpoint), new DefaultAzureCredential(), new KeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```

7. Aggiungere l'URL del Key Vault al file launchsettings.json. Il nome della variabile di ambiente *KEYVAULT_ENDPOINT* viene definito nel codice aggiunto nel passaggio 7.

    ![Aggiungere l'URL del Key Vault come variabile di ambiente del progetto](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Avviare il debug del progetto. Sarà eseguito correttamente.

## <a name="aspnet-and-net-applications"></a>Applicazioni ASP.NET e .NET

.NET 4.7.1 supporta i generatori di configurazione Key Vault e segreto, il che garantisce che i segreti possano essere spostati fuori dalla cartella di controllo del codice sorgente senza modifiche al codice.
Per procedere, [scaricare .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) ed eseguire la migrazione dell'applicazione se usa una versione precedente di .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Salvare le impostazioni segrete in un file del segreto fuori dalla cartella di controllo del codice sorgente
Se si sta scrivendo un prototipo veloce e non si desidera eseguire il provisioning di risorse di Azure, procedere con questa opzione.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci segreti utente** . Verrà installato un pacchetto NuGet **Microsoft.Configuration.ConfigurationBuilders. UserSecrets** , verrà creato un file per salvare le impostazioni segrete al di fuori del file di web.config e aggiungere una sezione **ConfigBuilders** nel file web.config.

2. Inserire le impostazioni segrete nell'elemento radice. di seguito è riportato un esempio

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <root>
      <secrets ver="1.0">
        <secret name="secret" value="foo"/>
        <secret name="secret1" value="foo_one" />
        <secret name="secret2" value="foo_two" />
      </secrets>
    </root>
    ```

3. Specificare che la sezione appSettings sta usando il generatore di configurazione segreto. Verificare che sia presente una voce per l'impostazione del segreto con un valore fittizio.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Eseguire il debug dell'app. Sarà eseguito correttamente.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Salvare le impostazioni segrete in un Azure Key Vault
Seguire le istruzioni nella sezione su ASP.NET Core per configurare un Key Vault per il progetto.

1. Installare il seguente pacchetto NuGet nel progetto
   ```
   Microsoft.Configuration.ConfigurationBuilders.Azure
   ```

2. Definire Key Vault generatore di configurazione in Web.config. Inserire questa sezione prima della sezione *appSettings* . Sostituire *VAULTNAME* in modo che sia il nome del Key Vault se il Key Vault è in Azure globale o l'URI completo se si usa il cloud sovrano.

    ```xml
     <configBuilders>
        <builders>
            <add name="Secrets" userSecretsId="695823c3-6921-4458-b60b-2b82bbd39b8d" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.UserSecrets, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
            <add name="AzureKeyVault" vaultName="[VaultName]" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
        </builders>
      </configBuilders>
    ```
3. Specificare che la sezione appSettings sta usando il generatore di configurazione Key Vault. Assicurarsi che tutte le voci per l'impostazione segreta abbiano un valore fittizio.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Avviare il debug del progetto. Sarà eseguito correttamente.