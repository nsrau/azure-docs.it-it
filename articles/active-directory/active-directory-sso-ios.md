<properties
	pageTitle="Come abilitare l'accesso Single Sign-On tra app in iOS usando ADAL | Microsoft Azure"
	description="Come usare le funzionalità di ADAL SDK per abilitare Single Sign-On tra le applicazioni."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/17/2015"
	ms.author="brandwe"/>


# Come abilitare l'accesso Single Sign-On tra app in iOS usando ADAL


Ora i clienti si aspettano che l'accesso SSO (Single Sign-On) venga fornito in modo che gli utenti debbano inserire le loro credenziali una volta sola e che le credenziali funzionino automaticamente per le varie applicazioni. La difficoltà di immissione di nome utente e password su uno schermo di piccole dimensioni, spesso abbinata a un fattore aggiuntivo (2FA) come una telefonata o un codice inviato con SMS, genera rapidamente insoddisfazione se un utente deve eseguire questa procedura più volte per il prodotto.

Inoltre, se si usa una piattaforma delle identità che può essere usata anche da altre applicazioni, ad esempio Microsoft Accounts o un account aziendale da Office 365, i clienti si aspettano che le credenziali siano disponibili all'uso in tutte le applicazioni, indipendentemente dal fornitore.

La piattaforma Microsoft Identity, insieme agli altri SDK di Microsoft Identity, fa tutto ciò e consente di fornire l'SSO ai clienti nella propria suite di applicazioni oppure, con la funzionalità broker e le applicazioni di Authenticator, in tutto il dispositivo.

Questa procedura illustrerà come configurare l'SDK all'interno dell'applicazione per offrire questo vantaggio ai clienti.

Questa procedura si applica a:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B


Si noti che il documento seguente presuppone che si sappia come [effettuare il provisioning delle applicazioni del portale legacy per Azure Active Directory](active-directory-how-to-integrate.md) nonché come integrare l'applicazione con [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## Concetti di SSO nella piattaforma Microsoft Identity

### Broker di Microsoft Identity

Microsoft fornisce per ogni piattaforma mobile applicazioni che consentono il bridging delle credenziali tra le applicazioni di fornitori diversi, oltre a offrire particolari funzionalità avanzate che richiedono un'unica posizione sicura da cui convalidare le credenziali. Queste applicazioni sono dette **broker**. Nei sistemi iOS e Android i broker vengono forniti tramite applicazioni scaricabili che i clienti installano in modo indipendente o di cui una società che gestisce alcuni o tutti i dispositivi dei dipendenti può effettuare il push nei dispositivi. I broker supportano la gestione della sicurezza per alcune applicazioni soltanto o per tutto il dispositivo, in base a quanto stabilito dagli amministratori IT. In Windows questa funzionalità viene fornita da uno strumento di selezione degli account predefinito del sistema operativo, noto come Web Authentication Broker.

Per comprendere come vengono usati questi broker e come i clienti possono visualizzarli nel flusso di accesso per la piattaforma Microsoft Identity, continuare a leggere l'articolo.

### Modelli di accesso dai dispositivi mobili

L'accesso alle credenziali nei dispositivi segue due modelli di base per la piattaforma Microsoft Identity:

* Accessi non assistiti da broker
* Accessi assistiti da broker

#### Accessi non assistiti da broker

Gli accessi non assistiti da broker sono esperienze di accesso che si verificano in linea con l'applicazione e usano l'archivio locale del dispositivo per l'applicazione. Questo archivio può essere condiviso tra le applicazioni, ma le credenziali sono strettamente associate all'app oppure alla suite di app che usano tali credenziali. Questa è l'esperienza probabilmente più comune in molte applicazioni per dispositivi mobili in cui si immettono un nome utente e una password direttamente nell'applicazione.

Questi accessi offrono i vantaggi seguenti:

-  L'esperienza utente si verifica interamente nell'applicazione.
-  Le credenziali possono essere condivise tra applicazioni firmate dallo stesso certificato, offrendo un'esperienza Single Sign-On alla suite di applicazioni. 
-  Il controllo dell'esperienza di accesso viene fornito all'applicazione prima e dopo l'accesso.

Questi accessi presentano gli svantaggi seguenti:

- L'utente non può eseguire l'accesso Single Sign-On in tutte le app che usano un'identità Microsoft, ma solo nelle identità Microsoft proprie dell'applicazione e che sono state configurate.
- L'applicazione non può essere usata con funzionalità aziendali più avanzate, ad esempio l'accesso condizionale o l'uso della suite di prodotti InTune.
- L'applicazione non supporta l'autenticazione basata su certificati per utenti aziendali.

Ecco come funzionano gli SDK di Microsoft Identity con l'archivio condiviso delle applicazioni per abilitare SSO:

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

#### Accessi assistiti da broker

Gli accessi assistiti da broker sono esperienze di accesso che si verificano nell'applicazione broker e usano l'archivio e la sicurezza del broker per condividere le credenziali tra tutte le applicazioni sul dispositivo che usufruiscono della piattaforma Microsoft Identity. Le applicazioni infatti si baseranno sul broker per far accedere gli utenti. Nei sistemi iOS e Android i broker vengono forniti tramite applicazioni scaricabili che i clienti installano in modo indipendente o di cui una società che gestisce i dispositivi degli utenti può effettuare il push nei dispositivi. Un esempio di questo tipo di applicazione è l'applicazione Azure Authenticator in iOS. In Windows questa funzionalità viene fornita da uno strumento di selezione degli account predefinito del sistema operativo, noto come Web Authentication Broker. L'esperienza varia in base alla piattaforma e talvolta può essere problematica per gli utenti se non viene gestita correttamente. È probabile che si abbia più familiarità con questo modello se si è installata l'applicazione Facebook e si usa la funzionalità di accesso di Facebook in un'altra applicazione. La piattaforma Microsoft Identity si basa sullo stesso modello.

Nel sistema operativo iOS questo genera un'animazione di "transizione" in cui l'applicazione viene inviata allo sfondo, mentre le applicazioni Azure Authenticator vengono portate in primo piano in modo che l'utente possa selezionare l'account per l'accesso.

Nei sistemi Android e Windows la selezione degli account viene visualizzata in alto nell'applicazione, disturbando meno l'utente.

#### Come viene richiamato il broker

Se nel dispositivo è installato un broker compatibile, ad esempio l'applicazione Azure Authenticator, gli SDK di Microsoft Identity richiamano automaticamente il broker quando un utente indica che vuole accedere con un account della piattaforma Microsoft Identity. Potrebbe trattarsi di un account Microsoft personale, di un account aziendale o dell'istituto di istruzione o di un account fornito e ospitato in Azure con i prodotti B2C e B2B. Usando la crittografia e algoritmi estremamente sicuri si ha la certezza che le credenziali vengano richieste e fornite all'applicazione in modo sicuro. Gli esatti dettagli tecnici di questi meccanismi non sono stati pubblicati, ma sono stati sviluppati in collaborazione con Apple e Google.

**Lo sviluppatore può scegliere se Microsoft Identity SDK deve chiamare il broker o usare il flusso non assistito dal broker.** Se tuttavia lo sviluppatore decide di non usare il flusso assistito dal broker, perde il vantaggio dell'utilizzo delle credenziali SSO che l'utente potrebbe avere già aggiunto nel dispositivo, oltre a impedire di usare l'applicazione con le funzionalità aziendali offerte da Microsoft ai clienti, ad esempio l'accesso condizionale, le funzionalità di gestione di Intune e l'autenticazione basata su certificati.

Questi accessi offrono i vantaggi seguenti:

-  L'utente può eseguire l'accesso SSO in tutte le applicazioni, indipendentemente dal fornitore.
-  L'applicazione può usufruire di funzionalità aziendali più avanzate, ad esempio l'accesso condizionale, o usare la suite di prodotti InTune.
-  L'applicazione può supportare l'autenticazione basata su certificati per gli utenti aziendali.
- Esperienza di accesso molto più sicura dato che l'identità dell'applicazione e quella dell'utente vengono verificate dall'applicazione broker con algoritmi di sicurezza aggiuntivi e con la crittografia.

Questi accessi presentano gli svantaggi seguenti:

- Nel sistema iOS l'utente viene escluso dall'esperienza dell'applicazione mentre vengono scelte le credenziali.
- Perdita della possibilità di gestire l'esperienza di accesso per i clienti nell'applicazione.



Ecco come funzionano gli SDK di Microsoft Identity con le applicazioni broker per abilitare SSO:

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
              
Sulla base di queste informazioni di base dovrebbe essere possibile comprendere meglio e implementare SSO nell'applicazione usando la piattaforma Microsoft Identity e gli SDK.


## Abilitazione di SSO tra app tramite ADAL

In questo esempio si userà ADAL iOS SDK per:

- Attivare SSO non assistito da broker per la suite di app
- Attivare il supporto per SSO assistito da broker

### Attivazione di SSO per SSO non assistito da broker

Per l'accesso SSO non assistito da broker tra applicazioni, gli SDK di Microsoft Identity gestiscono gran parte delle complessità di SSO, tra cui la ricerca dell'utente corretto nella cache e la gestione di un elenco di utenti connessi di cui è possibile effettuare una query.

Per abilitare SSO tra le applicazioni di cui si è proprietari, è necessario eseguire queste operazioni:

1. Verificare che tutte le applicazioni usino lo stesso ID client o ID applicazione. 
* Verificare che tutte le applicazioni condividano lo stesso certificato di firma di Apple per poter condividere i portachiavi.
* Richiedere lo stesso diritto per i portachiavi per ogni applicazione.
* Indicare agli SDK di Microsoft Identity il portachiavi condiviso da usare.

#### Uso dello stesso ID client/ID applicazione per tutte le applicazioni della suite di applicazioni

Per indicare alla piattaforma Microsoft Identity che è consentita la condivisione dei token tra le applicazioni, ogni applicazione dovrà condividere lo stesso ID client o ID applicazione. Questo è l'identificatore univoco fornito al momento della registrazione della prima applicazione nel portale.

Ci si potrebbe chiedere come identificare le varie app nel servizio Microsoft Identity se usano lo stesso ID applicazione. La risposta sono gli **URI di reindirizzamento**. Ogni applicazione può avere più URI di reindirizzamento registrati nel portale di caricamento. Ogni app della suite avrà un URI di reindirizzamento diverso. La situazione potrebbe essere simile alla seguente:

URI di reindirizzamento dell'app 1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

URI di reindirizzamento dell'app 2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI di reindirizzamento dell'app 3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Gli URI vengono annidati sotto lo stesso ID client/ID applicazione e cercati in base all'URI di reindirizzamento restituito a Microsoft durante la configurazione dell'SDK.

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



#### Creare la condivisione dei portachiavi tra le applicazioni

L'abilitazione della condivisione dei portachiavi esula dall'ambito di questo documento ed è illustrata da Apple nel documento relativo all'[aggiunta di funzionalità](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). È importante decidere quale portachiavi deve essere chiamato e aggiungere tale funzionalità in tutte le applicazioni.

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

Una volta abilitato il diritto per i portachiavi in ogni applicazione e quando si è pronti a usare SSO, notificare il portachiavi a Microsoft Identity SDK usando l'impostazione seguente in `ADAuthenticationSettings`:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING] 
Quando si condivide un portachiavi tra le applicazioni, qualsiasi applicazione può eliminare utenti o peggio ancora eliminare tutti i token dell'applicazione. Questo può essere un problema grave se sono presenti applicazioni che usano i token per svolgere operazioni in background. La condivisione di un portachiavi implica che è necessario prestare grande attenzione durante tutte le operazioni di eliminazione con gli SDK di Microsoft Identity.

L'operazione è terminata. Microsoft Identity SDK condividerà le credenziali tra tutte le applicazioni. Anche l'elenco degli utenti verrà condiviso tra le istanze dell'applicazione.

### Attivazione di SSO per SSO assistito da broker

La possibilità per un'applicazione di usare qualsiasi broker installato nel dispositivo è **disattivata per impostazione predefinita**. Per usare l'applicazione con il broker, è necessario apportare alcune modifiche alla configurazione e aggiungere codice all'applicazione.

Ecco i passaggi da seguire:

1. Abilitare la modalità broker nella chiamata del codice dell'applicazione a MS SDK.
2. Stabilire un nuovo URI di reindirizzamento e fornirlo sia all'app che alla registrazione dell'app
3. Registrazione di uno schema URL.
4. Supporto per iOS9: aggiungere un'autorizzazione al file info.plist.


#### Passaggio 1: Abilitare la modalità broker nell'applicazione
La possibilità per l'applicazione di usare il broker viene attivata quando si crea il "contesto" o la configurazione iniziale dell'oggetto di autenticazione. A tal fine, impostare il tipo delle credenziali nel codice:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
L'impostazione `AD_CREDENTIALS_AUTO` consentirà a Microsoft Identity SDK di provare a chiamare il broker, mentre `AD_CREDENTIALS_EMBEDDED` impedirà a Microsoft Identity SDK di chiamare il broker.

#### Passaggio 2: Registrazione di uno schema URL
La piattaforma Microsoft Identity usa gli URL per richiamare il broker e quindi restituire il controllo all'applicazione. Per completare tale round trip, è necessario uno schema URL registrato per l'applicazione, di cui la piattaforma Microsoft Identity verrà a conoscenza e che può andare ad aggiungersi a eventuali altri schemi app già registrati con l'applicazione.

> [AZURE.WARNING] 
È consigliabile rendere il più possibile univoco lo schema URL per ridurre al minimo le possibilità che un'altra app usi lo stesso schema URL. Apple non impone l'univocità degli schemi URL registrati nell'App Store.

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

#### Passaggio 3: Stabilire un nuovo URI di reindirizzamento con lo schema URL

Per assicurarsi che i token delle credenziali vengano sempre restituiti all'applicazione corretta, è necessario verificare che l'applicazione venga richiamata in un modo controllabile dal sistema operativo iOS. Il sistema operativo iOS segnala alle applicazioni broker Microsoft l'ID bundle dell'applicazione chiamante, di cui un'applicazione non autorizzata non può effettuare lo spoofing. Insieme all'URI dell'applicazione broker, questo garantisce quindi che i token vengano restituiti all'applicazione corretta. È necessario definire questo URI di reindirizzamento univoco nell'applicazione e impostarlo come URI di reindirizzamento nel portale per gli sviluppatori.

L'URI di reindirizzamento deve essere nel formato corretto:

`<app-scheme>://<your.bundle.id>`

Ad esempio: *x-msauth-mytestiosapp://com.myapp.mytestapp*

L'URI di reindirizzamento deve essere specificato nella registrazione dell'app usando il [portale di Azure classico](https://manage.windowsazure.com/). Per altre informazioni sulla registrazione dell'app Azure AD, vedere [Integrazione con Azure Active Directory](active-directory-how-to-integrate.md).


##### Passaggio 3a: Aggiungere un URI di reindirizzamento nell'app e nel portale per sviluppatori per supportare l'autenticazione basata su certificati

Per supportare l'autenticazione basata su certificati, è necessario registrare un secondo "msauth" nell'applicazione e nel [portale di Azure classico](https://manage.windowsazure.com/) per gestire l'autenticazione del certificato se si vuole aggiungere tale supporto nell'applicazione.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Ad esempio: **msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### Passaggio 4: iOS9: Aggiungere un parametro di configurazione all'app

ADAL usa –canOpenURL: per controllare se il broker è installato nel dispositivo. In iOS 9 Apple ha bloccato gli schemi di cui un'applicazione può effettuare una query. Sarà necessario aggiungere "msauth" alla sezione LSApplicationQueriesSchemes di `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string> </array>

### SSO è stato configurato

Ora Microsoft Identity SDK condividerà automaticamente le credenziali tra le applicazioni e richiamerà il broker, se presente nel dispositivo.

<!---HONumber=AcomDC_0323_2016-->