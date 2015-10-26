<properties
	pageTitle="Limitazioni e restrizioni di Modello app v2.0 | Microsoft Azure"
	description="Elenco di limitazioni e restrizioni in Modello app 2.0 di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/11/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Limitazioni e restrizioni

Nel periodo di anteprima pubblica di Modello app 2.0 molte funzionalità e caratteristiche non sono ancora supportate. Ognuna di queste limitazioni verrà rimossa prima che Modello app 2.0 sia disponibile a livello generale. È tuttavia necessario tenerle presenti, se si creano app durante la fase di anteprima pubblica.

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Supporto per le app di produzione
Le app che si integrano con Modello app 2.0 non devono essere rilasciate al pubblico come app di produzione. Modello app 2.0 è attualmente in fase di anteprima pubblica, eventuali modifiche possono essere introdotte in qualsiasi momento e non esiste alcun contratto di servizio garantito. Non verrà fornito alcun supporto per tutti gli eventi imprevisti che si possono verificare. Se si è disposti ad accettare il rischio di usare una dipendenza su un servizio che è ancora in fase di sviluppo, è necessario contattare @AzureAD per discutere l'ambito dell'app o del servizio.

## Restrizioni sulle app
I tipi di app seguenti non sono attualmente supportati nell'anteprima pubblica di Modello app 2.0. Per una descrizione dei tipi di app supportati, fare riferimento a [questo articolo](active-directory-v2-flows.md).

##### App a pagina singola (JavaScript)
Molte app moderne dispongono di un front-end dell'app a pagina singola scritto principalmente in JavaScript e spesso tramite framework di app a pagina singola, ad esempio AngularJS, Ember.js, Durandal e così via. Il servizio Azure AD disponibile a livello generale supporta queste app mediante il [flusso implicito di OAuth 2.0](active-directory-v2-protocols.md#oauth2-implicit-flow). Questo flusso non è tuttavia ancora disponibile in Modello app 2.0. Sarà disponibile a breve.

Se si desidera che un'app a pagina singola usi Modello app 2.0, è possibile implementare l'autenticazione usando il [flusso dell'app Web](active-directory-v2-flows.md#web-apps). Questo, tuttavia, non è l'approccio consigliato e la documentazione per questo scenario è limitata. Se si desidera avere un'idea dello scenario dell'app a pagina singola, consultare l'[esempio di codice dell'app a pagina singola di Azure AD disponibile a livello generale](active-directory-devquickstarts-angular.md).

##### App daemon e sul lato server
Anche le app che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, mediante il [flusso delle credenziali client di OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Questo flusso non è attualmente supportato da Modello app 2.0, ossia le app possono ottenere i token solo dopo che si è verificato un flusso di accesso utente interattivo. Il flusso delle credenziali client verrà aggiunto in futuro. Se si desidera vedere il flusso delle credenziali client nel modello app di Azure AD disponibile a livello generale, consultare l'[esempio Daemon su GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### API Web concatenate (On-Behalf-Of)
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Modello app 2.0. Questo scenario è comune nei client nativi che dispongono di un back-end dell'API Web, che a sua volta chiama un servizio Microsoft Online come Office 365 o l'API Graph.

Questo scenario dell'API Web concatenata può essere supportato tramite la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come [flusso On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Tuttavia, il flusso On-Behalf-Of non è attualmente implementato nell'anteprima di Modello app 2.0. Per verificare il funzionamento di questo flusso nel servizio Azure AD disponibile a livello generale, consultare l'[esempio di codice On-Behalf-Of su GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

##### API Web autonome
Nell'anteprima di Modello app 2.0 è possibile [creare un'API Web protetta mediante token OAuth](active-directory-v2-flows.md#web-apis) dall'endpoint 2.0. Tuttavia, tale API Web sarà in grado di ricevere token solo da un client che condivide lo stesso ID applicazione. La creazione di un servizio Web di terze parti a cui è possibile accedere da diversi client non è supportata.

Per informazioni su come creare un'API Web che accetti token da un client noto con lo stesso ID app, vedere gli esempi di API Web di Modello app 2.0 nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

## Restrizioni sugli utenti
Attualmente, qualsiasi app creata con Modello app 2.0 verrà esposta pubblicamente a tutti gli utenti che dispongono di un account Microsoft o Azure AD. Qualsiasi utente con uno dei due tipi di account sarà in grado di individuare o installare l'app, immettere le credenziali in Modello app 2.0 e fornire il consenso alle autorizzazioni dell'app. È possibile scrivere il codice dell'app in modo da rifiutare l'accesso di alcuni gruppi di utenti, ma questa operazione non impedirà loro di fornire il consenso all'app.

In effetti, le app non sono in grado di limitare i tipi di utenti che possono accedere all'app. Non è possibile creare app line-of-business (limitate agli utenti di un'organizzazione), app disponibili solo per utenti aziendali (con un account Azure AD) o app disponibili solo per utenti consumer (con un account Microsoft).

## Restrizioni sulle registrazioni di app
Attualmente, per tutte le app che si desidera integrare con Modello app 2.0 è necessario creare una nuova registrazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Qualsiasi app esistente con account Azure AD o Microsoft non sarà compatibile con Modello app 2.0, né verrà registrata in un portale diverso dal nuovo portale di registrazione delle app. Non è disponibile alcun percorso di migrazione per un'app dal servizio Azure AD disponibile a livello generale a Modello app 2.0.

In modo analogo, le app registrate nel nuovo portale di registrazione potranno essere usate solo con Modello app 2.0. Non è possibile usare il portale di registrazione delle app per creare app che si integrino con i servizi di Azure AD o dell'account Microsoft.

Le app registrate nel nuovo portale di registrazione delle app sono attualmente limitate a un set ristretto di valori di URI di reindirizzamento. L'URI di reindirizzamento per app e servizi Web deve iniziare con lo schema o `https`, mentre l'URI di reindirizzamento per tutte le altre piattaforme deve usare il valore hardcoded `urn:ietf:oauth:2.0:oob`.

Per informazioni su come registrare un'app nel nuovo portale di registrazione delle app, fare riferimento a [questo articolo](active-directory-v2-app-registration.md).

## Restrizioni sui servizi e sulle API
Modello app 2.0 attualmente supporta l'accesso di qualsiasi app registrata nel nuovo portale di registrazione delle app, purché si trovi nell'elenco dei [flussi di autenticazione supportati](active-directory-v2-flows.md). Tuttavia, queste app saranno in grado di acquisire solo token di accesso di OAuth 2.0 per un set molto limitato di risorse. L'endpoint 2.0 rilascerà token di accesso solo per:

- L'app che ha richiesto il token. Un'app può acquisire un token di accesso per se stessa, se l'app logica è costituita da diversi componenti o livelli. Per vedere questo scenario, consultare le esercitazioni nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).
- Le API REST di Posta, Calendario e Contatti di Outlook che si trovano in https://outlook.office.com. Per informazioni su come scrivere un'app che accede a queste API, fare riferimento alle esercitazioni nella sezione [introduttiva di Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

In futuro verranno aggiunti altri servizi Microsoft Online nonché il supporto per i servizi e le API Web.

## Restrizioni sulle librerie e sugli SDK
Non tutti i linguaggi e le piattaforme dispongono di librerie che supportano l'anteprima di Modello app 2.0. Il set di librerie di autenticazione è attualmente limitato a .NET, iOS, Android, NodeJS e JavaScript. Esempi di codice ed esercitazioni per ogni libreria sono disponibili nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

Se si desidera integrare un'app con Modello app 2.0 usando un linguaggio o una piattaforma differente, vedere il [riferimento ai protocolli OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) che contiene informazioni sulla costruzione dei messaggi HTTP necessari per comunicare con l'endpoint 2.0.

## Restrizioni sui protocolli
Modello app 2.0 supporta i protocolli Open ID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e caratteristiche di ciascun protocollo sono state incorporate in Modello app 2.0. Di seguito sono riportati alcuni esempi:

- Supporto completo per il parametro `prompt` di OpenID Connect
- Parametro `login_hint` di OpenID Connect
- Parametro `domain_hint` di OpenID Connect
- Endpoint `end_sesssion_endpoint` di OpenID Connect

Per comprendere meglio l'ambito della funzionalità del protocollo supportata in Modello app 2.0, leggere il [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

<!---HONumber=Oct15_HO3-->