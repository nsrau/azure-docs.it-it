
# Linee guida sulla sicurezza in Azure

## Sunto

Quando si sviluppano applicazioni per Azure, i principali aspetti da valutare per quanto riguarda la sicurezza sono l'identità e l'accesso. In questo argomento vengono illustrati questi aspetti per le applicazioni cloud e vengono fornite indicazioni su come proteggerle in modo ottimale.

## Panoramica

La sicurezza di un'applicazione dipende dalla relativa superficie. Quanto più grande è la superficie esposta dall'applicazione, tanto maggiori saranno i rischi per la sicurezza. Un'applicazione che esegue un processo batch senza intervento dell'utente, ad esempio, è meno esposta dal punto di vista della sicurezza rispetto a un sito Web pubblico.

Con il passaggio nel cloud, si acquisisce un certo grado di tranquillità nei confronti di infrastruttura e reti, che vengono gestite nei data center con procedure, strumenti e tecnologie di sicurezza all'avanguardia. D'altra parte, il cloud per sua stessa natura espone un'area superficiale maggiore dell'applicazione, che potrebbe in teoria diventare oggetto di attacchi. In generale, infatti, diversamente dai componenti in memoria, le tecnologie e i servizi cloud sono esposti come endpoint. I servizi di archiviazione, bus di servizio, database SQL
(in precedenza SQL Azure) di Azure e molti altri sono accessibili
tramite i rispettivi endpoint sulla rete.

Gli sviluppatori di applicazioni cloud hanno maggiori responsabilità per garantire standard elevati di sicurezza in fase di progettazione, sviluppo e manutenzione, in modo da impedire attacchi di utenti malintenzionati. Nel diagramma seguente, riportato nel [PDF di note sulla sicurezza in Azure][1] di J.D. Meier, si può notare che l'infrastruttura rientra nell'ambito del provider di cloud, in questo caso Azure, quindi la maggior parte degli interventi di sicurezza ricade sugli sviluppatori di applicazioni:

![Protezione
dell'applicazione](./media/SecurityRX/01_SecuringTheApplication.gif)

L'aspetto positivo è che tutte le procedure, le tecniche e i principi di sviluppo ai fini della sicurezza sono identici anche nel caso di applicazioni cloud. Le principali aree da valutare sono le seguenti:

* Tutto l'input deve essere convalidato per tipo, lunghezza, intervallo
  e modelli di stringa per evitare attacchi di tipo injection e tutti i
  dati che l'app restituisce devono essere correttamente purificati.
* I dati sensibili devono essere protetti sia in locale che in transito
  per limitare la divulgazione di informazioni e le minacce di
  manomissione.
* Le procedure di controllo e registrazione devono essere implementate
  adeguatamente per limitare le minacce di non ripudio.
* Le funzionalità di autenticazione e autorizzazione devono essere
  implementate con meccanismi comprovati offerti dalla piattaforma, per
  evitare minacce di spoofing di identità ed elevazione dei privilegi.

Per l'elenco completo di minacce, attacchi, vulnerabilità e contromisure, fare riferimento a un [foglio informativo: sull'infrastruttura di sicurezza delle applicazioni Web][2] e all'[indice di indicazioni per la sicurezza delle applicazioni][3] pubblicati dal team patterns & practices.

Nel cloud i meccanismi di autenticazione e controllo di accesso sono molti diversi da quelli disponibili per le applicazioni locali. Per le applicazioni cloud vengono offerte molte più opzioni che possono generare confusione e, di conseguenza, implementazioni difettose. La confusione aumenta quando si tratta di definire che cos'è un'app per cloud. Ad esempio, l'applicazione potrebbe essere distribuita nel cloud mentre il meccanismo di autenticazione potrebbe essere fornito da Active Directory. Viceversa, l'applicazione potrebbe essere distribuita in locale ma con i meccanismi di autenticazione nel cloud, ad esempio resi disponibili da Azure AD Access Control, in precedenza Servizio di controllo di accesso o ACS.

## Minacce, vulnerabilità e attacchi

Le minacce sono potenziali risultati negativi da evitare, ad esempio la divulgazione di informazioni sensibili o la mancata disponibilità di un servizio. Vengono comunemente classificate con il modello "STRIDE":

* **S**poofing, ovvero furto di identità
* **T**ampering, ovvero la manomissione dei dati
* **R**epudiation, ovvero il ripudio di azioni
* **I**nformation disclosure, ovvero la divulgazione di informazioni
* **D**enial of Service
* **E**levation of privileges, ovvero l'elevazione di privilegi

Le vulnerabilità sono bug introdotti nel codice dagli sviluppatori che espongono un'applicazione al rischio di attacchi. L'invio di dati sensibili in testo non crittografato, ad esempio, rende possibile la divulgazione di informazioni tramite attacchi di sniffing del traffico.

Gli attacchi sfruttano queste vulnerabilità per causare danni a un'applicazione. Il Cross Site Scripting (XSS), ad esempio, è un attacco che sfrutta l'output non purificato. Un altro esempio è l'eavesdropping in rete, ossia il tentativo di catturare le credenziali inviate in chiaro. Questi attacchi possono portare alla realizzazione di episodi di spoofing di identità. Per semplicità, le minacce, le vulnerabilità e gli attacchi possono essere paragonati a fatti negativi. I diagrammi seguenti forniscono un quadro completo dei fatti negativi relativi a un'applicazione Web distribuita in Azure (dal [PDF di note sulla sicurezza in Azure][1] di J.D. Meier):

![Minacce, vulnerabilità e
attacchi](./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif)

Gli sviluppatori hanno il controllo delle vulnerabilità. Con un numero minore di vulnerabilità introdotte, si riducono anche le opzioni che possono essere sfruttate per gli attacchi di utenti malintenzionati.

Le vulnerabilità legate a identità e accesso aprono la strada a tutte le minacce del modello STRIDE. Un meccanismo di autenticazione implementato in modo improprio, ad esempio, può portare a episodi di spoofing di identità e, di conseguenza, a operazioni di divulgazione delle informazioni, manomissione dei dati, elevazione di privilegi o addirittura all'arresto completo del servizio. Valutare le domande seguenti che potrebbero generare potenziali vulnerabilità nell'implementazione di identità e accesso delle app per cloud:

* Le credenziali vengono inviate in testo non crittografato tramite la
  rete ai servizi di Azure?
* Le credenziali dei servizi di Azure vengono archiviate in testo non
  crittografato?
* Le credenziali dei servizi di Azure sono conformi ai criteri per le
  password complesse?
* La verifica delle credenziali viene effettuata tramite Azure anziché
  con meccanismi personalizzati?
* Le sessioni di autenticazione dei servizi di Azure sono limitate o la
  durata dei token prevede una finestra temporale ragionevole?
* Vengono verificate le autorizzazioni per ogni punto di ingresso di
  Azure dell'app per cloud distribuiti?
* Se si verificano problemi con il meccanismo di autorizzazione, la
  sicurezza è comunque garantita, senza esporre informazioni sensibili
  né consentire accesso illimitato?

## Contromisure

Le contromisure ottimali contro gli attacchi consistono nell'utilizzare meccanismi di identità e accesso offerti dalla piattaforma anziché implementarli internamente. Valutare le principali tecnologie di identità e accesso riportate di seguito:

**Windows Identity Foundation (WIF).** WIF è una libreria di runtime
.NET inclusa in .NET Framework 4.5, disponibile anche come download
distinto per .NET 3.5/4.0, che gestisce protocolli come WS-Federation e WS-Trust e token come Security Assertion Markup Language (SAML), consentendo di evitare di scrivere codice molto complesso relativo alla sicurezza nell'applicazione. Per informazioni approfondite su WIF, vedere le risorse seguenti:

* [Esempi di Windows Identity Foundation 4.5][4] in MSDN Code Gallery.
* [Strumenti di Windows Identity Foundation 4.5 per Visual Studio 11
  Beta][5] in MSDN Code Gallery.
* [Runtime di Windows Identity Foundation (.NET 3.5/4.0)][6] in MSDN.
* [Esempi di Windows Identity Foundation 3.5/4.0 e modelli di Visual
  Studio 2008/2010][7] in MSDN.

**Azure AD Access Control (in precedenza ACS)**. Azure AD Access Control
è un servizio cloud che rende disponibile il servizio token di sicurezza
e consente la federazione con provider di identità diversi, ad esempio Active Directory aziendale o provider di identità Internet come Windows Live ID/account Microsoft, Facebook, Google, Yahoo! e Open ID 2.0. Le risorse seguenti forniscono informazioni approfondite su Azure AD Access Control:

* [Servizio di controllo di accesso 2.0][8]
* [Scenari e soluzioni supportati da ACS][9]
* [Procedure di ACS][10]
* [Guida all'identità basata sulle attestazioni e al controllo di
  accesso][11]
* [Identity Developer Training Kit][12]
* [Corso di formazione per sviluppatori di identità ospitato su
  MSDN][13]

**Active Directory Federation Services (AD FS).**Active Directory
Federation Services (AD FS) 2.0 fornisce il supporto per soluzioni di identità in grado di riconoscere attestazioni che implicano tecnologie Windows Server e Active Directory. AD FS 2.0 è compatibile con i protocolli WS-Trust, WS-Federation e SAML. Per informazioni approfondite su AD FS, vedere le risorse seguenti:

* [Mappa di contenuti AD FS 2.0][14]
* [Progettazione di Web SSO][15]
* [Progettazione di Web SSO federativo][16]

**Firme di accesso condiviso di Azure.** Le firme di accesso condiviso
consentono di ottimizzare l'accesso a una risorsa BLOB o contenitore. Per informazioni approfondite sulle firme di accesso condiviso, vedere le risorse seguenti:

* [Gestire l'accesso alle risorse di archiviazione di Azure][17]
* [Nuova funzionalità di archiviazione: firme di accesso condiviso][18]
* [Come utilizzare facilmente le firme di accesso condiviso][19]

## Mappa degli scenari

In questa sezione vengono illustrati brevemente i principali scenari trattati nell'argomento. Utilizzare queste informazioni come mappa per determinare la soluzione di identità appropriata per una specifica applicazione.

* **Applicazione Web Form ASP.NET con autenticazione federata.** In
  questo scenario l'accesso all'app Web Form ASP.NET viene controllato
  mediante identità Internet come Live ID/account Microsoft o identità
  aziendali gestite in Windows Server Active Directory.
* **Servizio WCF (SOAP) con autenticazione federata.**In questo scenario
  l'accesso al servizio WCF (SOAP) viene controllato mediante identità
  del servizio gestite da Azure AD Access Control.
* **Servizio WCF (SOAP) con autenticazione federata e identità in Active
  Directory.** In questo scenario l'accesso al servizio Web WCF (SOAP)
  viene controllato mediante identità gestite da Windows Server Active
  Directory aziendale.
* **Servizio WCF (REST) con autenticazione federata.**In questo scenario
  l'accesso al servizio WCF (REST) viene controllato mediante identità
  del servizio gestite da Azure AD Access Control.
* **Servizio WCF (REST) con With Live ID/account Microsoft, Facebook,
  Google, Yahoo!, Open ID.** In questo scenario l'accesso al servizio
  WCF (REST) viene controllato mediante identità Internet come Live
  ID/account Microsoft.
* **App Web ASP.NET per servizio WCF REST con token SWT condiviso.**
  Questo scenario prevede un'applicazione distribuita con un'app Web
  ASP.NET front-end e un servizio REST downstream e il contesto
  dell'utente deve essere mantenuto attraverso livelli fisici.
* **Autorizzazione con Controllo degli accessi in base al ruolo in
  servizi e applicazioni in grado di riconoscere attestazioni.** In
  questo scenario la logica delle autorizzazioni viene implementata
  nell'app in base ai ruoli.
* **Autorizzazione basata su attestazioni in applicazioni e servizi in
  grado di riconoscere attestazioni.** In questo scenario la logica
  delle autorizzazioni viene implementata nell'app in base a complesse
  regole di autorizzazione.
* **Scenari di identità e accesso nel servizio Archiviazione di
  Azure.**In questo scenario è necessario condividere in modo sicuro
  l'accesso a BLOB e contenitori di archiviazione di Azure.
* **Scenari di identità e accesso nel database SQL di Azure.**Il
  database SQL supporta solo l'autenticazione di SQL Server.
  L'autenticazione di Windows (sicurezza integrata) non è supportata.
  Gli utenti devono specificare le credenziali (account di accesso e
  password) ogni volta che si connettono al database SQL.
* **Scenari di identità e accesso nel bus di servizio di Azure.** In
  questo scenario è necessario accedere in modo sicuro alle code del bus
  di servizio di Azure.
* **Scenari di identità e accesso nella cache in memoria.** In questo
  scenario è necessario accedere in modo sicuro ai dati gestiti dalla
  cache in memoria.
* **Scenari di identità e accesso in Azure Marketplace.** In questo
  scenario è necessario accedere in modo sicuro ai set di dati di Azure
  Marketplace.

## Scenari di identità e accesso in Azure

In questa sezione vengono illustrati i comuni scenari di identità e accesso per diverse architetture di applicazioni. Utilizzare la mappa di scenari per un rapido orientamento.

### Applicazione Web Form ASP.NET con autenticazione federata

In questo scenario di architettura l'applicazione Web può essere distribuita in Azure o in locale. L'applicazione richiede l'autenticazione degli utenti tramite Active Directory aziendale o provider di identità Internet.

Per risolvere questi scenari, utilizzare Azure AD Access Control e Windows Identity Foundation.

![Azure AD Access
Control](./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: creare mediante ACS la prima applicazione ASP.NET in grado
  di riconoscere attestazioni][20]
* [Procedura: ospitare pagine di accesso nell'applicazione Web
  ASP.NET][21]
* [Procedura: implementare mediante WIF e ACS l'autorizzazione delle
  attestazioni in un'applicazione ASP.NET in grado di riconoscere
  attestazioni][22]
* [Procedura: implementare mediante WIF e ACS il controllo di accesso
  basato sui ruoli (RBAC) in un'applicazione ASP.NET in grado di
  riconoscere attestazioni][23]
* [Procedura: configurare i criteri di attendibilità tra ACS e le
  applicazioni Web ASP.NET utilizzando certificati X.509][24]
* [Esempio di codice: Web Form ASP.NET semplice][25]

### Servizio WCF (SOAP) con identità del servizio

In questo scenario di architettura per le applicazioni il servizio WCF
(SOAP) potrebbe essere distribuito in Azure o in locale. È accessibile
come servizio downstream da un'applicazione Web o anche da un altro servizio Web. È necessario controllare l'accesso mediante un'identità specifica dell'applicazione. Questo scenario si può paragonare al tipo di account del pool di app utilizzato in IIS. Anche se la tecnologia è diversa, infatti, gli approcci sono simili perché il servizio è accessibile tramite un account di ambito di applicazione anziché un account di utente finale.

Utilizzare la funzionalità di identità del servizio di Azure AD Access Control. Si tratta di una funzionalità simile all'account del pool di app IIS utilizzato per la distribuzione di app in Windows Server e IIS. Configurare Azure AD Access Control in modo che vengano emessi token SAML che verranno gestiti da WIF nel servizio WCF (SOAP).

![Servizio WCF (SOAP)][81]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: aggiungere identità del servizio con un certificato X.509,
  una password o una chiave simmetrica][26]
* [Procedura: eseguire l'autenticazione con un certificato client per
  l'accesso a un servizio WCF protetto da ACS][27]
* [Procedura: eseguire l'autenticazione con un nome utente e una
  password per l'accesso a un servizio WCF protetto da ACS][28]
* [Esempio di codice: autenticazione del certificato per WCF][29]
* [Esempio di codice: autenticazione del nome utente per WCF][30]

### Servizio WCF (SOAP) con autenticazione federata e identità in Active Directory

In questo scenario di architettura per le applicazioni il servizio WCF
(SOAP) potrebbe essere distribuito in Azure o in locale. È necessario
controllare l'accesso tramite un'identità gestita da Windows Server Active Directory (AD) aziendale.

Utilizzare Azure AD Access Control configurato per la federazione con Windows Server ADFS. In questo caso non è necessario configurare l'identità del servizio con Azure AD Access Control. L'agente che deve accedere al servizio WCF (SOAP) fornisce le credenziali ad ADFS e, dopo la corretta autenticazione, riceve il token emesso da ADFS. Il token viene quindi inviato ad Azure AD Access Control ed emesso nuovamente per l'agente, che lo utilizza per inviare la richiesta al servizio WCF
(SOAP).

![Servizio WCF (SOAP) con
AD](./media/SecurityRX/05_AzureADAccessControl.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: aggiungere identità del servizio con un certificato X.509,
  una password o una chiave simmetrica][26]
* [Procedura: configurare ADFS 2.0 come provider di identità][31]
* [Procedura: utilizzare il servizio di gestione per configurare ADFS
  2.0 come provider di identità enterprise][32]
* [Esempio di codice: autenticazione federata WCF con ADFS 2.0][33]

### Servizio WCF (REST) con identità del servizio

In questo scenario il servizio WCF (REST) potrebbe essere distribuito in Azure o in locale. È accessibile come servizio downstream da un'applicazione Web o anche da un altro servizio Web. È necessario controllare l'accesso mediante un'identità specifica dell'applicazione. Questo scenario si può paragonare al tipo di account del pool di app utilizzato in IIS. Anche se la tecnologia è diversa, infatti, gli approcci sono simili perché il servizio è accessibile tramite un account di ambito di applicazione anziché un account di utente finale.

Utilizzare la funzionalità di identità del servizio di Azure AD Access Control. Configurare Azure AD Access Control per l'emissione di token SWT (Simple Web Token). Per gestire il token SWT sul lato del servizio REST, è possibile implementare un gestore di token personalizzato e collegarlo alla pipeline WIF oppure è possibile analizzarlo
"manualmente" senza utilizzare l'infrastruttura WIF.

Consultare il diagramma seguente (WIF è facoltativo):

![Servizio REST](./media/SecurityRX/06_RESTService.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: configurare i criteri di attendibilità tra ACS e il
  servizio WCF utilizzando chiavi simmetriche][34]
* [Procedura: eseguire l'autenticazione a un servizio WCF REST
  distribuito in Azure tramite ACS][35]
* [Esempio di codice: servizio Web ASP.NET][36]
* [Esempio di codice: applicazione per Windows Phone 7][36]
* [WCF REST con token WCF rilasciato da Servizio di controllo di accesso
  di Azure (ACS)][37]

### Servizio WCF (REST) con Live ID/account Microsoft, Facebook, Google, Yahoo!, Open ID

In questo scenario il servizio WCF (REST) potrebbe essere distribuito in Azure o in locale. È necessario controllare l'accesso mediante un'identità Internet pubblica, ad esempio Live ID/account Microsoft o Facebook.

Utilizzare Azure AD Access Control per l'emissione di token SWT. Per gestire il token SWT sul lato del servizio REST, è possibile implementare un gestore di token personalizzato e collegarlo alla pipeline WIF oppure è possibile analizzarlo "manualmente" senza utilizzare l'infrastruttura WIF.

È importante notare che, per implementare questo scenario, è necessario
che l'applicazione utilizzi il controllo Web browser per raccogliere le credenziali dell'utente finale. Di conseguenza, questa opzione non è praticabile per gli scenari in cui l'accesso al servizio REST viene effettuato da un'app Web ASP.NET. È indicata solo per gli scenari in cui il servizio REST è accessibile dall'applicazione client dell'utente, ad esempio un'app di Windows Phone 7 o un rich client. Il motivo principale per la visualizzazione del controllo Web browser è che le identità Internet non supportano in modo nativo scenari di profili attivi, ossia di servizi Web, ma supportano principalmente scenari di profili passivi, ossia app Web, che si basano su reindirizzamenti del browser. È in casi come questo che il controllo Web browser risulta utile.

Consultare il diagramma seguente (WIF è facoltativo, quindi non viene mostrato):

![WIF è facoltativo](./media/SecurityRX/07_WIFisOptional.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: eseguire l'autenticazione a un servizio WCF REST
  distribuito in Azure tramite ACS][35]
* [Procedura: configurare Google come provider di identità][38]
* [Procedura: configurare Facebook come provider di identità][39]
* [Procedura: configurare Yahoo! come provider di identità][40]
* [Esempio di codice: applicazione per Windows Phone 7][36]
* [WCF REST con token WCF rilasciato da Servizio di controllo di accesso
  di Azure (ACS)][37]

### App Web ASP.NET per servizio WCF REST con token SWT condiviso

Questo scenario prevede un'applicazione distribuita con un'app Web ASP.NET front-end e un servizio REST downstream e il contesto dell'utente deve essere mantenuto attraverso livelli fisici. Ciò si rende talvolta necessario quando si implementa la registrazione o la logica delle autorizzazioni in base all'identità dell'utente finale nel servizio REST downstream.

Configurare Azure AD Access Control per l'emissione di token SWT. Il token SWT viene inviato all'app Web ASP.NET front-end e quindi condiviso con il servizio REST downstream. In questo caso, in Azure AD Access Control è configurata una sola relying party. Tuttavia, è necessario valutare diversi aspetti:

* Poiché WIF non fornisce un gestore di token SWT predefinito, è
  necessario implementarne uno personalizzato da utilizzare con l'app
  Web ASP.NET. È consigliabile sfruttare il supporto di WIF per il
  protocollo WS-Federation, che si basa su reindirizzamenti del browser,
  anziché scegliere implementazioni autonome.
* Quando si implementa un gestore di token SWT personalizzato,
  verificare che il token di bootstrap venga considerato e mantenuto. In
  caso contrario, non sarà possibile condividerlo e inviarlo al servizio
  REST downstream.
* Non è necessario utilizzare WIF in un servizio REST, ma è possibile
  analizzare il token "manualmente", poiché in questo caso i
  reindirizzamenti non dovranno essere gestiti.

![Applicazione Web
ASP.NET](./media/SecurityRX/08_ASPNETWebApptoREST.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: configurare Google come provider di identità][38]
* [Procedura: configurare Facebook come provider di identità][39]
* [Procedura: configurare Yahoo! come provider di identità][40]
* [app Web ASP.NET per delega del servizio WCF REST con token SWT
  condiviso][41]

### Controllo degli accessi in base al ruolo in servizi e applicazioni in grado di riconoscere attestazioni.

In questo scenario è necessario implementare le autorizzazioni nell'applicazione o nel servizio Web in base ai ruoli utente. L'utente con i ruoli richiesti ottiene l'accesso, che viene invece negato a quelli che non ne dispongono. In breve, l'applicazione deve rispondere a una semplice domanda: il ruolo dell'utente è X?

È possibile risolvere questo scenario in diversi modi, ossia utilizzando
Azure AD Access Control, WIF ClaimsAuthenticationManager, mapping samlSecurityTokenRequirement o RoleManager personalizzato.

WIF viene utilizzato in tutti i casi e supporta il metodo IPrincipal.IsInRole("MyRole"). Nella maggior parte dei casi, la chiave consiste nell'assicurarsi che sia disponibile un'attestazione di tipo ruolo con l'URI http://schemas.microsoft.com/ws/2008/06/identity/claims/role nel token in modo che WIF possa verificare l'appartenenza ai ruoli quando viene chiamato il metodo IsInRole.

**Azure AD Access Control**. In questa implementazione viene utilizzato
il motore di regole di trasformazione delle attestazioni di Azure AD Access Control. Con le regole di questo motore, è possibile trasformare qualsiasi attestazione in ingresso in un'attestazione di tipo ruolo, in modo che quando l'applicazione rileva l'applicazione o un servizio, WIF possa analizzare l'attestazione di tipo ruolo per garantire la corretta esecuzione della chiamata al metodo IsInRole.

![](./media/SecurityRX/09_RBAC.gif)

**WIF ClaimsAuthenticationManager**. In questa implementazione
utilizzare ClaimsAuthenticationManager come punto di estendibilità di WIF. Con questo approccio è possibile trasformare qualsiasi attestazione arbitraria in ingresso in un'attestazione di tipo ruolo nell'applicazione. La complessità della trasformazione è limitata solo dal codice che viene scritto.

![](./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif)

**Mapping samlSecurityTokenRequriement**. In questa implementazione si
utilizza la configurazione samlSecurityTokenRequirement in web.config per indicare a WIF quali tipi di attestazione si comportano da attestazioni di tipo ruolo. Se ad esempio il token esegue un'attestazione di tipo gruppo, è possibile eseguirne il mapping a un'attestazione di tipo ruolo. Con questo approccio è possibile realizzare solo mapping semplici.

![](./media/SecurityRX/11_SecurityTokenRequriementmapping.gif)

**Oggetto RoleManager personalizzato.** In questa implementazione si
utilizza l'oggetto RoleManger personalizzato. WIF viene utilizzato per esaminare le attestazioni in ingresso quando si implementano metodi di interfaccia RoleManager personalizzati, ad esempio GetAllRoles().

![](./media/SecurityRX/12_CustomRoleManager.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: implementare mediante WIF e ACS il controllo di accesso
  basato sui ruoli (RBAC) in un'applicazione ASP.NET in grado di
  riconoscere attestazioni][23]
* [Procedura: implementare la logica di trasformazione dei token
  mediante regole][42]
* [Autorizzazione con RoleManager per le applicazioni Web ASP.NET (WIF)
  in grado di riconoscere attestazioni][43]
* Esempio di codice: utilizzo delle attestazioni in IsInRole in [Windows
  Identity Foundation SDK][44]

### Autorizzazione basata su attestazioni in applicazioni e servizi in grado di riconoscere attestazioni

In questo scenario è necessario implementare una logica di autorizzazione complessa nell'applicazione o nel servizio Web e il metodo IsInRole() non è soddisfacente per queste esigenze. Se l'approccio delle autorizzazioni si basa sui ruoli, è consigliabile implementare il controllo degli accessi basato sui ruoli illustrato nella sezione precedente.

Utilizzare ClaimsAuthorizationManager come punto di estendibilità di WIF. ClaimsAuthorizationManager consente le chiamate di controllo di accesso esterno, in modo che il codice dell'applicazione sia più pulito di aspetto e più facilmente gestibile rispetto allo scenario in cui i controlli di accesso vengono implementati al suo interno.

![](./media/SecurityRX/13_ClaimsAuthorizationManager.gif)

Per implementare questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: implementare la logica di trasformazione dei token
  mediante regole][42]
* [Procedura: implementare mediante WIF e ACS l'autorizzazione delle
  attestazioni in un'applicazione ASP.NET in grado di riconoscere
  attestazioni][22]
* Esempio di codice: autorizzazione basata sulle attestazioni in
  [Windows Identity Foundation SDK][44]

## Scenari di identità e accesso in Archiviazione di Azure

In questo scenario è necessario condividere in modo sicuro l'accesso a BLOB e contenitori di Archiviazione di Azure.

Utilizzare le firme di accesso condiviso. Per accedere all'account del servizio di archiviazione dalla propria applicazione, utilizzare il codice hash condiviso disponibile tramite il portale di Azure per la configurazione e la gestione degli account del servizio di archiviazione. Se si desidera concedere a un altro utente l'accesso ai BLOB e ai contenitori nell'account del servizio di archiviazione, utilizzare gli URL delle firme di accesso condiviso.

Prestare particolare attenzione alla sicurezza nella gestione delle informazioni, per evitare esposizioni. Tenere inoltre presente la durata delle firme di accesso condiviso.

![](./media/SecurityRX/14_WindowsAzurestorage.gif)

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Gestire l'accesso alle risorse di archiviazione di Azure][17]
* [Nuova funzionalità di archiviazione: firme di accesso condiviso][18]
* [Come utilizzare facilmente le firme di accesso condiviso][19]

## Scenari di identità e accesso nel database SQL di Azure

Il database SQL supporta solo l'autenticazione di SQL Server. L'autenticazione di Windows (sicurezza integrata) non è supportata. Gli utenti devono specificare le credenziali (account di accesso e password) ogni volta che si connettono al database SQL. Prestare particolare attenzione nella gestione di nome utente e password per evitare la divulgazione di informazioni.

![](./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif)

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Linee guida e limitazioni sulla sicurezza (database SQL)][45]
* [Procedura: connettersi al database SQL tramite sqlcmd][46]
* [Procedura: connettersi al database SQL tramite ADO.NET][47]
* [Procedura: connettersi al database SQL tramite ASP.NET][48]
* [Procedura: connettersi al database SQL tramite WCF Data Services][49]
* [Procedura: connettersi al database SQL tramite PHP][50]
* [Procedura: connettersi al database SQL tramite JDBC][51]
* [Procedura: connettersi al database SQL tramite ADO.NET Entity
  Framework][52]

## Scenari di identità e accesso nel bus di servizio di Azure

Il bus di servizio e Azure AD Access Control presentano una relazione speciale, in quanto ogni spazio dei nomi servizio del bus di servizio è abbinato a uno spazio dei nomi servizio corrispondente del controllo di accesso con lo stesso nome, con il suffisso "-sb". Il motivo di questa relazione speciale è dovuto al fatto che il bus di servizio e il controllo di accesso gestiscono la relazione di trust reciproca e i segreti di crittografia associati. Per ulteriori informazioni, fare riferimento alle risorse elencate di seguito.

![](./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif)

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Sicurezza del bus di servizio con ACS][53] (video)
* [Sicurezza del bus di servizio con ACS][54] (diapositive)
* [Autenticazione e autorizzazione di Service Bus con il Servizio di
  controllo di accesso][55]

## Scenari di identità e accesso nella cache in memoria

La cache in memoria (in precedenza Cache di Azure) si basa su Azure AD Access Control per l'autenticazione. Utilizza le chiavi condivise disponibili tramite il portale di gestione. Utilizzare queste chiavi nel codice o nei file di configurazione per l'accesso alla cache. Accertarsi di archiviare in modo sicuro le chiavi per evitare la divulgazione di informazioni.

![](./media/SecurityRX/17_WindowsAzureCacheIdentity.gif)

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Procedura: configurare un client della cache a livello di codice
  (Memorizzazione nella cache condivisa di Azure)][56]
* [Procedura: configurare un client della cache mediante il file di
  configurazione dell'applicazione (Memorizzazione nella cache
  condivisa di Azure)][57]
* [Esempi di bus di servizio e memorizzazione nella cache di Azure][58]
  (sezione di esempi di memorizzazione nella cache)

## Scenari di identità e accesso in Azure Marketplace

Prima di ottenere l'accesso a un set di dati di Azure Marketplace, sia gratuito che a pagamento, è necessario che l'utente esegua ogni volta l'autenticazione. Quando si crea un'applicazione, il processo di autenticazione deve essere incluso nel codice. Esaminare gli scenari comuni seguenti:

### Accesso al set di dati personale

In questo scenario viene creata un'applicazione che utilizza i set di dati nella sottoscrizione Marketplace. Lo sviluppatore è l'utente dell'applicazione, che può essere distribuita in Azure, in locale o in Marketplace.

Utilizzare la chiave condivisa disponibile tramite la sottoscrizione Marketplace. Questa chiave si ottiene mediante il portale Marketplace.

![](./media/SecurityRX/18_IAccessMyDataset.gif)

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Implementare l'autenticazione di base HTTP nell'applicazione per
  Marketplace][59]

### Gli utenti accedono ai set di dati dello sviluppatore

In questo scenario viene creata un'applicazione che consente agli utenti di accedere al proprio set di dati. L'applicazione può essere distribuita in Azure, in locale o in Marketplace.

Per risolvere questo scenario, utilizzare la delega OAuth. Agli utenti verrà richiesto di specificare le credenziali di Live ID/account Microsoft e dovranno completare la procedura di consenso.

![](./media/SecurityRX/19_UsersAccessMyDatasets.gif)

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Esempio di client Web OAuth][60]
* [Esempio di rich client Web OAuth][61]

### Accesso dell'applicazione all'API Marketplace

In questo scenario si crea un'applicazione che accede all'API Marketplace. Per eseguire correttamente le chiamate all'API Marketplace, è necessario eseguire l'autenticazione. L'applicazione viene distribuita in Azure Marketplace.

![](./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif)

Per i dettagli sull'implementazione dell'autenticazione, consultare il kit di pubblicazione in Marketplace.

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

* [Download del kit di pubblicazione di app][62]
* [Introduzione ad Azure Marketplace per le applicazioni][63]

## Misure di sicurezza

In questa sezione vengono illustrate le misure di sicurezza per Windows Identity Foundation e Azure AD Access Control. Utilizzare queste informazioni come elenco di controllo di base della sicurezza per queste tecnologie durante la progettazione e la distribuzione di applicazioni.

### Windows Identity Foundation

Di seguito sono riportate le principali misure di sicurezza di WIF. Le informazioni sono estratte dagli articoli [Considerazione sulla progettazione][64] e [Sicurezza di Windows Identity Foundation (WIF) per applicazioni Web ASP.NET - Minacce e contromisure][65] .

* **IssuerNameRegistry**. Specifica i servizi token di sicurezza.
  Assicurarsi che siano elencati solo servizi token di sicurezza
  attendibili.
* **cookieHandler requireSsl="true"**. Specifica se i cookie della
  sessione vengono trasferiti tramite il protocollo SSL.
* **wsFederation's requireHttps="true"**. Specifica se la comunicazione
  del protocollo di federazione con il provider di identità viene
  eseguita tramite il protocollo SSL.
* **tokenReplayDetection enabled="true"**. Specifica se la funzionalità
  di rilevamento della riproduzione di token è abilitata. Tenere
  presente che questa funzionalità crea affinità con il server, in
  quanto gestisce copie locali dei token utilizzati.
* **audienceUris**. Specifica il gruppo di destinatari previsto per il
  token. Se l'applicazione riceve un token non previsto, verrà
  rifiutato da WIF.
* **requestValidation** e **httpRuntime requestValidationType**.
  Abilitano/disabilitano la funzionalità di convalida di ASP.NET. Vedere
  le indicazioni descritte in [Windows Identity Foundation (WIF):
  individuato un valore Request.Form potenzialmente pericoloso
  proveniente dal client][66]

### Azure AD Access Control

Nella distribuzione di Azure AD Access Control tenere presenti le misure di sicurezza seguenti. Le informazioni sono estratte da [Linee guida per la sicurezza di ACS][67] e [Linee guida per la gestione di certificati e chiavi][68].

* **Scadenza dei token del servizio token di sicurezza**. Utilizzare il
  portale di gestione di Azure AD Access Control per impostare una
  scadenza appropriata dei token.
* **Convalida dei dati durante l'utilizzo della funzionalità URL
  errori**. La funzionalità URL errori di Azure AD Access Control
  richiede l'accesso anonimo alla pagina dell'app quando vengono
  inviati messaggi di errore. Presupporre che tutti i dati provenienti
  da questa pagina siano pericolosi, in quanto inviati da un'origine
  non attendibile.
* **Crittografia dei token per scenari altamente sensibili**. Per
  ridurre il rischio di divulgazione delle informazioni disponibili nei
  token, è consigliabile crittografarli.
* **Crittografia dei cookie con RSA per la distribuzione in Azure**. Per
  impostazione predefinita, WIF crittografa i cookie con DPAPI. Crea
  affinità con il server e può comportare la generazione di eccezioni
  quando la distribuzione viene eseguita in ambienti Web farm e Azure.
  In questi scenari, utilizzare invece RSA.
* **Certificati per la firma di token**. Rinnovare periodicamente i
  certificati per la firma di token per evitare situazioni di Denial of
  Service. Azure AD Access Control firma tutti i token di sicurezza
  rilasciati. Quando si creano applicazioni che utilizzano token SAML
  emessi da ACS, per la firma vengono utilizzati certificati X.509.
  Quando i certificati di firma scadono, si riceveranno messaggi di
  errore se si tenta di richiedere un token.
* **Chiavi KSK dei token**. Rinnovare periodicamente le chiavi KSK dei
  token per evitare situazioni di Denial of Service. Azure AD Access
  Control firma tutti i token di sicurezza rilasciati. Quando si creano
  applicazioni che utilizzano token SWT emessi da ACS, vengono
  utilizzate chiavi KSK simmetriche a 256 bit. Quando le chiavi KSK
  scadono, si riceveranno messaggi di errore se si tenta di richiedere
  un token.
* **Certificati di crittografia dei token**. Rinnovare periodicamente i
  certificati di crittografia dei token per evitare situazioni di Denial
  of Service. La crittografia dei token è necessaria se un'applicazione
  relying party è un servizio Web che utilizza token di prova di
  possesso tramite il protocollo WS-Trust. Negli altri casi è invece
  facoltativa. Quando i certificati di crittografia scadono, si
  riceveranno messaggi di errore se si tenta di richiedere un token.
* **Certificati di decrittografia dei token**. Rinnovare periodicamente
  i certificati di decrittografia dei token per evitare situazioni di
  Denial of Service. Azure AD Access Control può accettare token
  crittografati di provider di identità WS-Federation, ad esempio ADFS
  2.0. Per la decrittografia viene utilizzato un certificato X.509
  ospitato in Azure AD Access Control. Quando i certificati di
  crittografia scadono, si riceveranno messaggi di errore se si tenta di
  richiedere un token.
* **Credenziali per l'identità del servizio**. Rinnovare periodicamente
  le credenziali per l'identità del servizio per evitare situazioni di
  Denial of Service. Le identità del servizio utilizzano credenziali
  configurate a livello globale per lo spazio dei nomi di Azure AD
  Access Control, che consentono ad applicazioni o client di eseguire
  l'autenticazione direttamente con Azure AD Access Control e di
  ricevere un token. All'identità del servizio Azure AD Access Control
  è possibile associare tre tipi di credenziali, ossia chiave
  simmetrica, password e certificato X.509. Quando le credenziali
  scadono, si inizieranno a ricevere eccezioni.
* **Credenziali per l'account del servizio di gestione Azure AD Access
  Control**. Rinnovare periodicamente le credenziali del servizio di
  gestione per evitare situazioni di Denial of Service. Il servizio di
  gestione di Azure AD Access Control è un componente chiave che
  consente di gestire e configurare a livello di programmazione le
  impostazioni dello spazio dei nomi di Azure AD Access Control.
  All'account del servizio di gestione è possibile associare tre tipi
  di credenziali, ossia chiave simmetrica, password e certificato X.509.
  Quando le credenziali scadono, si inizieranno a ricevere eccezioni.
* **Certificati di crittografia e firma del provider di identità
  WS-Federation**. Eseguire una query per verificare la validità del
  certificato del provider di identità WS-Federation ed evitare
  situazioni di Denial of Service. Il certificato del provider di
  identità WS-Federation è disponibile tramite i relativi metadati.
  Quando si configura il provider di identità WS-Federation, ad esempio
  ADFS, il certificato di firma di WS-Federation viene configurato
  tramite i metadati di WS-Federation disponibili via URL o come file.
  Dopo aver configurato il provider di identità WS-Federation,
  utilizzare il servizio di gestione di Azure AD Access Control per
  eseguire una query e verificare la validità dei relativi certificati.
  Quando il certificato scade, si inizieranno a ricevere eccezioni.

## Hosting condiviso tramite Siti Web di Azure

Tutti gli scenari e le soluzioni illustrati in questo argomento sono validi quando l'applicazione è ospitata in Siti Web di Azure.

## Macchine virtuali di Azure

Tutti gli scenari e le soluzioni illustrati in questo argomento sono validi quando l'applicazione è ospitata in Macchine virtuali di Azure.

## Risorse

* [Identity Developer Training Kit][69]
* [Corso di formazione per sviluppatori di identità ospitato su
  MSDN][70]
* [Guida all'identità basata sulle attestazioni e al controllo di
  accesso][71]
* [Servizio di controllo di accesso][72]
* [Procedure di ACS][10]
* [Applicazione Web ASP.NET con ruolo Web di Azure protetta tramite
  Servizio di controllo di accesso v2.0][73]
* [Video su Azure AD Access Control Service (ACS) Academy][74]
* [Microsoft Security Development Lifecycle][75]
* [SDL Threat Modeling Tool 3.1.8][76]
* [Blog su sicurezza e privacy][77]
* [Security Response Center][78]
* [Security Intelligence Report][79]
* [Security Development Lifecycle][75]
* [Centro per sviluppatori sulla sicurezza (MSDN)][80]



[1]: http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx
[2]: http://msdn.microsoft.com/en-us/library/ff649461.aspx
[3]: http://msdn.microsoft.com/en-us/library/ff650760.aspx
[4]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
[5]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
[6]: http://www.microsoft.com/en-us/download/details.aspx?id=17331
[7]: http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=4451
[8]: http://msdn.microsoft.com/library/gg429786.aspx
[9]: http://msdn.microsoft.com/en-us/library/gg185920.aspx
[10]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185939.aspx
[11]: http://msdn.microsoft.com/en-us/library/ff423674.aspx
[12]: http://www.microsoft.com/en-us/download/details.aspx?id=14347
[13]: http://msdn.microsoft.com/en-us/IdentityTrainingCourse
[14]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
[15]: http://technet.microsoft.com/en-us/library/dd807033(WS.10).aspx
[16]: http://technet.microsoft.com/en-us/library/dd807050(WS.10).aspx
[17]: http://msdn.microsoft.com/en-us/library/ee393343.aspx
[18]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
[19]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
[20]: http://msdn.microsoft.com/en-us/library/gg429779.aspx
[21]: http://msdn.microsoft.com/en-us/library/gg185926.aspx
[22]: http://msdn.microsoft.com/en-us/library/gg185907.aspx
[23]: http://msdn.microsoft.com/en-us/library/gg185914.aspx
[24]: http://msdn.microsoft.com/en-us/library/gg185947.aspx
[25]: http://msdn.microsoft.com/en-us/library/gg185938.aspx
[26]: http://msdn.microsoft.com/en-us/library/gg185924.aspx
[27]: http://msdn.microsoft.com/en-us/library/hh289316.aspx
[28]: http://msdn.microsoft.com/en-us/library/gg185954.aspx
[29]: http://msdn.microsoft.com/en-us/library/gg185952.aspx
[30]: http://msdn.microsoft.com/en-us/library/gg185927.aspx
[31]: http://msdn.microsoft.com/en-us/library/gg185961.aspx
[32]: http://msdn.microsoft.com/en-us/library/gg185905.aspx
[33]: http://msdn.microsoft.com/en-us/library/hh127796.aspx
[34]: http://msdn.microsoft.com/en-us/library/gg185958.aspx
[35]: http://msdn.microsoft.com/en-us/library/hh289317.aspx
[36]: http://msdn.microsoft.com/en-us/library/gg983271.aspx
[37]: http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0
[38]: http://msdn.microsoft.com/en-us/library/gg185976.aspx
[39]: http://msdn.microsoft.com/en-us/library/gg185919.aspx
[40]: http://msdn.microsoft.com/en-us/library/gg185977.aspx
[41]: http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82
[42]: http://msdn.microsoft.com/en-us/library/gg185955.aspx
[43]: http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx
[44]: http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504
[45]: http://msdn.microsoft.com/en-us/library/windowsazure/ff394108.aspx#authentication
[46]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336280.aspx
[47]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336243.aspx
[48]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621781.aspx
[49]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621789.aspx
[50]: http://msdn.microsoft.com/en-us/library/windowsazure/ff394110.aspx
[51]: http://msdn.microsoft.com/en-us/library/windowsazure/gg715284.aspx
[52]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951633.aspx
[53]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
[54]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
[55]: http://msdn.microsoft.com/en-us/library/hh403962.aspx
[56]: http://msdn.microsoft.com/en-us/library/windowsazure/gg618003.aspx
[57]: http://msdn.microsoft.com/en-us/library/windowsazure/gg278346.aspx
[58]: http://msdn.microsoft.com/en-us/library/ee706741.aspx
[59]: http://msdn.microsoft.com/en-us/library/gg193417.aspx
[60]: http://go.microsoft.com/fwlink/?LinkId=219162
[61]: http://go.microsoft.com/fwlink/?LinkId=219163
[62]: http://go.microsoft.com/fwlink/?LinkId=221323
[63]: https://datamarket.azure.com/
[64]: http://msdn.microsoft.com/en-us/library/ee517298.aspx
[65]: http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx
[66]: http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx
[67]: http://msdn.microsoft.com/en-us/library/gg185962.aspx
[68]: http://msdn.microsoft.com/en-us/library/hh204521.aspx
[69]: http://go.microsoft.com/fwlink/?LinkId=214555
[70]: http://go.microsoft.com/fwlink/?LinkId=214561
[71]: http://go.microsoft.com/fwlink/?LinkId=214562
[72]: http://msdn.microsoft.com/en-us/library/windowsazure/gg429786.aspx
[73]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
[74]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
[75]: http://www.microsoft.com/security/sdl/default.aspx
[76]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
[77]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
[78]: http://www.microsoft.com/security/msrc/default.aspx
[79]: http://www.microsoft.com/security/sir/
[80]: http://msdn.microsoft.com/security/
[81]:./media/SecurityRX/04_WCF(SOAP)Service.gif