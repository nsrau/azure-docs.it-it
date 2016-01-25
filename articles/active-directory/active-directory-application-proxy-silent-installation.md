<properties
	pageTitle="Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD | Microsoft Azure"
	description="Viene illustrato come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD per consentire l'accesso remoto alle applicazioni locali."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="kgremban"/>

# Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD

> [AZURE.NOTE]Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Può essere necessario inviare uno script di installazione a più server Windows o ai server Windows che non hanno l'interfaccia utente abilitata. In questo argomento viene illustrato come creare uno script di Windows PowerShell che consente l'installazione automatica e installa e registra il connettore del proxy di applicazione di Azure AD.

## Abilitazione dell'accesso
Il proxy dell’applicazione funziona mediante l'installazione di un servizio di Windows Server slim chiamato connettore all'interno della rete. Per il corretto funzionamento del connettore del proxy di applicazione, è necessario registrarlo nella directory di Azure AD con un account di amministratore globale e una password. Queste informazioni vengono in genere immesse durante l'installazione del connettore in una finestra di dialogo popup. In alternativa, è possibile usare Windows PowerShell per creare un oggetto credenziali per immettere le informazioni di registrazione, oppure è possibile creare il proprio token e usarlo per immettere le informazioni di registrazione.

## Passaggio 1: Installare il connettore senza registrazione


Per installare i file MSI del connettore senza registrare il connettore, procedere come segue:


1. Aprire un prompt dei comandi.
2. Eseguire il comando seguente in cui il parametro /q significa installazione non interattiva - durante l'installazione non verrà richiesto di accettare il contratto di licenza con l'utente finale.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Passaggio 2: Registrare il connettore in Azure Active Directory
Questa operazione viene eseguita utilizzando uno dei metodi seguenti.


- Registrare il connettore con un oggetto credenziali di Windows PowerShell
- Registrare il connettore con un token creato offline

### Registrare il connettore con un oggetto credenziali di Windows PowerShell


1. Creare l'oggetto credenziali di Windows PowerShell, eseguendo il comando seguente, dove "<username>" e "<password>" devono essere sostituiti con il nome utente e la password per la directory:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Passare a **C:\\Programmi\\Microsoft AAD App Proxy Connector** ed eseguire lo script di PowerShell utilizzando le credenziali oggetto che sono state create: dove $cred è il nome delle credenziali PowerShell oggetto che sono state create:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### Registrare il connettore con un token creato offline

1. Creare un token offline con la classe AuthenticationContext, utilizzando i valori nel frammento di codice:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Dopo aver ottenuto il token, creare una SecureString con il token: <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Eseguire il comando di Windows PowerShell seguente, dove SecureToken è il nome del token creato in precedenza e tenantID è la GUID del tenant: <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## Passaggi successivi
Si può fare molto di più con il proxy dell'applicazione:


- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)


### Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
* [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
* [Identità di Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->