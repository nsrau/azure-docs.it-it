---
title: Autenticazione negoziata in Android Azure
titlesuffix: Microsoft identity platform
description: Panoramica dell'autenticazione negoziata & l'autorizzazione per Android nella piattaforma Microsoft Identity
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
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697898"
---
# <a name="brokered-authentication-in-android"></a>Autenticazione negoziata in Android

È necessario utilizzare uno dei broker di autenticazione di Microsoft per partecipare a Single Sign-On (SSO) a livello di dispositivo e per soddisfare i criteri di accesso condizionale dell'organizzazione. L'integrazione con un broker offre i seguenti vantaggi:

- Single Sign-On del dispositivo
- Accesso condizionale per:
  - Protezione app Intune
  - Registrazione del dispositivo (aggiunta all'area di lavoro)Device Registration (Workplace Join)
  - Gestione dei dispositivi mobili
- Gestione account a livello di dispositivo
  -  tramite Impostazioni accountManager & Android
  - "Account di lavoro" - tipo di account personalizzato

In Android, Microsoft Authentication Broker è un componente incluso in [Microsoft Authenticator App](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [Intune Company Portal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Solo un'applicazione che ospita il broker sarà attiva come broker alla volta. Quale applicazione è attiva come broker è determinata dall'ordine di installazione sul dispositivo. Il primo ad essere installato, o l'ultimo presente sul dispositivo, diventa il broker attivo.

Il diagramma seguente illustra la relazione tra l'app, la libreria di autenticazione Microsoft (MSAL) e i broker di autenticazione di Microsoft.The following diagram illustrates the relationship between your app, the Microsoft Authentication Library (MSAL) and Microsoft authentication brokers.

![Diagramma di distribuzione del broker](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installazione di app che ospitano un broker

Le app di hosting broker possono essere installate dal proprietario del dispositivo dal proprio app store (in genere Google Play Store) in qualsiasi momento. Tuttavia, alcune API (risorse) sono protette da criteri di accesso condizionale che richiedono che i dispositivi siano:However, some APIs (resources) are protected by Conditional Access Policies that require devices to be:

- Registrato (aggiunto al posto di lavoro) e/o
- Registrato in Gestione dispositivi o
- Registrato in Intune App Protection

Se un dispositivo non ha già installato un'app broker, MSAL indica all'utente di installarne una non appena l'app tenta di ottenere un token in modo interattivo. L'app dovrà quindi guidare l'utente attraverso i passaggi per rendere il dispositivo conforme ai criteri richiesti.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effetti dell'installazione e della disinstallazione di un broker

### <a name="when-a-broker-is-installed"></a>Quando viene installato un broker

Quando un broker viene installato in un dispositivo, `acquireToken()`tutte le successive richieste di token interattive (chiamate a ) vengono gestite dal broker anziché localmente da MSAL. Qualsiasi stato SSO precedentemente disponibile per MSAL non è disponibile per il broker. Di conseguenza, l'utente dovrà eseguire nuovamente l'autenticazione o selezionare un account dall'elenco esistente di account noti al dispositivo.

L'installazione di un broker non richiede all'utente di accedere di nuovo. Solo quando l'utente `MsalUiRequiredException` deve risolvere un testamento, la prossima richiesta passerà al broker. `MsalUiRequiredException`viene generata per una serie di motivi e deve essere risolta in modo interattivo. Questi sono alcuni motivi comuni:

- L'utente ha modificato la password associata al proprio account.
- L'account dell'utente non soddisfa più un criterio di accesso condizionale.
- L'utente ha revocato il consenso per l'app da associare al proprio account.

### <a name="when-a-broker-is-uninstalled"></a>Quando un broker viene disinstallato

Se è installata una sola app di hosting broker e viene rimossa, l'utente dovrà eseguire nuovamente l'accesso. La disinstallazione del broker attivo rimuove l'account e i token associati dal dispositivo.

Se Intune Company Portal è installato e opera come broker attivo e microsoft Authenticator è installato, se Intune Company Portal (broker attivo) viene disinstallato, l'utente dovrà eseguire nuovamente l'accesso. Una volta effettuato nuovamente l'accesso, l'app Microsoft Authenticator diventa il broker attivo.

## <a name="integrating-with-a-broker"></a>Integrazione con un broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generazione di un URI di reindirizzamento per un broker

È necessario registrare un URI di reindirizzamento compatibile con il broker. L'URI di reindirizzamento per il broker deve includere il nome del pacchetto dell'app, nonché la rappresentazione codificata in base64 della firma dell'app.

Il formato dell'URI di reindirizzamento è:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Genera la firma con codifica URL Base64 usando le chiavi di firma dell'app. Di seguito sono riportati alcuni comandi di esempio che usano le chiavi di firma di debug:Here are some example commands that use your debug signing keys:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>WINDOWS

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Per informazioni sulla firma dell'app, vedere [Firma l'app.](https://developer.android.com/studio/publish/app-signing)

> [!IMPORTANT]
> Usa la chiave di firma di produzione per la versione di produzione dell'app.

### <a name="configure-msal-to-use-a-broker"></a>Configurare MSAL per l'utilizzo di un broker

Per usare un broker nella tua app, devi attestare di aver configurato il reindirizzamento del broker. Ad esempio, includere l'URI di reindirizzamento abilitato al broker e indicare che è stato registrato includendo quanto segue nel file di configurazione MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> La nuova interfaccia utente di registrazione dell'app del portale di Azure consente di generare l'URI di reindirizzamento del broker. Se l'app è stata registrata utilizzando l'esperienza precedente o se è stato fatto utilizzando il portale di registrazione dell'app Microsoft, potrebbe essere necessario generare l'URI di reindirizzamento e aggiornare manualmente l'elenco degli URI di reindirizzamento nel portale.

### <a name="broker-related-exceptions"></a>Eccezioni relative al broker

MSAL comunica con il broker in due modi:

- Servizio broker bound
- Android AccountManager

MSAL utilizza innanzitutto il servizio broker associato perché la chiamata a questo servizio non richiede alcuna autorizzazione Android.MSAL first uses the broker bound service because calling this service doesn't require any Android permissions. Se l'associazione al servizio associato non riesce, MSAL utilizzerà l'API AccountManager android. MSAL esegue questa operazione solo se `"READ_CONTACTS"` all'app è già stata concessa l'autorizzazione.

Se si `MsalClientException` ottiene un `"BROKER_BIND_FAILURE"`con codice di errore , allora ci sono due opzioni:

- Chiedere all'utente di disabilitare l'ottimizzazione dell'alimentazione per l'app Microsoft Authenticator e il portale aziendale di Intune.Ask the user to disable power optimization for the Microsoft Authenticator app and the Intune Company Portal.
- Chiedere all'utente `"READ_CONTACTS"` di concedere l'autorizzazione
