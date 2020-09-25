---
title: Autenticazione negoziata in Android | Azure
titleSuffix: Microsoft identity platform
description: Panoramica dell'autenticazione negoziata & autorizzazione per Android nella piattaforma di identità Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 2bb48971e86c2b61742735020469865fa969bee3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258410"
---
# <a name="brokered-authentication-in-android"></a>Autenticazione negoziata in Android

È necessario usare uno dei broker di autenticazione Microsoft per partecipare a un Single Sign-On a livello di dispositivo (SSO) e per soddisfare i criteri di accesso condizionale dell'organizzazione. L'integrazione con un broker offre i vantaggi seguenti:

- Single Sign-On del dispositivo
- Accesso condizionale per:
  - Protezione app Intune
  - Registrazione del dispositivo (Workplace Join)
  - Gestione dei dispositivi mobili
- Gestione degli account a livello di dispositivo
  -  tramite Android AccountManager & impostazioni account
  - "Account di lavoro"-tipo di account personalizzato

In Android, Microsoft Authentication Broker è un componente incluso in [Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [portale aziendale Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

Il diagramma seguente illustra la relazione tra l'app, Microsoft Authentication Library (MSAL) e i broker di autenticazione Microsoft.

![Diagramma che illustra il modo in cui un'applicazione è correlata a MSAL, app broker e gestione account Android.](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Installazione di app che ospitano un broker

Broker: le app di hosting possono essere installate dal proprietario del dispositivo dall'App Store (in genere Google Play Store) in qualsiasi momento. Tuttavia, alcune API (risorse) sono protette dai criteri di accesso condizionale che richiedono che i dispositivi siano:

- Registrata (aggiunta all'area di lavoro) e/o
- Registrato nella gestione dei dispositivi o
- Registrato in Protezione app di Intune

Se un dispositivo non dispone già di un'app broker installata, MSAL indica all'utente di installarne una non appena l'app tenta di ottenere un token in modo interattivo. L'app dovrà quindi guidare l'utente attraverso i passaggi per rendere il dispositivo conforme ai criteri richiesti.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effetti dell'installazione e della disinstallazione di un broker

### <a name="when-a-broker-is-installed"></a>Quando viene installato un broker

Quando un broker viene installato in un dispositivo, tutte le successive richieste di token interattivo (chiamate a `acquireToken()` ) vengono gestite dal Broker anziché localmente da MSAL. Qualsiasi stato SSO disponibile in precedenza per MSAL non è disponibile per il broker. Di conseguenza, l'utente dovrà ripetere l'autenticazione oppure selezionare un account dall'elenco esistente di account noti al dispositivo.

L'installazione di un broker non richiede l'accesso dell'utente. Solo quando l'utente deve risolvere un `MsalUiRequiredException` , la richiesta successiva verrà inviata al broker. `MsalUiRequiredException` può essere generata per diversi motivi e deve essere risolta in modo interattivo. Ad esempio:

- L'utente ha modificato la password associata al proprio account.
- L'account dell'utente non soddisfa più i criteri di accesso condizionale.
- L'utente ha revocato il consenso per l'associazione dell'app al proprio account.

#### <a name="multiple-brokers"></a>Più broker

Se in un dispositivo sono installati più broker, il broker installato per primo è sempre il broker attivo. Un singolo broker può essere attivo in un dispositivo.

### <a name="when-a-broker-is-uninstalled"></a>Quando viene disinstallato un broker

Se è installata una sola app di hosting broker, che verrà rimossa, l'utente dovrà eseguire di nuovo l'accesso. La disinstallazione del broker attivo consente di rimuovere l'account e i token associati dal dispositivo.

Se Portale aziendale Intune è installato e funziona come broker attivo e viene installato anche Microsoft Authenticator, se il Portale aziendale Intune (broker attivo) viene disinstallato, l'utente dovrà eseguire di nuovo l'accesso. Una volta eseguito l'accesso, l'app Microsoft Authenticator diventa il broker attivo.

## <a name="integrating-with-a-broker"></a>Integrazione con un broker

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generazione di un URI di reindirizzamento per un broker

È necessario registrare un URI di reindirizzamento compatibile con Service Broker. L'URI di reindirizzamento per il broker deve includere il nome del pacchetto dell'app e la rappresentazione con codifica Base64 della firma dell'app.

Il formato dell'URI di reindirizzamento è il seguente: `msauth://<yourpackagename>/<base64urlencodedsignature>`

È possibile usare lo [strumento](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) per generare un hash di firma con codifica Base64 usando le chiavi di firma dell'app e quindi usare il portale di Azure per generare l'URI di Reindirizzamento usando tale hash.

Linux e macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Dopo aver generato un hash della firma con *lo strumento*di riutilizzo, usare il portale di Azure per generare l'URI di reindirizzamento:

1. Accedere al [portale di Azure](https://protal.azure.com) e selezionare l'app Android in **registrazioni app**.
1. Selezionare **autenticazione**  >  **Aggiungi piattaforma**  >  **Android**.
1. Nel riquadro **Configura l'app Android** visualizzata immettere l' **hash della firma** generato in precedenza e un **nome di pacchetto**.
1. Selezionare il pulsante **Configura** .

Il portale di Azure genera l'URI di reindirizzamento per l'utente e lo Visualizza nel campo URI di **Reindirizzamento** del riquadro **Configurazione Android** .

Per altre informazioni sulla firma dell'app, vedere [firmare l'app](https://developer.android.com/studio/publish/app-signing) nella Guida dell'utente di Android Studio.

> [!IMPORTANT]
> Usare la chiave di firma della produzione per la versione di produzione dell'app.

### <a name="configure-msal-to-use-a-broker"></a>Configurare MSAL per l'uso di un broker

Per usare un broker nell'app, è necessario attestare che è stato configurato il reindirizzamento del broker. Includere, ad esempio, l'URI di reindirizzamento abilitato per il broker e indicare che è stato registrato, includendo le impostazioni seguenti nel file di configurazione di MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Eccezioni correlate a Service Broker

MSAL comunica con il broker in due modi:

- Servizio con binding broker
- Android AccountManager

MSAL USA innanzitutto il servizio associato al broker perché la chiamata a questo servizio non richiede alcuna autorizzazione per Android. Se l'associazione al servizio associato non riesce, MSAL userà l'API AccountManager di Android. MSAL esegue questa operazione solo se all'app è già stata concessa l' `"READ_CONTACTS"` autorizzazione.

Se si ottiene un `MsalClientException` con codice di errore `"BROKER_BIND_FAILURE"` , sono disponibili due opzioni:

- Chiedere all'utente di disabilitare l'ottimizzazione alimentazione per l'app Microsoft Authenticator e il Portale aziendale Intune.
- Chiedere all'utente di concedere l' `"READ_CONTACTS"` autorizzazione

## <a name="verifying-broker-integration"></a>Verifica dell'integrazione di Service Broker

Potrebbe non essere immediatamente chiaro che l'integrazione di Service Broker funziona, ma è possibile seguire questa procedura per verificare:

1. Nel dispositivo Android completare una richiesta utilizzando broker.
1. Nelle impostazioni del dispositivo Android cercare un account appena creato corrispondente all'account con cui è stata eseguita l'autenticazione. L'account deve essere di tipo *account*aziendale.

Se si desidera ripetere il test, è possibile rimuovere l'account dalle impostazioni.

## <a name="next-steps"></a>Passaggi successivi

La [modalità dispositivo condiviso per i dispositivi Android](msal-android-shared-devices.md) consente di configurare un dispositivo Android in modo che possa essere facilmente condiviso da più dipendenti.
