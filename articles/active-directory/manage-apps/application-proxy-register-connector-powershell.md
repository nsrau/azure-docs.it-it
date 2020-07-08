---
title: Installazione automatica del connettore proxy dell'app Azure AD | Microsoft Docs
description: Viene illustrato come eseguire un'installazione automatica del connettore proxy di applicazione di Azure AD per consentire l'accesso remoto alle applicazioni locali.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90c80d9edbefe9df05a5d64da612a89c3b251f
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850820"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Creare uno script di installazione automatica per il connettore del proxy di applicazione di Azure AD

Questo argomento descrive come creare uno script di Windows PowerShell che consente l'installazione automatica e la registrazione per il connettore del proxy di applicazione di Azure AD.

Questa funzionalità è utile quando si vuole:

* Installare il connettore in istanze di Windows Server che non hanno un'interfaccia utente abilitata o che non sono accessibili con Desktop remoto.
* Installare e registrare più connettori in una sola volta.
* Integrare l'installazione e la registrazione del connettore come parte di un'altra procedura.
* Creare un'immagine server standard che contiene i bit del connettore, ma che non è registrata.

Per il corretto funzionamento del [connettore del proxy di applicazione](application-proxy-connectors.md) , è necessario registrarlo con la directory Azure ad usando un amministratore e una password dell'applicazione. Queste informazioni vengono in genere immesse durante l'installazione del connettore in una finestra di dialogo popup, ma in alternativa è possibile usare PowerShell per automatizzare il processo.

Per l'installazione automatica sono previsti due passaggi. In primo luogo, installare il connettore. In secondo luogo, registrare il connettore in Azure AD.

## <a name="install-the-connector"></a>Installare il connettore
Per installare il connettore senza registrazione, seguire questa procedura:

1. Aprire un prompt dei comandi.
2. Eseguire il comando seguente, dove il parametro /q significa che l'installazione non è interattiva. Durante un'installazione di questo tipo non viene chiesto di accettare il contratto di licenza con l'utente finale.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Registrare il connettore in Azure Active Directory
Esistono due metodi da usare per registrare il connettore:

* Registrare il connettore con un oggetto credenziali di Windows PowerShell
* Registrare il connettore con un token creato offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrare il connettore con un oggetto credenziali di Windows PowerShell
1. Creare un oggetto credenziali di Windows PowerShell `$cred` che contiene un nome utente e una password di amministratore per la directory. Eseguire il comando seguente, sostituendo *\<username\>* e *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Passare a **C:\Programmi\Microsoft AAD App Proxy Connector** ed eseguire lo script seguente usando l'oggetto `$cred` creato:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrare il connettore con un token creato offline
1. Creare un token offline usando la classe AuthenticationContext tramite i valori in questo frammento di codice il Cmdlet PowerShell riportato di seguito:

   **Uso di C#:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **Usando PowerShell:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Dopo aver ottenuto il token, creare una SecureString con il token:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Eseguire il comando di Windows PowerShell seguente, sostituendo \<tenant GUID\> con l'ID directory:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Passaggi successivi
* [Pubblicare applicazioni mediante il proprio nome di dominio](application-proxy-configure-custom-domain.md)
* [Abilitare l'accesso Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)
* [Risolvere i problemi che si verificano con il proxy di applicazione](application-proxy-troubleshoot.md)
