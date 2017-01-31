# <a name="azure-security-guidance"></a>Linee guida sulla sicurezza in Azure
## <a name="abstract"></a>Sunto
Quando si sviluppano applicazioni per Azure, i principali aspetti da valutare per quanto riguarda la sicurezza sono l'identità e l'accesso.
In questo argomento vengono illustrati questi aspetti per le applicazioni cloud e vengono fornite indicazioni su come proteggerle in modo ottimale.

## <a name="overview"></a>Panoramica
La sicurezza di un'applicazione dipende dalla relativa superficie. Quanto più grande è la superficie esposta dall'applicazione, tanto maggiori saranno i rischi per la sicurezza. Ad esempio, un'applicazione eseguita come processo batch automatico è meno esposta dal punto di vista della sicurezza rispetto a un sito Web disponibile a livello pubblico.

Con il passaggio nel cloud, si acquisisce un certo grado di tranquillità nei confronti di infrastruttura e reti, che vengono gestite nei data center con procedure, strumenti e tecnologie di sicurezza all'avanguardia. D'altra parte, il cloud per sua stessa natura espone un'area superficiale maggiore dell'applicazione, che potrebbe in teoria diventare oggetto di attacchi. In generale, infatti, diversamente dai componenti in memoria, le tecnologie e i servizi cloud sono esposti come endpoint. I servizi di archiviazione, bus di servizio, database SQL (in precedenza SQL Azure) di Azure e molti altri sono accessibili tramite i rispettivi endpoint sulla rete.

Gli sviluppatori di applicazioni cloud hanno maggiori responsabilità per garantire standard elevati di sicurezza in fase di progettazione, sviluppo e manutenzione, in modo da impedire attacchi di utenti malintenzionati.
Nel diagramma seguente, riportato nel [PDF delle note sulla sicurezza di Azure](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) di J.D. Meier, si può notare che l'infrastruttura rientra nell'ambito del provider di cloud, in questo caso Azure, e quindi la maggior parte del lavoro legato alla sicurezza spetta agli sviluppatori di applicazioni:

![Protezione dell'applicazione][01]

L'aspetto positivo è che tutte le procedure, le tecniche e i principi di sviluppo ai fini della sicurezza sono identici anche nel caso di applicazioni cloud. Le principali aree da valutare sono le seguenti:

* Tutto l'input deve essere convalidato per tipo, lunghezza, intervallo e modelli di stringa per evitare attacchi di tipo injection e tutti i dati che l'app restituisce devono essere correttamente purificati.
* I dati sensibili devono essere protetti sia in locale che in transito per limitare la divulgazione di informazioni e le minacce di manomissione.
* Le procedure di controllo e registrazione devono essere implementate adeguatamente per limitare le minacce di non ripudio.
* Le funzionalità di autenticazione e autorizzazione devono essere implementate con meccanismi comprovati offerti dalla piattaforma, per evitare minacce di spoofing di identità ed elevazione dei privilegi.

Per l'elenco completo di minacce, attacchi, vulnerabilità e contromisure, fare riferimento al [Cheat Sheet: Web Application Security Frame](http://msdn.microsoft.com/library/ff649461.aspx) (Foglio informativo sull'infrastruttura di sicurezza delle applicazioni Web) e al [Security Guidance for Applications Index](http://msdn.microsoft.com/library/ff650760.aspx) (Indice di linee guida per la sicurezza delle applicazioni) pubblicati dal team patterns & practices.

Nel cloud i meccanismi di autenticazione e controllo di accesso sono molti diversi da quelli disponibili per le applicazioni locali. Per le applicazioni cloud vengono offerte molte più opzioni che possono generare confusione e, di conseguenza, implementazioni difettose. La confusione aumenta quando si tratta di definire che cos'è un'app per cloud. Ad esempio, l'applicazione potrebbe essere distribuita nel cloud mentre il meccanismo di autenticazione potrebbe essere fornito da Active Directory. Viceversa, l'applicazione potrebbe essere distribuita in locale ma con i meccanismi di autenticazione nel cloud, ad esempio resi disponibili da Azure AD Access Control, in precedenza Servizio di controllo di accesso o ACS.

## <a name="threats-vulnerabilities-and-attacks"></a>Minacce, vulnerabilità e attacchi
Le minacce sono potenziali risultati negativi da evitare, ad esempio la divulgazione di informazioni sensibili o la mancata disponibilità di un servizio.
Vengono comunemente classificate con il modello "STRIDE":

* **S**poofing, ovvero furto di identità
* **T**ampering, ovvero la manomissione dei dati
* **R**epudiation, ovvero il ripudio di azioni
* **I**nformation disclosure, ovvero la divulgazione di informazioni
* **D**enial of Service
* **E**levation of privileges, ovvero l'elevazione di privilegi

Le vulnerabilità sono bug introdotti nel codice dagli sviluppatori che espongono un'applicazione al rischio di attacchi. L'invio di dati sensibili in testo non crittografato, ad esempio, rende possibile la divulgazione di informazioni tramite attacchi di sniffing del traffico.

Gli attacchi sfruttano queste vulnerabilità per causare danni a un'applicazione. Il Cross Site Scripting (XSS), ad esempio, è un attacco che sfrutta l'output non purificato. Un altro esempio è l'eavesdropping in rete, ossia il tentativo di catturare le credenziali inviate in chiaro. Questi attacchi possono portare alla realizzazione di episodi di spoofing di identità. Per semplicità, le minacce, le vulnerabilità e gli attacchi possono essere paragonati a fatti negativi.
I seguenti diagrammi offrono una panoramica degli aspetti negativi correlati a un'applicazione Web distribuita in Azure (dal
[PDF delle note sulla sicurezza di Azure](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) di J. D. Meier:

![Minacce, vulnerabilità e attacchi][02]

Gli sviluppatori hanno il controllo delle vulnerabilità. Con un numero minore di vulnerabilità introdotte, si riducono anche le opzioni che possono essere sfruttate per gli attacchi di utenti malintenzionati.

Le vulnerabilità legate a identità e accesso aprono la strada a tutte le minacce del modello STRIDE. Un meccanismo di autenticazione implementato in modo improprio, ad esempio, può portare a episodi di spoofing di identità e, di conseguenza, a operazioni di divulgazione delle informazioni, manomissione dei dati, elevazione di privilegi o addirittura all'arresto completo del servizio. Valutare le seguenti domande che potrebbero generare potenziali vulnerabilità nell'implementazione di identità e accesso delle app per cloud:

* Si inviano credenziali in chiaro ai servizi di Azure tramite la rete?
* Le credenziali dei servizi di Azure vengono archiviate in testo non crittografato?
* Le credenziali dei servizi di Azure sono conformi ai criteri per le password complesse?
* La verifica delle credenziali viene effettuata tramite Azure anziché con meccanismi personalizzati?
* Le sessioni di autenticazione dei servizi di Azure sono limitate o la durata dei token prevede una finestra temporale ragionevole?
* Vengono verificate le autorizzazioni per ogni punto di ingresso di Azure dell'app per cloud distribuiti?
* Se si verificano problemi con il meccanismo di autorizzazione, la sicurezza è comunque garantita, senza esporre informazioni sensibili né consentire accesso illimitato?

## <a name="countermeasures"></a>Contromisure
Le contromisure ottimali contro gli attacchi consistono nell'utilizzare meccanismi di identità e accesso offerti dalla piattaforma anziché implementarli internamente. Valutare le principali tecnologie di identità e accesso riportate di seguito:

**Windows Identity Foundation (WIF).** WIF è una libreria di runtime .NET inclusa in .NET Framework 4.5, disponibile anche come download distinto per .NET 3.5/4.0, che gestisce protocolli come WS-Federation e WS-Trust e token come Security Assertion Markup Language (SAML), consentendo di evitare di scrivere codice molto complesso relativo alla sicurezza nell'applicazione. Per informazioni approfondite su WIF, vedere le risorse seguenti:

* [Esempi di Windows Identity Foundation 4.5](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication) in MSDN Code Gallery.
* [Strumenti di Windows Identity Foundation 4.5 per Visual Studio 11 Beta](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) in MSDN Code Gallery.
* [Runtime di Windows Identity Foundation (.NET 3.5/4.0)](http://www.microsoft.com/download/details.aspx?id=17331) in MSDN.
* [Esempi di Windows Identity Foundation 3.5/4.0 e modelli di Visual Studio 2008/2010](http://www.microsoft.com/download/details.aspx?displaylang=en&id=4451) in MSDN.

**Azure AD Access Control (in precedenza ACS)**. Azure AD Access Control è un servizio cloud che include il servizio token di sicurezza e consente la federazione con diversi provider di identità, ad esempio i provider di identità aziendali Active Directory o i provider di identità Internet come Windows Live ID/account Microsoft, Facebook, Google, Yahoo! e Open ID 2.0. Le risorse seguenti forniscono informazioni approfondite su Azure AD Access Control:

* [Servizio di controllo di accesso 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
* [Scenari e soluzioni supportati da ACS](http://msdn.microsoft.com/library/gg185920.aspx)
* [Procedure di ACS](http://msdn.microsoft.com/library/windowsazure/gg185939.aspx)
* [Guida all'identità basata sulle attestazioni e al controllo di accesso](http://msdn.microsoft.com/library/ff423674.aspx)
* [Kit di formazione per sviluppatori di identità](http://www.microsoft.com/download/details.aspx?id=14347)
* [Corso di formazione per sviluppatori di identità ospitato su MSDN](http://msdn.microsoft.com/IdentityTrainingCourse)

**Active Directory Federation Services (AD FS).**Active Directory Federation Services (AD FS) 2.0 offre il supporto per soluzioni di gestione delle identità compatibili con le attestazioni che interagiscono con Windows Server e con la tecnologia Active Directory. AD FS 2.0 è compatibile con i protocolli WS-Trust, WS-Federation e SAML. Per informazioni approfondite su AD FS, vedere le risorse seguenti:

* [Mappa di contenuti di ADFS 2.0](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
* [Web SSO Design][Web SSO Design] (Progettazione di Web SSO)
* [Federated Web SSO Design][Federated Web SSO Design] (Progettazione di Web SSO federativo)

**Firme di accesso condiviso di Azure.** Le firme di accesso condiviso consentono di ottimizzare l'accesso a una risorsa BLOB o contenitore. Per informazioni approfondite sulle firme di accesso condiviso, vedere le risorse seguenti:

* [Gestione dell'accesso a BLOB e contenitori](http://msdn.microsoft.com/library/ee393343.aspx)
* [Nuova funzionalità di archiviazione: firme di accesso condiviso](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
* [Come usare facilmente le firme di accesso condiviso](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

## <a name="scenarios-map"></a>Mappa degli scenari
In questa sezione vengono illustrati brevemente i principali scenari trattati nell'argomento.
Usare queste informazioni come mappa per determinare la soluzione di identità appropriata per una specifica applicazione.

* **Applicazione Web Form ASP.NET con autenticazione federata.** In questo scenario l'accesso all'app Web Form ASP.NET viene controllato mediante identità Internet come Live ID/account Microsoft o identità aziendali gestite in Windows Server Active Directory.
* **Servizio WCF (SOAP) con autenticazione federata.**In questo scenario l'accesso al servizio WCF (SOAP) viene controllato mediante identità del servizio gestite da Azure AD Access Control.
* **Servizio WCF (SOAP) con autenticazione federata e identità in Active Directory.** In questo scenario l'accesso al servizio Web WCF (SOAP) viene controllato mediante identità gestite da Windows Server Active Directory aziendale.
* **Servizio WCF (REST) con autenticazione federata.**In questo scenario l'accesso al servizio WCF (REST) viene controllato mediante identità del servizio gestite da Azure AD Access Control.
* **Servizio WCF (REST) con Live ID/account Microsoft, Facebook, Google, Yahoo!, Open ID.** In questo scenario l'accesso al servizio WCF (REST) viene controllato mediante identità Internet come Live ID/account Microsoft.
* **App Web ASP.NET per servizio WCF REST con token SWT condiviso.** Questo scenario prevede un'applicazione distribuita con un'app Web ASP.NET front-end e un servizio REST downstream e il contesto dell'utente deve essere mantenuto attraverso livelli fisici.
* **Autorizzazione con Controllo degli accessi in base al ruolo in servizi e applicazioni in grado di riconoscere attestazioni.** In questo scenario la logica delle autorizzazioni viene implementata nell'app in base ai ruoli.
* **Autorizzazione basata su attestazioni in applicazioni e servizi in grado di riconoscere attestazioni.** In questo scenario la logica delle autorizzazioni viene implementata nell'app in base a complesse regole di autorizzazione.
* **Scenari di identità e accesso nel servizio Archiviazione di Azure.**In questo scenario è necessario condividere in modo sicuro l'accesso a BLOB e contenitori di archiviazione di Azure.
* **Scenari di identità e accesso nel database SQL di Azure.**Il database SQL supporta solo l'autenticazione di SQL Server. L'autenticazione di Windows (sicurezza integrata) non è supportata. Gli utenti devono specificare le credenziali (account di accesso e password) ogni volta che si connettono al database SQL.
* **Scenari di identità e accesso nel bus di servizio di Azure.**In questo scenario è necessario accedere in modo sicuro alle code del bus di servizio di Azure.
* **Scenari di identità e accesso nella cache in memoria.**In questo scenario è necessario accedere in modo sicuro ai dati gestiti dalla cache in memoria.
* **Scenari di identità e accesso in Azure Marketplace.**In questo scenario è necessario accedere in modo sicuro ai set di dati di Azure Marketplace.

## <a name="azure-identity-and-access-scenarios"></a>Scenari di identità e accesso in Azure
In questa sezione vengono illustrati i comuni scenari di identità e accesso per diverse architetture di applicazioni. Utilizzare la mappa di scenari per un rapido orientamento.

### <a name="aspnet-web-form-application-with-federated-authentication"></a>Applicazione Web Form ASP.NET con autenticazione federata
In questo scenario di architettura l'applicazione Web può essere distribuita in Azure o in locale. L'applicazione richiede l'autenticazione degli utenti tramite Active Directory aziendale o provider di identità Internet.

Per risolvere questi scenari, usare Azure AD Access Control e Windows Identity Foundation.

![Azure AD Access Control][03]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Creare la prima applicazione ASP.NET in grado di riconoscere attestazioni con ACS](http://msdn.microsoft.com/library/gg429779.aspx)
* [Procedura: Ospitare pagine di accesso nell'applicazione Web ASP.NET](http://msdn.microsoft.com/library/gg185926.aspx)
* [Procedura: Implementare mediante WIF e ACS l'autorizzazione delle attestazioni in un'applicazione ASP.NET in grado di riconoscere attestazioni](http://msdn.microsoft.com/library/gg185907.aspx)    
* [Procedura: Implementare Controllo degli accessi in base al ruolo mediante WIF e ACS in un'applicazione ASP.NET in grado di riconoscere attestazioni](http://msdn.microsoft.com/library/gg185914.aspx)
* [Procedura: Configurare criteri di attendibilità tra ACS e le applicazioni Web ASP.NET usando certificati X.509](http://msdn.microsoft.com/library/gg185947.aspx)
* [Esempio di codice: Web Form ASP.NET semplice](http://msdn.microsoft.com/library/gg185938.aspx)

### <a name="wcf-soap-service-with-service-identity"></a>Servizio WCF (SOAP) con identità del servizio
In questo scenario di architettura per le applicazioni il servizio WCF (SOAP) potrebbe essere distribuito in Azure o in locale. È accessibile come servizio downstream da un'applicazione Web o anche da un altro servizio Web. È necessario controllare l'accesso mediante un'identità specifica dell'applicazione. Questo scenario si può paragonare al tipo di account del pool di app utilizzato in IIS. Anche se la tecnologia è diversa, infatti, gli approcci sono simili perché il servizio è accessibile tramite un account di ambito di applicazione anziché un account di utente finale.

Utilizzare la funzionalità di identità del servizio di Azure AD Access Control.
Si tratta di una funzionalità simile all'account del pool di app IIS utilizzato per la distribuzione di app in Windows Server e IIS. Configurare Azure AD Access Control in modo che vengano emessi token SAML che verranno gestiti da WIF nel servizio WCF (SOAP).

![Servizio WCF (SOAP)][04]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Aggiungere identità del servizio con un certificato X.509, una password o una chiave simmetrica](http://msdn.microsoft.com/library/gg185924.aspx)
* [Procedura: Eseguire l'autenticazione con un certificato client in un servizio WCF protetto da ACS](http://msdn.microsoft.com/library/hh289316.aspx)
* [Procedura: Eseguire l'autenticazione con un nome utente e una password in un servizio WCF protetto da ACS](http://msdn.microsoft.com/library/gg185954.aspx)
* [Esempio di codice: autenticazione del certificato WCF](http://msdn.microsoft.com/library/gg185952.aspx)
* [Esempio di codice: autenticazione del nome utente WCF](http://msdn.microsoft.com/library/gg185927.aspx)

### <a name="wcf-soap-service-with-federated-authentication-identities-in-active-directory"></a>Servizio WCF (SOAP) con autenticazione federata e identità in Active Directory
In questo scenario di architettura per le applicazioni il servizio WCF (SOAP) potrebbe essere distribuito in Azure o in locale. È necessario controllare l'accesso tramite un'identità gestita da Windows Server Active Directory (AD) aziendale.

Utilizzare Azure AD Access Control configurato per la federazione con Windows Server ADFS. In questo caso non è necessario configurare l'identità del servizio con Azure AD Access Control. L'agente che deve accedere al servizio WCF (SOAP) fornisce le credenziali ad ADFS e, dopo la corretta autenticazione, riceve il token emesso da ADFS. Il token viene quindi inviato ad Azure AD Access Control ed emesso nuovamente per l'agente, che lo utilizza per inviare la richiesta al servizio WCF (SOAP).

![Servizio WCF (SOAP) con AD][05]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Aggiungere identità del servizio con un certificato X.509, una password o una chiave simmetrica](http://msdn.microsoft.com/library/gg185924.aspx)
* [Procedura: Configurare ADFS 2.0 come provider di identità](http://msdn.microsoft.com/library/gg185961.aspx)
* [Procedura: Usare il servizio di gestione per configurare ADFS 2.0 come provider di identità aziendale](http://msdn.microsoft.com/library/gg185905.aspx)
* [Esempio di codice: autenticazione federata WCF con ADFS 2.0 ](http://msdn.microsoft.com/library/hh127796.aspx)

### <a name="wcf-rest-service-with-service-identities"></a>Servizio WCF (REST) con identità del servizio
In questo scenario il servizio WCF (REST) può essere distribuito in Azure o in locale. È accessibile come servizio downstream da un'applicazione Web o anche da un altro servizio Web. È necessario controllare l'accesso mediante un'identità specifica dell'applicazione. Questo scenario si può paragonare al tipo di account del pool di app utilizzato in IIS. Anche se la tecnologia è diversa, infatti, gli approcci sono simili perché il servizio è accessibile tramite un account di ambito di applicazione anziché un account di utente finale.

Utilizzare la funzionalità di identità del servizio di Azure AD Access Control.
Configurare Azure AD Access Control per l'emissione di token SWT (Simple Web Token). Per gestire il token SWT sul lato del servizio REST, è possibile implementare un gestore di token personalizzato e collegarlo alla pipeline WIF oppure è possibile analizzarlo "manualmente" senza utilizzare l'infrastruttura WIF.

Consultare il seguente diagramma (WIF è facoltativo):

![Servizio REST][06]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Configurare criteri di attendibilità tra ACS e il servizio WCF usando chiavi simmetriche](http://msdn.microsoft.com/library/gg185958.aspx)
* [Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS](http://msdn.microsoft.com/library/hh289317.aspx)
* [Esempio di codice: Servizio Web ASP.NET](http://msdn.microsoft.com/library/gg983271.aspx)
* [Esempio di codice: applicazione per Windows Phone 7](http://msdn.microsoft.com/library/gg983271.aspx)
* [WCF REST con token SWT rilasciato da Servizio di controllo di accesso (ACS) di Azure](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

### <a name="wcf-rest-service-with-live-id--microsoft-account-facebook-google-yahoo-open-id"></a>Servizio WCF (REST) con Live ID/account Microsoft, Facebook, Google, Yahoo!, Open ID
In questo scenario il servizio WCF (REST) può essere distribuito in Azure o in locale. È necessario controllare l'accesso mediante un'identità Internet pubblica, ad esempio Live ID/account Microsoft o Facebook.

Utilizzare Azure AD Access Control per l'emissione di token SWT. Per gestire il token SWT sul lato del servizio REST, è possibile implementare un gestore di token personalizzato e collegarlo alla pipeline WIF oppure è possibile analizzarlo "manualmente" senza utilizzare l'infrastruttura WIF.

È importante notare che, per implementare questo scenario, è necessario che l'applicazione utilizzi il controllo Web browser per raccogliere le credenziali dell'utente finale. Di conseguenza, questa opzione non è praticabile per gli scenari in cui l'accesso al servizio REST viene effettuato da un'app Web ASP.NET. È indicata solo per gli scenari in cui il servizio REST è accessibile dall'applicazione client dell'utente, ad esempio un'app di Windows Phone 7 o un rich client. Il motivo principale per la visualizzazione del controllo Web browser è che le identità Internet non supportano in modo nativo scenari di profili attivi, ossia di servizi Web, ma supportano prevalentemente scenari di profili passivi (app Web) basati su reindirizzamenti del browser, in cui il controllo Web browser si rivela utile.

Consultare il seguente diagramma (WIF è facoltativo, quindi non viene mostrato):

![WIF è facoltativo][07]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS](http://msdn.microsoft.com/library/hh289317.aspx)
* [Procedura: Configurare Google come provider di identità](http://msdn.microsoft.com/library/gg185976.aspx)
* [Procedura: Configurare Facebook come provider di identità](http://msdn.microsoft.com/library/gg185919.aspx)
* [Procedura: Configurare Yahoo! come provider di identità](http://msdn.microsoft.com/library/gg185977.aspx)
* [ Esempio di codice: applicazione per Windows Phone 7](http://msdn.microsoft.com/library/gg983271.aspx)
* [WCF REST con token SWT rilasciato da Servizio di controllo di accesso (ACS) di Azure](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

### <a name="aspnet-web-app-to-rest-wcf-service-using-shared-swt-token"></a>App Web ASP.NET per servizio WCF REST con token SWT condiviso
Questo scenario prevede un'applicazione distribuita con un'app Web ASP.NET front-end e un servizio REST downstream e il contesto dell'utente deve essere mantenuto attraverso livelli fisici. Ciò si rende talvolta necessario quando si implementa la registrazione o la logica delle autorizzazioni in base all'identità dell'utente finale nel servizio REST downstream.

Configurare Azure AD Access Control per l'emissione di token SWT. Il token SWT viene inviato all'app Web ASP.NET front-end e quindi condiviso con il servizio REST downstream. In questo caso, in Azure AD Access Control è configurata una sola relying party. Tuttavia, è necessario valutare diversi aspetti:

* Poiché WIF non fornisce un gestore di token SWT predefinito, è necessario implementarne uno personalizzato da utilizzare con l'app Web ASP.NET. È consigliabile sfruttare il supporto di WIF per il protocollo WS-Federation, che si basa su reindirizzamenti del browser, anziché scegliere implementazioni autonome.
* Quando si implementa un gestore di token SWT personalizzato, verificare che il token di bootstrap venga considerato e mantenuto.
  In caso contrario, non sarà possibile condividerlo e inviarlo al servizio REST downstream.
* Non è necessario usare WIF in un servizio REST, ma è possibile analizzare il token "manualmente", poiché in questo caso i reindirizzamenti non dovranno essere gestiti.

![Applicazione Web ASP.NET][08]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Configurare Google come provider di identità](http://msdn.microsoft.com/library/gg185976.aspx)
* [Procedura: Configurare Facebook come provider di identità](http://msdn.microsoft.com/library/gg185919.aspx)
* [Procedura: Configurare Yahoo! come provider di identità](http://msdn.microsoft.com/library/gg185977.aspx)
* [Delega da un'app Web ASP.NET al servizio WCF REST usando un token SWT condiviso](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

### <a name="role-based-access-control-rbac-in-claims-aware-applications-and-services"></a>Controllo degli accessi in base al ruolo in servizi e applicazioni in grado di riconoscere attestazioni.
In questo scenario è necessario implementare l'autorizzazione nell'applicazione o servizio Web in base ai ruoli utente: gli utenti che dispongono dei ruoli necessari possono accedere, mentre gli altri no. In breve, l'applicazione deve rispondere a una semplice domanda: il ruolo dell'utente è X?

È possibile risolvere questo scenario in diversi modi, ossia utilizzando Azure AD Access Control, WIF ClaimsAuthenticationManager, mapping samlSecurityTokenRequirement o RoleManager personalizzato.

WIF viene utilizzato in tutti i casi e supporta il metodo IPrincipal.IsInRole("MyRole"). Nella maggior parte dei casi, la chiave consiste nell'assicurarsi che sia disponibile un'attestazione di tipo ruolo con l'URI http://schemas.microsoft.com/ws/2008/06/identity/claims/role nel token in modo che WIF possa verificare l'appartenenza ai ruoli quando viene chiamato il metodo IsInRole.

**Azure AD Access Control**. In questa implementazione viene usato il motore delle regole di trasformazione delle attestazioni di Azure AD Access Control. Con le regole di questo motore, è possibile trasformare qualsiasi attestazione in ingresso in un'attestazione di tipo ruolo, in modo che quando l'applicazione rileva l'applicazione o un servizio, WIF possa analizzare l'attestazione di tipo ruolo per garantire la corretta esecuzione della chiamata al metodo IsInRole.

![][09]

**WIF ClaimsAuthenticationManager**. In questa implementazione utilizzare ClaimsAuthenticationManager come punto di estendibilità di WIF. Con questo approccio è possibile trasformare qualsiasi attestazione arbitraria in ingresso in un'attestazione di tipo ruolo nell'applicazione. La complessità della trasformazione è limitata solo dal codice che viene scritto.

![][10]

**Mapping samlSecurityTokenRequriement**. In questa implementazione si utilizza la configurazione samlSecurityTokenRequirement in web.config per indicare a WIF quali tipi di attestazione si comportano da attestazioni di tipo ruolo. Se ad esempio il token esegue un'attestazione di tipo gruppo, è possibile eseguirne il mapping a un'attestazione di tipo ruolo. Con questo approccio è possibile realizzare solo mapping semplici.

![][11]

**Oggetto RoleManager personalizzato.** In questa implementazione si utilizza l'oggetto RoleManger personalizzato. WIF viene usato per esaminare le attestazioni in ingresso quando si implementano metodi di interfaccia RoleManager personalizzati, ad esempio GetAllRoles().

![][12]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Implementare Controllo degli accessi in base al ruolo mediante WIF e ACS in un'applicazione ASP.NET in grado di riconoscere attestazioni](http://msdn.microsoft.com/library/gg185914.aspx)
* [Procedura: Implementare la logica di trasformazione dei token mediante regole](http://msdn.microsoft.com/library/gg185955.aspx)
* [Autorizzazione con RoleManager per applicazioni Web ASP.NET (WIF) in grado di riconoscere attestazioni](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
* Esempio di codice: uso di attestazioni in IsInRole in [Windows Identity Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

### <a name="claims-based-authorization-in-claims-aware-applications-and-services"></a>Autorizzazione basata su attestazioni in applicazioni e servizi in grado di riconoscere attestazioni
In questo scenario è necessario implementare una logica di autorizzazione complessa nell'applicazione o nel servizio Web e il metodo IsInRole() non è soddisfacente per queste esigenze. Se l'approccio delle autorizzazioni si basa sui ruoli, è consigliabile implementare il controllo degli accessi basato sui ruoli illustrato nella sezione precedente.

Utilizzare ClaimsAuthorizationManager come punto di estendibilità di WIF.
ClaimsAuthorizationManager consente le chiamate di controllo di accesso esterno, in modo che il codice dell'applicazione sia più pulito di aspetto e più facilmente gestibile rispetto allo scenario in cui i controlli di accesso vengono implementati al suo interno.

![][13]

Per implementare questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Implementare la logica di trasformazione dei token mediante regole](http://msdn.microsoft.com/library/gg185955.aspx)
* [Procedura: Implementare mediante WIF e ACS l'autorizzazione delle attestazioni in un'applicazione ASP.NET in grado di riconoscere attestazioni](http://msdn.microsoft.com/library/gg185907.aspx)
* Esempio di codice: autorizzazione basata sulle attestazioni in [Windows Identity Foundation SDK](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

## <a name="azure-storage-service-identity-and-access-scenarios"></a>Scenari di identità e accesso in Archiviazione di Azure
In questo scenario è necessario condividere in modo sicuro l'accesso a BLOB e contenitori di archiviazione di Azure.

Utilizzare le firme di accesso condiviso. Per accedere all'account del servizio di archiviazione dalla propria applicazione, utilizzare il codice hash condiviso disponibile tramite il portale di Azure per la configurazione e la gestione degli account del servizio di archiviazione. Se si desidera concedere a un altro utente l'accesso ai BLOB e ai contenitori nell'account del servizio di archiviazione, utilizzare gli URL delle firme di accesso condiviso.

Prestare particolare attenzione alla sicurezza nella gestione delle informazioni, per evitare esposizioni. Tenere inoltre presente la durata delle firme di accesso condiviso.

![][14]

Per risolvere questo scenario, fare riferimento alle seguenti risorse

* [Gestione dell'accesso a BLOB e contenitori](http://msdn.microsoft.com/library/ee393343.aspx)
* [Nuova funzionalità di archiviazione: firme di accesso condiviso](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
* [Come usare facilmente le firme di accesso condiviso](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

## <a name="azure-sql-database-identity-and-access-scenarios"></a>Scenari di identità e accesso nel database SQL di Azure
Il database SQL supporta solo l'autenticazione di SQL Server. L'autenticazione di Windows (sicurezza integrata) non è supportata. Gli utenti devono specificare le credenziali (account di accesso e password) ogni volta che si connettono al database SQL. Prestare particolare attenzione durante la gestione di nome utente e password per evitare la divulgazione delle informazioni.

![][15]

Per risolvere questo scenario, vedere l'argomento della Guida seguente:<br/>
[Sviluppo per il database SQL di Azure: procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)

Oppure vedere uno dei numerosi argomenti figlio, ad esempio:

* [Procedura: Connettersi al database SQL tramite sqlcmd](http://msdn.microsoft.com/library/azure/ee336280.aspx)
* [Codice di esempio: logica di tentativi per la connessione al database SQL di Azure con ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)
* [Procedura: Connettersi al database SQL tramite PHP](http://msdn.microsoft.com/library/azure/ff394110.aspx)
* [Procedura: Connettersi al database SQL tramite JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx)

Oppure vedere:<br/>
[Linee guida e limitazioni per la sicurezza per il database SQL di Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx#authentication)

## <a name="azure-service-bus-identity-and-access-scenarios"></a>Scenari di identità e accesso nel bus di servizio di Azure
Il bus di servizio e Azure AD Access Control presentano una relazione speciale, in quanto ogni spazio dei nomi servizio del bus di servizio è abbinato a uno spazio dei nomi servizio corrispondente del controllo di accesso con lo stesso nome, con il suffisso "-sb". Il motivo di questa relazione speciale è dovuto al fatto che il bus di servizio e il controllo di accesso gestiscono la relazione di trust reciproca e i segreti di crittografia associati. Per altre informazioni, fare riferimento alle risorse elencate di seguito.

![][16]

Per risolvere questo scenario, fare riferimento alle seguenti risorse:

* [Sicurezza del bus di servizio con ACS](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (video)
* [Sicurezza del bus di servizio con ACS](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (diapositive)
* [Autenticazione e autorizzazione del bus di servizio con Servizio di controllo di accesso](http://msdn.microsoft.com/library/hh403962.aspx)

## <a name="in-memory-cache-identity-and-access-scenarios"></a>Scenari di identità e accesso nella cache in memoria
La cache in memoria (in precedenza Cache di Azure) si basa su Azure AD Access Control per l'autenticazione. Utilizza le chiavi condivise disponibili tramite il portale di gestione. Utilizzare queste chiavi nel codice o nei file di configurazione per l'accesso alla cache. Accertarsi di archiviare in modo sicuro le chiavi per evitare la divulgazione di informazioni.

![][17]

Per risolvere questo scenario, fare riferimento alle seguenti risorse:

* [Procedura: Configurare un client della cache a livello di codice per Cache di Azure](http://msdn.microsoft.com/library/windowsazure/gg618003.aspx)
* [Procedura: Configurare un client della cache mediante un file di configurazione dell'applicazione per Cache di Azure](http://msdn.microsoft.com/library/windowsazure/gg278346.aspx)
* [Esempi di bus di servizio e memorizzazione nella cache di Azure](http://msdn.microsoft.com/library/ee706741.aspx) (sezione di esempi di memorizzazione nella cache)

## <a name="azure-marketplace-identity-and-access-scenarios"></a>Scenari di identità e accesso in Azure Marketplace
Prima di ottenere l'accesso a un set di dati di Azure Marketplace, sia gratuito che a pagamento, è necessario che l'utente esegua ogni volta l'autenticazione. Quando si crea un'applicazione, il processo di autenticazione deve essere incluso nel codice. Esaminare gli scenari comuni seguenti:

### <a name="i-access-my-dataset"></a>Accesso al set di dati personale
In questo scenario viene creata un'applicazione che usa i set di dati nella sottoscrizione Marketplace. Lo sviluppatore è l'utente dell'applicazione,
che può essere distribuita in Azure, in locale o in Marketplace.

Utilizzare la chiave condivisa disponibile tramite la sottoscrizione Marketplace. Questa chiave si ottiene mediante il portale Marketplace.

![][18]

Per risolvere questo scenario, fare riferimento alle seguenti risorse:

* [Implementare l'autenticazione di base HTTP nell'app per Marketplace](http://msdn.microsoft.com/library/gg193417.aspx)

### <a name="users-access-my-datasets"></a>Gli utenti accedono ai set di dati dello sviluppatore
In questo scenario viene creata un'applicazione che consente agli utenti di accedere al proprio set di dati. L'applicazione può essere distribuita in Azure, in locale o in Marketplace.

Per risolvere questo scenario, utilizzare la delega OAuth. Agli utenti verrà richiesto di specificare le credenziali di Live ID/account Microsoft e dovranno completare la procedura di consenso.

![][19]

Per risolvere questo scenario, fare riferimento alle seguenti risorse:

* [Esempio di client Web OAuth](http://go.microsoft.com/fwlink/?LinkId=219162)
* [Esempio di rich client OAuth](http://go.microsoft.com/fwlink/?LinkId=219163)

### <a name="application-access-marketplace-api"></a>Accesso dell'applicazione all'API Marketplace
In questo scenario si crea un'applicazione che accede all'API Marketplace. Per eseguire correttamente le chiamate all'API Marketplace, è necessario eseguire l'autenticazione. L'applicazione viene distribuita in Azure Marketplace.

![][20]

Per i dettagli sull'implementazione dell'autenticazione, consultare il kit di pubblicazione in Marketplace.

Per risolvere questo scenario, fare riferimento alle seguenti risorse:

* [Download del kit di pubblicazione di app](http://go.microsoft.com/fwlink/?LinkId=221323)
* [Introduzione ad Azure Marketplace per le applicazioni](https://datamarket.azure.com/)

## <a name="security-knobs"></a>Misure di sicurezza
In questa sezione vengono illustrate le misure di sicurezza per Windows Identity Foundation e Azure AD Access Control. Utilizzare queste informazioni come elenco di controllo di base della sicurezza per queste tecnologie durante la progettazione e la distribuzione di applicazioni.

### <a name="windows-identity-foundation"></a>Windows Identity Foundation
Di seguito sono riportate le principali misure di sicurezza di WIF. Le informazioni sono estratte dagli articoli [Considerazioni sulla progettazione di WIF](http://msdn.microsoft.com/library/ee517298.aspx) e [Windows Identity Foundation (WIF) Security for ASP.NET Web Applications - Threats & Countermeasures](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx) (Sicurezza di Windows Identity Foundation (WIF) per applicazioni Web ASP.NET - Minacce e contromisure).

* **IssuerNameRegistry**. Specifica i servizi token di sicurezza. Assicurarsi che siano elencati solo servizi token di sicurezza attendibili.
* **cookieHandler requireSsl="true"**. Specifica se i cookie della sessione vengono trasferiti tramite il protocollo SSL.
* **wsFederation's requireHttps="true"**. Specifica se la comunicazione del protocollo di federazione con il provider di identità viene eseguita tramite il protocollo SSL.
* **tokenReplayDetection enabled="true"**. Specifica se la funzionalità di rilevamento della riproduzione di token è abilitata. Tenere presente che questa funzionalità crea affinità con il server, in quanto gestisce copie locali dei token utilizzati.
* **audienceUris**. Specifica il gruppo di destinatari previsto per il token. Se l'applicazione riceve un token non previsto, verrà rifiutato da WIF.
* **requestValidation** e **httpRuntime requestValidationType**.
  Abilitano/disabilitano la funzionalità di convalida di ASP.NET. Vedere le indicazioni descritte in [Windows Identity Foundation (WIF): individuato un valore Request.Form potenzialmente pericoloso proveniente dal client](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)

### <a name="azure-ad-access-control"></a>Azure AD Access Control
Nella distribuzione di Azure AD Access Control tenere presenti le misure di sicurezza seguenti. Le informazioni sono estratte da [Linee guida per la sicurezza di ACS](http://msdn.microsoft.com/library/gg185962.aspx) e [Linee guida per la gestione dei certificati e delle chiavi](http://msdn.microsoft.com/library/hh204521.aspx).

* **Scadenza dei token del servizio token di sicurezza**. Utilizzare il portale di gestione di Azure AD Access Control per impostare una scadenza appropriata dei token.
* **Convalida dei dati durante l'utilizzo della funzionalità URL errori**. La funzionalità URL errori di Azure AD Access Control richiede l'accesso anonimo alla pagina dell'app quando vengono inviati messaggi di errore. Presupporre che tutti i dati provenienti da questa pagina siano pericolosi, in quanto inviati da un'origine non attendibile.
* **Crittografia dei token per scenari altamente sensibili**. Per ridurre il rischio di divulgazione delle informazioni disponibili nei token, è consigliabile crittografarli.
* **Crittografia dei cookie con RSA per la distribuzione in Azure**.
  Per impostazione predefinita, WIF crittografa i cookie con DPAPI. Crea affinità con il server e può comportare la generazione di eccezioni quando la distribuzione viene eseguita in ambienti Web farm e Azure. In questi scenari usare invece RSA.
* **Certificati per la firma di token**. Rinnovare periodicamente i certificati per la firma di token per evitare situazioni di Denial of Service. Azure AD Access Control firma tutti i token di sicurezza rilasciati. Quando si creano applicazioni che utilizzano token SAML emessi da ACS, per la firma vengono utilizzati certificati X.509. Quando i certificati di firma scadono, si riceveranno messaggi di errore se si tenta di richiedere un token.
* **Chiavi KSK dei token**. Rinnovare periodicamente le chiavi KSK dei token per evitare situazioni di Denial of Service. Azure AD Access Control firma tutti i token di sicurezza rilasciati. Quando si creano applicazioni che utilizzano token SWT emessi da ACS, vengono utilizzate chiavi KSK simmetriche a 256 bit. Quando le chiavi KSK scadono, si riceveranno messaggi di errore se si tenta di richiedere un token.
* **Certificati di crittografia dei token**. Rinnovare periodicamente i certificati di crittografia dei token per evitare situazioni di Denial of Service. La crittografia dei token è necessaria se un'applicazione relying party è un servizio Web che utilizza token di prova di possesso tramite il protocollo WS-Trust. Negli altri casi è invece facoltativa. Quando i certificati di crittografia scadono, si riceveranno messaggi di errore se si tenta di richiedere un token.
* **Certificati di decrittografia dei token**. Rinnovare periodicamente i certificati di decrittografia dei token per evitare situazioni di Denial of Service. Azure AD Access Control può accettare token crittografati di provider di identità WS-Federation, ad esempio ADFS 2.0. Per la decrittografia viene utilizzato un certificato X.509 ospitato in Azure AD Access Control.
  Quando i certificati di decrittografia scadono, si riceveranno messaggi di errore se si tenta di richiedere un token.
* **Credenziali per l'identità del servizio**. Rinnovare periodicamente le credenziali per l'identità del servizio per evitare situazioni di Denial of Service. Le identità del servizio usano credenziali configurate a livello globale per lo spazio dei nomi di Azure AD Access Control, che consentono ad applicazioni o client di eseguire l'autenticazione direttamente con Azure AD Access Control e di ricevere un token. All'identità del servizio Azure AD Access Control possono essere associati tre tipi di credenziali, ossia chiave simmetrica, password e certificato X.509. Quando le credenziali scadono, si inizieranno a ricevere eccezioni.
* **Credenziali per l'account del servizio di gestione Azure AD Access Control**. Rinnovare periodicamente le credenziali del servizio di gestione per evitare situazioni di Denial of Service. Il servizio di gestione di Azure AD Access Control è un componente chiave che consente di gestire e configurare a livello di codice le impostazioni dello spazio dei nomi di Azure AD Access Control. All'account del servizio di gestione è possibile associare tre tipi di credenziali, ossia chiave simmetrica, password e certificato X.509. Quando le credenziali scadono, si inizieranno a ricevere eccezioni.
* **Certificati di crittografia e firma del provider di identità WS-Federation**. Eseguire una query per verificare la validità del certificato del provider di identità WS-Federation ed evitare situazioni di Denial of Service. Il certificato del provider di identità WS-Federation è disponibile tramite i relativi metadati.
  Quando si configura il provider di identità WS-Federation, ad esempio ADFS, il certificato di firma di WS-Federation viene configurato tramite i metadati di WS-Federation disponibili via URL o come file. Dopo aver configurato il provider di identità WS-Federation, utilizzare il servizio di gestione di Azure AD Access Control per eseguire una query e verificare la validità dei relativi certificati. Quando il certificato scade, si inizieranno a ricevere eccezioni.

## <a name="shared-hosting-using-azure-websites"></a>Hosting condiviso tramite Siti Web di Azure
Tutti gli scenari e le soluzioni presenti in questo argomento sono validi quando l'applicazione è ospitata in Siti Web di Azure.

## <a name="azure-virtual-machines"></a>Macchine virtuali di Azure
Tutti gli scenari e le soluzioni illustrati in questo argomento sono validi quando l'applicazione è ospitata in Macchine virtuali di Azure.

## <a name="resources"></a>Risorse
* [Kit di formazione per sviluppatori di identità](http://go.microsoft.com/fwlink/?LinkId=214555)
* [Corso di formazione per sviluppatori di identità ospitato su MSDN](http://go.microsoft.com/fwlink/?LinkId=214561)
* [Guida all'identità basata sulle attestazioni e al controllo di accesso](http://go.microsoft.com/fwlink/?LinkId=214562)
* [Servizio di controllo di accesso](http://msdn.microsoft.com/library/windowsazure/gg429786.aspx)
* [Procedure di ACS](http://msdn.microsoft.com/library/windowsazure/gg185939.aspx)
* [Proteggere l'applicazione Web ASP.NET basata su ruoli Web di Azure tramite Servizio di controllo di accesso v2.0](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
* [Video Academy sul servizio Azure AD Access Control (ACS)](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
* [Microsoft Security Development Lifecycle](http://www.microsoft.com/security/sdl/default.aspx)
* [SDL Threat Modeling Tool 3.1.8](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
* [Blog su sicurezza e privacy](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
* [Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)
* [Security Intelligence Report](http://www.microsoft.com/security/sir/)
* [Security Development Lifecycle](http://www.microsoft.com/security/sdl/default.aspx)
* [Security Developer Center (MSDN)](http://msdn.microsoft.com/security/)

[01]:./media/SecurityRX/01_SecuringTheApplication.gif
[02]:./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]:./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]:./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]:./media/SecurityRX/05_AzureADAccessControl.gif
[06]:./media/SecurityRX/06_RESTService.gif
[07]:./media/SecurityRX/07_WIFisOptional.gif
[08]:./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]:./media/SecurityRX/09_RBAC.gif
[10]:./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]:./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]:./media/SecurityRX/12_CustomRoleManager.gif
[13]:./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]:./media/SecurityRX/14_WindowsAzurestorage.gif
[15]:./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]:./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]:./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]:./media/SecurityRX/18_IAccessMyDataset.gif
[19]:./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]:./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Web SSO Design]: http://technet.microsoft.com/library/dd807033(WS.10).aspx
[Federated Web SSO Design]: http://technet.microsoft.com/library/dd807050(WS.10).aspx


<!--HONumber=Jan17_HO3-->


