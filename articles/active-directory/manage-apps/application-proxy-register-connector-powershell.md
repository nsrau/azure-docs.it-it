---
title: Installazione automatica del connettore proxy dell'app Azure AD | Microsoft Docs
description: Viene illustrato come eseguire un'installazione automatica del connettore proxy di applicazione di Azure AD per consentire l'accesso remoto alle applicazioni locali.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb193119186c2cf9e758f8c74f99f18c5fb389b8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58792519"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Creare uno script di installazione automatica per il connettore del proxy di applicazione di Azure AD

Questo argomento descrive come creare uno script di Windows PowerShell che consente l'installazione automatica e la registrazione per il connettore del proxy di applicazione di Azure AD.

Questa funzionalità è utile quando si vuole:

* Installare il connettore in istanze di Windows Server che non hanno un'interfaccia utente abilitata o che non sono accessibili con Desktop remoto.
* Installare e registrare più connettori in una sola volta.
* Integrare l'installazione e la registrazione del connettore come parte di un'altra procedura.
* Creare un'immagine server standard che contiene i bit del connettore, ma che non è registrata.

Per il [connettore Proxy di applicazione](application-proxy-connectors.md) per funzionare, deve essere registrato con la directory di Azure AD tramite un amministratore dell'applicazione e una password. Queste informazioni vengono in genere immesse durante l'installazione del connettore in una finestra di dialogo popup, ma in alternativa è possibile usare PowerShell per automatizzare il processo.

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
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrare il connettore con un token creato offline
1. Creare un token offline usando la classe AuthenticationContext tramite i valori in questo frammento di codice il Cmdlet PowerShell riportato di seguito:

    **Tramite C#:**

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

    **Tramite PowerShell**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Dopo aver ottenuto il token, creare una SecureString con il token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Eseguire il comando Windows PowerShell seguente, sostituendo \<tenant GUID\> con l'ID della directory:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Passaggi successivi 
* [Pubblicare applicazioni mediante il proprio nome di dominio](application-proxy-configure-custom-domain.md)
* [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](application-proxy-troubleshoot.md)


