---
title: Come abilitare l&quot;accesso Single Sign-On tra app su Android tramite ADAL | Documentazione Microsoft
description: "Come utilizzare le funzionalità dell&quot;SDK ADAL per abilitare il Single Sign-On tra le applicazioni. "
services: active-directory
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 3af96dd06223c4b32ccd3e1d73e1017980fcbccc
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Come abilitare l'accesso Single Sign-On tra app su Android tramite ADAL
Ora i clienti si aspettano che l'accesso SSO (Single Sign-On) venga fornito in modo che gli utenti debbano inserire le loro credenziali una volta sola e che le credenziali valgano automaticamente per le varie applicazioni. La difficoltà di immissione di nome utente e password su uno schermo di piccole dimensioni, spesso abbinata a un fattore aggiuntivo (2FA) come una telefonata o un codice inviato tramite SMS, rende l'utente insoddisfatto se questa procedura va ripetuta più volte per il prodotto.

Inoltre, se si applica una piattaforma delle identità che può essere usata da altre applicazioni, ad esempio Microsoft Accounts o un account aziendale di Office365, i clienti si aspettano che le credenziali siano disponibili all'uso in tutte le applicazioni, indipendentemente dal fornitore.

La piattaforma Microsoft Identity, insieme agli altri SDK di Microsoft Identity, soddisfa queste aspettative e consente di offrire l'SSO ai clienti nella propria suite di applicazioni oppure, analogamente alla funzionalità broker e alle applicazioni di Authenticator, in tutto il dispositivo.

Questa procedura illustrerà come configurare l'SDK all'interno dell'applicazione per offrire questo vantaggio ai clienti.

Questa procedura si applica a:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Accesso condizionale di Azure Active Directory

Il documento precedente presuppone che si sappia come effettuare il [provisioning delle applicazioni nel portale legacy per Azure Active Directory](active-directory-how-to-integrate.md) e che l'applicazione sia stata integrata con [Microsoft Identity Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concetti di SSO nella piattaforma Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Broker di Microsoft Identity
Microsoft offre applicazioni per ogni piattaforma mobile che consentono il bridging delle credenziali tra le applicazioni di fornitori diversi e offre particolari funzionalità avanzate che richiedono un'unica posizione sicura da cui convalidare le credenziali. Queste applicazioni sono dette **broker**. Nei sistemi iOS e Android i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati al dispositivo da una società che gestisce alcuni o tutti i dispositivi dei suoi dipendenti. I broker supportano la gestione della sicurezza per alcune applicazioni soltanto o per tutto il dispositivo, a seconda di quanto stabilito dagli amministratori IT. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.

Per altre informazioni su come vengono usati questi broker e come vengono visualizzati dai clienti nel flusso di accesso per la piattaforma Microsoft Identity, continuare a leggere.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Modelli di accesso dai dispositivi mobili
Nella piattaforma Microsoft Identity l'accesso alle credenziali nei dispositivi avviene in base a due modelli:

* Accessi non assistiti da broker
* Accessi assistiti da broker

#### <a name="non-broker-assisted-logins"></a>Accessi non assistiti da broker
Gli accessi non assistiti da broker sono esperienze di accesso in linea con l'applicazione e utilizzano le risorse di archiviazione locali nel dispositivo per l'applicazione. Le risorse di archiviazione possono essere condivise tra le applicazioni, ma le credenziali sono strettamente associate all'applicazione oppure a suite di applicazioni che usano le credenziali. Questa è l'esperienza più frequente in molte applicazioni per dispositivi mobili in cui si immettono un nome utente e una password all'interno dell'applicazione stessa.

Questi tipi di accessi offrono i seguenti vantaggi:

* L'esperienza utente si svolge interamente all'interno dell'applicazione.
* Le credenziali possono essere condivise tra applicazioni firmate dallo stesso certificato, offrendo un'esperienza di Single Sign-On alla suite di applicazioni.
* Il controllo dell'esperienza di accesso viene fornito all'applicazione prima e dopo l'accesso.

Questi tipi di accessi presentano i seguenti svantaggi:

* L'utente non può eseguire il Single Sign-On per tutte le app che usano un'identità Microsoft, ma solo per le identità Microsoft configurate dall'applicazione.
* L'applicazione non può essere usata con funzionalità aziendali più avanzate, ad esempio l'Accesso condizionale o la suite di prodotti InTune.
* L'applicazione non supporta l'autenticazione basata su certificati per gli utenti aziendali.

Di seguito viene illustrato il funzionamento degli SDK di Microsoft Identity con risorse di archiviazione condivise delle applicazioni per abilitare l'SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Accessi assistiti da broker
Gli accessi assistiti da broker sono esperienze di accesso che si svolgono all'interno dell'applicazione broker e usano le risorse di archiviazione e la sicurezza del broker per condividere le credenziali tra tutte le applicazioni sul dispositivo che applicano la piattaforma Microsoft Identity. Ciò significa che le applicazioni si affidano al broker per l'accesso degli utenti. Nei sistemi iOS e Android i broker sono inseriti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati al dispositivo da una società che gestisce il dispositivo dell'utente. Un esempio di questo tipo di applicazione è l'applicazione Azure Authenticator basata su iOS. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.
L'esperienza varia in base alla piattaforma e talvolta può essere un elemento di disturbo per gli utenti se non viene gestita correttamente. Chi ha installato l'applicazione Facebook e usa la funzionalità di connessione di Facebook in un'altra applicazione avrà probabilmente maggiore dimestichezza con questo modello. La piattaforma Microsoft Identity usa lo stesso modello.

Nel sistema operativo iOS questo genera un'animazione di "transizione" in cui l'applicazione viene inviata sullo sfondo mentre le applicazioni di Azure Authenticator vengono portate in primo piano in modo che l'utente possa scegliere con quale account accedere.  

Nei sistemi Android e Windows la selezione degli account viene visualizzata in alto nell'applicazione, con un impatto meno fastidioso per l'utente.

#### <a name="how-the-broker-gets-invoked"></a>Come viene richiamato il broker
Se nel dispositivo è installato un broker compatibile, ad esempio l'applicazione Azure Authenticator, gli SDK di Microsoft Identity richiamano automaticamente il broker quando un utente indica che vuole accedere con un account della piattaforma Microsoft Identity. Potrebbe trattarsi di un account Microsoft personale, di un account aziendale o dell'istituto di istruzione o di un account dato e ospitato in Azure con i prodotti B2C e B2B. 
 
 #### <a name="how-we-ensure-the-application-is-valid"></a>Come si garantisce la validità dell'applicazione
 
 La necessità di verificare l'identità di una chiamata di applicazione al broker è fondamentale per la sicurezza garantita con l'accesso assistito da broker. Né IOS né Android applicano identificatori univoci che sono validi solo per una determinata applicazione, cosicché le applicazioni dannose possono effettuare lo "spoofing" di un identificatore dell'applicazione legittimo e ricevere i token per l'applicazione legittima. Per garantire sempre la comunicazione con l'applicazione corretta in fase di esecuzione, chiediamo agli sviluppatori di assicurare un URI di reindirizzamento personalizzato al momento della registrazione dell'applicazione con Microsoft. **Le modalità di creazione dell'URI di reindirizzamento da parte degli sviluppatori vengono trattate in dettaglio di seguito.** Questo URI di reindirizzamento personalizzato contiene l'identificazione personale del certificato dell'applicazione e Google Play Store ne garantisce l'univocità per l'applicazione. Quando un'applicazione chiama il broker, questo richiede al sistema operativo Android per fornire l'identificazione personale del certificato che ha chiamato il broker. Il broker offre questa identificazione personale del certificato a Microsoft nella chiamata al sistema di identità. Se l'identificazione personale del certificato dell'applicazione non corrisponde all'identificazione personale del certificato offerto dallo sviluppatore durante la registrazione, verrà negato l'accesso ai token per la risorsa richiesti dall'applicazione. Tale controllo garantisce che solo l'applicazione registrata dallo sviluppatore riceva i token.

**Lo sviluppatore può scegliere se Microsoft Identity SDK deve chiamare il broker o usare il flusso non assistito dal broker.** Se lo sviluppatore decide di non usare il flusso assistito dal broker, rinuncia al vantaggio dell'uso delle credenziali SSO che l'utente potrebbe avere già aggiunto nel dispositivo e impedisce di usare l'applicazione con le funzionalità aziendali offerte da Microsoft, ad esempio l'Accesso condizionale, le funzionalità di gestione di Intune e l'autenticazione basata su certificati.

Questi tipi di accessi offrono i seguenti vantaggi:

* L'utente usufruisce del Single Sign-On per tutte le proprie applicazioni, indipendentemente dal fornitore.
* L'applicazione può usare funzionalità aziendali più avanzate, ad esempio l'Accesso condizionale o la suite di prodotti InTune.
* L'applicazione può supportare l'autenticazione basata su certificati per gli utenti aziendali.
* Un'esperienza di accesso molto più sicura dato che l'identità dell'applicazione e l'utente vengono verificati dall'applicazione broker con algoritmi di sicurezza aggiuntivi e la crittografia.

Questi tipi di accessi presentano i seguenti svantaggi:

* Nel sistema iOS l'utente viene trasferito dall'esperienza dell'applicazione durante la selezione delle credenziali.
* Perdita della possibilità di gestire l'esperienza di accesso per i clienti nell'applicazione.

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

## <a name="enabling-cross-app-sso-using-adal"></a>Abilitazione di SSO tra app tramite ADAL
In questo esempio si usa l'SDK ADAL Android per:

* Attivare SSO non assistito da broker per la suite di app
* Attivare il supporto per SSO assistito da broker

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Attivazione dell'SSO per l'SSO non assistito da broker
Per l'SSO non assistito da broker tra applicazioni, gli SDK di Microsoft Identity risolvono automaticamente gran parte delle complessità dell'SSO, tra cui la ricerca dell'utente corretto nella cache e la gestione di un elenco di utenti connessi di cui è possibile effettuare una query.

Per abilitare l'SSO tra le applicazioni di cui si è proprietari, eseguire le operazioni seguenti:

1. Verificare che tutte le applicazioni usino lo stesso ID client o ID applicazione.
2. Verificare che tutte le applicazioni abbiano lo stesso set di SharedUserID.
3. Verificare che tutte le applicazioni condividano lo stesso certificato di firma da Google Play Store per condividere l'archiviazione.

#### <a name="step-1-using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Passaggio 1: Utilizzo dello stesso ID client / ID applicazione per tutte le applicazioni della suite di applicazioni
Per comunicare alla piattaforma Microsoft Identity che è consentita la condivisione dei token tra le applicazioni, ciascuna delle applicazioni dovrà condividere lo stesso ID client o ID applicazione. Si tratta dell'identificatore univoco fornito al momento della registrazione della prima applicazione nel portale.

Ci si potrebbe chiedere come si fa a identificare le varie applicazioni nel servizio di gestione delle identità Microsoft se tutte utilizzano lo stesso ID applicazione. La risposta sono gli **URI di reindirizzamento**. Ogni applicazione può avere più URI di reindirizzamento registrati nel portale di caricamento. Ogni app della suite avrà un URI di reindirizzamento diverso. La situazione potrebbe essere simile alla seguente:

URI di reindirizzamento dell'app 1: `msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D`

URI di reindirizzamento dell'app 2: `msauth://com.example.userapp1/KmB7PxIytyLkbGHuI%2UitkW%2Fejk%4E`

URI di reindirizzamento dell'app 3: `msauth://com.example.userapp2/Pt85PxIyvbLkbKUtBI%2SitkW%2Fejk%9F`

....

Gli URI vengono nidificati sotto lo stesso ID client / ID applicazione e cercati in base all'URI di reindirizzamento restituito nella configurazione dell'SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Il formato degli URI di reindirizzamento viene illustrato di seguito. È possibile utilizzare qualsiasi URI di reindirizzamento ad eccezione del caso in cui si desideri supportare il broker, nel qual caso gli URI di reindirizzamento devono essere simili ai precedenti*

#### <a name="step-2-configuring-shared-storage-in-android"></a>Passaggio 2: Configurazione dell'archiviazione condivisa in Android
L'impostazione di `SharedUserID` esula dal contesto di questo documento, ma per maggiori informazioni si può leggere la documentazione di Google Android su [Manifest](http://developer.android.com/guide/topics/manifest/manifest-element.html). È importante decidere il nome di sharedUserID e usarlo in tutte le applicazioni.

Dopo aver creato l'attributo `SharedUserID` in tutte le applicazioni, si può usare SSO.

> [!WARNING]
> Quando si condivide una risorsa di archiviazione tra le applicazioni, qualsiasi applicazione può eliminare utenti o peggio ancora eliminare tutti i token dell'applicazione. Questo può essere un problema grave se sono presenti applicazioni che usano i token per svolgere operazioni in background. La condivisione della risorsa di archiviazione implica che è necessario prestare grande attenzione durante tutte le operazioni di eliminazione tramite gli SDK di Microsoft Identity.
> 
> 

L'operazione è terminata. L'SDK di Microsoft Identity condividerà le credenziali tra tutte le applicazioni. Anche l'elenco degli utenti verrà condiviso tra le istanze dell'applicazione.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Attivazione di SSO per SSO assistito da broker
La possibilità per un'applicazione di usare qualsiasi broker installato nel dispositivo è **disattivata per impostazione predefinita**. Per usare l'applicazione con il broker è necessario apportare alcune modifiche alla configurazione e aggiungere una parte di codice all'applicazione.

Ecco i passaggi da seguire:

1. Abilitare la modalità broker nella chiamata del codice dell'applicazione dell'SDK MS
2. Definire un nuovo URI di reindirizzamento e indicarlo sia all'app che alla registrazione dell'app
3. Impostazione delle autorizzazioni corrette nel manifesto Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passaggio 1: Abilitare la modalità broker nell'applicazione
La capacità dell'applicazione di usare il broker è attivata quando si creano le "impostazioni" o la configurazione iniziale dell'istanza di Authentication. A tal fine, impostare il tipo ApplicationSettings nel codice:

```
AuthenticationSettings.Instance.setUseBroker(true);
```


#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passaggio 2: Creare un nuovo URI di reindirizzamento con lo schema dell'URL
Per fare in modo che i token delle credenziali vengano sempre restituiti all'applicazione corretta, è necessario assicurarsi che il richiamo dell'applicazione avvenga in un modo verificabile dal sistema operativo Android. Il sistema operativo Android utilizza in Google Play Store l'hash del certificato, che non può essere contraffatto da un'applicazione non autorizzata. Di conseguenza, l'hash del certificato viene usato insieme all'URI dell'applicazione broker per garantire che i token vengano restituiti all'applicazione corretta. È necessario definire questo URI di reindirizzamento univoco nell'applicazione e impostarlo come URI di reindirizzamento nel portale per gli sviluppatori.

L'URI di reindirizzamento deve essere nel formato corretto:

`msauth://packagename/Base64UrlencodedSignature`

Ad esempio: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

L'URI di reindirizzamento deve essere specificato nella registrazione dell'app tramite il [portale di Azure](https://portal.azure.com/). Per altre informazioni sulla registrazione di app Azure AD, vedere [Integrazione con Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Passaggio 3: Impostare le autorizzazioni corrette all'interno dell'applicazione
L'applicazione broker in Android usa la funzionalità AccountManager del sistema operativo Android per gestire le credenziali nelle applicazioni. Per utilizzare il broker in Android, il manifesto dell'app deve disporre delle autorizzazioni per usare gli account di AccountManager. Questo argomento viene discusso in modo dettagliato nella [documentazione di Google relativa ad AccountManager qui](http://developer.android.com/reference/android/accounts/AccountManager.html)

In particolare, le autorizzazioni sono:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>L'SSO è stato configurato!
Ora Microsoft Identity SDK condividerà automaticamente le credenziali tra le applicazioni e richiamerà il broker, se presente nel dispositivo.


