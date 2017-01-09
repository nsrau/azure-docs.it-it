---
title: Come eseguire un&quot;installazione invisibile all&quot;utente del connettore del proxy di applicazione di Azure AD | Documentazione Microsoft
description: Viene illustrato come eseguire un&quot;installazione invisibile all&quot;utente del connettore del proxy di applicazione di Azure AD per consentire l&quot;accesso remoto alle applicazioni locali.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 01c62e1e3074d7f9addd75ba51d6131921a4ecc7


---
# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD
Può essere necessario inviare uno script di installazione a più server Windows o ai server Windows che non hanno l'interfaccia utente abilitata. In questo argomento viene illustrato come creare uno script di Windows PowerShell che consente l'installazione automatica e installa e registra il connettore del proxy di applicazione di Azure AD.

## <a name="enabling-access"></a>Abilitazione dell'accesso
Il proxy dell’applicazione funziona mediante l'installazione di un servizio di Windows Server slim chiamato connettore all'interno della rete. Per il corretto funzionamento del connettore del proxy di applicazione, è necessario registrarlo nella directory di Azure AD con un account di amministratore globale e una password. Queste informazioni vengono in genere immesse durante l'installazione del connettore in una finestra di dialogo popup. In alternativa, è possibile usare Windows PowerShell per creare un oggetto credenziale in modo da immettere le informazioni di registrazione oppure è possibile creare un token personale e usarlo per immettere le informazioni di registrazione.

## <a name="step-1--install-the-connector-without-registration"></a>Passaggio 1: Installare il connettore senza registrazione
Per installare i file MSI del connettore senza registrare il connettore, procedere come segue:

1. Aprire un prompt dei comandi.
2. Eseguire il comando seguente in cui il parametro /q significa installazione non interattiva - durante l'installazione non verrà richiesto di accettare il contratto di licenza con l'utente finale.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>Passaggio 2: Registrare il connettore in Azure Active Directory
Questa operazione viene eseguita utilizzando uno dei metodi seguenti.

* Registrare il connettore con un oggetto credenziali di Windows PowerShell
* Registrare il connettore con un token creato offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrare il connettore con un oggetto credenziali di Windows PowerShell
1. Creare l'oggetto credenziali di Windows PowerShell usando il comando seguente e sostituendo "<username>" e "<password>" con il nome utente e la password per la directory:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script usando l'oggetto credenziali di PowerShell creato, dove $cred è il nome di tale oggetto:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrare il connettore con un token creato offline
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





1. Dopo aver ottenuto il token, creare una SecureString con il token:  <br>
   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
2. Eseguire il comando di Windows PowerShell seguente, dove SecureToken è il nome del token creato in precedenza e tenantID è la GUID del tenant:  <br>
   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="see-also"></a>Vedere anche
* [Abilitare il proxy di applicazione per Azure Active Directory](active-directory-application-proxy-enable.md)
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Nov16_HO3-->


