---
title: Come abilitare l'accesso Single Sign-On tra app su Android tramite ADAL | Documentazione Microsoft
description: "Come utilizzare le funzionalità dell'SDK ADAL per abilitare il Single Sign-On tra le applicazioni. "
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/13/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 4abf6bd2d82753e22d4fde92e219109274ce36be
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601275"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Come abilitare l'accesso Single Sign-On tra app su Android tramite ADAL
L'autenticazione Single Sign-On (SSO) con cui gli utenti devono inserire le proprie credenziali una sola volta per usare tutte le applicazioni è ora uno standard del settore. La difficoltà di immettere il nome utente e la password su uno schermo di piccole dimensioni, spesso abbinata a un fattore aggiuntivo (2FA) come una telefonata o un codice inviato tramite SMS, costituisce un problema per l'utente se la procedura va ripetuta più volte.

Se inoltre si applica una piattaforma delle identità che può essere usata da altre applicazioni, ad esempio Microsoft Accounts o un account aziendale di Microsoft 365, i clienti si aspettano che le credenziali siano disponibili all'uso in tutte le applicazioni, indipendentemente dal server di pubblicazione.

La piattaforma Microsoft Identity, insieme agli altri SDK di Microsoft Identity, soddisfa queste aspettative e consente di offrire l'SSO ai clienti nella propria suite di applicazioni oppure, analogamente alla funzionalità broker e alle applicazioni di Authenticator, in tutto il dispositivo.

Questa procedura illustra come configurare l'SDK all'interno dell'applicazione per offrire l'autenticazione SSO ai clienti.

Il documento precedente presuppone che si sappia come integrare la propria applicazione con il [Microsoft Identity Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concetti di SSO nella piattaforma Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Broker di Microsoft Identity
Microsoft offre applicazioni per ogni piattaforma per dispositivi mobili che consentono il bridging delle credenziali tra le applicazioni di fornitori diversi e offre particolari funzionalità avanzate che richiedono un'unica posizione sicura da cui convalidare le credenziali. L'SDK chiama queste applicazioni con il termine **broker**. Nei sistemi iOS e Android, i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati tramite push al dispositivo da una società che gestisce alcuni o tutti i dispositivi dei propri dipendenti. I broker supportano la gestione della sicurezza per alcune applicazioni soltanto o per tutto il dispositivo, a seconda di quanto stabilito dagli amministratori IT. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.

#### <a name="broker-assisted-logins"></a>Accessi assistiti da broker
Gli accessi assistiti da broker sono esperienze di accesso che si svolgono all'interno dell'applicazione broker e usano le risorse di archiviazione e la sicurezza del broker per condividere le credenziali tra tutte le applicazioni sul dispositivo che applicano la piattaforma Microsoft Identity. Ciò significa che le applicazioni si affidano al broker per l'accesso degli utenti. Nei sistemi iOS e Android, i broker sono inseriti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati tramite push al dispositivo da una società che gestisce il dispositivo dell'utente. Un esempio di questo tipo di applicazione è l'applicazione Microsoft Authenticator per iOS. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.
L'esperienza varia in base alla piattaforma e talvolta può essere un elemento di disturbo per gli utenti se non viene gestita correttamente. Chi ha installato l'applicazione Facebook e usa la funzionalità di connessione di Facebook in un'altra applicazione avrà probabilmente maggiore dimestichezza con questo modello. La piattaforma Microsoft Identity usa lo stesso modello.

In Android la selezione degli account viene visualizzata nella parte superiore dell'applicazione, con un impatto meno fastidioso per l'utente.

#### <a name="how-the-broker-gets-invoked"></a>Come viene richiamato il broker
Se nel dispositivo è installato un broker compatibile, ad esempio l'applicazione Microsoft Authenticator, gli SDK di Microsoft Identity richiamano automaticamente il broker quando un utente indica che vuole accedere con un account della piattaforma Microsoft Identity. 
 
 #### <a name="how-microsoft-ensures-the-application-is-valid"></a>Modalità con cui Microsoft assicura la validità dell'applicazione
 
 La necessità di verificare l'identità di una chiamata di applicazione al broker è fondamentale per la sicurezza garantita con l'accesso assistito da broker. I sistemi iOS e Android non applicano identificatori univoci che sono validi solo per una determinata applicazione, pertanto le applicazioni dannose possono effettuare lo "spoofing" di un identificatore dell'applicazione legittimo e ricevere i token per l'applicazione legittima. Per garantire che Microsoft comunichi sempre con l'applicazione giusta in fase di esecuzione, lo sviluppatore deve specificare un URI di reindirizzamento personalizzato quando registra la propria applicazione con Microsoft. **Le modalità di creazione dell'URI di reindirizzamento da parte degli sviluppatori vengono trattate in dettaglio di seguito.** Questo URI di reindirizzamento personalizzato contiene l'identificazione personale del certificato dell'applicazione e Google Play Store ne garantisce l'univocità per l'applicazione. Quando un'applicazione chiama il broker, questo richiede al sistema operativo Android per fornire l'identificazione personale del certificato che ha chiamato il broker. Il broker invia questa identificazione personale del certificato a Microsoft nella chiamata al sistema di identità. Se l'identificazione personale del certificato dell'applicazione non corrisponde all'identificazione personale del certificato specificata dallo sviluppatore durante la registrazione, verrà negato l'accesso ai token per la risorsa richiesta dall'applicazione. Questo controllo garantisce che solo l'applicazione registrata dallo sviluppatore riceva i token.

Gli accessi SSO con broker offrono i vantaggi seguenti:

* L'utente usufruisce del Single Sign-On per tutte le proprie applicazioni, indipendentemente dal fornitore.
* L'applicazione può usare funzionalità aziendali più avanzate, ad esempio l'Accesso condizionale o la suite di prodotti Microsoft InTune.
* L'applicazione può supportare l'autenticazione basata su certificati per gli utenti aziendali.
* Un'esperienza di accesso molto più sicura dato che le identità dell'applicazione e dell'utente vengono verificati dall'applicazione broker con algoritmi di sicurezza aggiuntivi e la crittografia.

Di seguito viene rappresentato il funzionamento degli SDK di Microsoft Identity con le applicazioni broker per abilitare l'SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

Sulla base di queste informazioni di base dovrebbe essere possibile comprendere meglio e implementare l'SSO all'interno dell'applicazione con la piattaforma e gli SDK di Microsoft Identity.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Attivazione di SSO per SSO assistito da broker
La possibilità per un'applicazione di usare qualsiasi broker installato nel dispositivo è **disattivata per impostazione predefinita**. Per usare l'applicazione con il broker, è necessario apportare alcune modifiche alla configurazione e aggiungere una parte di codice all'applicazione.

Ecco i passaggi da seguire:

1. Abilitare la modalità broker nella chiamata del codice dell'applicazione a MS SDK
2. Definire un nuovo URI di reindirizzamento e indicarlo sia all'app che alla registrazione dell'app
3. Impostazione delle autorizzazioni corrette nel manifesto Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passaggio 1: Abilitare la modalità broker nell'applicazione
La capacità dell'applicazione di usare il broker è attivata quando si creano le "impostazioni" o la configurazione iniziale dell'istanza di Authentication. Per eseguire questa operazione nell'app:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passaggio 2: Creare un nuovo URI di reindirizzamento con lo schema dell'URL
Per fare in modo che i token delle credenziali vengano sempre restituiti all'applicazione corretta, è necessario assicurarsi che il richiamo all'applicazione avvenga in un modo verificabile dal sistema operativo Android. Il sistema operativo Android utilizza in Google Play Store l'hash del certificato, che non può essere soggetto a spoofing da un'applicazione non autorizzata. Insieme all'URI dell'applicazione broker, Microsoft si assicura che i token vengano restituiti all'applicazione corretta. È necessario che nell'applicazione venga registrato un URI di reindirizzamento univoco.

L'URI di reindirizzamento deve essere nel formato corretto:

`msauth://packagename/Base64UrlencodedSignature`

Ad esempio: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

È possibile specificare l'URI di reindirizzamento nella registrazione dell'app tramite il [portale di Azure](https://portal.azure.com/). Per altre informazioni sulla registrazione di app Azure AD, vedere [Integrazione con Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Passaggio 3: Impostare le autorizzazioni corrette all'interno dell'applicazione
L'applicazione broker in Android usa la funzionalità Account Manager del sistema operativo Android per gestire le credenziali nelle applicazioni. Per utilizzare il broker in Android, il manifesto dell'app deve disporre delle autorizzazioni per usare gli account di AccountManager. Le autorizzazioni vengono discusse in modo dettagliato nella [documentazione di Google relativa ad Account Manager qui](http://developer.android.com/reference/android/accounts/AccountManager.html)

In particolare, le autorizzazioni sono:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>L'SSO è stato configurato!
Ora Microsoft Identity SDK condividerà automaticamente le credenziali tra le applicazioni e richiamerà il broker, se presente nel dispositivo.

