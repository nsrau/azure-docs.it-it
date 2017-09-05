---
title: Installazione automatica del connettore proxy dell'app Azure AD | Microsoft Docs
description: Viene illustrato come eseguire un'installazione automatica del connettore proxy di applicazione di Azure AD per consentire l'accesso remoto alle applicazioni locali.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 9e28c89d8f64f0ae3d4150017ca544e606075c45
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="silently-install-the-azure-ad-application-proxy-connector"></a>Installazione automatica del connettore proxy di applicazione di Azure AD
Può essere necessario inviare uno script di installazione a più server Windows o ai server Windows che non hanno l'interfaccia utente abilitata. In questo argomento viene illustrato come creare uno script di Windows PowerShell che consente l'installazione automatica e la registrazione del connettore proxy di applicazione di Azure AD.

Questa funzionalità è utile quando si vuole:

* Installare il connettore in computer senza alcun livello di interfaccia utente o quando non è possibile usare il protocollo RDP del computer.
* Installare e registrare più connettori in una sola volta.
* Integrare l'installazione e la registrazione del connettore come parte di un'altra procedura.
* Creare un'immagine server standard che contiene i bit del connettore, ma che non è registrata.

Il proxy dell’applicazione funziona mediante l'installazione di un servizio di Windows Server slim chiamato connettore all'interno della rete. Per il corretto funzionamento del connettore del proxy di applicazione, è necessario registrarlo nella directory di Azure AD con un account di amministratore globale e una password. Queste informazioni vengono in genere immesse durante l'installazione del connettore in una finestra di dialogo popup. Tuttavia, è possibile usare Windows PowerShell per creare un oggetto credenziale in modo da immettere le informazioni di registrazione. Oppure è possibile creare un token personale e usarlo per immettere le informazioni di registrazione.

## <a name="install-the-connector"></a>Installare il connettore
Per installare i file MSI del connettore senza registrare il connettore, procedere come segue:

1. Aprire un prompt dei comandi.
2. Eseguire il comando seguente in cui il parametro /q significa installazione non interattiva. Durante l'installazione non viene richiesto di accettare il contratto di licenza con l'utente finale.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrare il connettore in Azure Active Directory
Esistono due metodi da usare per registrare il connettore:

* Registrare il connettore con un oggetto credenziali di Windows PowerShell
* Registrare il connettore con un token creato offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrare il connettore con un oggetto credenziali di Windows PowerShell
1. Creare l'oggetto credenziali di Windows PowerShell eseguendo questo comando. Sostituire *\<username\>* e *\<password\>* con il nome utente e la password per la directory:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script usando l'oggetto credenziali di PowerShell creato. Sostituire *$cred* con il nome dell'oggetto credenziali di PowerShell creato:
   
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
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

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


2. Dopo aver ottenuto il token, creare una SecureString con il token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Eseguire il comando Windows PowerShell seguente, sostituendo \<tenant GUID\> con l'ID della directory:

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>Passaggi successivi 
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)



