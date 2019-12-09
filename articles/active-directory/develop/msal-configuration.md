---
title: File di configurazione della libreria di autenticazione Microsoft Android | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f643022c85a44b2202fcbd91be50664882c8ba7b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916827"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>File di configurazione della libreria di autenticazione Microsoft Android

Android Microsoft Authentication Library (MSAL) viene fornito con un [file JSON di configurazione predefinito](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) che è possibile personalizzare per definire il comportamento dell'app client pubblica per elementi quali l'autorità predefinita, le autorità che verranno usate e così via.

Questo articolo consente di comprendere le diverse impostazioni nel file di configurazione e come specificare il file di configurazione da usare nell'app basata su MSAL.

## <a name="configuration-settings"></a>Impostazioni di configurazione

### <a name="general-settings"></a>Impostazioni generali

| Proprietà | Tipo di dati | Obbligatoria | Note |
|-----------|------------|-------------|-------|
| `client_id` | Stringa | SÌ | ID client dell'app dalla pagina di [registrazione dell'applicazione](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Stringa | SÌ | URI di reindirizzamento dell'app dalla [pagina di registrazione dell'applicazione](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | Elenca\<autorità > | No | Elenco delle autorità necessarie per l'app |
| `authorization_user_agent` | AuthorizationAgent (enumerazione) | No | Valori possibili: `DEFAULT`, `BROWSER`, `WEBVIEW` |
| `http` | HttpConfiguration | No | Configurare `HttpUrlConnection` `connect_timeout` e `read_timeout` |
| `logging` | LoggingConfiguration | No | Specifica il livello di dettaglio della registrazione. Le configurazioni facoltative includono: `pii_enabled`, che accetta un valore booleano, e `log_level`, che accetta `ERROR`, `WARNING`, `INFO`o `VERBOSE`. |

### <a name="client_id"></a>client_id

ID client o ID app creato al momento della registrazione dell'applicazione.

### <a name="redirect_uri"></a>redirect_uri

URI di reindirizzamento registrato durante la registrazione dell'applicazione. Se l'URI di reindirizzamento è a un'app broker, fare riferimento a [URI di reindirizzamento per le app client pubbliche](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) per assicurarsi di usare il formato dell'URI di reindirizzamento corretto per l'app Broker.

### <a name="authorities"></a>autorità

Elenco di autorità note e considerate attendibili dall'utente. Oltre alle autorità elencate qui, MSAL interroga Microsoft per ottenere un elenco di cloud e autorità noti a Microsoft. In questo elenco di autorità specificare il tipo di autorità ed eventuali altri parametri facoltativi, ad esempio `"audience"`, che devono essere allineati con i destinatari dell'app in base alla registrazione dell'app. Di seguito è riportato un elenco di esempi di autorità:

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

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Eseguire il mapping dell'autorità AAD & destinatari agli endpoint della piattaforma Microsoft Identity

| Type | Destinatari | ID tenant | Authority_Url | Endpoint risultante | Note |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | https://login.microsoftonline.com/common | `common` è un alias tenant per l'account. Come un tenant di Azure Active Directory specifico o il sistema di account Microsoft. |
| AAD | AzureADMyOrg | contoso.com | | https://login.microsoftonline.com/contoso.com | Solo gli account presenti in contoso.com possono acquisire un token. Qualsiasi dominio verificato o GUID del tenant può essere usato come ID tenant. |
| AAD | AzureADMultipleOrgs | | | https://login.microsoftonline.com/organizations | Con questo endpoint è possibile usare solo account Azure Active Directory. Gli account Microsoft possono essere membri di organizzazioni. Per acquisire un token utilizzando un account Microsoft per una risorsa in un'organizzazione, specificare il tenant aziendale da cui si desidera il token. |
| AAD | PersonalMicrosoftAccount | | | https://login.microsoftonline.com/consumers | Solo gli account Microsoft possono usare questo endpoint. |
| B2C | | | Visualizza endpoint risultante | https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/ | Solo gli account presenti nel tenant contoso.onmicrosoft.com possono acquisire un token. In questo esempio, i criteri B2C fanno parte del percorso dell'URL dell'autorità. |

> [!NOTE]
> La convalida dell'autorità non può essere abilitata e disabilitata in MSAL.
> Le autorità sono note come sviluppatore come specificato tramite configurazione o note a Microsoft tramite metadati.
> Se MSAL riceve una richiesta di un token a un'autorità sconosciuta, viene restituito un `MsalClientException` di tipo `UnknownAuthority`.

#### <a name="authority-properties"></a>Proprietà dell'autorità

| Proprietà | Tipo di dati  | Obbligatoria | Note |
|-----------|-------------|-----------|--------|
| `type` | Stringa | SÌ | Rispecchia il tipo di destinatari o di account a cui è destinata l'app. Valori possibili: `AAD`, `B2C` |
| `audience` | Oggetto | No | Si applica solo quando Type =`AAD`. Specifica l'identità a cui è destinata l'app. Usare il valore della registrazione dell'app |
| `authority_url` | Stringa | SÌ | Obbligatorio solo quando Type =`B2C`. Specifica l'URL dell'autorità o i criteri che devono essere usati dall'app  |
| `default` | boolean | SÌ | Quando si specifica una o più autorità, è necessario un singolo `"default":true`. |

#### <a name="audience-properties"></a>Proprietà del gruppo di destinatari

| Proprietà | Tipo di dati  | Obbligatoria | Note |
|-----------|-------------|------------|-------|
| `type` | Stringa | SÌ | Specifica i destinatari a cui si vuole assegnare l'app. Valori possibili: `AzureADandPersonalMicrosoftAccount`, `PersonalMicrosoftAccount`, `AzureADMultipleOrgs`, `AzureADMyOrg` |
| `tenant_id` | Stringa | SÌ | Obbligatorio solo quando `"type":"AzureADMyOrg"`. Facoltativo per altri valori `type`. Può trattarsi di un dominio tenant, ad esempio `contoso.com`, o un ID tenant, ad esempio `72f988bf-86f1-41af-91ab-2d7cd011db46`) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Indica se usare una visualizzazione Web incorporata o il browser predefinito nel dispositivo quando si accede a un account o si autorizza l'accesso a una risorsa.

Valori possibili:
- `DEFAULT`: preferisce il browser del sistema. Usa la visualizzazione Web incorporata se nel dispositivo non è disponibile un browser.
- `WEBVIEW`: utilizzare la visualizzazione Web incorporata.
- `BROWSER`: usa il browser predefinito nel dispositivo.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Per i client che supportano più cloud nazionali, specificare `true`. La piattaforma Microsoft Identity reindirizza automaticamente al cloud nazionale corretto durante l'autorizzazione e il riscatto dei token. È possibile determinare il cloud nazionale dell'account di accesso esaminando l'autorità associata al `AuthenticationResult`. Si noti che il `AuthenticationResult` non fornisce l'indirizzo endpoint specifico del cloud nazionale della risorsa per cui si richiede un token.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Valore booleano che indica se si usa un URI di reindirizzamento in-Broker compatibile con Microsoft Identity Broker. Impostare su `false` se non si vuole usare il broker nell'app.

Se si usa l'autorità AAD con audience impostata su `"MicrosoftPersonalAccount"`, il broker non verrà usato.

### <a name="http"></a>http

Configurare le impostazioni globali per i timeout HTTP, ad esempio:

| Proprietà | Tipo di dati | Obbligatoria | Note |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | No | Tempo in millisecondi |
| `read_timeout` | int | No | Tempo in millisecondi |

### <a name="logging"></a>registrazione

Per la registrazione sono disponibili le impostazioni globali seguenti:

| Proprietà | Tipo di dati  | Obbligatoria | Note |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | No | Indica se creare dati personali |
| `log_level`   | boolean | No | Messaggi di log da restituire |
| `logcat_enabled` | boolean | No | Indica se eseguire l'output in log Cat oltre all'interfaccia di registrazione |

### <a name="account_mode"></a>account_mode

Specifica il numero di account che possono essere usati all'interno dell'app alla volta. I valori possibili sono:

- `MULTIPLE` (predefinita)
- `SINGLE`

La creazione di un `PublicClientApplication` utilizzando una modalità account che non corrisponde a questa impostazione genererà un'eccezione.

Per altre informazioni sulle differenze tra account singoli e multipli, vedere [app di account singolo e multiple](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Un elenco di browser consentiti compatibili con MSAL. Questi browser gestiscono correttamente i reindirizzamenti a finalità personalizzate. È possibile aggiungere a questo elenco. Il valore predefinito è specificato nella configurazione predefinita mostrata di seguito.
``
## <a name="the-default-msal-configuration-file"></a>Il file di configurazione MSAL predefinito

Di seguito è illustrata la configurazione predefinita di MSAL fornita con MSAL. È possibile visualizzare la versione più recente su [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Questa configurazione è integrata dai valori forniti dall'utente. I valori forniti sostituiscono le impostazioni predefinite.

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

Nell'esempio seguente viene illustrata una configurazione di base che specifica l'ID client, l'URI di reindirizzamento, il fatto che un reindirizzamento di Service Broker sia registrato e un elenco di autorità.

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

## <a name="how-to-use-a-configuration-file"></a>Come usare un file di configurazione

1. Creare un file di configurazione. Si consiglia di creare il file di configurazione personalizzato in `res/raw/auth_config.json`. Ma è possibile inserirlo ovunque si desideri.
2. Indicare a MSAL dove cercare la configurazione quando si costruisce la `PublicClientApplication`. ad esempio:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
