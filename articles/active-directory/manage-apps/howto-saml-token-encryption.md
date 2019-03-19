---
title: Crittografia dei token SAML in Azure Active Directory
description: Informazioni su come configurare la crittografia dei token SAML in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: celested
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a7a12cf677661c36a42df36a8fdc6b8f4a8ef75
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669891"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Procedura: Configurare la crittografia dei token SAML di Azure AD (anteprima)

> [!NOTE]
> La crittografia dei token è una funzionalità Premium di Azure Active Directory (Azure AD). Per altre informazioni sulle edizioni, le funzionalità e i prezzi di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

La crittografia dei token SAML consente di usare asserzioni SAML crittografate con un'applicazione che la supporta. Quando è configurata per un'applicazione, Azure AD crittografa le asserzioni SAML che emette per l'applicazione usando la chiave pubblica ottenuta da un certificato archiviato in Azure AD. L'applicazione deve usare la chiave privata corrispondente per decrittografare il token in modo da poterlo usare come evidenza di autenticazione per l'utente connesso.

La crittografia delle asserzioni SAML tra Azure AD e l'applicazione fornisce un'ulteriore garanzia che il contenuto del token non possa essere intercettato e che i dati personali o aziendali non possano essere compromessi.

Anche senza crittografia dei token, i token SAML di Azure AD non vengono mai passati in rete in chiaro. Azure AD richiede che gli scambi di richiesta-risposta dei token avvengano su canali HTTPS/TLS crittografati in modo che le comunicazioni tra l'IDP, il browser e l'applicazione abbiano luogo attraverso collegamenti crittografati. Considerare il valore della crittografia dei token per la propria situazione specifica rispetto al sovraccarico di gestione di certificati aggiuntivi.   

Per configurare la crittografia dei token, è necessario caricare un file di certificato X.509 contenente la chiave pubblica nell'oggetto applicazione di Azure AD che rappresenta l'applicazione. È possibile scaricare il certificato X.509 dall'applicazione stessa oppure ottenerlo dal fornitore dell'applicazione, qualora quest'ultimo fornisca le chiavi di crittografia. Se l'applicazione si aspetta che l'utente fornisca una chiave privata, è possibile crearla usando strumenti di crittografia, quindi caricare la parte relativa alla chiave privata nell'archivio chiavi dell'applicazione e il certificato di chiave pubblica corrispondente in Azure AD.

Azure AD usa AES-256 per crittografare i dati delle asserzioni SAML.

## <a name="configure-saml-token-encryption"></a>Configurare la crittografia dei token SAML

Per configurare la crittografia dei token SAML, seguire questa procedura:

1. Ottenere un certificato di chiave pubblica corrispondente a una chiave privata configurata nell'applicazione.

    Creare una coppia di chiavi asimmetriche da usare per la crittografia. Oppure, se l'applicazione fornisce una chiave pubblica da usare per la crittografia, seguire le istruzioni dell'applicazione per scaricare il certificato X.509.

    La chiave pubblica deve essere archiviata in un file di certificato X.509 nel formato con estensione cer.

    Se l'applicazione usa una chiave creata dall'utente per la propria istanza, seguire le istruzioni fornite dall'applicazione per installare la chiave privata che verrà usata dall'applicazione per decrittografare i token dal tenant di Azure AD.

1. Aggiungere il certificato alla configurazione dell'applicazione in Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Per configurare la crittografia dei token nel portale di Azure

È possibile aggiungere il certificato pubblico alla configurazione dell'applicazione tramite il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare al pannello **Azure Active Directory > Applicazioni aziendali** e selezionare l'applicazione per la quale si vuole configurare la crittografia dei token.

1. Nella pagina dell'applicazione selezionare **Crittografia di token**.

    ![Opzione Crittografia di token nel portale di Azure](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > L'opzione **Crittografia di token** è disponibile solo per le applicazioni SAML che sono state configurate dal pannello **Applicazioni aziendali** nel portale di Azure e vale sia per le app della raccolta di applicazioni che per le app che non fanno parte della raccolta. Per altre applicazioni questa opzione di menu è disabilitata. Per le applicazioni registrate tramite l'esperienza **Registrazioni app** nel portale di Azure è possibile configurare la crittografia per i token SAML usando il manifesto dell'applicazione, con Microsoft Graph o PowerShell.

1. Nella pagina **Crittografia di token** selezionare **Importa certificato** per importare il file con estensione cer che contiene il certificato X.509 pubblico.

    ![Importare il file con estensione cer che contiene il certificato X.509](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Dopo aver importato il certificato e aver configurato la chiave privata per l'uso sul lato applicazione, attivare la crittografia selezionando **...** accanto allo stato dell'identificazione personale, quindi selezionare **Attiva certificato per la crittografia di token** dal menu a discesa.

1. Selezionare **Sì** per confermare l'attivazione del certificato per la crittografia di token.

1. Verificare che le asserzioni SAML emesse per l'applicazione siano crittografate.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Per disattivare la crittografia dei token nel portale di Azure

1. Nel portale di Azure passare a **Azure Active Directory > Applicazioni aziendali** e selezionare l'applicazione per la quale è abilitata la crittografia dei token SAML.

1. Nella pagina dell'applicazione selezionare **Crittografia di token**, trovare il certificato e quindi selezionare l'opzione **...** per visualizzare il menu a discesa.

1. Selezionare **Disattiva certificato per la crittografia di token**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Configurare la crittografia dei token SAML usando l'API Graph, PowerShell o il manifesto dell'applicazione

I certificati di crittografia vengono archiviati nell'oggetto applicazione in Azure AD con un tag di utilizzo `encrypt`. È possibile configurare più certificati di crittografia. Quello attivo per la crittografia dei token è identificato dall'attributo `tokenEncryptionKeyID`.

Per configurare la crittografia dei token tramite l'API Graph o PowerShell è necessario l'ID oggetto dell'applicazione. È possibile trovare questo valore a livello di codice oppure nella pagina **Proprietà** del portale di Azure, nel campo **ID oggetto**.

Quando si configura un oggetto keyCredential tramite Graph, PowerShell o il manifesto dell'applicazione, occorre generare un GUID da usare per il keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Per configurare la crittografia dei token tramite Microsoft Graph

1. Aggiornare l'oggetto `keyCredentials` dell'applicazione con un certificato X.509 per la crittografia. L'esempio seguente illustra come farlo.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identificare il certificato di crittografia attivo per la crittografia dei token. L'esempio seguente illustra come farlo.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Per configurare la crittografia dei token tramite PowerShell

Questa funzionalità sarà disponibile a breve. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials “<KeyCredentialsObject>”  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```
-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Per configurare la crittografia dei token tramite il manifesto dell'applicazione

1. Nel portale di Azure passare a **Azure Active Directory > Registrazioni app**.

1. Selezionare **Tutte le app** dal menu a discesa e quindi selezionare l'applicazione aziendale da configurare.

1. Nella pagina dell'applicazione selezionare **Manifesto** per modificare il [manifesto dell'applicazione](../develop/reference-app-manifest.md).

1. Impostare il valore per l'attributo `tokenEncryptionKeyId`.

    L'esempio seguente mostra un manifesto dell'applicazione configurato con due certificati di crittografia e con il secondo selezionato come quello attivo che usa il tokenEnryptionKeyId.

    ```
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Modalità di utilizzo del protocollo SAML da parte di Azure AD](../develop/active-directory-saml-protocol-reference.md)
* Informazioni sul formato, le caratteristiche di sicurezza e il contenuto dei [token SAML in Azure AD](../develop/reference-saml-tokens.md)
