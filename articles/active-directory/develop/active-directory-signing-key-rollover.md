---
title: Rollover della chiave di firma nella piattaforma di identità Microsoft
description: Questo articolo illustra le procedure consigliate di rollover della chiave di firma per Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: 42f100618ac6ce8769c4a7da67a5bd586794c63b
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115595"
---
# <a name="signing-key-rollover-in-microsoft-identity-platform"></a>Rollover della chiave di firma nella piattaforma di identità Microsoft
Questo articolo descrive le informazioni che è necessario conoscere sulle chiavi pubbliche usate dalla piattaforma di identità Microsoft per firmare i token di sicurezza. È importante sottolineare che queste chiavi si riattivano periodicamente e, in caso di emergenza, possono essere immediatamente sottoposte a rollback. Tutte le applicazioni che usano la piattaforma di identità Microsoft devono essere in grado di gestire il processo di rollover della chiave a livello di codice o di stabilire un processo di rollover manuale periodico. Continuare la lettura per comprendere il funzionamento delle chiavi, come valutare l'impatto del rollover nell'applicazione e come aggiornare l'applicazione o stabilire un processo di rollover manuale periodico per gestire il rollover della chiave, se necessario.

## <a name="overview-of-signing-keys-in-microsoft-identity-platform"></a>Panoramica delle chiavi di firma nella piattaforma di identità Microsoft
Microsoft Identity Platform usa la crittografia a chiave pubblica basata su standard di settore per stabilire una relazione di trust tra se stesso e le applicazioni che lo usano. In pratica, questo funziona nel modo seguente: Microsoft Identity Platform usa una chiave di firma costituita da una coppia di chiavi pubblica e privata. Quando un utente accede a un'applicazione che usa la piattaforma di identità Microsoft per l'autenticazione, Microsoft Identity Platform crea un token di sicurezza che contiene informazioni sull'utente. Questo token è firmato dalla piattaforma Microsoft Identity usando la relativa chiave privata prima che venga inviato di nuovo all'applicazione. Per verificare che il token sia valido e originato da Microsoft Identity Platform, l'applicazione deve convalidare la firma del token usando la chiave pubblica esposta dalla piattaforma di identità Microsoft contenuta nel [documento di individuazione di OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) del tenant o nel [documento di metadati della Federazione](../azuread-dev/azure-ad-federation-metadata.md)SAML/WS-Fed.

Per motivi di sicurezza, la chiave di firma della piattaforma Microsoft Identity viene sottoposta a rollup in modo periodico e, in caso di emergenza, è possibile eseguirne immediatamente il rollover. Qualsiasi applicazione che si integra con la piattaforma di identità Microsoft deve essere preparata a gestire un evento di rollover della chiave indipendentemente dalla frequenza con cui può verificarsi. Se l'applicazione non ha la logica necessaria e prova a usare una chiave scaduta per verificare la firma su un token, la richiesta di accesso avrà esito negativo.

È sempre disponibile più di una chiave valida nel documento di individuazione di OpenID Connect e nel documento di metadati della federazione. L'applicazione deve poter usare qualsiasi chiave specificata nel documento perché di una chiave può essere eseguito il rollover a breve, un'altra può essere quella sostitutiva e così via.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Come valutare se l'applicazione sarà interessata e come intervenire
Il modo in cui l'applicazione gestisce il rollover della chiave dipende da variabili come il tipo di applicazione o la libreria e il protocollo di identità usati. Le sezioni seguenti valutano se i tipi di applicazioni più comuni sono interessati dal rollover della chiave e offrono indicazioni su come aggiornare l'applicazione per supportare il rollover automatico o aggiornare la chiave manualmente.

* [Applicazioni client native che accedono alle risorse](#nativeclient)
* [API / applicazioni Web che accedono alle risorse](#webclient)
* [API / applicazioni Web che proteggono le risorse e sono state compilate con i servizi app di Azure](#appservices)
* [API/applicazioni Web che proteggono le risorse usando il middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [API/applicazioni Web che proteggono le risorse usando il middleware OpenID Connect o JwtBearerAuthentication di .NET Core](#owincore)
* [API / applicazioni Web che proteggono le risorse usando il modulo Node.js passport-azure-ad](#passport)
* [API/applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2015 o versioni successive](#vs2015)
* [Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2013](#vs2013)
* API Web che proteggono le risorse e sono state create con Visual Studio 2013
* [Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2012](#vs2012)
* [Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2010 o 2008 o con Windows Identity Foundation](#vs2010)
* [API / applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato](#other)

Queste indicazioni **non** sono valide per:

* Applicazioni aggiunte dalla raccolta di applicazioni di Azure AD (incluse quelle personalizzate), che hanno indicazioni separate per la chiave di firma. [Ulteriori informazioni.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Applicazioni locali pubblicate tramite il proxy di applicazione, che non prevedono le chiavi di firma.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Applicazioni client native che accedono alle risorse
Le applicazioni che si limitano ad accedere alle risorse, come Microsoft Graph, l'insieme di credenziali delle credenziali, l'API di Outlook e altre API Microsoft) in genere ottengono solo un token e lo passano al proprietario della risorsa. Dato che tali applicazioni non proteggono risorse, il token non viene controllato e quindi non è necessario assicurarsi che sia firmato correttamente.

Le applicazioni client native, sia per desktop che per dispositivi mobili, rientrano in questa categoria e quindi non sono interessate dal rollover.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>API / applicazioni Web che accedono alle risorse
Le applicazioni che si limitano ad accedere alle risorse, come Microsoft Graph, l'insieme di credenziali delle credenziali, l'API di Outlook e altre API Microsoft) in genere ottengono solo un token e lo passano al proprietario della risorsa. Dato che tali applicazioni non proteggono risorse, il token non viene controllato e quindi non è necessario assicurarsi che sia firmato correttamente.

Le applicazioni Web e le API Web che usano il flusso solo app (credenziali client / certificato client) rientrano in questa categoria e quindi non sono interessate dal rollover.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>API / applicazioni Web che proteggono le risorse e sono state compilate con i servizi app di Azure
La funzionalità di autenticazione / autorizzazione (EasyAuth) dei servizi app di Azure ha già la logica necessaria per gestire automaticamente il rollover della chiave.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>API / applicazioni Web che proteggono le risorse usando middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication
Se l'applicazione usa middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication, ha già la logica necessaria per gestire automaticamente il rollover della chiave.

È possibile verificare che l'applicazione usi middleware di questo tipo cercando uno dei frammenti seguenti nei file Startup.cs o Startup.Auth.cs dell'applicazione

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>API / applicazioni Web che proteggono le risorse usando middleware .NET Core OpenID Connect o JwtBearerAuthentication
Se l'applicazione usa middleware .NET Core OWIN OpenID Connect o JwtBearerAuthentication, ha già la logica necessaria per gestire automaticamente il rollover della chiave.

È possibile verificare che l'applicazione usi middleware di questo tipo cercando uno dei frammenti seguenti nei file Startup.cs o Startup.Auth.cs dell'applicazione

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>API / applicazioni Web che proteggono le risorse usando il modulo Node.js passport-azure-ad
Se l'applicazione usa il modulo Node.js passport-ad, ha già la logica necessaria per gestire automaticamente il rollover della chiave.

È possibile verificare che l'applicazione usi il modulo passport-ad cercando il frammento seguente nel file app.js dell'applicazione

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>API/applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2015 o versioni successive
Se l'applicazione è stata creata usando un modello di applicazione Web in Visual Studio 2015 o versione successiva e sono stati selezionati **account aziendali o dell'Istituto di istruzione** dal menu **Modifica autenticazione** , è già presente la logica necessaria per gestire automaticamente il rollover della chiave. Questa logica, incorporata nel middleware OWIN OpenID Connect, recupera e memorizza nella cache le chiavi dal documento di individuazione di OpenID Connect e le aggiorna periodicamente.

Se l'autenticazione è stata aggiunta alla soluzione manualmente, l'applicazione potrebbe non avare la logica di rollover della chiave necessaria. Sarà necessario scriverlo autonomamente oppure seguire i passaggi in [applicazioni/API Web usando qualsiasi altra libreria o implementando manualmente i protocolli supportati](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2013
Se l'applicazione è stata creata usando un modello di applicazione Web in Visual Studio 2013 e sono stati selezionati **account aziendali** dal menu **Modifica autenticazione**, l'applicazione ha già la logica necessaria per gestire automaticamente il rollover della chiave. Questa logica archivia l'identificatore univoco dell'organizzazione e informazioni sulla chiave di firma in due tabelle di database associate al progetto. La stringa di connessione per il database si trova nel file Web.config del progetto.

Se l'autenticazione è stata aggiunta alla soluzione manualmente, l'applicazione potrebbe non avare la logica di rollover della chiave necessaria. Sarà necessario scriverla oppure seguire i passaggi illustrati in [API/Applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato](#other).

I passaggi seguenti consentono di verificare il corretto funzionamento della logica dell'applicazione.

1. In Visual Studio 2013 aprire la soluzione, quindi fare clic sulla scheda **Esplora server** nella finestra a destra.
2. Espandere **Connessioni dati**, l'opzione relativa alla connessione predefinita****, quindi **Tabelle**. Trovare la tabella relativa alle chiavi dell'autorità di emissione****, fare clic con il pulsante destro del mouse su di essa, quindi scegliere **Mostra dati tabella**.
3. Nella tabella relativa alle chiavi dell'autorità di emissione**** è presente almeno una riga che corrisponde al valore di identificazione personale della chiave. Eliminare tutte le righe nella tabella.
4. Fare clic con il pulsante destro del mouse sulla tabella **Tenant**, quindi scegliere **Mostra dati tabella**.
5. Nella tabella **Tenant** è presente almeno una riga che corrisponde a un identificatore di tenant di directory univoco. Eliminare tutte le righe nella tabella. Se non si eliminano le righe nella tabella **Tenant** e nella tabella **IssuingAuthorityKeys**, si verificherà un errore in fase di esecuzione.
6. Compilare ed eseguire l'applicazione. Dopo aver effettuato l'accesso al proprio account sarà possibile arrestare l'applicazione.
7. Tornare a **Esplora server** ed esaminare i valori nella tabella **Tenant** e in quella relativa alle chiavi dell'autorità di emissione.**** Si noterà che sono stati popolati automaticamente con le informazioni appropriate dal documento di metadati della federazione.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>API Web che proteggono le risorse e sono state create con Visual Studio 2013
Se l'applicazione API Web è stata creata in Visual Studio 2013 tramite il modello API Web ed è stata selezionata l'opzione **Account aziendali** nel menu **Modifica autenticazione**, la logica necessaria è già disponibile nell'applicazione.

Se l'autenticazione è stata configurata manualmente, seguire le istruzioni riportate di seguito per informazioni su come configurare l'API Web per aggiornare automaticamente le informazioni sulla chiave.

Il frammento di codice seguente illustra come ottenere le chiavi più recenti dal documento di metadati della federazione e quindi usare il [gestore dei token JWT](/previous-versions/dotnet/framework/security/json-web-token-handler) per convalidare il token. Il frammento di codice presuppone che si userà il proprio meccanismo di memorizzazione nella cache per salvare in modo permanente la chiave per convalidare i token futuri dalla piattaforma di identità Microsoft, sia che si tratti di un database, di un file di configurazione o di un'altra posizione.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2012
Se l'applicazione è stata creata in Visual Studio 2012 è stato probabilmente usato lo strumento Identità e accesso per configurare l'applicazione. È anche probabile che si usi [Validating Issuer Name Registry (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). Il VINR è responsabile della gestione delle informazioni sui provider di identità attendibili (piattaforma di identità Microsoft) e delle chiavi usate per convalidare i token emessi da tali provider. VINR semplifica anche l'aggiornamento automatico delle informazioni sulla chiave archiviate in un file Web.config, scaricando il documento di metadati della federazione più recente associato alla directory, verificando se la configurazione è aggiornata con il documento più recente e aggiornando l'applicazione per l'uso della nuova chiave se necessario.

Se l'applicazione è stata creata usando uno degli esempi di codice o le procedure dettagliate offerte da Microsoft, la logica di rollover della chiave è già inclusa nel progetto. Si noterà che il codice seguente esiste già nel progetto. Se l'applicazione non ha questa logica, seguire questa procedura per aggiungerla e verificarne il corretto funzionamento.

1. In **Esplora soluzioni** aggiungere un riferimento all'assembly **System.IdentityModel** per il progetto appropriato.
2. Aprire il file **Global.asax.cs** e aggiungere le seguenti direttive using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Aggiungere il seguente metodo al file **Global.asax.cs**:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Richiamare il metodo **RefreshValidationSettings()** nel metodo **Application_Start()** in **Global.asax.cs** come illustrato di seguito:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Dopo aver eseguito questi passaggi, il file Web.config dell'applicazione verrà aggiornato con le informazioni più recenti del documento di metadati della federazione, incluse le chiavi più recenti. Questo aggiornamento verrà eseguito ogni volta che il pool di applicazioni viene riciclato in IIS. Per impostazione predefinita, IIS è impostato per il riciclo delle applicazioni ogni 29 ore.

Seguire questa procedura per verificare che la logica di rollover della chiave funzioni.

1. Dopo aver verificato che l'applicazione usi il codice indicato in precedenza, aprire il file **Web.config** e passare al blocco **\<issuerNameRegistry>**, verificando in particolare le righe seguenti:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Nell' **\<add thumbprint="">** impostazione, modificare il valore di identificazione personale sostituendo qualsiasi carattere con uno diverso. Salvare il file **Web.config**.
3. Compilare l'applicazione ed eseguirla. Se è possibile completare il processo di accesso, l'applicazione aggiorna in modo corretto la chiave scaricando le informazioni necessarie dal documento di metadati della federazione della directory. Se si verificano problemi di accesso, assicurarsi che le modifiche nell'applicazione siano corrette leggendo l'articolo [aggiungere l'accesso all'applicazione Web usando la piattaforma di identità Microsoft](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) oppure scaricare ed esaminare l'esempio di codice seguente: [applicazione cloud multi-tenant per Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2008 o 2010 o con Windows Identity Foundation (WIF) v1.0 per .NET 3.5
Se è stata compilata un'applicazione in WIF v1.0 non esistono meccanismi per aggiornare automaticamente la configurazione dell'applicazione per l'uso di una nuova chiave.

* *Modo più semplice* : usare lo strumento FedUtil incluso in WIF SDK, che può recuperare il documento di metadati più recente e aggiornare la configurazione.
* Aggiornare l'applicazione a .NET 4.5, che include la versione più recente di WIF nello spazio dei nomi System. Sarà quindi possibile usare [Validating Issuer Name Registry (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) per eseguire gli aggiornamenti automatici della configurazione dell'applicazione.
* Eseguire un rollover manuale seguendo le istruzioni alla fine di questo documento.

Istruzioni per usare FedUtil per aggiornare la configurazione:

1. Verificare di avere l'SDK WIF v1.0 installato nel computer di sviluppo per Visual Studio 2008 o 2010. È possibile [scaricarlo da qui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se non è ancora installato.
2. In Visual Studio aprire la soluzione, fare clic con il pulsante destro del mouse sul progetto applicabile, quindi scegliere **Aggiorna metadati di federazione**. Se questa opzione non è disponibile, FedUtil o l'SDK WIF v1.0 non è stato installato.
3. Quando viene richiesto, selezionare **Aggiorna** per iniziare ad aggiornare i metadati di federazione. Se si ha accesso all'ambiente server in cui è ospitata l'applicazione, è possibile usare facoltativamente l' [utilità di pianificazione dell'aggiornamento automatico dei metadati](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))di FedUtil.
4. Fare clic su **Fine** per completare il processo di aggiornamento.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>API / applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato
Se si usano altre librerie o si implementa manualmente qualsiasi protocollo supportato, sarà necessario esaminare la libreria o l'implementazione per verificare che la chiave venga recuperata dal documento di individuazione di OpenID Connect o dal documento di metadati della federazione. Un modo per eseguire la verifica è cercare nel codice o nel codice della libreria chiamate al documento di individuazione di OpenID o al documento di metadati della federazione.

Se la chiave è archiviata in una posizione qualsiasi o è hardcoded nell'applicazione, è possibile recuperarla manualmente e aggiornarla di conseguenza eseguendo un rollover manuale in base alle istruzioni disponibili alla fine di questo documento. Si consiglia **vivamente di migliorare l'applicazione in modo da supportare il rollover automatico** usando uno degli approcci descritti in questo articolo per evitare future rotture e sovraccarico se la piattaforma di identità Microsoft aumenta la cadenza di rollover o presenta un rollover fuori banda di emergenza.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Come testare l'applicazione per stabilire se sarà interessata
È possibile verificare se l'applicazione supporta il rollover automatico della chiave scaricando gli script e seguendo le istruzioni contenute in [questo repository GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Come eseguire un rollover manuale se l'applicazione non supporta il rollover automatico
Se l'applicazione non **supporta il** rollover automatico, sarà necessario definire un processo che monitora periodicamente le chiavi di firma della piattaforma Microsoft Identity ed esegue un rollover manuale di conseguenza. [Questo repository GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contiene gli script e le istruzioni necessarie.