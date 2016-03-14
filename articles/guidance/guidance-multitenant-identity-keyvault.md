<properties
   pageTitle="Uso dell'insieme di credenziali delle chiavi per proteggere i segreti dell'applicazione | Microsoft Azure"
   description="Come usare il servizio insieme di credenziali delle chiavi per archiviare i segreti dell'applicazione"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Uso dell'insieme di credenziali delle chiavi di Azure per proteggere i segreti dell'applicazione

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

## Panoramica

Alcune impostazioni dell'applicazione sono sensibili e devono essere protette, ad esempio:

- Stringhe di connessione del database
- Password
- Chiavi crittografiche

Per una sicurezza ottimale, è consigliabile non archiviare mai questi segreti nel controllo del codice sorgente. È infatti possibile che vengano diffusi con grande facilità, anche se l'archivio del codice sorgente è privato. Il problema non è costituito soltanto dalla necessità di non diffondere i segreti al pubblico generale. Nei progetti di grandi dimensioni è possibile che si voglia limitare l'accesso ai segreti di produzione solo ad alcuni sviluppatori e operatori. Le impostazioni per gli ambienti di test o di sviluppo sono diverse.

Una opzione più sicura consiste nell'archiviare questi segreti nell'[insieme di credenziali delle chiavi di Azure][KeyVault]. L'insieme di credenziali delle chiavi è un servizio ospitato su cloud per la gestione delle chiavi crittografiche e di altri segreti. Questo articolo illustra come usare l'insieme di credenziali delle chiavi per archiviare le impostazioni di configurazione per l'app.

Nell'applicazione [Tailspin Surveys][Surveys] le impostazioni seguenti sono segrete:

- Stringa di connessione del database.
- Stringa di connessione Redis.
- Segreto client per l'applicazione Web.

Per archiviare i segreti di configurazione nell'insieme di credenziali delle chiavi, l'applicazione Surveys implementa un provider di configurazione personalizzato, che si aggancia al [sistema di configurazione][configuration] di ASP.NET Core 1.0. Il provider personalizzato legge le impostazioni di configurazione dall'insieme di credenziali delle chiavi all'avvio.

L'applicazione Surveys carica le impostazioni di configurazione dalle posizioni seguenti:

- File appsettings.json
- [Archivio dei segreti utente][user-secrets] (solo ambiente di sviluppo per finalità di test)
- Ambiente di hosting (impostazioni delle app nelle app Web di Azure)
- Insieme di credenziali delle chiavi

Ogni impostazione esegue l'override della precedente, quindi eventuali impostazioni archiviate nell'insieme di credenziali delle chiavi hanno precedenza sulle altre.

> [AZURE.NOTE] Per impostazione predefinita, il provider di configurazione dell'insieme di credenziali delle chiavi è disabilitato. Non è necessario per l'esecuzione locale dell'applicazione. Occorre abilitarlo in un ambiente di produzione.

> [AZURE.NOTE] Il provider dell'insieme di credenziali delle chiavi non è attualmente supportato per .NET Core, perché richiede il pacchetto [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault].

All'avvio l'applicazione legge le impostazioni da ogni provider di configurazione registrato e le usa per popolare un oggetto opzioni fortemente tipizzato. Per altre informazioni, vedere [Uso di oggetti opzioni e di configurazione][options].

## Implementazione

La classe [KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] è un provider di configurazione che si collega al [sistema di configurazione][configuration] di ASP.NET Core 1.0.

Per usare `KeyVaultConfigurationProvider`, chiamare il metodo di estensione `AddKeyVaultSecrets` nella classe di avvio:

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

Si noti che `KeyVaultConfigurationProvider` richiede alcune impostazioni di configurazione, che devono essere archiviate in una delle altre origini di configurazione.

All'avvio dell'applicazione, `KeyVaultConfigurationProvider` enumera tutti i segreti nell'insieme di credenziali delle chiavi, cercando per ogni segreto un tag denominato 'ConfigKey'. Il valore del tag è il nome dell'impostazione di configurazione.

> [AZURE.NOTE] [Tags]I valori [key-tag] sono metadati facoltativi archiviati con una chiave. I tag vengono usati in questo caso perché i nomi delle chiavi non possono contenere caratteri di due punti (:).

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] Vedere [KeyVaultConfigurationProvider.cs].

## Configurazione dell'insieme di credenziali delle chiavi nell'app Surveys

Prerequisiti:

- Installare i [Cmdlet di Azure Resource Manager][azure-rm-cmdlets].
- Configurare l'applicazione Surveys come illustrato nell'articolo relativo all'[esecuzione dell'applicazione Surveys][readme].

Procedure generali:

1. Configurare un utente amministratore nel tenant.
2. Configurare un certificato client.
3. Creare un insieme di credenziali delle chiavi.
4. Aggiungere le impostazioni di configurazione all'insieme di credenziali delle chiavi.
5. Rimuovere il commento dal codice che abilita l'insieme di credenziali delle chiavi.
6. Aggiornare i segreti utente dell'applicazione.

### Configurare un utente amministratore

> [AZURE.NOTE] Per creare un insieme di credenziali delle chiavi, è necessario usare un account che può gestire la sottoscrizione di Azure. Eventuali applicazioni autorizzate a leggere dall'insieme di credenziali delle chiavi, inoltre, devono essere registrate nello stesso tenant in cui si trova l'account.

In questo passaggio si verifica che sia possibile creare un insieme di credenziali delle chiavi quando si accede come utente dal tenant in cui è registrata l'app Surveys.

Cambiare prima di tutto la directory associata alla sottoscrizione di Azure.

1. Accedere al [portale di gestione di Azure][azure-management-portal].

2. Fare clic su **Impostazioni**.

    ![Impostazioni](media/guidance-multitenant-identity/settings.png)

3. Selezionare la sottoscrizione di Azure.

4. Fare clic su **Modifica directory** nella parte inferiore del portale.

    ![Impostazioni](media/guidance-multitenant-identity/edit-directory.png)

5. In "Modifica directory associata" selezionare il tenant di Azure AD in cui è registrata l'applicazione Surveys,

    ![Impostazioni](media/guidance-multitenant-identity/edit-directory2.png)

6. Fare clic sul pulsante con la freccia e completare la finestra di dialogo.

Creare un utente amministratore nel tenant di Azure AD in cui è registrata l'applicazione Surveys.

1. Accedere al [portale di gestione di Azure][azure-management-portal].

2. Selezionare il tenant di Azure AD in cui è registrata l'applicazione.

3. Fare clic su **Utenti** > **Aggiungi utente**.

4. Nella finestra di dialogo **Aggiungi utente** assegnare l'utente al ruolo Amministratore globale.

Aggiungere l'utente amministratore come coamministratore per la sottoscrizione di Azure.

1. Accedere al [portale di gestione di Azure][azure-management-portal].

2. Fare clic su **Impostazioni** e selezionare la sottoscrizione di Azure.

3. Fare clic su **Amministratori**.

4. Fare clic su **Aggiungi** nella parte inferiore del portale.

5. Immettere l'indirizzo di posta elettronica dell'utente amministratore creato in precedenza.

6. Selezionare la casella di controllo per la sottoscrizione.

7. Fare clic sul segno di spunta per completare la finestra di dialogo.

![Aggiungere un coamministratore](media/guidance-multitenant-identity/co-admin.png)


### Configurare un certificato client

1. Eseguire lo script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault], come indicato di seguito:
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    Per il parametro `Subject` immettere qualsiasi nome, ad esempio "surveysapp". Lo script genera un certificato autofirmato e lo archivia nell'archivio certificati "Utente corrente/Personale".

2. L'output dallo script è un frammento JSON. Aggiungere il frammento al manifesto dell'applicazione dell'app Web, come indicato di seguito:

    1. Accedere al [portale di gestione di Azure][azure-management-portal] e passare alla directory di Azure AD.

    2. Fare clic su **Applicazioni**.

    3. Selezionare l'applicazione Surveys.

    4.	Fare clic su **Gestisci manifesto** e selezionare **Scarica manifesto**.

    5.	Aprire il file JSON del manifesto in un editor di testo. Incollare l'output dello script nella proprietà `keyCredentials`. Dovrebbe essere simile a quello riportato di seguito:
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	Salvare le modifiche al file JSON.

    7.	Tornare al portale. Fare clic su **Gestisci manifesto** > **Carica manifesto** e caricare il file JSON.

3. Aggiungere lo stesso frammento JSON al manifesto dell'applicazione dell'API Web (Surveys.WebAPI).

4. Eseguire il comando seguente per ottenere l'identificazione personale del certificato.
    ```
    certutil -store -user my [subject]
    ```
    dove `[subject]` è il valore specificato per Subject nello script di PowerShell. L'identificazione personale è elencata in "Cert Hash(sha1)". Rimuovere gli spazi tra i numeri esadecimali.

L'identificazione personale verrà usata in seguito.

### Creare un insieme di credenziali delle chiavi

1. Eseguire lo script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault], come indicato di seguito:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    Quando vengono richieste le credenziali, accedere come l'utente di Azure AD creato in precedenza. Lo script crea un nuovo gruppo di risorse e un nuovo insieme di credenziali delle chiavi in tale gruppo di risorse.

    Nota: per il parametro -Location è possibile usare il comando di PowerShell seguente, che consente di ottenere un elenco di aree valide:

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. Eseguire di nuovo SetupKeyVault.ps con i parametri seguenti:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    dove

    - key vault name: nome assegnato all'insieme di credenziali delle chiavi nel passaggio precedente.
    - web app client ID: ID client per l'applicazione Web Surveys.
    - web api client ID: ID client per l'applicazione Surveys.WebAPI.

    Esempio:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] È possibile ottenere gli ID client dal [portale di gestione di Azure][azure-management-portal]. Selezionare il tenant di Azure AD, selezionare l'applicazione e fare clic su **Configura**.

    Lo script autorizza l'app Web e l'API Web a recuperare segreti dall'insieme di credenziali delle chiavi. Per altre informazioni, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure][authorize-app].

### Aggiungere le impostazioni di configurazione all'insieme di credenziali delle chiavi

1. Eseguire SetupKeyVault.ps come indicato di seguito:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    dove

    - key vault name: nome assegnato all'insieme di credenziali delle chiavi nel passaggio precedente.
    - Redis DNS name: nome DNS dell'istanza della cache Redis.
    - Redis access key: chiave di accesso per l'istanza della cache Redis.

    Questo comando aggiunge un segreto all'insieme di credenziali delle chiavi. Il segreto è costituito da una coppia nome/valore e da un tag:

    -	Il nome della chiave non viene usato dall'applicazione, ma deve essere univoco nell'insieme di credenziali delle chiavi.
    -	Il valore corrisponde al valore dell'opzione di configurazione, ovvero, in questo caso, alla stringa di connessione Redis.
    -	Il tag "ConfigKey" include il nome della chiave di configurazione.

2. A questo punto è consigliabile verificare se i segreti sono stati archiviati correttamente nell'insieme di credenziali delle chiavi. Eseguire il seguente comando PowerShell:

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    L'output deve mostrare il valore del segreto e alcuni metadati:

    ![Output di PowerShell](media/guidance-multitenant-identity/get-secret.png)

3. Eseguire di nuovo SetupKeyVault.ps per aggiungere la stringa di connessione del database:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    dove `<<DB connection string>>` è il valore della stringa di connessione del database.

    Per il test con il database locale, copiare la stringa di connessione dal file Tailspin.Surveys.Web/appsettings.json. Se si esegue questa operazione, assicurarsi di modificare la doppia barra rovesciata ('\\\') in una singola barra rovesciata. La doppia barra rovesciata è un carattere di escape nel file JSON.

    Esempio:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### Rimuovere i commenti dal codice che abilita l'insieme di credenziali delle chiavi

1. Aprire la soluzione Tailspin.Surveys.

2. In [Tailspin.Surveys.Web/Startup.cs][web-startup] individuare il blocco di codice seguente e rimuovere il commento.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. In [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup] individuare il blocco di codice seguente e rimuovere il commento.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. In [Tailspin.Surveys.Web/Startup.cs][web-startup] individuare il codice per la registrazione di `ICredentialService`. Rimuovere il commento dalla riga che usa `CertificateCredentialService` e dalla riga che usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    Questa modifica consente all'app Web di usare l'[asserzione client][client-assertion] per ottenere i token di accesso OAuth. Se si usa l'asserzione client, non è necessario alcun segreto client OAuth. In alternativa, è possibile archiviare il segreto client in un insieme di credenziali delle chiavi. Sia l'insieme di credenziali delle chiavi che l'asserzione client, tuttavia, usano un certificato client. Se quindi si abilita l'insieme di credenziali delle chiavi, è consigliabile abilitare anche l'asserzione client.

### Aggiornare i segreti utente

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.Web e scegliere **Gestisci segreti utente**. Nel file secrets.json eliminare il codice JSON esistente e incollare il codice seguente:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

Sostituire le voci racchiuse tra [parentesi quadre] con i valori corretti.

- `AzureAd:ClientId`: ID client dell'app Surveys.
- `AzureAd:WebApiResourceId`: URI ID app specificato durante la creazione dell'applicazione Surveys.WebAPI in Azure AD.
- `Asymmetric:CertificateThumbprint`: identificazione personale del certificato ottenuta in precedenza, durante la creazione del certificato client.
- `KeyVault:Name`: nome dell'insieme di credenziali delle chiavi.

> [AZURE.NOTE] `Asymmetric:ValidationRequired` è False perché il certificato creato in precedenza non è firmato dall'autorità di certificazione radice. In produzione usare un certificato firmato da un'autorità di certificazione e impostare `ValidationRequired` su True.

Salvare il file secrets.json aggiornato.

In Esplora soluzioni fare quindi clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.WebApi e scegliere **Gestisci segreti utente**. Eliminare il codice JSON esistente e incollare il codice seguente:

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

Sostituire le voci tra [parentesi quadre] e salvare il file secrets.json.

> [AZURE.NOTE] Per l'API Web assicurarsi di usare l'ID client per l'applicazione Surveys.WebAPI, non l'applicazione Surveys.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[key-tag]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[parte di una serie]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->