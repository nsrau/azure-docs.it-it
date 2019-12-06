---
title: Autenticazione negoziata in Android | Azure
titlesuffix: Microsoft identity platform
description: Panoramica dell'autenticazione negoziata & autorizzazione per Android nella piattaforma di identità Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5204ad71efa2587341600d2c5c1e5195d15445e
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843717"
---
# <a name="brokered-authentication-in-android"></a>Autenticazione negoziata in Android

È necessario usare uno dei broker di autenticazione Microsoft per partecipare al Single Sign-on (SSO) a livello di dispositivo e per soddisfare i criteri di accesso condizionale dell'organizzazione. L'integrazione con un broker offre i vantaggi seguenti:

- Single Sign-On del dispositivo
- Accesso condizionale per:
  - Protezione app di Intune
  - Registrazione del dispositivo (Workplace Join)
  - Gestione di dispositivi mobili
- Gestione degli account a livello di dispositivo
  -  tramite Android AccountManager & impostazioni account
  - "Account di lavoro"-tipo di account personalizzato

In Android, Microsoft Authentication Broker è un componente incluso in [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [portale aziendale Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Una sola applicazione che ospita il broker sarà attiva come broker alla volta. Quale applicazione è attiva come broker è determinata dall'ordine di installazione nel dispositivo. Il primo da installare, o l'ultimo presente nel dispositivo, diventa il broker attivo.

Il diagramma seguente illustra la relazione tra l'app, Microsoft Authentication Library (MSAL) e i broker di autenticazione Microsoft.

![Diagramma di distribuzione broker](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installazione di app che ospitano un broker

Broker: le app di hosting possono essere installate dal proprietario del dispositivo dall'App Store (in genere Google Play Store) in qualsiasi momento. Tuttavia, alcune API (risorse) sono protette dai criteri di accesso condizionale che richiedono che i dispositivi siano:

- Registrata (aggiunta all'area di lavoro) e/o
- Registrato nella gestione dei dispositivi o
- Registrato in Protezione app di Intune

Se per un dispositivo non è già installata un'app broker, MSAL indica all'utente di installarne una non appena l'app tenta di ottenere un token in modo interattivo. L'app dovrà quindi guidare l'utente attraverso i passaggi per rendere il dispositivo conforme ai criteri richiesti.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effetti dell'installazione e della disinstallazione di un broker

### <a name="when-a-broker-is-installed"></a>Quando viene installato un broker

Quando un broker viene installato in un dispositivo, tutte le richieste di token interattive successive (chiamate a `acquireToken()`) vengono gestite dal Broker anziché localmente da MSAL. Qualsiasi stato SSO disponibile in precedenza per MSAL non è disponibile per il broker. Di conseguenza, l'utente dovrà ripetere l'autenticazione oppure selezionare un account dall'elenco esistente di account noti al dispositivo.

L'installazione di un broker non richiede l'accesso dell'utente. Solo quando l'utente deve risolvere un `MsalUiRequiredException` la richiesta successiva verrà inviata al broker. `MsalUiRequiredException` viene generata per diversi motivi e deve essere risolta in modo interattivo. Di seguito sono riportati alcuni motivi comuni:

- L'utente ha modificato la password associata al proprio account.
- L'account dell'utente non soddisfa più i criteri di accesso condizionale.
- L'utente ha revocato il consenso per l'associazione dell'app al proprio account.

### <a name="when-a-broker-is-uninstalled"></a>Quando viene disinstallato un broker

Se è installata una sola app di hosting broker, che verrà rimossa, l'utente dovrà eseguire di nuovo l'accesso. La disinstallazione del broker attivo consente di rimuovere l'account e i token associati dal dispositivo.

Se Portale aziendale Intune è installato e funziona come broker attivo e viene installato anche Microsoft Authenticator, se il Portale aziendale Intune (broker attivo) viene disinstallato, l'utente dovrà eseguire di nuovo l'accesso. Una volta eseguito l'accesso, l'app Microsoft Authenticator diventa il broker attivo.

## <a name="integrating-with-a-broker"></a>Integrazione con un broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generazione di un URI di reindirizzamento per un broker

È necessario registrare un URI di reindirizzamento compatibile con Service Broker. L'URI di reindirizzamento per il broker deve includere il nome del pacchetto dell'app, nonché la rappresentazione con codifica Base64 della firma dell'app.

Il formato dell'URI di reindirizzamento è: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Generare la firma codificata con URL Base64 usando le chiavi di firma dell'app. Di seguito sono riportati alcuni comandi di esempio che usano le chiavi di firma del debug:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Per informazioni sulla firma dell'app, vedere [firmare l'app](https://developer.android.com/studio/publish/app-signing) .

> [!IMPORTANT]
> Usare la chiave di firma della produzione per la versione di produzione dell'app.

### <a name="configure-msal-to-use-a-broker"></a>Configurare MSAL per l'uso di un broker

Per usare un broker nell'app, è necessario attestare che è stato configurato il reindirizzamento del broker. Includere, ad esempio, l'URI di reindirizzamento abilitato per il broker e indicare che è stato registrato, includendo quanto segue nel file di configurazione MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> La nuova interfaccia utente di registrazione delle app di portale di Azure consente di generare l'URI di reindirizzamento del broker. Se l'app è stata registrata con l'esperienza precedente o ha usato il portale di registrazione delle app Microsoft, potrebbe essere necessario generare l'URI di reindirizzamento e aggiornare manualmente l'elenco degli URI di reindirizzamento nel portale.

### <a name="broker-related-exceptions"></a>Eccezioni correlate a Service Broker

MSAL comunica con il broker in due modi:

- Servizio con binding broker
- Android AccountManager

MSAL USA innanzitutto il servizio associato al broker perché la chiamata a questo servizio non richiede alcuna autorizzazione per Android. Se l'associazione al servizio associato non riesce, MSAL userà l'API AccountManager di Android. MSAL esegue questa operazione solo se all'app è già stata concessa l'autorizzazione `"READ_CONTACTS"`.

Se si ottiene un `MsalClientException` con codice di errore `"BROKER_BIND_FAILURE"`, sono disponibili due opzioni:

- Chiedere all'utente di disabilitare l'ottimizzazione alimentazione per l'app Microsoft Authenticator e il Portale aziendale Intune.
- Chiedere all'utente di concedere l'autorizzazione `"READ_CONTACTS"`
