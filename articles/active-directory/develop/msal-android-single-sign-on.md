---
title: Come abilitare l'accesso Single Sign-on tra app in Android usando MSAL | Azure
titleSuffix: Microsoft identity platform
description: Come usare Microsoft Authentication Library (MSAL) per Android per abilitare Single Sign-On tra le applicazioni.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 3f5791bfcf6547b7fc4e84bee3d4c1c49453af9c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169495"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Procedura: abilitare l'accesso Single Sign-on tra app in Android usando MSAL

Single Sign-on (SSO) consente agli utenti di immettere le proprie credenziali una sola volta e di utilizzare le credenziali automaticamente tra le applicazioni.

La [piattaforma Microsoft Identity](./index.yml) e Microsoft Authentication Library (MSAL) consentono di abilitare l'accesso SSO attraverso la propria suite di app. Con la funzionalità Broker e le applicazioni di autenticazione, è possibile estendere SSO nell'intero dispositivo.

In questa procedura verrà illustrato come configurare gli SDK utilizzati dall'applicazione per fornire l'accesso SSO ai clienti.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura presuppone che si sappia come:

- Eseguire il provisioning dell'app usando il portale di Azure. Per altre informazioni su questo argomento, vedere le istruzioni per la creazione di un'app nell' [esercitazione su Android](./tutorial-v2-android.md#create-a-project) .
- Integrare l'applicazione con [Microsoft Authentication Library per Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Metodi per Single Sign-On

Sono disponibili due modi per le applicazioni che usano MSAL per Android per ottenere l'accesso SSO:

* Tramite un' [applicazione broker](#sso-through-brokered-authentication)
* Tramite il [browser di sistema](#sso-through-system-browser)


   Si consiglia di usare un'applicazione Broker per vantaggi come SSO a livello di dispositivo, gestione degli account e accesso condizionale. Tuttavia, è necessario che gli utenti scarichino applicazioni aggiuntive.

## <a name="sso-through-brokered-authentication"></a>SSO tramite autenticazione negoziata

Si consiglia di usare uno dei broker di autenticazione Microsoft per partecipare a Single Sign-On a livello di dispositivo (SSO) e per soddisfare i criteri di accesso condizionale dell'organizzazione. L'integrazione con un broker offre i vantaggi seguenti:

- Single Sign-On del dispositivo
- Accesso condizionale per:
  - Protezione app Intune
  - Registrazione del dispositivo (Workplace Join)
  - Gestione dei dispositivi mobili
- Gestione degli account a livello di dispositivo
  -  tramite Android AccountManager & impostazioni account
  - "Account di lavoro"-tipo di account personalizzato

In Android, Microsoft Authentication Broker è un componente incluso nelle app [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [portale aziendale Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) .

Il diagramma seguente illustra la relazione tra l'app, Microsoft Authentication Library (MSAL) e i broker di autenticazione Microsoft.

![Diagramma che illustra il modo in cui un'applicazione è correlata a MSAL, app broker e gestione account Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Installazione di app che ospitano un broker

Broker: le app di hosting possono essere installate dal proprietario del dispositivo dall'App Store (in genere Google Play Store) in qualsiasi momento. Tuttavia, alcune API (risorse) sono protette dai criteri di accesso condizionale che richiedono che i dispositivi siano:

- Registrata (aggiunta all'area di lavoro) e/o
- Registrato nella gestione dei dispositivi o
- Registrato in Protezione app di Intune

Se un dispositivo non dispone già di un'app broker installata, MSAL indica all'utente di installarne una non appena l'app tenta di ottenere un token in modo interattivo. L'app dovrà quindi guidare l'utente attraverso i passaggi per rendere il dispositivo conforme ai criteri richiesti.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Effetti dell'installazione e della disinstallazione di un broker

#### <a name="when-a-broker-is-installed"></a>Quando viene installato un broker

Quando un broker viene installato in un dispositivo, tutte le successive richieste di token interattivo (chiamate a `acquireToken()` ) vengono gestite dal Broker anziché localmente da MSAL. Qualsiasi stato SSO disponibile in precedenza per MSAL non è disponibile per il broker. Di conseguenza, l'utente dovrà ripetere l'autenticazione oppure selezionare un account dall'elenco esistente di account noti al dispositivo.

L'installazione di un broker non richiede l'accesso dell'utente. Solo quando l'utente deve risolvere un `MsalUiRequiredException` , la richiesta successiva verrà inviata al broker. `MsalUiRequiredException` può essere generata per diversi motivi e deve essere risolta in modo interattivo. Ad esempio:

- L'utente ha modificato la password associata al proprio account.
- L'account dell'utente non soddisfa più i criteri di accesso condizionale.
- L'utente ha revocato il consenso per l'associazione dell'app al proprio account.

**Più broker** : se in un dispositivo sono installati più broker, il broker installato per primo è sempre il broker attivo. Un singolo broker può essere attivo in un dispositivo.

#### <a name="when-a-broker-is-uninstalled"></a>Quando viene disinstallato un broker

Se è installata una sola app di hosting broker, che verrà rimossa, l'utente dovrà eseguire di nuovo l'accesso. La disinstallazione del broker attivo consente di rimuovere l'account e i token associati dal dispositivo.

Se Portale aziendale Intune è installato e funziona come broker attivo e viene installato anche Microsoft Authenticator, se il Portale aziendale Intune (broker attivo) viene disinstallato, l'utente dovrà eseguire di nuovo l'accesso. Una volta eseguito l'accesso, l'app Microsoft Authenticator diventa il broker attivo.

### <a name="integrating-with-a-broker"></a>Integrazione con un broker

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Genera un URI di reindirizzamento per un broker

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

Dopo aver generato un hash della firma con *lo strumento* di riutilizzo, usare il portale di Azure per generare l'URI di reindirizzamento:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare l'app Android in **registrazioni app**.
1. Selezionare **autenticazione**  >  **Aggiungi piattaforma**  >  **Android**.
1. Nel riquadro **Configura l'app Android** visualizzata immettere l' **hash della firma** generato in precedenza e un **nome di pacchetto**.
1. Selezionare il pulsante **Configura** .

Il portale di Azure genera l'URI di reindirizzamento per l'utente e lo Visualizza nel campo URI di **Reindirizzamento** del riquadro **Configurazione Android** .

Per altre informazioni sulla firma dell'app, vedere [firmare l'app](https://developer.android.com/studio/publish/app-signing) nella Guida dell'utente di Android Studio.

> [!IMPORTANT]
> Usare la chiave di firma della produzione per la versione di produzione dell'app.

#### <a name="configure-msal-to-use-a-broker"></a>Configurare MSAL per l'uso di un broker

Per usare un broker nell'app, è necessario attestare che è stato configurato il reindirizzamento del broker. Includere, ad esempio, l'URI di reindirizzamento abilitato per il broker e indicare che è stato registrato, includendo le impostazioni seguenti nel file di configurazione di MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Eccezioni correlate a Service Broker

MSAL comunica con il broker in due modi:

- Servizio con binding broker
- Android AccountManager

MSAL USA innanzitutto il servizio associato al broker perché la chiamata a questo servizio non richiede alcuna autorizzazione per Android. Se l'associazione al servizio associato non riesce, MSAL userà l'API AccountManager di Android. MSAL esegue questa operazione solo se all'app è già stata concessa l' `"READ_CONTACTS"` autorizzazione.

Se si ottiene un `MsalClientException` con codice di errore `"BROKER_BIND_FAILURE"` , sono disponibili due opzioni:

- Chiedere all'utente di disabilitare l'ottimizzazione alimentazione per l'app Microsoft Authenticator e il Portale aziendale Intune.
- Chiedere all'utente di concedere l' `"READ_CONTACTS"` autorizzazione

### <a name="verify-broker-integration"></a>Verificare l'integrazione di Service Broker

Potrebbe non essere immediatamente chiaro che l'integrazione di Service Broker funziona, ma è possibile seguire questa procedura per verificare:

1. Nel dispositivo Android completare una richiesta utilizzando broker.
1. Nelle impostazioni del dispositivo Android cercare un account appena creato corrispondente all'account con cui è stata eseguita l'autenticazione. L'account deve essere di tipo *account* aziendale.

Se si desidera ripetere il test, è possibile rimuovere l'account dalle impostazioni.

## <a name="sso-through-system-browser"></a>SSO tramite browser di sistema

Le applicazioni Android hanno la possibilità di usare WebView, browser di sistema o schede personalizzate Chrome per l'esperienza utente di autenticazione. Se l'applicazione non usa l'autenticazione negoziata, sarà necessario usare il browser del sistema anziché la visualizzazione Web nativa per ottenere l'accesso SSO.

### <a name="authorization-agents"></a>Agenti di autorizzazione

La scelta di una strategia specifica per gli agenti di autorizzazione è facoltativa e rappresenta altre app di funzionalità che possono essere personalizzate. Per la maggior parte delle app vengono usate le impostazioni predefinite di MSAL (vedere [informazioni sul file di configurazione di Android MSAL](msal-configuration.md) per visualizzare le diverse impostazioni predefinite).

MSAL supporta l'autorizzazione utilizzando un `WebView` o il browser del sistema.  Nell'immagine seguente viene illustrato il modo in cui viene utilizzato il `WebView` o il browser di sistema con CustomTabs o senza CustomTabs:

![Esempi di accesso MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Implicazioni di Single Sign-on

Per impostazione predefinita, le applicazioni integrate con MSAL utilizzano le schede personalizzate del browser del sistema per l'autorizzazione. A differenza delle visualizzazioni Web, le schede personalizzate condividono un file jar dei cookie con il browser di sistema predefinito che consente un minor numero di accessi con le app Web o altre app native integrate con schede personalizzate.

Se l'applicazione usa una `WebView` strategia senza integrare Microsoft Authenticator o portale aziendale supporto nell'app, gli utenti non avranno un'esperienza Single Sign-on nel dispositivo o tra app native e app Web.

Se l'applicazione usa MSAL con un broker come Microsoft Authenticator o Portale aziendale Intune, gli utenti possono avere un'esperienza SSO tra le applicazioni se hanno un accesso attivo con una delle app.

### <a name="webview"></a>WebView

Per usare la visualizzazione WebView in-app, inserire la riga seguente nel file JSON di configurazione dell'app che viene passato a MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Quando si usa in-app `WebView` , l'utente accede direttamente all'app. I token vengono conservati all'interno della sandbox dell'app e non sono disponibili al di fuori del file jar dei cookie dell'app. Di conseguenza, l'utente non può avere un'esperienza SSO tra le applicazioni, a meno che le app non si integrino con l'autenticatore o Portale aziendale.

Tuttavia, `WebView` offre la possibilità di personalizzare l'aspetto dell'interfaccia utente di accesso. Per altre informazioni su come eseguire questa personalizzazione, vedere [Webviews Android](https://developer.android.com/reference/android/webkit/WebView) .

### <a name="default-browser-plus-custom-tabs"></a>Browser predefinito più schede personalizzate

Per impostazione predefinita, MSAL usa il browser e una strategia personalizzata per le [schede](https://developer.chrome.com/multidevice/android/customtabs) . È possibile indicare in modo esplicito questa strategia per impedire modifiche nelle versioni future a `DEFAULT` usando la configurazione JSON seguente nel file di configurazione personalizzato:

```json
"authorization_user_agent" : "BROWSER"
```

Usare questo approccio per offrire un'esperienza SSO tramite il browser del dispositivo. MSAL usa un file jar di cookie condiviso, che consente ad altre app native o app Web di ottenere l'accesso SSO sul dispositivo usando il cookie di sessione di salvataggio permanente impostato da MSAL.

### <a name="browser-selection-heuristic"></a>Euristica selezione del browser

Poiché è impossibile per MSAL specificare il pacchetto del browser esatto da usare in ogni ampia gamma di telefoni Android, MSAL implementa un'euristica di selezione del browser che tenta di fornire il migliore SSO tra dispositivi.

MSAL recupera principalmente il browser predefinito da Gestione pacchetti e controlla se si trova in un elenco testato di browser sicuri. In caso contrario, MSAL esegue il fallback utilizzando WebView anziché avviare un altro browser non predefinito dall'elenco di sicurezza. Il browser predefinito verrà scelto indipendentemente dal fatto che supporti schede personalizzate. Se il browser supporta le schede personalizzate, MSAL avvierà la scheda personalizzata. le schede personalizzate hanno un aspetto più vicino a un in-app `WebView` e consentono la personalizzazione dell'interfaccia utente di base. Per altre informazioni, vedere [schede personalizzate in Android](https://developer.chrome.com/multidevice/android/customtabs) .

Se non sono presenti pacchetti del browser nel dispositivo, MSAL usa il in-app `WebView` . Se l'impostazione predefinita del dispositivo non viene modificata, è necessario avviare lo stesso browser per ogni accesso per garantire un'esperienza SSO.

#### <a name="tested-browsers"></a>Browser testati

I browser seguenti sono stati testati per verificare se il reindirizzamento a specificato nel file di configurazione è stato eseguito correttamente `"redirect_uri"` :

| Dispositivo | Browser incorporato | Chrome | Opera  | Microsoft Edge | Browser UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | pass | pass |non applicabile |non applicabile |non applicabile |non applicabile |
| Samsung S7 (API 25) | passaggio<sup>1</sup> | pass | pass | pass | fail |pass |
| Huawei (API 26) |passaggio<sup>2</sup> | pass | fail | pass | pass |pass |
| Vivo (API 26) |pass|pass|pass|pass|pass|fail|
| Pixel 2 (API 26) |pass | pass | pass | pass | fail |pass |
| OPPO | pass | non applicabile<sup>3</sup>|non applicabile  |non applicabile |non applicabile | non applicabile|
| OnePlus (API 25) |pass | pass | pass | pass | fail |pass |
| Nexus (API 28) |pass | pass | pass | pass | fail |pass |
|MI | pass | pass | pass | pass | fail |pass |

<sup>1</sup> Il browser predefinito di Samsung è Samsung Internet.<br/>
<sup>2</sup> Il browser predefinito di Huawei è il browser Huawei.<br/>
<sup>3</sup> Non è possibile modificare il browser predefinito all'interno dell'impostazione del dispositivo oppo.

## <a name="next-steps"></a>Passaggi successivi

La [modalità dispositivo condiviso per i dispositivi Android](msal-android-shared-devices.md) consente di configurare un dispositivo Android in modo che possa essere facilmente condiviso da più dipendenti.