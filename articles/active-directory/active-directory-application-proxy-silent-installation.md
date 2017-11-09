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
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf3058832ba656a1a18aea194bf4e5ce4e800e76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Creare uno script di installazione automatica per il connettore del proxy di applicazione di Azure AD

Questo argomento descrive come creare uno script di Windows PowerShell che consente l'installazione automatica e la registrazione per il connettore del proxy di applicazione di Azure AD.

Questa funzionalità è utile quando si vuole:

* Installare il connettore in istanze di Windows Server che non hanno un'interfaccia utente abilitata o che non sono accessibili con Desktop remoto.
* Installare e registrare più connettori in una sola volta.
* Integrare l'installazione e la registrazione del connettore come parte di un'altra procedura.
* Creare un'immagine server standard che contiene i bit del connettore, ma che non è registrata.

Per il corretto funzionamento del [connettore del proxy di applicazione](application-proxy-understand-connectors.md), è necessario registrarlo nella directory di Azure AD usando un nome utente e una password di amministratore globale. Queste informazioni vengono in genere immesse durante l'installazione del connettore in una finestra di dialogo popup, ma in alternativa è possibile usare PowerShell per automatizzare il processo.

Per l'installazione automatica sono previsti due passaggi. In primo luogo, installare il connettore. In secondo luogo, registrare il connettore in Azure AD. 

## <a name="install-the-connector"></a>Installare il connettore
Per installare il connettore senza registrazione, seguire questa procedura:

1. Aprire un prompt dei comandi.
2. Eseguire il comando seguente, dove il parametro /q significa che l'installazione non è interattiva. Durante un'installazione di questo tipo non viene chiesto di accettare il contratto di licenza con l'utente finale.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrare il connettore in Azure Active Directory
Esistono due metodi da usare per registrare il connettore:

* Registrare il connettore con un oggetto credenziali di Windows PowerShell
* Registrare il connettore con un token creato offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrare il connettore con un oggetto credenziali di Windows PowerShell
1. Creare un oggetto credenziali di Windows PowerShell `$cred` che contiene un nome utente e una password di amministratore per la directory. Eseguire il comando seguente sostituendo *\<username\>* e *\<password\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script seguente usando l'oggetto `$cred` creato:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrare il connettore con un token creato offline
1. Creare un token offline usando la classe AuthenticationContext con i valori in questo frammento di codice:

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


