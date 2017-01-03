---
title: Come abilitare l&quot;accesso Single Sign-On tra app in iOS usando ADAL | Microsoft Docs
description: "Come utilizzare le funzionalità dell&quot;SDK ADAL per abilitare il Single Sign-On tra le applicazioni. "
services: active-directory
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/16/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a213fc71697a4ed0917df29fb32617d6e389f0b6


---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Come abilitare l'accesso Single Sign-On tra app in iOS usando ADAL
Ora i clienti si aspettano che l'accesso SSO (Single Sign-On) venga fornito in modo che gli utenti debbano inserire le loro credenziali una volta sola e che le credenziali funzionino automaticamente per le varie applicazioni. La difficoltà di immissione di nome utente e password su uno schermo di piccole dimensioni, spesso abbinata a un fattore aggiuntivo (2FA) come una telefonata o un codice inviato tramite SMS, rende l'utente insoddisfatto se questa procedura va ripetuta più volte per il prodotto.

Inoltre, se ci si serve di una piattaforma delle identità che può essere usata da altre applicazioni, ad esempio Microsoft Accounts o un account aziendale di Office365, i clienti si aspettano che le credenziali siano disponibili all'uso in tutte le applicazioni, indipendentemente dal fornitore.

La piattaforma Microsoft Identity, insieme agli altri SDK di Microsoft Identity, soddisfa queste aspettative e consente di offrire l'SSO ai clienti nella propria suite di applicazioni oppure, analogamente alla funzionalità broker e alle applicazioni di Authenticator, in tutto il dispositivo.

Questa procedura illustrerà come configurare l'SDK all'interno dell'applicazione per offrire questo vantaggio ai clienti.

Questa procedura si applica a:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Accesso condizionale di Azure Active Directory

Si noti che il documento seguente presuppone che si sappia come effettuare il [provisioning delle applicazioni nel portale legacy per Azure Active Directory](develop/active-directory-how-to-integrate.md) e che l'applicazione sia stata integrata con [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Concetti di SSO nella piattaforma Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Broker di Microsoft Identity
Microsoft fornisce applicazioni per ogni piattaforma mobile che consentono il bridging delle credenziali tra le applicazioni di fornitori diversi nonché offrono particolari funzionalità avanzate che richiedono un'unica posizione sicura da cui convalidare le credenziali. Queste applicazioni sono dette **broker**. Nei sistemi iOS e Android i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati al dispositivo da una società che gestisce alcuni o tutti i dispositivi dei suoi dipendenti. I broker supportano la gestione della sicurezza per alcune applicazioni soltanto o per tutto il dispositivo, a seconda di quanto stabilito dagli amministratori IT. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.

Per comprendere come vengono usati questi broker e come vengono visualizzati dai clienti nel flusso di accesso per la piattaforma Microsoft Identity, continuare a leggere per altre informazioni.

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

* L'utente non può eseguire il Single Sign-On a tutte le app che usano un'identità Microsoft ma solo alle identità Microsoft proprie dell'applicazione che sono state configurate.
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
Gli accessi assistiti da broker sono esperienze di accesso che si svolgono all'interno dell'applicazione broker e usano le risorse di archiviazione e la sicurezza del broker per condividere le credenziali tra tutte le applicazioni sul dispositivo che usano la piattaforma Microsoft Identity. Ciò significa che le applicazioni si affidano al broker per l'accesso degli utenti. Nei sistemi iOS e Android i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati al dispositivo da una società che gestisce il dispositivo dell'utente. Un esempio di questo tipo di applicazione è l'applicazione Azure Authenticator basata su iOS. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.
L'esperienza varia in base alla piattaforma e talvolta può essere un elemento di disturbo per gli utenti se non viene gestita correttamente. Chi ha installato l'applicazione Facebook e usa la funzionalità di accesso di Facebook in un'altra applicazione avrà probabilmente maggiore dimestichezza con questo modello. La piattaforma Microsoft Identity si basa sullo stesso modello.

Nel sistema operativo iOS questo genera un'animazione di "transizione" in cui l'applicazione viene inviata sullo sfondo mentre le applicazioni di Azure Authenticator vengono portate in primo piano in modo che l'utente possa scegliere con quale account accedere.  

Nei sistemi Android e Windows la selezione degli account viene visualizzata in alto nell'applicazione, con un impatto meno fastidioso per l'utente.

#### <a name="how-the-broker-gets-invoked"></a>Come viene richiamato il broker
Se nel dispositivo è installato un broker compatibile, ad esempio l'applicazione Azure Authenticator, gli SDK di Microsoft Identity richiamano automaticamente il broker quando un utente indica che vuole accedere con un account della piattaforma Microsoft Identity. Potrebbe trattarsi di un account Microsoft personale, di un account aziendale o dell'istituto di istruzione o di un account fornito e ospitato in Azure con i prodotti B2C e B2B. Usando la crittografia e algoritmi estremamente sicuri si ha la certezza che le credenziali vengano richieste e fornite all'applicazione in modo sicuro. I particolari tecnici esatti di questi meccanismi non sono stati pubblicati ma sono stati sviluppati in collaborazione con Apple e Google.

**Lo sviluppatore può scegliere se Microsoft Identity SDK deve chiamare il broker o usare il flusso non assistito dal broker.**  Se lo sviluppatore decide di non usare il flusso assistito dal broker, rinuncia al vantaggio dell'utilizzo delle credenziali SSO che l'utente potrebbe avere già aggiunto nel dispositivo, nonché impedisce di usare l'applicazione con le funzionalità aziendali fornite da Microsoft, ad esempio l'Accesso condizionale, le funzionalità di gestione di Intune e l'autenticazione basata su certificati.

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
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
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
In questo esempio si userà ADAL iOS SDK per:

* Attivare SSO non assistito da broker per la suite di app
* Attivare il supporto per SSO assistito da broker

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Attivazione dell'SSO per l'SSO non assistito da broker
Per l'SSO non assistito da broker tra applicazioni, gli SDK di Microsoft Identity risolvono automaticamente gran parte delle complessità dell'SSO, tra cui la ricerca dell'utente corretto nella cache e la gestione di un elenco di utenti connessi di cui è possibile effettuare una query.

Per abilitare l'SSO tra le applicazioni di cui si è proprietari, eseguire le operazioni seguenti:

1. Verificare che tutte le applicazioni usino lo stesso ID client o ID applicazione.
2. Verificare che tutte le applicazioni condividano lo stesso certificato di firma di Apple per poter condividere i portachiavi.
3. Richiedere lo stesso diritto per i portachiavi per ogni applicazione.
4. Indicare agli SDK di Microsoft Identity il portachiavi condiviso da usare.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Uso dello stesso ID client/ID applicazione per tutte le applicazioni della suite di applicazioni
Per indicare alla piattaforma Microsoft Identity che è consentita la condivisione dei token tra le applicazioni, ogni applicazione dovrà condividere lo stesso ID client o ID applicazione. Si tratta dell'identificatore univoco fornito al momento della registrazione della prima applicazione nel portale.

Ci si potrebbe chiedere come si fa a identificare le varie applicazioni nel servizio di gestione delle identità Microsoft se tutte utilizzano lo stesso ID applicazione. La risposta sono gli **URI di reindirizzamento**. Ogni applicazione può avere più URI di reindirizzamento registrati nel portale di caricamento. Ogni app della suite avrà un URI di reindirizzamento diverso. La situazione potrebbe essere simile alla seguente:

URI di reindirizzamento dell'app 1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

URI di reindirizzamento dell'app 2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI di reindirizzamento dell'app 3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

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


*Il formato degli URI di reindirizzamento viene illustrato di seguito. È possibile usare qualsiasi URI di reindirizzamento a meno che non si voglia supportare il broker, nel qual caso deve essere simile ai precedenti*

#### <a name="create-keychain-sharing-between-applications"></a>Creare la condivisione dei portachiavi tra le applicazioni
L'abilitazione della condivisione dei portachiavi esula dall'ambito di questo documento ed è illustrata da Apple nel documento relativo all' [Adding Capabilities](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)(Aggiunta di funzionalità). È importante decidere quale portachiavi deve essere chiamato e aggiungere tale funzionalità in tutte le applicazioni.

Una volta configurati correttamente i diritti, nella directory del progetto verrà visualizzato un file denominato `entitlements.plist` il cui contenuto è simile al seguente:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Una volta abilitato il diritto per i portachiavi in ogni applicazione e quando si è pronti a usare SSO, notificare il portachiavi a Microsoft Identity SDK usando l'impostazione seguente in `ADAuthenticationSettings` :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando si condivide un portachiavi tra le applicazioni, qualsiasi applicazione può eliminare utenti o peggio ancora eliminare tutti i token dell'applicazione. Questo può essere un problema grave se sono presenti applicazioni che usano i token per svolgere operazioni in background. La condivisione di un portachiavi implica che è necessario prestare grande attenzione durante tutte le operazioni di eliminazione con gli SDK di Microsoft Identity.
> 
> 

L'operazione è terminata. L'SDK di Microsoft Identity condividerà le credenziali tra tutte le applicazioni. Anche l'elenco degli utenti verrà condiviso tra le istanze dell'applicazione.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Attivazione di SSO per SSO assistito da broker
La possibilità per un'applicazione di usare qualsiasi broker installato nel dispositivo è **disattivata per impostazione predefinita**. Per usare l'applicazione con il broker è necessario apportare alcune modifiche alla configurazione e aggiungere una parte di codice all'applicazione.

Ecco i passaggi da seguire:

1. Abilitare la modalità broker nella chiamata del codice dell'applicazione a MS SDK.
2. Stabilire un nuovo URI di reindirizzamento e fornirlo sia all'app che alla registrazione dell'app
3. Registrazione di uno schema URL.
4. Supporto per iOS9: aggiungere un'autorizzazione al file info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Passaggio 1: Abilitare la modalità broker nell'applicazione
La possibilità per l'applicazione di usare il broker viene attivata quando si crea il "contesto" o la configurazione iniziale dell'oggetto di autenticazione. A tal fine, impostare il tipo delle credenziali nel codice:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
L'impostazione `AD_CREDENTIALS_AUTO` consentirà a Microsoft Identity SDK di provare a chiamare il broker, mentre `AD_CREDENTIALS_EMBEDDED` impedirà a Microsoft Identity SDK di chiamare il broker.

#### <a name="step-2-registering-a-url-scheme"></a>Passaggio 2: Registrazione di uno schema URL
La piattaforma Microsoft Identity usa gli URL per richiamare il broker e quindi restituire il controllo all'applicazione. Per completare tale round trip, è necessario uno schema URL registrato per l'applicazione, di cui la piattaforma Microsoft Identity verrà a conoscenza e che può andare ad aggiungersi a eventuali altri schemi app già registrati con l'applicazione.

> [!WARNING]
> È consigliabile rendere il più possibile univoco lo schema URL per ridurre al minimo le possibilità che un'altra app usi lo stesso schema URL. Apple non impone l'univocità degli schemi URL registrati nell'App Store.
> 
> 

Ecco un esempio di come appare nella configurazione del progetto. È anche possibile usare XCode:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Passaggio 3: Stabilire un nuovo URI di reindirizzamento con lo schema URL
Per assicurarsi che i token delle credenziali vengano sempre restituiti all'applicazione corretta, è necessario verificare che l'applicazione venga richiamata in un modo controllabile dal sistema operativo iOS. Il sistema operativo iOS segnala alle applicazioni broker Microsoft l'ID bundle dell'applicazione chiamante, di cui un'applicazione non autorizzata non può effettuare lo spoofing. Di conseguenza, l'hash del certificato viene usato insieme all'URI dell'applicazione broker per garantire che i token vengano restituiti all'applicazione corretta. È necessario definire questo URI di reindirizzamento univoco nell'applicazione e impostarlo come URI di reindirizzamento nel portale per gli sviluppatori.

L'URI di reindirizzamento deve essere nel formato corretto:

`<app-scheme>://<your.bundle.id>`

Ad esempio: *x-msauth-mytestiosapp://com.myapp.mytestapp*

L'URI di reindirizzamento deve essere specificato nella registrazione dell'app tramite il [portale di Azure classico](https://manage.windowsazure.com/). Per altre informazioni sulla registrazione di app Azure AD, vedere [Integrazione con Azure Active Directory](develop/active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Passaggio 3a: Aggiungere un URI di reindirizzamento nell'app e nel portale per sviluppatori per supportare l'autenticazione basata su certificati
Per supportare l'autenticazione basata su certificati, è necessario registrare un secondo "msauth" nell'applicazione e nel [portale di Azure classico](https://manage.windowsazure.com/) per gestire l'autenticazione del certificato, se si vuole aggiungere tale supporto nell'applicazione.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Ad esempio: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Passaggio 4: iOS9: Aggiungere un parametro di configurazione all'app
ADAL usa –canOpenURL: per controllare se il broker è installato nel dispositivo. In iOS 9 Apple ha bloccato gli schemi di cui un'applicazione può effettuare una query. Sarà necessario aggiungere "msauth" alla sezione LSApplicationQueriesSchemes di `info.plist file`.

<key>LSApplicationQueriesSchemes</key>

<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>L'SSO è stato configurato!
Ora Microsoft Identity SDK condividerà automaticamente le credenziali tra le applicazioni e richiamerà il broker, se presente nel dispositivo.




<!--HONumber=Nov16_HO3-->


