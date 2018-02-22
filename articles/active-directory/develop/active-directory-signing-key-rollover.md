---
title: Rollover della chiave di firma in Azure AD
description: Questo articolo illustra le procedure consigliate di rollover della chiave di firma per Azure Active Directory
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5396baa57fe0b49809d9fe06eb2b2feda2ed9ba8
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Rollover della chiave di firma in Azure Active Directory
Questo articolo illustra che cosa è necessario sapere sulle chiavi pubbliche usate per la firma dei token di sicurezza in Azure Active Directory (Azure AD). È importante notare che il rollover di queste chiavi viene eseguito periodicamente e in caso di emergenza può essere eseguito immediatamente. Tutte le applicazioni che usano Azure AD devono poter gestire a livello di codice il processo di rollover della chiave o stabilire un processo di rollover manuale periodico. Continuare la lettura per comprendere il funzionamento delle chiavi, come valutare l'impatto del rollover nell'applicazione e come aggiornare l'applicazione o stabilire un processo di rollover manuale periodico per gestire il rollover della chiave, se necessario.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Informazioni generali sulle chiavi di firma in Azure AD
Azure AD usa la crittografia a chiave pubblica basata su standard di settore per stabilire una relazione di trust tra se stesso e le applicazioni che usano Azure AD. In termini pratici, Azure AD usa una chiave di firma costituita da una coppia di chiavi pubbliche e private. Quando un utente accede a un'applicazione che usa Azure AD per l'autenticazione, Azure AD crea un token di sicurezza contenente informazioni sull'utente. Questo token viene firmato da Azure AD con la chiave privata prima che venga inviato di nuovo all'applicazione. Per verificare che il token sia valido e sia stato originato da Azure AD, l'applicazione deve convalidare la firma del token usando la chiave pubblica esposta da Azure AD contenuta nel [documento di individuazione di OpenID Connect](http://openid.net/specs/openid-connect-discovery-1_0.html) del tenant o nel [documento dei metadati della federazione](active-directory-federation-metadata.md) SAML/WS-Fed.

Per motivi di sicurezza, il rollover della chiave di firma di Azure AD viene eseguito periodicamente e può essere eseguito immediatamente in caso di emergenza. Qualsiasi applicazione che si integra con Azure AD deve poter gestire un evento di rollover della chiave indipendentemente dalla frequenza con cui può verificarsi. Se l'applicazione non ha la logica necessaria e prova a usare una chiave scaduta per verificare la firma su un token, la richiesta di accesso avrà esito negativo.

È sempre disponibile più di una chiave valida nel documento di individuazione di OpenID Connect e nel documento di metadati della federazione. L'applicazione deve poter usare qualsiasi chiave specificata nel documento perché di una chiave può essere eseguito il rollover a breve, un'altra può essere quella sostitutiva e così via.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Come valutare se l'applicazione sarà interessata e come intervenire
Il modo in cui l'applicazione gestisce il rollover della chiave dipende da variabili come il tipo di applicazione o la libreria e il protocollo di identità usati. Le sezioni seguenti valutano se i tipi di applicazioni più comuni sono interessati dal rollover della chiave e offrono indicazioni su come aggiornare l'applicazione per supportare il rollover automatico o aggiornare la chiave manualmente.

* [Applicazioni client native che accedono alle risorse](#nativeclient)
* [API / applicazioni Web che accedono alle risorse](#webclient)
* [API / applicazioni Web che proteggono le risorse e sono state compilate con i servizi app di Azure](#appservices)
* [API / applicazioni Web che proteggono le risorse usando middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [API / applicazioni Web che proteggono le risorse usando middleware .NET Core OpenID Connect o JwtBearerAuthentication](#owincore)
* [API / applicazioni Web che proteggono le risorse usando il modulo Node.js passport-azure-ad](#passport)
* [API/applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2015 o Visual Studio 2017](#vs2015)
* [Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2013](#vs2013)
* [API Web che proteggono le risorse e sono state create con Visual Studio 2013](#vs2013_webapi)
* [Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2012](#vs2012)
* [Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2010 o 2008 o con Windows Identity Foundation](#vs2010)
* [API / applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato](#other)

Queste indicazioni **non** sono valide per:

* Applicazioni aggiunte dalla raccolta di applicazioni di Azure AD (incluse quelle personalizzate), che hanno indicazioni separate per la chiave di firma. [Altre informazioni.](../active-directory-sso-certs.md)
* Applicazioni locali pubblicate tramite il proxy di applicazione, che non prevedono le chiavi di firma.

### <a name="nativeclient"></a>Applicazioni client native che accedono alle risorse
Le applicazioni che si limitano ad accedere alle risorse, come Microsoft Graph, l'insieme di credenziali, l'API Outlook e altre API Microsoft, in genere ottengono soltanto un token e lo passano al proprietario della risorsa. Dato che tali applicazioni non proteggono risorse, il token non viene controllato e quindi non è necessario assicurarsi che sia firmato correttamente.

Le applicazioni client native, sia per desktop che per dispositivi mobili, rientrano in questa categoria e quindi non sono interessate dal rollover.

### <a name="webclient"></a>API / applicazioni Web che accedono alle risorse
Le applicazioni che si limitano ad accedere alle risorse, come Microsoft Graph, l'insieme di credenziali, l'API Outlook e altre API Microsoft, in genere ottengono soltanto un token e lo passano al proprietario della risorsa. Dato che tali applicazioni non proteggono risorse, il token non viene controllato e quindi non è necessario assicurarsi che sia firmato correttamente.

Le applicazioni Web e le API Web che usano il flusso solo app (credenziali client / certificato client) rientrano in questa categoria e quindi non sono interessate dal rollover.

### <a name="appservices"></a>API / applicazioni Web che proteggono le risorse e sono state compilate con i servizi app di Azure
La funzionalità di autenticazione / autorizzazione (EasyAuth) dei servizi app di Azure ha già la logica necessaria per gestire automaticamente il rollover della chiave.

### <a name="owin"></a>API / applicazioni Web che proteggono le risorse usando middleware .NET OWIN OpenID Connect, WS-Fed o WindowsAzureActiveDirectoryBearerAuthentication
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

### <a name="owincore"></a>API / applicazioni Web che proteggono le risorse usando middleware .NET Core OpenID Connect o JwtBearerAuthentication
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

### <a name="passport"></a>API / applicazioni Web che proteggono le risorse usando il modulo Node.js passport-azure-ad
Se l'applicazione usa il modulo Node.js passport-ad, ha già la logica necessaria per gestire automaticamente il rollover della chiave.

È possibile verificare che l'applicazione usi il modulo passport-ad cercando il frammento seguente nel file app.js dell'applicazione

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>API/applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2015 o Visual Studio 2017
Se l'applicazione è stata creata usando un modello di applicazione Web in Visual Studio 2015 o Visual Studio 2017 ed è stata selezionata l'opzione **Account aziendali o dell'istituto di istruzione** nel menu **Modifica autenticazione**, l'applicazione ha già la logica necessaria per gestire automaticamente il rollover della chiave. Questa logica, incorporata nel middleware OWIN OpenID Connect, recupera e memorizza nella cache le chiavi dal documento di individuazione di OpenID Connect e le aggiorna periodicamente.

Se l'autenticazione è stata aggiunta alla soluzione manualmente, l'applicazione potrebbe non avare la logica di rollover della chiave necessaria. Sarà necessario scriverla oppure seguire i passaggi illustrati in [API/Applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato](#other).

### <a name="vs2013"></a>Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2013
Se l'applicazione è stata creata usando un modello di applicazione Web in Visual Studio 2013 e sono stati selezionati **account aziendali** dal menu **Modifica autenticazione**, l'applicazione ha già la logica necessaria per gestire automaticamente il rollover della chiave. Questa logica archivia l'identificatore univoco dell'organizzazione e informazioni sulla chiave di firma in due tabelle di database associate al progetto. La stringa di connessione per il database si trova nel file Web.config del progetto.

Se l'autenticazione è stata aggiunta alla soluzione manualmente, l'applicazione potrebbe non avare la logica di rollover della chiave necessaria. Sarà necessario scriverla oppure seguire i passaggi illustrati in [API/Applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato](#other).

I passaggi seguenti consentono di verificare il corretto funzionamento della logica dell'applicazione.

1. In Visual Studio 2013 aprire la soluzione e quindi fare clic sulla scheda **Esplora Server** nella finestra di destra.
2. Espandere **Connessioni dati**, **DefaultConnection** e quindi **Tabelle**. Trovare la tabella **IssuingAuthorityKeys**, fare clic con il pulsante destro del mouse e quindi scegliere **Mostra dati tabella**.
3. Nella tabella **IssuingAuthorityKeys** sarà presente almeno una riga che corrisponde al valore di identificazione personale per la chiave. Eliminare tutte le righe nella tabella.
4. Fare clic con il pulsante destro del mouse sulla tabella **Tenant** e quindi scegliere **Mostra dati tabella**.
5. Nella tabella **Tenant** sarà presente almeno una riga che corrisponde a un identificatore di tenant directory univoco. Eliminare tutte le righe nella tabella. Se non si eliminano le righe nella tabella **Tenant** e nella tabella **IssuingAuthorityKeys**, si verificherà un errore in fase di esecuzione.
6. Compilare ed eseguire l'applicazione. Dopo aver effettuato l'accesso al proprio account sarà possibile arrestare l'applicazione.
7. Tornare a **Esplora Server** ed esaminare i valori nelle tabelle **IssuingAuthorityKeys** e **Tenant**. Si noterà che sono stati popolati automaticamente con le informazioni appropriate dal documento di metadati della federazione.

### <a name="vs2013"></a>API Web che proteggono le risorse e sono state create con Visual Studio 2013
Se è stata creata un'applicazione API Web in Visual Studio 2013 usando il modello API Web e sono stati selezionati **account aziendali** dal menu **Modifica autenticazione**, l'applicazione ha già la logica necessaria.

Se l'autenticazione è stata configurata manualmente, seguire queste istruzioni per informazioni su come configurare l'API Web per aggiornare automaticamente le informazioni sulle chiavi.

Il frammento di codice seguente illustra come ottenere le chiavi più recenti dal documento di metadati della federazione e quindi usare il [gestore dei token JWT](https://msdn.microsoft.com/library/dn205065.aspx) per convalidare il token. Il frammento di codice presuppone che si userà il proprio meccanismo di memorizzazione nella cache per mantenere la chiave per la convalida dei futuri token di Azure AD, che si trovino in un database, in un file di configurazione o altrove.

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

### <a name="vs2012"></a>Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2012
Se l'applicazione è stata creata in Visual Studio 2012 è stato probabilmente usato lo strumento Identità e accesso per configurare l'applicazione. È anche probabile che si usi [Validating Issuer Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR gestisce le informazioni sui provider di identità attendibili (Azure AD) e le chiavi usate per convalidare i token rilasciati dai provider. VINR semplifica anche l'aggiornamento automatico delle informazioni sulla chiave archiviate in un file Web.config, scaricando il documento di metadati della federazione più recente associato alla directory, verificando se la configurazione è aggiornata con il documento più recente e aggiornando l'applicazione per l'uso della nuova chiave se necessario.

Se l'applicazione è stata creata usando uno degli esempi di codice o le procedure dettagliate offerte da Microsoft, la logica di rollover della chiave è già inclusa nel progetto. Si noterà che il codice seguente esiste già nel progetto. Se l'applicazione non ha questa logica, seguire questa procedura per aggiungerla e verificarne il corretto funzionamento.

1. In **Esplora soluzioni** aggiungere un riferimento all'assembly **System.IdentityModel** per il progetto appropriato.
2. Aprire il file **Global.asax.cs** e aggiungere le direttive using seguenti:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Aggiungere il metodo seguente al file **Global.asax.cs** :
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Richiamare il metodo **RefreshValidationSettings()** nel metodo **Application_Start()** in **Global.asax.cs** come illustrato:
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

1. Dopo aver verificato che l'applicazione usi il codice indicato in precedenza, aprire il file **Web.config** e passare al blocco **<issuerNameRegistry>**, verificando in particolare le righe seguenti:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Nella tabella **<add thumbprint=””>** modificare il valore di identificazione personale sostituendo un carattere con uno diverso. Salvare il file **Web.config** .
3. Compilare l'applicazione ed eseguirla. Se è possibile completare il processo di accesso, l'applicazione aggiorna in modo corretto la chiave scaricando le informazioni necessarie dal documento di metadati della federazione della directory. In caso di problemi di accesso verificare che le modifiche nell'applicazione siano corrette leggendo l'articolo [Adding Sign-On to Your Web Application Using Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) (Aggiunta del processo di accesso nell'applicazione Web tramite Azure AD) oppure scaricando ed esaminando l'esempio di codice seguente: [Multi-Tenant Cloud Application for Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) (Applicazione cloud multi-tenant per Azure Active Directory).

### <a name="vs2010"></a>Applicazioni Web che proteggono le risorse e sono state create con Visual Studio 2008 o 2010 o con Windows Identity Foundation (WIF) v1.0 per .NET 3.5
Se è stata compilata un'applicazione in WIF v1.0 non esistono meccanismi per aggiornare automaticamente la configurazione dell'applicazione per l'uso di una nuova chiave.

* *Modo più semplice* : usare lo strumento FedUtil incluso in WIF SDK, che può recuperare il documento di metadati più recente e aggiornare la configurazione.
* Aggiornare l'applicazione a .NET 4.5, che include la versione più recente di WIF nello spazio dei nomi System. Sarà quindi possibile usare [Validating Issuer Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) per eseguire gli aggiornamenti automatici della configurazione dell'applicazione.
* Eseguire un rollover manuale seguendo le istruzioni alla fine di questo documento.

Istruzioni per usare FedUtil per aggiornare la configurazione:

1. Verificare di avere l'SDK WIF v1.0 installato nel computer di sviluppo per Visual Studio 2008 o 2010. È possibile [scaricarlo da qui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se non è ancora installato.
2. In Visual Studio aprire la soluzione, quindi fare clic con il pulsante destro del mouse sul progetto applicabile e scegliere **Update federation metadata**(Aggiorna metadati federazione). Se questa opzione non è disponibile, FedUtil o l'SDK WIF v1.0 non è stato installato.
3. Al prompt selezionare **Aggiorna** per iniziare l'aggiornamento dei metadati della federazione. Se si ha accesso all'ambiente server in cui è ospitata l'applicazione, è possibile usare facoltativamente l' [utilità di pianificazione dell'aggiornamento automatico dei metadati](https://msdn.microsoft.com/library/ee517272.aspx)di FedUtil.
4. Fare clic su **Fine** per completare il processo di aggiornamento.

### <a name="other"></a>API / applicazioni Web che proteggono le risorse usando qualsiasi altra libreria o con implementazione manuale di qualsiasi protocollo supportato
Se si usano altre librerie o si implementa manualmente qualsiasi protocollo supportato, sarà necessario esaminare la libreria o l'implementazione per verificare che la chiave venga recuperata dal documento di individuazione di OpenID Connect o dal documento di metadati della federazione. Un modo per eseguire la verifica è cercare nel codice o nel codice della libreria chiamate al documento di individuazione di OpenID o al documento di metadati della federazione.

Se la chiave è archiviata in una posizione qualsiasi o è hardcoded nell'applicazione, è possibile recuperarla manualmente e aggiornarla di conseguenza eseguendo un rollover manuale in base alle istruzioni disponibili alla fine di questo documento. **È vivamente consigliato ottimizzare l'applicazione per il supporto del rollover automatico** usando uno degli approcci descritti in questo articolo per evitare interruzioni future e sovraccarichi se Azure AD aumenta la cadenza di rollover o esegue un rollover di emergenza fuori programma.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Come testare l'applicazione per stabilire se sarà interessata
È possibile verificare se l'applicazione supporta il rollover automatico della chiave scaricando gli script e seguendo le istruzioni contenute in [questo repository GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Come eseguire un rollover manuale se l'applicazione non supporta il rollover automatico
Se l'applicazione **non** supporta il rollover automatico, sarà necessario stabilire un processo che monitori periodicamente le chiavi di firma di Azure AD ed esegua un rollover manuale di conseguenza. [Questo repository GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contiene gli script e le istruzioni necessarie.

