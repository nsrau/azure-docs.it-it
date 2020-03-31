---
title: File di configurazione di Android MSAL Azure
titleSuffix: Microsoft identity platform
description: Panoramica del file di configurazione di Android Microsoft Authentication Library (MSAL), che rappresenta la configurazione di un'applicazione in Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050367"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>File di configurazione della libreria di autenticazione Microsoft Android

La libreria di autenticazione Microsoft Android (MSAL) viene fornita con un file JSON di [configurazione predefinito](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) personalizzato per definire il comportamento dell'app client pubblica per elementi quali l'autorità predefinita, le autorità che verranno usate e così via.

Questo articolo ti aiuterà a comprendere le varie impostazioni nel file di configurazione e come specificare il file di configurazione da usare nell'app basata su MSAL.

## <a name="configuration-settings"></a>Impostazioni di configurazione

### <a name="general-settings"></a>Impostazioni generali

| Proprietà | Tipo di dati | Obbligatoria | Note |
|-----------|------------|-------------|-------|
| `client_id` | string | Sì | ID client dell'app dalla [pagina di registrazione dell'applicazione](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | string | Sì | URI di reindirizzamento dell'app dalla [pagina di registrazione dell'applicazione](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | Autorità\<elenco> | No | L'elenco delle autorità necessarie per l'app |
| `authorization_user_agent` | AuthorizationAgent (enum) | No | Valori `DEFAULT`possibili: `BROWSER`, ,`WEBVIEW` |
| `http` | HttpConfigurazione (Configurazione guidata) | No | Configura `HttpUrlConnection` `connect_timeout` e`read_timeout` |
| `logging` | LoggingConfiguration | No | Specifica il livello di dettaglio della registrazione. Le configurazioni `pii_enabled`facoltative includono: , `log_level`che `ERROR`accetta `WARNING` `INFO`un `VERBOSE`valore booleano e , che accetta , , o . |

### <a name="client_id"></a>client_id

ID client o ID app creato al momento della registrazione dell'applicazione.

### <a name="redirect_uri"></a>redirect_uri

URI di reindirizzamento registrato durante la registrazione dell'applicazione. Se l'URI di reindirizzamento è a un'app broker, fai riferimento a URI di [reindirizzamento per le app client pubbliche](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) per assicurarti di usare il formato URI di reindirizzamento corretto per l'app broker.

### <a name="authorities"></a>Autorità

L'elenco delle autorità che sono noti e di fiducia da voi. Oltre alle autorità elencate di seguito, MSAL interroga anche Microsoft per ottenere un elenco di cloud e autorità note a Microsoft. In questo elenco di autorità specificare il tipo di `"audience"`autorità ed eventuali parametri facoltativi aggiuntivi, ad esempio , che devono essere allineati al pubblico dell'app in base alla registrazione dell'app. Di seguito è riportato un elenco di esempio di autorità:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Eseguire il mapping dell'autorità AAD & il pubblico agli endpoint della piattaforma di identità Microsoft

| Type | Destinatari | ID tenant | Authority_Url | Endpoint risultante | Note |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`è un alias tenant per cui si trova l'account. Ad esempio un tenant di Azure Active Directory specifico o il sistema di account Microsoft.Such as a specific Azure Active Directory tenant or the Microsoft account system. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Solo gli account presenti in contoso.com possono acquisire un token. Qualsiasi dominio verificato, o GUID tenant, può essere utilizzato come ID tenant. |
| AAD | AzureADMultipleOrgsAzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Solo gli account di Azure Active Directory possono essere usati con questo endpoint. Gli account Microsoft possono essere membri di organizzazioni. Per acquisire un token usando un account Microsoft per una risorsa in un'organizzazione, specificare il tenant dell'organizzazione da cui si desidera ottenere il token. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Solo gli account Microsoft possono utilizzare questo endpoint. |
| B2C | | | Vedere Endpoint risultante | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Solo gli account presenti nel contoso.onmicrosoft.com tenant possono acquisire un token. In questo esempio, il criterio B2C fa parte del percorso URL dell'autorità. |

> [!NOTE]
> La convalida dell'autorità non può essere abilitata e disabilitata in MSAL.
> Le autorità sono note all'utente come sviluppatore come specificato tramite configurazione o note a Microsoft tramite metadati.
> Se MSAL riceve una richiesta di un token `MsalClientException` a `UnknownAuthority` un'autorità sconosciuta, risulta un tipo.

#### <a name="authority-properties"></a>Proprietà dell'autorità

| Proprietà | Tipo di dati  | Obbligatoria | Note |
|-----------|-------------|-----------|--------|
| `type` | string | Sì | Esegue il mirroring del pubblico o del tipo di account a cui è destinata l'app. Valori possibili: `AAD`,`B2C` |
| `audience` | Oggetto | No | Si applica solo`AAD`quando il tipo è . Specifica l'identità di destinazione dell'app. Usare il valore della registrazione dell'appUse the value from your app registration |
| `authority_url` | string | Sì | Obbligatorio solo quando`B2C`si digita. Specifica l'URL dell'autorità o i criteri che l'app deve usare  |
| `default` | boolean | Sì | Quando `"default":true` si specifica una o più autorità, è necessario un singolo. |

#### <a name="audience-properties"></a>Proprietà gruppo di destinatari

| Proprietà | Tipo di dati  | Obbligatoria | Note |
|-----------|-------------|------------|-------|
| `type` | string | Sì | Specifica il gruppo di destinatari a cui vuole rivolgersi l'app. Valori `AzureADandPersonalMicrosoftAccount`possibili: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, , , ,`AzureADMyOrg` |
| `tenant_id` | string | Sì | Obbligatorio solo `"type":"AzureADMyOrg"`quando . Facoltativo `type` per altri valori. Può trattarsi di un `contoso.com`dominio tenant, ad `72f988bf-86f1-41af-91ab-2d7cd011db46`esempio , o di un ID tenant, ad esempio ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Indica se utilizzare una visualizzazione Web incorporata o il browser predefinito nel dispositivo quando si accede a un account o si autorizza l'accesso a una risorsa.

Valori possibili:
- `DEFAULT`: preferisce il browser di sistema. Utilizza la visualizzazione Web incorporata se un browser non è disponibile nel dispositivo.
- `WEBVIEW`: utilizzare la visualizzazione Web incorporata.
- `BROWSER`: utilizza il browser predefinito sul dispositivo.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Per i client che supportano `true`più cloud nazionali, specificare . La piattaforma di identità Microsoft reindirizzerà automaticamente al cloud nazionale corretto durante l'autorizzazione e il riscatto dei token. È possibile determinare il cloud nazionale dell'account di accesso `AuthenticationResult`esaminando l'autorità associata al file . Si noti che l'oggetto `AuthenticationResult` non fornisce l'indirizzo dell'endpoint nazionale specifico del cloud della risorsa per cui si richiede un token.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Valore booleano che indica se si utilizza un URI di reindirizzamento in-broker compatibile con Microsoft Identity Broker. Impostare `false` su se non si vuole usare il broker all'interno dell'app.

Se si utilizza l'autorità AAD `"MicrosoftPersonalAccount"`con il pubblico impostato su , il broker non verrà utilizzato.

### <a name="http"></a>http

Configurare le impostazioni globali per i timeout HTTP, ad esempio:Configure global settings for HTTP timeouts, such as:

| Proprietà | Tipo di dati | Obbligatoria | Note |
| ---------|-----------|------------|--------|
| `connect_timeout` | INT | No | Tempo in millisecondi |
| `read_timeout` | INT | No | Tempo in millisecondi |

### <a name="logging"></a>registrazione

Le seguenti impostazioni globali sono per la registrazione:

| Proprietà | Tipo di dati  | Obbligatoria | Note |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | No | Se emettere dati personali |
| `log_level`   | boolean | No | Messaggi di log da generare |
| `logcat_enabled` | boolean | No | Se generare per registrare cat oltre all'interfaccia di registrazione |

### <a name="account_mode"></a>account_mode

Specifica il numero di account che possono essere utilizzati contemporaneamente all'interno dell'app. I valori possibili sono:

- `MULTIPLE` (impostazione predefinita)
- `SINGLE`

La costruzione `PublicClientApplication` di una modalità using che non corrisponde a questa impostazione genererà un'eccezione.

Per ulteriori informazioni sulle differenze tra account singoli e multipli, vedere [App per account singolo e multiple](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Elenco consenti di browser compatibili con MSAL. Questi browser gestiscono correttamente i reindirizzamenti a finalità personalizzate. È possibile aggiungere a questo elenco. L'impostazione predefinita viene fornita nella configurazione predefinita illustrata di seguito.
``
## <a name="the-default-msal-configuration-file"></a>Il file di configurazione MSAL predefinito

Di seguito è riportata la configurazione MSAL predefinita fornita con MSAL. È possibile visualizzare l'ultima versione su [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Questa configurazione è integrata dai valori forniti dall'utente. I valori forniti sostituiscono i valori predefiniti.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Esempio di configurazione di base

Nell'esempio seguente viene illustrata una configurazione di base che specifica l'ID client, l'URI di reindirizzamento, se è registrato un reindirizzamento del broker e un elenco di autorità.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Come utilizzare un file di configurazione

1. Creare un file di configurazione. È consigliabile creare il file `res/raw/auth_config.json`di configurazione personalizzato in . Ma si può mettere ovunque che si desidera.
2. Indicare a MSAL dove cercare la `PublicClientApplication`configurazione quando si costruisce il file . Ad esempio:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
