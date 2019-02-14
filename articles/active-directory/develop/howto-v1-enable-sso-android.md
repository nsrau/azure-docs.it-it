---
title: Come abilitare l'accesso Single Sign-On tra app su Android tramite ADAL | Documentazione Microsoft
description: Come usare le funzionalità dell'SDK ADAL per abilitare l'accesso Single Sign-On tra le applicazioni.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e27ecc73c19b92f8dcf77849c2f91a5eb57ac8e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168039"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Procedura: Abilitare l'accesso Single Sign-On tra app su Android tramite ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Con l'accesso Single Sign-On (SSO), gli utenti devono inserire le proprie credenziali solo una volta e le credenziali funzioneranno automaticamente non solo in tutte le applicazioni, ma anche nelle piattaforme utilizzate da altre applicazioni (ad esempio, account Microsoft o un account aziendale di Microsoft 365), indipendentemente dal server di pubblicazione.

La piattaforma delle identità di Microsoft, insieme agli SDK, consente di abilitare l'accesso Single Sign-On nella propria suite di app oppure, con la funzionalità broker e le applicazioni di Authenticator, in tutto il dispositivo.

In questa procedura si apprenderà come configurare l'SDK all'interno dell'applicazione per offrire l'autenticazione SSO ai clienti.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura presuppone che si sia in grado di:

- Effettuare il provisioning dell'app tramite il portale legacy per Azure Active Directory (Azure AD). Per altre informazioni, vedere [Register an app with the Azure AD v1.0 endpoint](quickstart-v1-add-azure-ad-app.md) (Registrare un'app con l'endpoint Azure AD v1.0).
- Integrare l'applicazione con [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Concetti dell'accesso Single Sign-On

### <a name="identity-brokers"></a>Broker di identità

Microsoft offre applicazioni per ogni piattaforma per dispositivi mobili che consente il bridging delle credenziali tra le applicazioni di fornitori diversi e offre funzionalità avanzate che richiedono un'unica posizione sicura da cui convalidare le credenziali. Queste applicazioni prendono il nome di **broker**.

Nei sistemi iOS e Android, i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati tramite push al dispositivo da una società che gestisce alcuni o tutti i dispositivi dei propri dipendenti. I broker supportano la gestione della sicurezza per alcune applicazioni soltanto o per tutto il dispositivo, a seconda della configurazione stabilita dagli amministratori IT. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.

#### <a name="broker-assisted-login"></a>Accesso assistito da broker

Gli accessi assistiti da broker sono esperienze di accesso che si svolgono all'interno dell'applicazione broker e usano le risorse di archiviazione e la sicurezza del broker per condividere le credenziali tra tutte le applicazioni sul dispositivo che applicano la piattaforma delle identità. Ciò significa che le applicazioni si affidano al broker per l'accesso degli utenti. Nei sistemi iOS e Android, i broker sono inseriti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati tramite push al dispositivo da una società che gestisce il dispositivo dell'utente. Un esempio di questo tipo di applicazione è l'applicazione Microsoft Authenticator per iOS. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.
L'esperienza varia in base alla piattaforma e talvolta può essere un elemento di disturbo per gli utenti se non viene gestita correttamente. Chi ha installato l'applicazione Facebook e usa la funzionalità di connessione di Facebook in un'altra applicazione avrà probabilmente maggiore dimestichezza con questo modello. La piattaforma delle identità usa lo stesso modello.

In Android la selezione degli account viene visualizzata nella parte superiore dell'applicazione, con un impatto meno fastidioso per l'utente.

#### <a name="how-the-broker-gets-invoked"></a>Come viene richiamato il broker

Se nel dispositivo è installato un broker compatibile, ad esempio l'applicazione Microsoft Authenticator, gli SDK delle identità richiamano automaticamente il broker quando un utente indica che vuole accedere con un account della piattaforma delle identità.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Modalità con cui Microsoft assicura la validità dell'applicazione

La necessità di verificare l'identità di una chiamata di applicazione al broker è fondamentale per la sicurezza garantita con l'accesso assistito da broker. I sistemi iOS e Android non applicano identificatori univoci che sono validi solo per una determinata applicazione, pertanto le applicazioni dannose possono effettuare lo "spoofing" di un identificatore dell'applicazione legittimo e ricevere i token per l'applicazione legittima. Per garantire che Microsoft comunichi sempre con l'applicazione giusta in fase di esecuzione, lo sviluppatore deve specificare un URI di reindirizzamento personalizzato quando registra la propria applicazione con Microsoft. **Le modalità di creazione dell'URI di reindirizzamento da parte degli sviluppatori vengono trattate in dettaglio di seguito.** Questo URI di reindirizzamento personalizzato contiene l'identificazione personale del certificato dell'applicazione e Google Play Store ne garantisce l'univocità per l'applicazione. Quando un'applicazione chiama il broker, questo richiede al sistema operativo Android per fornire l'identificazione personale del certificato che ha chiamato il broker. Il broker invia questa identificazione personale del certificato a Microsoft nella chiamata al sistema di identità. Se l'identificazione personale del certificato dell'applicazione non corrisponde all'identificazione personale del certificato specificata dallo sviluppatore durante la registrazione, verrà negato l'accesso ai token per la risorsa richiesta dall'applicazione. Questo controllo garantisce che solo l'applicazione registrata dallo sviluppatore riceva i token.

Gli accessi SSO con broker offrono i vantaggi seguenti:

* L'utente usufruisce del Single Sign-On per tutte le proprie applicazioni, indipendentemente dal fornitore.
* L'applicazione può usare funzionalità aziendali più avanzate, ad esempio l'Accesso condizionale o la suite di prodotti Microsoft InTune.
* L'applicazione può supportare l'autenticazione basata su certificati per gli utenti aziendali.
* Un'esperienza di accesso molto più sicura dato che le identità dell'applicazione e dell'utente vengono verificati dall'applicazione broker con algoritmi di sicurezza aggiuntivi e la crittografia.

Di seguito viene rappresentato il funzionamento degli SDK con le applicazioni broker per abilitare l'SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Attivazione di SSO per SSO assistito da broker

La possibilità per un'applicazione di usare qualsiasi broker installato nel dispositivo è disattivata per impostazione predefinita. Per usare l'applicazione con il broker, è necessario apportare alcune modifiche alla configurazione e aggiungere una parte di codice all'applicazione.

Ecco i passaggi da seguire:

1. Abilitare la modalità broker nella chiamata del codice dell'applicazione a MS SDK
2. Definire un nuovo URI di reindirizzamento e indicarlo sia all'app che alla registrazione dell'app
3. Impostazione delle autorizzazioni corrette nel manifesto Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passaggio 1: Abilitare la modalità broker nell'applicazione

La capacità dell'applicazione di usare il broker è attivata quando si creano le "impostazioni" o la configurazione iniziale dell'istanza di Authentication. Per eseguire questa operazione nell'app:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passaggio 2: Stabilire un nuovo URI di reindirizzamento con lo schema URL

Per fare in modo che i token delle credenziali vengano sempre restituiti all'applicazione corretta, è necessario assicurarsi che il richiamo all'applicazione avvenga in un modo verificabile dal sistema operativo Android. Il sistema operativo Android utilizza in Google Play Store l'hash del certificato, che non può essere soggetto a spoofing da un'applicazione non autorizzata. Insieme all'URI dell'applicazione broker, Microsoft si assicura che i token vengano restituiti all'applicazione corretta. È necessario che nell'applicazione venga registrato un URI di reindirizzamento univoco.

L'URI di reindirizzamento deve essere nel formato corretto:

`msauth://packagename/Base64UrlencodedSignature`

Ad esempio: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

È possibile specificare l'URI di reindirizzamento nella registrazione dell'app tramite il [portale di Azure](https://portal.azure.com/). Per altre informazioni sulla registrazione di app Azure AD, vedere [Integrazione con Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Passaggio 3: Impostare le autorizzazioni corrette all'interno dell'applicazione

L'applicazione broker in Android usa la funzionalità Account Manager del sistema operativo Android per gestire le credenziali nelle applicazioni. Per utilizzare il broker in Android, il manifesto dell'app deve disporre delle autorizzazioni per usare gli account di AccountManager. Le autorizzazioni vengono discusse in modo dettagliato nella [documentazione di Google relativa ad Account Manager qui](https://developer.android.com/reference/android/accounts/AccountManager.html)

In particolare, le autorizzazioni sono:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>L'SSO è stato configurato!

Ora l'SDK delle identità condividerà automaticamente le credenziali tra le applicazioni e richiamerà il broker, se presente nel dispositivo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [Protocollo SAML per Single Sign-On](single-sign-on-saml-protocol.md)
