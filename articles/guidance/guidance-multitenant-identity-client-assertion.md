<properties
   pageTitle="Uso di asserzioni client per ottenere token di accesso da Azure AD | Microsoft Azure"
   description="Come usare le asserzioni client per ottenere token di accesso da Azure AD."
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

# Uso di asserzioni client per ottenere token di accesso da Azure AD

Questo articolo fa [parte di una serie]. Per questa serie è anche disponibile un'[applicazione di esempio] completa.

## Contesto

Quando si usa il flusso del codice di autorizzazione o il flusso ibrido in OpenID Connect, il client scambia un codice di autorizzazione con un token di accesso. Durante questo passaggio, il client deve autenticarsi al server.

![Segreto client](media/guidance-multitenant-identity/client-secret.png)

Una modalità per l'autenticazione del client è l'uso del segreto client. In questo modo, l'applicazione [Tailspin Surveys][Surveys] viene configurata per impostazione predefinita.

Ecco una richiesta di esempio dal client al provider di identità per la richiesta di un token di accesso. Si noti il parametro `client_secret`.

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

Il segreto è una stringa, è quindi necessario assicurarsi di non perdere il valore. La procedura consigliata consiste nel mantenere il segreto client fuori dal controllo del codice sorgente. Quando si distribuisce in Azure, memorizzare il segreto in un'[impostazione dell'app][configure-web-app].

Chiunque abbia accesso alla sottoscrizione di Azure, tuttavia, può visualizzare le impostazioni dell'app. È inoltre sempre possibile verificare i segreti nel controllo del codice sorgente, ad esempio negli script di distribuzione, condividerli tramite posta elettronica e così via.

Per una maggiore sicurezza, è possibile usare l'_asserzione client_ anziché un segreto client. Con l'asserzione client, il client usa un certificato X.509 per dimostrare che la richiesta del token proviene dal client. Il certificato client è installato nel server Web. In genere, risulta più semplice limitare l'accesso al certificato, che assicurarsi che nessuno riveli inavvertitamente un segreto client.

Ecco una richiesta di token mediante un'asserzione client:

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

Si noti che non viene più usato il parametro `client_secret`. Il parametro `client_assertion`, invece, contiene un token JWT firmato mediante il certificato client. Il parametro `client_assertion_type` specifica il tipo di asserzione , in questo caso il token JWT. Il server convalida il token JWT. Se il token JWT non è valido, la richiesta del token restituisce un errore.

> [AZURE.NOTE] I certificati X.509 non rappresentano l'unico esempio di asserzioni client. In questo articolo, vengono descritti tali certificati in quanto supportati da Azure AD.

## Uso di asserzioni client nell'applicazione Surveys

Questa sezione illustra come configurare l'applicazione Tailspin Surveys per l'uso dell'asserzione client. In questi passaggi, verrà generato un certificato autofirmato adatto allo sviluppo, ma non alla fase di produzione.

1. Eseguire lo script PowerShell [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault], come indicato di seguito:

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    Per il parametro `Subject` immettere qualsiasi nome, ad esempio "surveysapp". Lo script genera un certificato autofirmato e lo archivia nell'archivio certificati "Utente corrente/Personale".

2. L'output dallo script è un frammento JSON. Aggiungere il frammento al manifesto dell'applicazione dell'app Web, come indicato di seguito:

    1. Accedere al [portale di gestione di Azure][azure-management-portal] e passare alla directory di Azure AD.

    2. Fare clic su **Applicazioni**.

    3. Selezionare l'applicazione Surveys.

    4.	Fare clic su **Gestisci manifesto** e selezionare **Scarica manifesto**.

    5.	Aprire il file JSON del manifesto in un editor di testo. Incollare l'output dello script nella proprietà `keyCredentials`. Il codice dovrebbe essere simile al seguente:

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

    6.	Salvare le modifiche apportate al file JSON.

    7.	Tornare al portale. Fare clic su **Gestisci manifesto** > **Carica manifesto** e caricare il file JSON.

3. Eseguire il comando seguente per ottenere l'identificazione personale del certificato.

    ```
    certutil -store -user my [subject]
    ```

    dove `[subject]` è il valore specificato per Subject nello script di PowerShell. L'identificazione personale è elencata in "Cert Hash(sha1)". Rimuovere gli spazi tra i numeri esadecimali.

4. Aggiornare il segreto dell'app. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto Tailspin.Surveys.Web e scegliere **Gestisci segreti utente**. Aggiungere una voce per "Asymmetric" in "AzureAd", come illustrato di seguito:

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    È necessario impostare `ValidationRequired` su false, poiché il certificato non è un oggetto firmato da un'autorità di certificazione principale. In fase di produzione, usare un certificato firmato da un'autorità di certificazione e impostare `ValidationRequired` su true.

    Eliminare inoltre la voce relativa a `ClientSecret`, poiché non è necessaria con l'asserzione client.

5. In Startup.cs individuare il codice per la registrazione di `ICredentialService`. Rimuovere il commento dalla riga che usa `CertificateCredentialService` e dalla riga che usa `ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

In fase di esecuzione, l'applicazione Web legge il certificato dall'archivio certificati. Il certificato deve essere installato nello stesso computer dell'app Web.

## Risorse aggiuntive

- [Uso dei certificati nelle applicazioni di Siti Web di Azure][using-certs-in-websites]
- [RFC 7521][RFC7521]. Definisce il meccanismo generale per l'invio di un'asserzione client.
- [RFC 7523][RFC7523]. Definisce la modalità d'uso di token JWT per l'asserzione client.


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[RFC7521]: https://tools.ietf.org/html/rfc7521
[RFC7523]: https://tools.ietf.org/html/rfc7523
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[parte di una serie]: guidance-multitenant-identity.md
[applicazione di esempio]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->