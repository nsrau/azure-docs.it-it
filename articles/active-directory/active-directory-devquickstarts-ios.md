<properties
	pageTitle="Introduzione a iOS per Azure AD | Microsoft Azure"
	description="Come compilare un'applicazione iOS che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Integrare Azure AD in un'app iOS

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si sta sviluppando un'applicazione desktop, Azure AD semplifica e facilita l'autenticazione degli utenti con gli account Active Directory. Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per i client iOS che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'applicazione Objective C To-Do List che:

-	Ottiene i token di accesso per la chiamata all'API Graph di Azure AD con il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Cerca in una directory gli utenti con un determinato alias.
-	Disconnette gli utenti.

Per compilare l'applicazione funzionante completa, sarà necessario:

2. Registrare l'applicazione con Azure AD.
3. Installare e configurare ADAL.
5. Usare ADAL per ottenere i token da Azure AD.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). Sarà necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## Passaggio 1: Scaricare ed eseguire il server di esempio dell'API REST TODO per .Net o Node.js

Questo esempio è scritto specificamente per funzionare con l'esempio esistente per la compilazione di una singola API REST ToDo del tenant per Microsoft Azure Active Directory. Questo è un prerequisito per l'Avvio rapido.

Per informazioni su come configurarlo, vedere l'esempio esistente qui:

* [Servizio API REST di esempio di Microsoft Azure Active Directory per Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Passaggio 2: Registrare l'API Web con il tenant di Microsoft Azure AD

**Che operazioni si stanno eseguendo?**

*Microsoft Active Directory supporta l'aggiunta di due tipi di applicazioni. API Web che offrono servizi a utenti e applicazioni (sul Web o in un'applicazione in esecuzione su un dispositivo) che accedono a tali API Web. In questo passaggio si registrerà l'API Web in esecuzione in locale per il test di questo esempio. In genere questa API Web è un servizio REST che offre le funzionalità a cui un'app deve accedere. Microsoft Azure Active Directory è in grado di proteggere qualsiasi endpoint.*

*Qui si presume che verrà registrata l'API REST TODO citata sopra, ma questa procedura è valida per qualsiasi API Web che si vuole proteggere con Azure Active Directory.*

Passaggi per registrare un'API Web con Microsoft Azure AD

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su Active Directory nel pannello di navigazione a sinistra.
3. Fare clic sul tenant della directory in cui si vuole registrare l'applicazione di esempio.
4. Fare clic sulla scheda Applicazioni.
5. Nel pannello fare clic su Aggiungi.
6. Fare clic su "Aggiungi un'applicazione che l'organizzazione sta sviluppando".
7. Immettere un nome descrittivo per l'applicazione, ad esempio "TodoListService", selezionare "Applicazione Web e/o API Web", quindi fare clic su Avanti.
8. Come URL di accesso immettere l'URL di base per l'esempio, che per impostazione predefinita è `https://localhost:8080`.
9. Come URI ID app immettere `https://<your_tenant_name>/TodoListService`, sostituendo `<your_tenant_name>` con il nome del tenant di Azure AD. Fare clic su OK per completare la registrazione.
10. Nel portale di Azure fare clic sulla scheda Configura dell'applicazione.
11. **Trovare il valore dell'ID client e prenderne nota**, perché sarà necessario in un secondo momento durante la configurazione dell'applicazione.

## Passaggio 3: Registrare l'applicazione client nativa iOS di esempio

La registrazione dell'applicazione Web è il primo passaggio. Successivamente, sarà necessario anche informare Azure Active Directory dell'applicazione. In questo modo l'applicazione può comunicare con l'API Web appena registrata.

**Che operazioni si stanno eseguendo?**

*Come indicato in precedenza, Microsoft Azure Active Directory supporta l'aggiunta di due tipi di applicazioni. API Web che offrono servizi a utenti e applicazioni (sul Web o in un'applicazione in esecuzione su un dispositivo) che accedono a tali API Web. In questo passaggio si registrerà l'applicazione di questo esempio. La registrazione è necessaria per permettere all'applicazione di richiedere l'accesso all'API Web appena registrata. Se l'applicazione non è registrata, Azure Active Directory non le consentirà nemmeno di chiedere di effettuare l'accesso. Questo fa parte della sicurezza del modello.*

*Qui si presume che verrà registrata l'applicazione di esempio citata sopra, ma questa procedura è valida per qualsiasi app si voglia sviluppare.*

**Perché si inseriscono sia un'applicazione che un'API Web in un solo tenant?**

*Come si può immaginare, è possibile compilare un'app che accede a un'API esterna registrata in Azure Active Directory da un altro tenant. In tal caso, ai clienti verrà richiesto di acconsentire all'uso dell'API nell'applicazione. L'aspetto interessante è che Active Directory Authentication Library per iOS gestisce automaticamente la richiesta di consenso. Iniziando a usare funzionalità più avanzate, si può immaginare come questa sia una parte importante del lavoro necessario per accedere alla gamma di API Microsoft da Azure e da Office, oltre che da altri provider di servizi. Per ora, poiché sia l'API Web che l'applicazione sono state registrate nello stesso tenant, non verrà visualizzata alcuna richiesta di consenso. Di solito ciò avviene se si sta sviluppando un'applicazione che verrà usata solo in azienda.*

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su Active Directory nel pannello di navigazione a sinistra.
3. Fare clic sul tenant della directory in cui si vuole registrare l'applicazione di esempio.
4. Fare clic sulla scheda Applicazioni.
5. Nel pannello fare clic su Aggiungi.
6. Fare clic su "Aggiungi un'applicazione che l'organizzazione sta sviluppando".
7. Immettere un nome descrittivo per l'applicazione, ad esempio "TodoListClient-iOS", selezionare "Applicazione client nativa", quindi fare clic su Avanti.
8. Per l'URI di reindirizzamento, immettere `http://TodoListClient`. Fare clic su Fine.
9. Fare clic sulla scheda Configura dell'applicazione.
10. Trovare il valore dell'ID client e prenderne nota, perché sarà necessario in un secondo momento durante la configurazione dell'applicazione.
11. In "Autorizzazioni per altre applicazioni" fare clic su "Aggiungi applicazione". Selezionare "Altro" nell'elenco a discesa "Mostra", quindi fare clic sul segno di spunta superiore. Individuare e fare clic su TodoListService, quindi fare clic sul segno di spunta inferiore per aggiungere l'applicazione. Selezionare "Accesso a TodoListService" dall'elenco a discesa "Autorizzazioni delegate", quindi salvare la configurazione.


## Passaggio 4: Scaricare il codice di esempio client nativo iOS

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## Passaggio 5: Scaricare ADAL per iOS e aggiungerlo all'area di lavoro XCode

#### Scaricare ADAL per iOS

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### Importare la libreria nell'area di lavoro

In XCode fare clic con il pulsante destro del mouse sulla directory del progetto e scegliere "Add files to iOS Sample".

Quando richiesto, selezionare la directory in cui si è clonato ADAL per iOS

#### Aggiungere libADALiOS.a alle librerie e ai framework collegati

Fare clic sul pulsante add in "Linked Frameworks and Libraries" e aggiungere il file della libreria dai framework importati.

Compilare il progetto per assicurarsi che tutto venga compilato correttamente.


## Passaggio 6: Configurare il file settings.plist con le informazioni dell'API Web

In "Supporting Files" è presente un file settings.plist. Include le informazioni seguenti:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

Sostituire le informazioni nel file plist con le impostazioni dell'API Web.

##### NOTA

Le impostazioni predefinite correnti sono configurate per usare il [Servizio API REST di esempio di Azure Active Directory per Node.js](https://github.com/AzureADSamples/WebAPI-Nodejs). Sarà necessario, tuttavia, specificare l'ID client dell'API Web. Se si esegue la propria API, sarà necessario aggiornare gli endpoint come richiesto.

## Passaggio 7: Compilare ed eseguire l'applicazione

Dovrebbe essere possibile connettersi all'endpoint dell'API REST. Verranno richieste le credenziali dell'account Azure Active Directory.

Per altre risorse, vedere: [AzureADSamples su GitHub >](https://github.com/AzureAdSamples), la documentazione su Azure AD in [Azure.com >](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58--> 