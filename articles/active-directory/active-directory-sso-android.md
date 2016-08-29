<properties
	pageTitle="Come abilitare l'accesso Single Sign-On tra app su Android tramite ADAL | Microsoft Azure"
	description="Come utilizzare le funzionalità dell'SDK ADAL per abilitare il Single Sign-On tra le applicazioni. "
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="brandwe"/>


# Come abilitare l'accesso Single Sign-On tra app su Android tramite ADAL


Ora i clienti si aspettano che l'accesso SSO (Single Sign-On) venga fornito in modo che gli utenti debbano inserire le loro credenziali una volta sola e che le credenziali valgano automaticamente per le varie applicazioni. La difficoltà di immissione di nome utente e password su uno schermo di piccole dimensioni, spesso abbinata a un fattore aggiuntivo (2FA) come una telefonata o un codice inviato tramite SMS, rende l'utente insoddisfatto se questa procedura va ripetuta più volte per il prodotto.

Inoltre, se ci si serve di una piattaforma delle identità che può essere usata da altre applicazioni, ad esempio Microsoft Accounts o un account aziendale di Office365, i clienti si aspettano che le credenziali siano disponibili all'uso in tutte le applicazioni, indipendentemente dal fornitore.

La piattaforma Microsoft Identity, insieme agli altri SDK di Microsoft Identity, soddisfa queste aspettative e consente di offrire l'SSO ai clienti nella propria suite di applicazioni oppure, analogamente alla funzionalità broker e alle applicazioni di Authenticator, in tutto il dispositivo.

Questa procedura illustrerà come configurare l'SDK all'interno dell'applicazione per offrire questo vantaggio ai clienti.

Questa procedura si applica a:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Accesso condizionale di Azure Active Directory

Si noti che il documento seguente presuppone che si sappia come [effettuare il provisioning delle applicazioni nel portale legacy per Azure Active Directory](active-directory-how-to-integrate.md) nonché come integrare l'applicazione con [Microsoft Identity Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## Concetti di SSO nella piattaforma Microsoft Identity

### Broker di Microsoft Identity

Microsoft fornisce applicazioni per ogni piattaforma mobile che consentono il bridging delle credenziali tra le applicazioni di fornitori diversi nonché offrono particolari funzionalità avanzate che richiedono un'unica posizione sicura da cui convalidare le credenziali. Queste applicazioni sono dette **broker**. Nei sistemi iOS e Android i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati al dispositivo da una società che gestisce alcuni o tutti i dispositivi dei suoi dipendenti. I broker supportano la gestione della sicurezza per alcune applicazioni soltanto o per tutto il dispositivo, a seconda di quanto stabilito dagli amministratori IT. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker.

Per comprendere come vengono usati questi broker e come vengono visualizzati dai clienti nel flusso di accesso per la piattaforma Microsoft Identity, continuare a leggere per altre informazioni.

### Modelli di accesso dai dispositivi mobili

Nella piattaforma Microsoft Identity l'accesso alle credenziali nei dispositivi avviene in base a due modelli:

* Accessi non assistiti da broker
* Accessi assistiti da broker

#### Accessi non assistiti da broker

Gli accessi non assistiti da broker sono esperienze di accesso in linea con l'applicazione e utilizzano le risorse di archiviazione locali nel dispositivo per l'applicazione. Le risorse di archiviazione possono essere condivise tra le applicazioni, ma le credenziali sono strettamente associate all'applicazione oppure a suite di applicazioni che usano le credenziali. Questa è l'esperienza più frequente in molte applicazioni per dispositivi mobili in cui si immettono un nome utente e una password all'interno dell'applicazione stessa.

Questi tipi di accessi offrono i seguenti vantaggi:

-  L'esperienza utente si svolge interamente all'interno dell'applicazione.
-  Le credenziali possono essere condivise tra applicazioni firmate dallo stesso certificato, offrendo un'esperienza di Single Sign-On alla suite di applicazioni.
-  Il controllo dell'esperienza di accesso viene fornito all'applicazione prima e dopo l'accesso.

Questi tipi di accessi presentano i seguenti svantaggi:

- L'utente non può eseguire il Single Sign-On a tutte le app che usano un'identità Microsoft ma solo alle identità Microsoft proprie dell'applicazione che sono state configurate.
- L'applicazione non può essere usata con funzionalità aziendali più avanzate, ad esempio l'Accesso condizionale o la suite di prodotti InTune.
- L'applicazione non supporta l'autenticazione basata su certificati per gli utenti aziendali.

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

#### Accessi assistiti da broker

Gli accessi assistiti da broker sono esperienze di accesso che si svolgono all'interno dell'applicazione broker e usano le risorse di archiviazione e la sicurezza del broker per condividere le credenziali tra tutte le applicazioni sul dispositivo che usano la piattaforma Microsoft Identity. Ciò significa che le applicazioni si affidano al broker per l'accesso degli utenti. Nei sistemi iOS e Android i broker sono forniti tramite applicazioni scaricabili che i clienti possono installare in modo indipendente oppure possono essere inviati al dispositivo da una società che gestisce il dispositivo dell'utente. Un esempio di questo tipo di applicazione è l'applicazione Azure Authenticator basata su iOS. In Windows questa funzionalità è fornita da un sistema di selezione dell'account incorporato nel sistema operativo, il cui nome tecnico è Web Authentication Broker. L'esperienza varia in base alla piattaforma e talvolta può essere un elemento di disturbo per gli utenti se non viene gestita correttamente. Chi ha installato l'applicazione Facebook e usa la funzionalità di accesso di Facebook in un'altra applicazione avrà probabilmente maggiore dimestichezza con questo modello. La piattaforma Microsoft Identity si basa sullo stesso modello.

Nel sistema operativo iOS questo genera un'animazione di "transizione" in cui l'applicazione viene inviata sullo sfondo mentre le applicazioni di Azure Authenticator vengono portate in primo piano in modo che l'utente possa scegliere con quale account accedere.

Nei sistemi Android e Windows la selezione degli account viene visualizzata in alto nell'applicazione, con un impatto meno fastidioso per l'utente.

#### Come viene richiamato il broker

Se nel dispositivo è installato un broker compatibile, ad esempio l'applicazione Azure Authenticator, gli SDK di Microsoft Identity richiamano automaticamente il broker quando un utente indica che desidera accedere con un account qualunque dalla piattaforma Microsoft Identity. Può utilizzare un account Microsoft personale, un account scolastico o aziendale o un account fornito e ospitato in Azure tramite i prodotti B2C e B2B. Grazie all'uso della crittografia e di algoritmi estremamente sicuri, le credenziali vengono richieste e recapitate all'applicazione in modo sicuro. I particolari tecnici esatti di questi meccanismi non sono stati pubblicati ma sono stati sviluppati in collaborazione con Apple e Google.

**Lo sviluppatore può scegliere se Microsoft Identity SDK deve chiamare il broker o usare il flusso non assistito dal broker.** Se lo sviluppatore decide di non usare il flusso assistito dal broker, rinuncia al vantaggio dell'utilizzo delle credenziali SSO che l'utente potrebbe avere già aggiunto nel dispositivo, nonché impedisce di usare l'applicazione con le funzionalità aziendali fornite da Microsoft, ad esempio l'Accesso condizionale, le funzionalità di gestione di Intune e l'autenticazione basata su certificati.

Questi tipi di accessi offrono i seguenti vantaggi:

-  L'utente usufruisce del Single Sign-On per tutte le proprie applicazioni, indipendentemente dal fornitore.
-  L'applicazione può usare funzionalità aziendali più avanzate, ad esempio l'Accesso condizionale o la suite di prodotti InTune.
-  L'applicazione può supportare l'autenticazione basata su certificati per gli utenti aziendali.
- Un'esperienza di accesso molto più sicura dato che l'identità dell'applicazione e l'utente vengono verificati dall'applicazione broker con algoritmi di sicurezza aggiuntivi e la crittografia.

Questi tipi di accessi presentano i seguenti svantaggi:

- Nel sistema iOS l'utente viene trasferito dall'esperienza dell'applicazione durante la selezione delle credenziali.
- Perdita della possibilità di gestire l'esperienza di accesso per i clienti nell'applicazione.



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


## Abilitazione dell'SSO tra app tramite ADAL

In questo esempio si userà l'SDK ADAL Android per:

- Attivare l'SSO non assistito dal broker per la propria suite di applicazioni
- Attivare il supporto per SSO assistito da broker


### Attivazione dell'SSO per l'SSO non assistito da broker

Per l'SSO non assistito da broker tra applicazioni, gli SDK di Microsoft Identity risolvono automaticamente gran parte delle complessità dell'SSO, tra cui la ricerca dell'utente corretto nella cache e la gestione di un elenco di utenti connessi di cui è possibile effettuare una query.

Per abilitare l'SSO tra le applicazioni di cui si è proprietari, eseguire le operazioni seguenti:

1. Verificare che tutte le applicazioni usino lo stesso ID client o ID applicazione.
* Verificare che tutte le applicazioni abbiano lo stesso set di SharedUserID.
* Verificare che tutte le applicazioni condividano lo stesso certificato di firma da Google Play Store per condividere l'archiviazione.

#### Passaggio 1: Utilizzo dello stesso ID client / ID applicazione per tutte le applicazioni della suite di applicazioni

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


#### Passaggio 2: Configurazione dell'archiviazione condivisa in Android

L'impostazione di `SharedUserID` esula dal contesto di questo documento, ma per maggiori informazioni si può leggere la documentazione di Google Android su [Manifest](http://developer.android.com/guide/topics/manifest/manifest-element.html). È importante decidere il nome di sharedUserID e usarlo in tutte le applicazioni.

Dopo aver creato l'attributo `SharedUserID` in tutte le applicazioni, si può usare SSO.

> [AZURE.WARNING] 
Quando si condivide una risorsa di archiviazione tra le applicazioni, qualsiasi applicazione può eliminare utenti o peggio ancora eliminare tutti i token dell'applicazione. Questo può essere un problema grave se le applicazioni usano i token per svolgere operazioni in background. La condivisione della risorsa di archiviazione implica che è necessario prestare grande attenzione durante tutte le operazioni di eliminazione tramite gli SDK di Microsoft Identity.

L'operazione è terminata. L'SDK di Microsoft Identity condividerà le credenziali tra tutte le applicazioni. Anche l'elenco degli utenti verrà condiviso tra le istanze dell'applicazione.

### Attivazione di SSO per SSO assistito da broker

La possibilità per un'applicazione di usare qualsiasi broker installato nel dispositivo è **disattivata per impostazione predefinita**. Per usare l'applicazione con il broker è necessario apportare alcune modifiche alla configurazione e aggiungere una parte di codice all'applicazione.

Ecco i passaggi da seguire:

1. Abilitare la modalità broker nella chiamata del codice dell'applicazione dell'SDK MS
2. Definire un nuovo URI di reindirizzamento e indicarlo sia all'app che alla registrazione dell'app
3. Impostazione delle autorizzazioni corrette nel manifesto Android


#### Passaggio 1: Abilitare la modalità broker nell'applicazione
La capacità dell'applicazione di usare il broker è attivata quando si creano le "impostazioni" o la configurazione iniziale dell'istanza di Authentication. A tal fine, impostare il tipo ApplicationSettings nel codice:

```
AuthenticationSettings.Instance.setUseBroker(true);
```


#### Passaggio 2: Creare un nuovo URI di reindirizzamento con lo schema dell'URL

Per fare in modo che i token delle credenziali vengano sempre restituiti all'applicazione corretta, è necessario assicurarsi che il richiamo dell'applicazione avvenga in un modo verificabile dal sistema operativo Android. Il sistema operativo Android utilizza in Google Play Store l'hash del certificato, che non può essere contraffatto da un'applicazione non autorizzata. Di conseguenza, l'hash del certificato viene usato insieme all'URI dell'applicazione broker per garantire che i token vengano restituiti all'applicazione corretta. È necessario definire questo URI di reindirizzamento univoco nell'applicazione e impostarlo come URI di reindirizzamento nel portale per gli sviluppatori.

L'URI di reindirizzamento deve essere nel formato corretto:

`msauth://packagename/Base64UrlencodedSignature`

Ad esempio: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

L'URI di reindirizzamento deve essere specificato nella registrazione dell'app tramite il [portale di Azure classico](https://manage.windowsazure.com/). Per altre informazioni sulla registrazione dell'app Azure AD, vedere [Integrazione con Azure Active Directory](active-directory-how-to-integrate.md).


#### Passaggio 3: Impostare le autorizzazioni corrette all'interno dell'applicazione

L'applicazione broker in Android usa la funzionalità AccountManager del sistema operativo Android per gestire le credenziali nelle applicazioni. Per utilizzare il broker in Android, il manifesto dell'app deve disporre delle autorizzazioni per usare gli account di AccountManager. Questo argomento viene discusso in modo dettagliato nella [documentazione di Google relativa ad AccountManager qui](http://developer.android.com/reference/android/accounts/AccountManager.html)

In particolare, le autorizzazioni sono:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### L'SSO è stato configurato!

Ora l'SDK di Microsoft Identity condividerà automaticamente le credenziali tra le applicazioni e richiamerà il broker, se presente nel dispositivo.

<!---HONumber=AcomDC_0817_2016-->