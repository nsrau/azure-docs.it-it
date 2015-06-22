<properties
   pageTitle="Come vengono aggiunte le applicazioni in Azure Active Directory."
   description="Questo articolo descrive il modo in cui le applicazioni vengono aggiunge a un'istanza di Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="03/23/2015"
      ms.author="shoatman"/>

# Come vengono aggiunte le applicazioni in Azure AD e perché

Uno degli aspetti più sconcertanti quando si visualizza un elenco di applicazioni nell'istanza di Azure Active Directory sta nel comprendere da dove provengono le applicazioni e perché si trovano nella directory. Questo articolo fornisce una panoramica generale del modo in cui le applicazioni sono rappresentate nella directory, oltre alle informazioni necessarie per comprendere perché un'applicazione è stata inclusa nella directory.

## Quali sono i servizi che Azure AD offre alle applicazioni?

Le applicazioni vengono aggiunte in Azure AD in modo che sia possibile sfruttare uno o più servizi offerti. I servizi comprendono:

* Autenticazione e autorizzazione delle app
* Autenticazione e autorizzazione degli utenti
* Accesso Single Sign-On tramite federazione o password
* Provisioning e sincronizzazione degli utenti
* Controllo degli accessi in base al ruolo. È possibile usare la directory per definire i ruoli dell'applicazione per eseguire i controlli di autorizzazione in base ai ruoli in un'app.
* Servizi di autorizzazione oAuth (usati da Office 365 e altre app Microsoft per autorizzare l'accesso ad API e risorse).
* Proxy e pubblicazione dell'applicazione. È possibile pubblicare un'app in Internet da una rete privata

## In che modo sono rappresentate le applicazioni nella directory?

Le applicazioni sono rappresentate in Azure AD tramite 2 oggetti: un oggetto applicazione e un oggetto entità servizio. Esiste un oggetto applicazione, registrato in una directory "home"/"proprietario" o "pubblicazione" e uno o più oggetti entità servizio che rappresentano l'applicazione in ogni directory in cui opera.

L'oggetto applicazione descrive l'app ad Azure AD (servizio multitenant) e può includere gli elementi seguenti: (*Nota: questo elenco potrebbe non essere completo)

* Nome, logo e autore
* Segreti (chiavi simmetriche e/o asimmetriche usate per autenticare l'app)
* Dipendenze API (oAuth)
* API/risorse/ambiti pubblicati (oAuth)
* Ruoli app (Controllo degli accessi in base al ruolo)
* Metadati e configurazione Single Sign-On (SSO)
* Metadati e configurazione del provisioning utenti
* Metadati e configurazione proxy

L'entità servizio è un record dell'applicazione in ogni directory in cui questa opera, inclusa la relativa home directory. L'entità servizio:

* Fa riferimento a un oggetto applicazione tramite la proprietà ID app
* Registra le assegnazioni del ruolo app al gruppo e all'utente locale
* Registra le autorizzazioni di amministratore e utente locale concesse all'app
    * Ad esempio, l'autorizzazione dell'app ad accedere all'indirizzo di posta elettronica di un utente specifico
* Registra i criteri locali, inclusi i criteri di accesso condizionale
* Registra le impostazioni locali alternative per un'app
    * Regole di trasformazione delle attestazioni
    * Mapping degli attributi (provisioning utenti)
    * Ruoli app specifici del tenant (se l'app supporta i ruoli personalizzati)
    * Nome/Logo

### Diagramma degli oggetti applicazione e delle entità servizio tra le directory

![Diagramma che illustra gli oggetti applicazione e le entità servizio presenti all'interno delle istanze di Azure AD.][apps_service_principals_directory]

Come si può notare nel diagramma, Microsoft mantiene internamente due directory (a sinistra) usate per pubblicare le applicazioni.
 
* Una per le app Microsoft (directory di servizi Microsoft)
* Una per le app di terze parti preintegrate (directory Raccolta app)

Ai fornitori/editori di applicazioni che procedono all'integrazione con Azure AD è richiesto di avere una directory di pubblicazione (ad alcuni una directory SaaS).

Applicazioni aggiunte dall'utente:

* App sviluppate dall'utente (integrate con AAD)
* App connesse per l'accesso Single Sign-On
* App pubblicate tramite il proxy di applicazione di Azure AD.

### Note ed eccezioni

* Non tutte le entità servizio fanno riferimento a oggetti applicazione. Questo perché al momento della creazione di Azure AD, i servizi forniti alle applicazioni erano molto più limitati e l'entità servizio era sufficiente per stabilire l'identità di un'app. L'entità servizio originale assomigliava più all'account del servizio di Windows Server Active Directory. Per questo motivo, è ancora possibile creare entità servizio usando Azure AD PowerShell senza dover prima creare un oggetto applicazione. L'API Graph richiede che venga creato un oggetto app prima di creare un'entità servizio.
* Non tutte le informazioni riportate sopra sono al momento esposte a livello di codice. Le funzionalità seguenti sono disponibili solo nell'interfaccia utente:
    * Regole di trasformazione delle attestazioni
    * Mapping degli attributi (provisioning utenti)
* Per informazioni dettagliate sugli oggetti applicazione ed entità servizio, vedere la documentazione di riferimento all'API REST di Azure AD Graph. *Suggerimento*: la documentazione di riferimento all'API Graph di Azure AD è quanto di più somigliante al riferimento a uno schema per Azure AD disponibile al momento.  
    * [Applicazione](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Entità servizio](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## In che modo le app vengono aggiunte alla mia istanza di Azure AD?
È possibile aggiungere un'app in Azure AD in diversi modi:

* Aggiungere un'app dalla [Raccolta app di Azure Active Directory](http://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Iscriversi o accedere a un'app di terze parti integrata in Azure Active Directory, ad esempio [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx)
    * Durante la procedura di iscrizione/accesso, agli utenti viene richiesto di concedere all'app l'autorizzazione ad accedere al proprio profilo e altri tipi di autorizzazioni. Quando viene dato il primo consenso, l'entità servizio che rappresenta l'app viene aggiunta alla directory.
* Iscriversi o accedere ai Microsoft Online Services, ad esempio [Office 365](http://products.office.com/)
    * Quando si effettua l'abbonamento a Office 365 o si richiede una versione di valutazione, vengono create una o più entità servizio nella directory che rappresenta i vari servizi usati per fornire tutte le funzionalità associate a Office 365.
    * Alcuni servizi di Office 365, ad esempio SharePoint, creano entità servizio in modo continuativo per consentire la comunicazione sicura tra i componenti, inclusi i flussi di lavoro.
* Aggiungere un'app in fase di sviluppo nel portale di gestione di Azure. Vedere https://msdn.microsoft.com/library/azure/dn132599.aspx
* Aggiungere un'app in fase di sviluppo tramite Visual Studio. Vedere:
    * [Metodi di autenticazione ASP.NET](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Servizi connessi](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Aggiungere un'app per l'uso del [proxy di applicazione di Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Connettere un'app per l'accesso Single Sign-On tramite SAML o SSO basato su password
* Sono disponibili altri modi, incluse varie esperienze utente in Azure ed esperienze di esplorazione delle API nei centri per sviluppatori

## Chi è autorizzato ad aggiungere applicazioni alla mia istanza di Azure AD?

Solo gli amministratori globali possono:

* Aggiungere app dalla raccolta di app Azure AD (app di terze parti preintegrate)
* Pubblicare un'app tramite il proxy di applicazione di Azure AD

Tutti gli utenti inclusi nella directory sono autorizzati ad aggiungere le applicazioni in fase di sviluppo con discrezionalità sulle applicazioni che condividono o che possono accedere ai dati aziendali. *Ricordare che l'iscrizione o l'accesso a un'app e la concessione delle autorizzazioni possono determinare la creazione di un'entità servizio.*

Anche se questo può inizialmente destare qualche preoccupazione, tenere presente quanto segue:

* Le app hanno usato Windows Server Active Directory per autenticare gli utenti per anni senza richiedere la registrazione dell'applicazione nella directory. Ora le organizzazioni hanno maggiore visibilità sul numero esatto di applicazioni che usano la directory e per quale scopo.
* Non serve un processo di registrazione/pubblicazione delle app basato su amministratore. Con Active Directory Federation Services gli amministratori dovevano aggiungere le app come relying party per conto degli sviluppatori. Ora gli sviluppatori possono eseguire queste operazioni in modo indipendente.
* L'iscrizione o l'accesso alle app tramite un account aziendale è un fatto positivo a livello dell'azienda. Se l'utente lascia la società, perderà l'accesso al proprio account nelle applicazioni in uso.
* È positivo anche il fatto di poter tenere sotto controllo quali dati sono stati condivisi e con quali applicazioni. I dati sono più trasportabili che mai ed è quindi utile sapere chi ha condiviso determinati dati e con quali applicazioni.
* Le app che usano Azure AD per l'autenticazione oAuth stabiliscono quali sono le autorizzazioni che gli utenti possono concedere alle applicazioni e quali autorizzazioni richiedono il consenso da parte dell'amministratore. Non è necessario ricordare che solo gli amministratori possono dare il consenso per ambiti più ampi e autorizzazioni più significative.
* Gli utenti che aggiungono app e consentono loro di accedere ai propri dati sono eventi controllati, pertanto è possibile visualizzare i report di controllo nel portale di gestione di Azure per determinare il modo in cui un'app è stata aggiunta alla directory.

**Nota:** *la stessa Microsoft ha usato la configurazione predefinita nel corso degli ultimi mesi.*

È comunque possibile impedire agli utenti della directory aggiungere applicazioni e prendere decisioni in merito alle informazioni da condividere con le applicazioni modificando la configurazione della directory nel portale di gestione di Azure. Per accedere alla configurazione seguente, passare al portale di gestione di Azure, quindi selezionare la scheda "Configura" della propria directory.

![Schermata dell'interfaccia utente per la configurazione delle impostazioni delle app integrate][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

Altre informazioni su come aggiungere applicazioni in Azure AD e su come configurare i servizi per le app.

* Sviluppatori: [Informazioni su come integrare un'applicazione con AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Sviluppatori: [Esaminare il codice di esempio per le app integrate con Azure Active Directory in GitHub](https://github.com/AzureADSamples)
* Sviluppatori e professionisti IT: [Esaminare la documentazione di riferimento all'API REST per l'API Graph di Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Professionisti IT: [Informazioni su come usare le applicazioni preintegrate in Azure Active Directory dalla Raccolta app](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Professionisti IT: [Esercitazioni per la configurazione di app preintegrate specifiche](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Professionisti IT: [Informazioni su come pubblicare un'app tramite il proxy di applicazione di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

<!--Image references-->
[apps_service_principals_directory]: media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]: media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg

<!---HONumber=58--> 