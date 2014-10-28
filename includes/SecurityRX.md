# Linee guida sulla sicurezza in Azure

## Sunto

Quando si sviluppano applicazioni per Azure, l'identità e l'accesso sono i principali aspetti da considerare per quanto riguarda la sicurezza. Questo argomento descrive tali aspetti nelle applicazioni cloud e come proteggerle in modo ottimale.

## Panoramica

La sicurezza di un'applicazione dipende dalla relativa superficie. Quanto più grande è la superficie esposta dall'applicazione, tanto maggiori saranno i rischi per la sicurezza. Un'applicazione che esegue, ad esempio, un processo batch senza intervento dell'utente è meno esposta dal punto di vista della sicurezza rispetto a un sito Web pubblico.

Con il passaggio al cloud, si acquisisce un certo grado di tranquillità nei confronti di
infrastruttura e reti, che vengono gestite nei data center con procedure, strumenti e tecnologie di sicurezza all'avanguardia. D'altra parte, il cloud per sua stessa natura espone una superficie di attacco maggiore dell'applicazione, che potrebbe in teoria diventare oggetto di attacchi. In generale,infatti, diversamente dai componenti in memoria, le tecnologie e i servizi cloud sono esposti come endpoint. I servizi di archiviazione, bus di servizio, database SQL (in precedenza SQL Azure) di Azure e molti altri sono accessibili tramite i rispettivi endpoint sulla rete.

Gli sviluppatori di applicazioni cloud hanno maggiori responsabilità per garantire standard elevati di sicurezza in fase di progettazione, sviluppo e manutenzione,in modo da impedire attacchi di utenti malintenzionati. Nel diagramma seguente, riportato nel [PDF di note
sulla sicurezza][PDF di note sulla sicurezza]in Azure di J.D. Meier, si può notare che l'infrastruttura rientra nell'ambito del provider di cloud, in questo caso Azure, quindi la maggior parte degli interventi di sicurezza ricade sugli sviluppatori di applicazioni:

![Protezione dell'applicazione][Protezione dell'applicazione]

L'aspetto positivo è che tutte le procedure, le tecniche e i principi di sviluppo ai fini della sicurezza sono identici anche nel caso di applicazioni cloud. Le principali aree da valutare sono le seguenti:

-   Tutto l'input deve essere convalidato per tipo, lunghezza, intervallo e modelli di stringa per evitare attacchi di tipo injection e tutti i dati che l'app restituisce devono essere correttamente purificati.
-   I dati sensibili devono essere protetti sia in locale che in transito per limitare la divulgazione di informazioni e le minacce di manomissione. 
-   Le procedure di controllo e registrazione devono essere implementate adeguatamente per limitare le minacce di non ripudio.
-   Le funzionalità di autenticazione e autorizzazione devono essere implementate con meccanismi comprovati offerti dalla piattaforma, per evitare minacce di spoofing di identità ed elevazione dei privilegi.

Per l'elenco completo di minacce, attacchi, vulnerabilità e contromisure, fare riferimento a un [foglio informativo: sull'infrastruttura di sicurezza delle applicazioni Web][foglio informativo: sull'infrastruttura di sicurezza delle applicazioni Web] e all'[indice di linee guida per la sicurezza delle applicazioni][indice di linee guida per la sicurezza delle applicazioni] pubblicati dal team patterns & practices.

Nel cloud i meccanismi di autenticazione e controllo di accesso sono molti diversi da quelli disponibili per le applicazioni locali. Per le applicazioni cloud vengono offerte molte più opzioni che possono generare confusione e, di conseguenza, implementazioni difettose. La confusione aumenta quando si tratta di definire che cos'è un'app per cloud. Ad esempio, l'applicazione potrebbe essere distribuita nel cloud, mentre il meccanismo di autenticazione potrebbe essere fornito da Active Directory. Viceversa, l'applicazione potrebbe essere distribuita in locale ma con i meccanismi di autenticazione nel cloud, ad esempio resi disponibili da Azure AD Access Control, in precedenza Servizio di controllo di accesso o ACS.

## Minacce, vulnerabilità e attacchi

Le minacce sono potenziali risultati negativi da evitare, ad esempio la divulgazione di informazioni sensibili o la mancata disponibilità di un servizio. Vengono comunemente classificate con il modello "STRIDE":

-   **S**poofing, ovvero furto di identità
-   **T**ampering, ovvero la manomissione dei dati
-   **R**epudiation, ovvero il ripudio di azioni
-   **I**nformation disclosure, ovvero la divulgazione di informazioni
-   **D**enial of Service
-   **E**levation of privileges, ovvero l'elevazione di privilegi

Le vulnerabilità sono bug introdotti nel codice dagli sviluppatori che espongono un'applicazione al rischio di attacchi. L'invio di dati sensibili in testo non crittografato, ad esempio, rende possibile la divulgazione di informazioni tramite attacchi di sniffing del traffico.

Gli attacchi sfruttano queste vulnerabilità per causare danni a un'applicazione. Il Cross Site Scripting (XSS), ad esempio, è un attacco che sfrutta l'output non purificato. Un altro esempio è l'eavesdropping in rete, ossia il tentativo di catturare le credenziali inviate in chiaro. Questi attacchi possono portare alla realizzazione di episodi di spoofing di identità. Per semplicità, le minacce, le vulnerabilità e gli attacchi possono essere paragonati a fatti negativi. I diagrammi seguenti forniscono un quadro completo dei fatti negativi relativi a un'applicazione Web distribuita in Azure (dal PDF di note sulla sicurezza in Azure [PDF di note sulla sicurezza][PDF di note sulla sicurezza]

![Minacce, vulnerabilità e attacchi][Minacce, vulnerabilità e attacchi]

Gli sviluppatori hanno il controllo delle vulnerabilità. Con un numero minore di vulnerabilità introdotte, si riducono anche le opzioni che possono essere sfruttate per gli attacchi di utenti malintenzionati.

Le vulnerabilità legate a identità e accesso aprono la strada a tutte le minacce del modello STRIDE. Un meccanismo di autenticazione implementato in modo improprio, ad esempio, può portare a episodi di spoofing di identità e, di conseguenza,a operazioni di divulgazione delle informazioni, manomissione dei dati, elevazione di privilegi o addirittura all'arresto completo del servizio. Valutare le domande seguenti che potrebbero generare potenziali vulnerabilità nell'implementazione di identità e accesso delle app per cloud:

-   Le credenziali vengono inviate in testo non crittografato tramite la rete a Servizi di
    Azure?
-   Le credenziali dei servizi di Azure vengono archiviate in testo non crittografato?
-   Le credenziali di Servizi di Azure sono conformi ai criteri
    per le password complesse?
-   La verifica delle credenziali viene effettuata tramite Azure
    anziché con meccanismi personalizzati?
-   Le sessioni di autenticazione dei servizi di Azure sono limitate o la durata dei
    token prevede una finestra temporale ragionevole?
-   Vengono verificate le autorizzazioni per ogni punto di ingresso di Azure
    dell'app per cloud distribuiti?
-   Se si verificano problemi con il meccanismo di autorizzazione, la sicurezza è comunque garantita, senza esporre
    informazioni sensibili né consentire l'accesso illimitato?

## Contromisure

Le contromisure ottimali contro gli attacchi consistono nell'usare meccanismi di identità e accesso offerti dalla piattaforma anziché implementarli internamente. Valutare le principali tecnologie di identità e accesso riportate di seguito:

**Windows Identity Foundation (WIF).** WIF è una libreria di runtime .NET inclusa in .NET Framework 4.5, disponibile anche come download distinto per .NET 3.5/4.0, che gestisce protocolli come WS-Federation e WS-Trust e token come Security Assertion Markup Language (SAML), evitando di dover scrivere codice molto complesso relativo alla sicurezza nell'applicazione. Per informazioni approfondite su WIF, vedere le risorse seguenti:

<ul>
<li><a href="http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&amp;f%5B0%5D.Value=wif&amp;f%5B1%5D.Type=Topic&amp;f%5B1%5D.Value=claims-based%20authentication">Esempi di Windows Identity Foundation 4.5</a>in MSDN Code Gallery.</li>
<li><a href="http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e">Strumenti di Windows Identity Foundation 4.5 per Visual Studio 11 Beta</a> in MSDN Code Gallery.</li>
<li><a href="http://www.microsoft.com/it-it/download/details.aspx?id=17331">Runtime di Windows Identity Foundation (.NET 3.5/4.0)</a>in MSDN.</li>
<li><a href="http://www.microsoft.com/it-it/download/details.aspx?displaylang=en&amp;id=4451">Esempi di Windows Identity Foundation 3.5/4.0 e modelli di Visual Studio 2008/2010</a> in MSDN.</li>
</ul>

**Azure AD Access Control (in precedenza ACS)**
Azure AD Access Control è un servizio cloud che rende disponibile il servizio token di sicurezza e consente la federazione con provider di identità diversi,ad esempio Active Directory aziendale o provider di identità Internet come Windows Live ID/account Microsoft, Facebook, Google, Yahoo! e Open ID 2.0. Le risorse seguenti forniscono informazioni approfondite su Azure AD Access Control:

-   [Servizio di controllo di accesso 2.0][Servizio di controllo di accesso 2.0]
-   [Scenari e soluzioni supportati da ACS][Scenari e soluzioni supportati da ACS]
-   [Procedure di ACS][Procedure di ACS]
-   [Guida all'identità basata sulle attestazioni e al controllo di accesso][Guida all'identità basata sulle attestazioni e al controllo di accesso]
-   [Identity Developer Training Kit][Identity Developer Training Kit]
-   [Corso di formazione per sviluppatori di identità ospitato su MSDN][Corso di formazione per sviluppatori di identità ospitato su MSDN]

**Active Directory Federation Services (AD FS).**Active Directory
Federation Services (AD FS) 2.0 fornisce il supporto per soluzioni di identità in grado di riconoscere
attestazioni che implicano tecnologie Windows Server e Active
Directory. AD FS 2.0 è compatibile con i protocolli WS-Trust, WS-Federation e
SAML. Per informazioni approfondite su AD
FS, vedere le risorse seguenti:

-   [Mappa di contenuti AD FS 2.0][Mappa di contenuti AD FS 2.0]
-   [Progettazione di Web SSO][Progettazione di Web SSO]
-   [Progettazione di Web SSO federativo][Progettazione di Web SSO federativo]

**Firme di accesso condiviso di Azure.** Le firme di accesso condiviso consentono di ottimizzare l'accesso a una risorsa BLOB o contenitore. Per informazioni approfondite sulle firme di accesso condiviso, vedere le risorse seguenti:

-   [Gestire l'accesso alle risorse di archiviazione di Azure][Gestire l'accesso alle risorse di archiviazione di Azure]
-   [Nuova funzionalità di archiviazione: firme di accesso condiviso][Nuova funzionalità di archiviazione: firme di accesso condiviso]
-   [Come usare facilmente le firme di accesso condiviso][Come usare facilmente le firme di accesso condiviso]

## Mappa degli scenari

Questa sezione illustra brevemente i principali scenari trattati nell'argomento. Usare queste informazioni come mappa per determinare la soluzione di identità appropriata per una specifica applicazione.

-   **Applicazione Web Form ASP.NET con autenticazione federata.** In
    questo scenario l'accesso all'app Web Form ASP.NET viene controllato mediante
    identità Internet come Live ID/account Microsoft o
    identità aziendali gestite in Windows Server Active Directory.
-   **Servizio WCF (SOAP) con autenticazione federata.**In questo
    scenario l'accesso al servizio WCF (SOAP) viene controllato mediante identità
    del servizio gestite da Azure AD Access Control.
-   **Servizio WCF (SOAP) con autenticazione federata e identità in
    Active Directory.** In questo scenario l'accesso al servizio Web WCF
    (SOAP) viene controllato mediante identità gestite da Windows
    Server Active Directory aziendale.
-   **Servizio WCF (REST) con autenticazione federata.**In questo
    scenario l'accesso al servizio WCF (REST) viene controllato mediante identità del
    servizio gestite da Azure AD Access Control.
-   **Servizio WCF (REST) con Live ID/account Microsoft, Facebook,
    Google, Yahoo!, Open ID.** In questo scenario l'accesso al
    servizio WCF (REST) viene controllato mediante identità Internet come Live ID/account
    Microsoft.
-   **App Web ASP.NET per servizio WCF REST con token SWT condiviso.** Questo
    scenario prevede un'applicazione distribuita con un'app Web ASP.NET
    front-end e un servizio REST downstream e il contesto dell'utente
    finale deve essere mantenuto attraverso livelli fisici.
-   **Autorizzazione con Controllo di accesso basato sui ruoli in servizi e applicazioni
    in grado di riconoscere attestazioni.** In questo scenario la logica delle autorizzazioni viene
    implementata nell'app in base ai ruoli.
-   **Autorizzazione basata su attestazioni in applicazioni e
    servizi in grado di riconoscere attestazioni.** In questo scenario la logica delle autorizzazioni viene
    implementata nell'app in base a complesse regole di autorizzazione.
-   **Scenari di identità e accesso nel servizio di archiviazione di Azure.**In
    questo scenario è necessario condividere in modo sicuro l'accesso a BLOB e contenitori di archiviazione di
    Azure.
-   **Scenari di identità e accesso nel database SQL di Azure.**Il database
    SQL supporta solo l'autenticazione di SQL Server. L'autenticazione
    di Windows (sicurezza integrata) non è supportata. Gli utenti devono
    specificare le credenziali (account di accesso e password) ogni volta che si connettono al database
    SQL.
-   **Scenari di identità e accesso nel bus di servizio di Azure.**In questo
    scenario è necessario accedere in modo sicuro alle code del bus di servizio di Azure.
-   **Scenari di identità e accesso nella cache in memoria.**In questo scenario
    è necessario accedere in modo sicuro ai dati gestiti dalla cache in memoria.
-   **Scenari di identità e accesso in Azure Marketplace.**In questo
    scenario è necessario accedere in modo sicuro ai set di dati di Azure
    Marketplace.

## Scenari di identità e accesso in Azure

Questa sezione illustra i comuni scenari di identità e accesso per diverse architetture di applicazioni. Usare la mappa degli scenari per un rapido orientamento.

### Applicazione Web Form ASP.NET con autenticazione federata

In questo scenario di architettura l'applicazione Web può essere distribuita in Azure o in locale. L'applicazione richiede l'autenticazione degli utenti tramite Active Directory aziendale o provider di identità Internet.

Per risolvere questi scenari, usare Azure AD Access Control e Windows Identity Foundation.

![Azure AD Access Control][Azure AD Access Control]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

<ul>
<li><a href="http://msdn.microsoft.com/it-it/library/gg429779.aspx">Procedura: Creare mediante ACS la prima applicazione ASP.NET in grado di riconoscere attestazioni</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185926.aspx">Procedura: Ospitare pagine di accesso nell'applicazione Web ASP.NET</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185907.aspx">Procedura: Implementare mediante WIF e ACS l'autorizzazione delle attestazioni in un'applicazione ASP.NET in grado di riconoscere attestazioni</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185914.aspx">Procedura: Implementare mediante WIF e ACS il controllo di accesso basato sui ruoli (RBAC) in un'applicazione ASP.NET in grado di riconoscere attestazioni</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185947.aspx">Procedura: Configurare i criteri di attendibilit&agrave; tra ACS e le applicazioni Web ASP.NET usando certificati X.509</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185938.aspx">Esempio di codice: Web Form ASP.NET semplice</a></li>
</ul>

### Servizio WCF (SOAP) con identità del servizio

In questo scenario di architettura per le applicazioni il servizio WCF (SOAP) potrebbe essere distribuito in Azure o in locale. È accessibile come servizio downstream da un'applicazione Web o anche da un altro servizio Web. È necessario controllare l'accesso mediante un'identità specifica dell'applicazione. Questo scenario si può paragonare al tipo di account del pool di app usato in IIS. Anche se la tecnologia è diversa, gli approcci sono simili perché il servizio è accessibile tramite un account di ambito di applicazione anziché un account di utente finale.

Usare la funzionalità di identità del servizio di Azure AD Access Control. Si tratta di una funzionalità simile all'account del pool di app IIS usato per la distribuzione di app in Windows Server e IIS. Configurare Azure AD Access Control in modo che vengano emessi token SAML che verranno gestiti da WIF nel servizio WCF (SOAP).

![Servizio WCF (SOAP)][Servizio WCF (SOAP)]

<ul>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185924.aspx">Procedura: Aggiungere identit&agrave; del servizio con un certificato X.509, una password o una chiave simmetrica</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/hh289316.aspx">Procedura: Eseguire l'autenticazione con un certificato client per l'accesso a un servizio WCF protetto da ACS</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185954.aspx">Procedura: Eseguire l'autenticazione con un nome utente e una password per l'accesso a un servizio WCF protetto da ACS</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185952.aspx">Esempio di codice: autenticazione del certificato per WCF</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185927.aspx">Esempio di codice: autenticazione del nome utente per WCF</a></li>
</ul>

### Servizio WCF (SOAP) con autenticazione federata e identità in Active Directory

In questo scenario di architettura per le applicazioni il servizio WCF (SOAP) potrebbe essere distribuito in Azure o in locale. È necessario controllare l'accesso tramite un'identità gestita da Windows Server Active Directory (AD) aziendale.

Usare Azure AD Access Control configurato per la federazione con Windows Server ADFS. In questo caso non è necessario configurare l'identità del servizio con Azure AD Access Control. L'agente che deve accedere al servizio WCF (SOAP) fornisce le credenziali ad ADFS e, dopo la corretta autenticazione, riceve il token emesso da ADFS. Il token viene quindi inviato ad Azure AD Access Control ed emesso nuovamente per l'agente, che lo usa per inviare la richiesta al servizio WCF (SOAP).

![Servizio WCF (SOAP) con AD][Servizio WCF (SOAP) con AD]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

<ul>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185924.aspx">Procedura: Aggiungere identit&agrave; del servizio con un certificato X.509, una password o una chiave simmetrica</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185961.aspx">Procedura: Configurare ADFS 2.0 come provider di identit&agrave;</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/gg185905.aspx">Procedura: Usare il servizio di gestione per configurare ADFS 2.0 come provider di identit&agrave; enterprise</a></li>
<li><a href="http://msdn.microsoft.com/it-it/library/hh127796.aspx">Esempio di codice: autenticazione federata WCF con ADFS 2.0</a></li>
</ul>

### Servizio WCF (REST) con identità del servizio

In questo scenario il servizio WCF (REST) potrebbe essere distribuito in Azure o in locale. È accessibile come servizio downstream da un'applicazione Web o anche da un altro servizio Web. È necessario controllare l'accesso mediante un'identità specifica dell'applicazione. Questo scenario si può paragonare al tipo di account del pool di app usato in IIS. Anche se la tecnologia è diversa, gli approcci sono simili perché il servizio è accessibile tramite un account di ambito di applicazione anziché un account di utente finale.

Usare la funzionalità di identità del servizio di Azure AD Access Control. Configurare Azure AD Access Control per l'emissione di token SWT (Simple Web Token). Per gestire il token SWT sul lato del servizio REST, è possibile implementare un gestore di token personalizzato e collegarlo alla pipeline WIF oppure è possibile analizzarlo "manualmente" senza usare l'infrastruttura WIF.

Consultare il diagramma seguente (WIF è facoltativo):

![Servizio REST][Servizio REST]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

-   [Procedura: Configurare i criteri di attendibilità tra ACS e il servizio WCF usando chiavi simmetriche][Procedura: Configurare i criteri di attendibilità tra ACS e il servizio WCF usando chiavi simmetriche]
-   [Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS][Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS]
-   [Esempio di codice: servizio Web ASP.NET][Esempio di codice: servizio Web ASP.NET]
-   [Esempio di codice: applicazione per Windows Phone 7][Esempio di codice: servizio Web ASP.NET]
-   [WCF REST con token WCF rilasciato da Servizio di controllo di accesso di Azure (ACS)][WCF REST con token WCF rilasciato da Servizio di controllo di accesso di Azure (ACS)]

### Servizio WCF (REST) con Live ID/account Microsoft, Facebook, Google, Yahoo!, Open ID

In questo scenario il servizio WCF (REST) può essere distribuito in Azure o in locale. È necessario controllare l'accesso mediante un'identità Internet pubblica, ad esempio Live ID/account Microsoft o Facebook.

Usare Azure AD Access Control per l'emissione di token SWT. Per gestire il token SWT sul lato del servizio REST, è possibile implementare un gestore di token personalizzato e collegarlo alla pipeline WIF oppure è possibile analizzarlo "manualmente" senza usare l'infrastruttura WIF.

È importante notare che, per implementare questo scenario, è necessario che l'applicazione usi il controllo Web browser per raccogliere le credenziali dell'utente finale. Di conseguenza, questa opzione non è praticabile per gli scenari in cui l'accesso al servizio REST viene effettuato da un'app Web ASP.NET. È indicata solo per gli scenari in cui il servizio REST è accessibile dall'applicazione client dell'utente, ad esempio un'app di Windows Phone 7 o un rich client. Il motivo principale per la visualizzazione del controllo Web browser è che le identità Internet non supportano in modo nativo scenari di profili attivi, ossia di servizi Web, ma supportano principalmente scenari di profili passivi, ossia app Web, che si basano su reindirizzamenti del browser. È in casi come questo che il controllo Web browser risulta utile.

Consultare il diagramma seguente (WIF è facoltativo, quindi non viene mostrato):

![WIF è facoltativo][WIF è facoltativo]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

-   [Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS][Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS]
-   [Procedura: Configurare Google come provider di identità][Procedura: Configurare Google come provider di identità]
-   [Procedura: Configurare Facebook come provider di identità][Procedura: Configurare Facebook come provider di identità]
-   [Procedura: Configurare Yahoo! come provider di identità][Procedura: Configurare Yahoo! come provider di identità]
-   [Esempio di codice: applicazione per Windows Phone 7][Esempio di codice: servizio Web ASP.NET]
-   [WCF REST con token WCF rilasciato da Servizio di controllo di accesso di Azure (ACS)][WCF REST con token WCF rilasciato da Servizio di controllo di accesso di Azure (ACS)]

### App Web ASP.NET per servizio WCF REST con token SWT condiviso

Questo scenario prevede un'applicazione distribuita con un'app Web ASP.NET front-end e un servizio REST downstream e il contesto dell'utente deve essere mantenuto attraverso livelli fisici. Ciò si rende talvolta necessario quando si implementa la registrazione o la logica delle autorizzazioni in base all'identità dell'utente finale nel servizio REST downstream.

Configurare Azure AD Access Control per l'emissione di token SWT. Il token SWT viene inviato all'app Web ASP.NET front-end e quindi condiviso con il servizio REST downstream. In questo caso, in Azure AD Access Control è configurata una sola relying party. Tuttavia, è necessario valutare diversi aspetti:

-   Poiché WIF non fornisce un gestore di token SWT predefinito, è necessario implementarne uno personalizzato da usare con l'app Web ASP.NET. È consigliabile sfruttare il supporto di WIF per il protocollo WS-Federation, che si basa su reindirizzamenti del browser, anziché scegliere implementazioni autonome.
-   Quando si implementa un gestore di token SWT personalizzato, verificare che il token di bootstrap venga considerato e mantenuto. In caso contrario, non sarà possibile condividerlo e inviarlo al servizio REST downstream.
-   Non è necessario usare WIF in un servizio REST, ma è possibile analizzare il token "manualmente", poiché in questo caso i reindirizzamenti non dovranno essere gestiti.

![Applicazione Web ASP.NET][Applicazione Web ASP.NET]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

-   [Procedura: Configurare Google come provider di identità][Procedura: Configurare Google come provider di identità]
-   [Procedura: Configurare Facebook come provider di identità][Procedura: Configurare Facebook come provider di identità]
-   [Procedura: Configurare Yahoo! come provider di identità][Procedura: Configurare Yahoo! come provider di identità]
-   [App Web ASP.NET per delega del servizio WCF REST con token SWT condiviso][App Web ASP.NET per delega del servizio WCF REST con token SWT condiviso]

### Controllo di accesso basato sui ruoli in servizi e applicazioni in grado di riconoscere attestazioni.

In questo scenario è necessario implementare le autorizzazioni nell'applicazione o nel servizio Web in base ai ruoli utente. L'utente con i ruoli richiesti ottiene l'accesso, che viene invece negato a quelli che non ne dispongono. In breve, l'applicazione deve rispondere a una semplice domanda: il ruolo dell'utente è X?

È possibile risolvere questo scenario in diversi modi, ossia usando Azure AD Access Control, WIF ClaimsAuthenticationManager, mapping samlSecurityTokenRequirement o RoleManager personalizzato.

WIF viene usato in tutti i casi e supporta il metodo IPrincipal.IsInRole("MyRole"). Nella maggior parte dei casi, la chiave consiste nell'assicurarsi che sia disponibile un'attestazione di tipo ruolo con l'URI <http://schemas.microsoft.com/ws/2008/06/identity/claims/role> nel token, in modo che WIF possa verificare l'appartenenza ai ruoli quando viene chiamato il metodo IsInRole.

**Azure AD Access Control**. In questa implementazione viene usato il motore di regole di trasformazione delle attestazioni di Azure AD Access Control. Con le regole di questo motore, è possibile trasformare qualsiasi attestazione in ingresso in un'attestazione di tipo ruolo, in modo che quando il token rileva l'applicazione o un servizio, WIF possa analizzare l'attestazione di tipo ruolo per garantire la corretta esecuzione della chiamata al metodo IsInRole.

![][]

**WIF ClaimsAuthenticationManager**. In questa implementazione usare ClaimsAuthenticationManager come punto di estendibilità di WIF. Con questo approccio è possibile trasformare qualsiasi attestazione arbitraria in ingresso in un'attestazione di tipo ruolo nell'applicazione. La complessità della trasformazione è limitata solo dal codice scritto.

![][1]

**Mapping samlSecurityTokenRequriement**. In questa implementazione si usa la configurazione samlSecurityTokenRequirement in web.config per indicare a WIF quali tipi di attestazione si comportano da attestazioni di tipo ruolo. Se ad esempio il token esegue un'attestazione di tipo gruppo, è possibile eseguirne il mapping a un'attestazione di tipo ruolo. Con questo approccio è possibile realizzare solo mapping semplici.

![][2]

**Oggetto RoleManager personalizzato.** In questa implementazione si usa l'oggetto RoleManger personalizzato. WIF viene usato per esaminare le attestazioni in ingresso quando si implementano metodi di interfaccia RoleManager personalizzati, ad esempio GetAllRoles().

![][3]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

-   [Procedura: Implementare mediante WIF e ACS il controllo di accesso basato sui ruoli (RBAC) in un'applicazione ASP.NET in grado di riconoscere attestazioni][Procedura: Implementare mediante WIF e ACS il controllo di accesso basato sui ruoli (RBAC) in un'applicazione ASP.NET in grado di riconoscere attestazioni]
-   [Procedura: Implementare la logica di trasformazione dei token mediante regole][Procedura: Implementare la logica di trasformazione dei token mediante regole]
-   [Autorizzazione con RoleManager per le applicazioni Web ASP.NET (WIF) in grado di riconoscere attestazioni][Autorizzazione con RoleManager per le applicazioni Web ASP.NET (WIF) in grado di riconoscere attestazioni]
-   Esempio di codice: uso delle attestazioni in IsInRole in [Windows Identity Foundation SDK][Windows Identity Foundation SDK]

### Autorizzazione basata su attestazioni in applicazioni e servizi in grado di riconoscere attestazioni

In questo scenario è necessario implementare una logica di autorizzazione complessa nell'applicazione o nel servizio Web e il metodo IsInRole() non è soddisfacente per queste esigenze. Se l'approccio delle autorizzazioni si basa sui ruoli, è consigliabile implementare il controllo di accesso basato sui ruoli illustrato nella sezione precedente.

Usare ClaimsAuthorizationManager come punto di estendibilità di WIF. ClaimsAuthorizationManager consente le chiamate di controllo di accesso esterno, in modo che l'aspetto del codice dell'applicazione sia più chiaro e più facilmente gestibile rispetto allo scenario in cui i controlli di accesso vengono implementati al suo interno.

![][4]

Per implementare questo scenario, fare riferimento alle risorse seguenti:

-   [Procedura: Implementare la logica di trasformazione dei token mediante regole][Procedura: Implementare la logica di trasformazione dei token mediante regole]
-   [Procedura: Implementare mediante WIF e ACS l'autorizzazione delle attestazioni in un'applicazione ASP.NET in grado di riconoscere attestazioni][Procedura: Implementare mediante WIF e ACS l'autorizzazione delle attestazioni in un'applicazione ASP.NET in grado di riconoscere attestazioni]
-   Esempio di codice: autorizzazione basata sulle attestazioni in [Windows Identity Foundation SDK][Windows Identity Foundation SDK]

## Scenari di identità e accesso in Archiviazione di Azure

In questo scenario è necessario condividere in modo sicuro l'accesso a BLOB e contenitori di Archiviazione di Azure.

Usare le firme di accesso condiviso. Per accedere all'account del servizio di archiviazione dalla propria applicazione, usare il codice hash condiviso disponibile tramite il portale di Azure per la configurazione e la gestione degli account del servizio di archiviazione. Se si desidera concedere a un altro utente l'accesso ai BLOB e ai contenitori nell'account del servizio di archiviazione, usare gli URL delle firme di accesso condiviso.

Prestare particolare attenzione alla sicurezza nella gestione delle informazioni, per evitarne l'esposizione. Tenere inoltre presente la durata delle firme di accesso condiviso.

![][5]

Per risolvere questo scenario, fare riferimento alle risorse seguenti

-   [Gestire l'accesso alle risorse di archiviazione di Azure][Gestire l'accesso alle risorse di archiviazione di Azure]
-   [Nuova funzionalità di archiviazione: firme di accesso condiviso][Nuova funzionalità di archiviazione: firme di accesso condiviso]
-   [Come usare facilmente le firme di accesso condiviso][Come usare facilmente le firme di accesso condiviso]

## Scenari di identità e accesso nel database SQL di Azure

Il database SQL supporta solo l'autenticazione di SQL Server. L'autenticazione di Windows (sicurezza integrata) non è supportata. Gli utenti devono specificare le credenziali (account di accesso e password) ogni volta che si connettono a un database SQL. Prestare particolare attenzione nella gestione di nome utente e password per evitare la divulgazione di informazioni.

![][6]

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

-   [Linee guida e limitazioni sulla sicurezza (database SQL)][Linee guida e limitazioni sulla sicurezza (database SQL)]
-   [Procedura: Connettersi al database SQL tramite sqlcmd][Procedura: Connettersi al database SQL tramite sqlcmd]
-   [Procedura: Connettersi al database SQL tramite ADO.NET][Procedura: Connettersi al database SQL tramite ADO.NET]
-   [Procedura: Connettersi al database SQL tramite ASP.NET][Procedura: Connettersi al database SQL tramite ASP.NET]
-   [Procedura: Connettersi al database SQL tramite WCF Data Services][Procedura: Connettersi al database SQL tramite WCF Data Services]
-   [Procedura: Connettersi al database SQL tramite PHP][Procedura: Connettersi al database SQL tramite PHP]
-   [Procedura: Connettersi al database SQL tramite JDBC][Procedura: Connettersi al database SQL tramite JDBC]
-   [Procedura: Connettersi al database SQL tramite ADO.NET Entity Framework][Procedura: Connettersi al database SQL tramite ADO.NET Entity Framework]

## Scenari di identità e accesso nel bus di servizio di Azure

Il bus di servizio e Azure AD Access Control presentano una relazione speciale, in quanto ogni spazio dei nomi servizio del bus di servizio è abbinato a uno spazio dei nomi servizio corrispondente del controllo di accesso con lo stesso nome, con il suffisso "-sb". Il motivo di questa relazione speciale è dovuto al fatto che il bus di servizio e il controllo di accesso gestiscono la relazione di trust reciproca e i segreti di crittografia associati. Per altre informazioni, fare riferimento alle risorse elencate di seguito.

![][7]

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

-   [Sicurezza del bus di servizio con ACS][Sicurezza del bus di servizio con ACS] (video)
-   [Sicurezza del bus di servizio con ACS][8] (diapositive)
-   [Autenticazione e autorizzazione di Service Bus con il Servizio di controllo di accesso][Autenticazione e autorizzazione di Service Bus con il Servizio di controllo di accesso]

## Scenari di identità e accesso nella cache in memoria

La cache in memoria (in precedenza Cache di Azure) si basa su Azure AD Access Control per l'autenticazione. Usa le chiavi condivise disponibili tramite il portale di gestione. Usare queste chiavi nel codice o nei file di configurazione per l'accesso alla cache. Accertarsi di archiviare in modo sicuro le chiavi per evitare la divulgazione di informazioni.

![][9]

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

-   [Procedura: Configurare un client della cache a livello di codice(Memorizzazione nella cache condivisa di Azure)][Procedura: Configurare un client della cache a livello di codice(Memorizzazione nella cache condivisa di Azure)]
-   [Procedura: Configurare un client della cache mediante il file di configurazione dell'applicazione(Memorizzazione nella cache condivisa di Azure)][Procedura: Configurare un client della cache mediante il file di configurazione dell'applicazione(Memorizzazione nella cache condivisa di Azure)]
-   [Esempi di bus di servizio e memorizzazione nella cache di Azure][Esempi di bus di servizio e memorizzazione nella cache di Azure] (sezione di esempi di memorizzazione nella cache)

## Scenari di identità e accesso in Azure Marketplace

Prima di ottenere l'accesso a un set di dati di Azure Marketplace, sia gratuito che a pagamento, è necessario che l'utente esegua ogni volta l'autenticazione. Quando si crea un'applicazione, il processo di autenticazione deve essere incluso nel codice. Esaminare gli scenari comuni seguenti:

### Accesso al set di dati personale

In questo scenario viene creata un'applicazione che usa i set di dati nella sottoscrizione Marketplace. Lo sviluppatore è l'utente dell'applicazione, che può essere distribuita in Azure, in locale o in Marketplace.

Usare la chiave condivisa disponibile tramite la sottoscrizione Marketplace. Questa chiave si ottiene mediante il portale Marketplace.

![][10]

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

-   [Implementare l'autenticazione di base HTTP nell'applicazione per Marketplace][Implementare l'autenticazione di base HTTP nell'applicazione per Marketplace]

### Gli utenti accedono ai set di dati dello sviluppatore

In questo scenario viene creata un'applicazione che consente agli utenti di accedere al proprio set di dati. L'applicazione può essere distribuita in Azure, in locale o in Marketplace.

Per risolvere questo scenario, usare la delega OAuth. Agli utenti verrà richiesto di specificare le credenziali di Live ID/account Microsoft e dovranno completare la procedura di consenso.

![][11]

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

-   [Esempio di client Web OAuth][Esempio di client Web OAuth]
-   [Esempio di rich client Web OAuth][Esempio di rich client Web OAuth]

### Accesso dell'applicazione all'API Marketplace

In questo scenario si crea un'applicazione che accede all'API Marketplace. Per eseguire correttamente le chiamate all'API Marketplace, è necessario eseguire l'autenticazione. L'applicazione viene distribuita in Azure Marketplace.

![][12]

Per i dettagli sull'implementazione dell'autenticazione, consultare il kit di pubblicazione in Marketplace.

Per risolvere questo scenario, fare riferimento alle risorse seguenti:

-   [Download del kit di pubblicazione di app][Download del kit di pubblicazione di app]
-   [Introduzione ad Azure Marketplace per le applicazioni][Introduzione ad Azure Marketplace per le applicazioni]

## Misure di sicurezza

Questa sezione illustra le misure di sicurezza per Windows Identity Foundation e Azure AD Access Control. Usare queste informazioni come elenco di controllo di base della sicurezza per queste tecnologie durante la progettazione e la distribuzione di applicazioni.

### Windows Identity Foundation

Di seguito sono riportate le principali misure di sicurezza di WIF. Le informazioni sono estratte dagli articoli [Considerazione sulla progettazione WIF][Considerazione sulla progettazione WIF] e [Sicurezza di Windows Identity Foundation (WIF) per applicazioni Web ASP.NET - Minacce e contromisure][Sicurezza di Windows Identity Foundation (WIF) per applicazioni Web ASP.NET - Minacce e contromisure].

-   **IssuerNameRegistry**. Specifica i servizi token di
    sicurezza. Assicurarsi che siano elencati solo servizi token di sicurezza attendibili.
-   **cookieHandler requireSsl="true"**. Specifica se i cookie della
    sessione vengono trasferiti tramite il protocollo SSL.
-   **wsFederation's requireHttps="true"**. Specifica se la comunicazione del protocollo
    di federazione con il provider di identità viene eseguita tramite il
    protocollo SSL.
-   **tokenReplayDetection enabled="true"**. Specifica se la funzionalità di rilevamento della riproduzione
    di token è abilitata. Tenere presente che questa funzionalità
    crea affinità con il server, in quanto gestisce copie locali dei token usati.
-   **audienceUris**. Specifica il gruppo di destinatari previsto per il token. Se
    l'applicazione riceve un token non previsto,
    verrà rifiutato da WIF.
-   **requestValidation** e **httpRuntime requestValidationType**.
    Abilitano/disabilitano la funzionalità di convalida di ASP.NET. Vedere le linee guida
    descritte in [Windows Identity Foundation (WIF): individuato un valore
    Request.Form potenzialmente pericoloso proveniente dal client.][Windows Identity Foundation (WIF): individuato un valore
    Request.Form potenzialmente pericoloso proveniente dal client.]

### Azure AD Access Control

Nella distribuzione di Azure AD Access Control tenere presenti le misure di sicurezza seguenti. Le informazioni sono estratte da [Linee guida per la sicurezza di ACS][Linee guida per la sicurezza di ACS] e [Linee guida per la gestione di certificati e chiavi][Linee guida per la gestione di certificati e chiavi].

-   **Scadenza dei token del servizio token di sicurezza**. Usare il portale di gestione
    di Azure AD Access Control per impostare una scadenza appropriata dei token.
-   **Convalida dei dati durante l'uso della funzionalità URL errori**. La funzionalità URL errori di Azure
    AD Access Control richiede l'accesso anonimo alla
    pagina dell'app quando vengono inviati messaggi di errore. Presupporre che tutti i dati provenienti da
    questa pagina siano pericolosi, in quanto inviati da un'origine non attendibile.
-   **Crittografia dei token per scenari altamente sensibili**. Per ridurre il
    rischio di divulgazione delle informazioni disponibili nei token, è
    consigliabile crittografarli.
-   **Crittografia dei cookie con RSA per la distribuzione in Azure**.
    Per impostazione predefinita, WIF crittografa i cookie con DPAPI. Crea affinità
    con il server e può comportare la generazione di eccezioni quando la distribuzione viene eseguita in ambienti Web farm e
    Azure. In questi scenari, usare invece
    RSA.
-   **Certificati per la firma di token**. Rinnovare periodicamente i certificati per la firma di token
    per evitare situazioni di Denial of Service. Azure AD Access
    Control firma tutti i token di sicurezza rilasciati. Quando si creano applicazioni che usano token
    SAML rilasciati da ACS, per la firma vengono
    usati certificati X.509. Quando i certificati di firma scadono, si riceveranno
    messaggi di errore se si tenta di richiedere un token.
-   **Chiavi KSK dei token**. Rinnovare periodicamente le chiavi KSK dei token per
    evitare situazioni di Denial of Service. Azure AD Access Control firma tutti
    i token di sicurezza rilasciati. Quando si creano applicazioni che usano token SWT rilasciati da
    ACS, vengono usate
    chiavi KSK simmetriche a 256 bit. Quando le chiavi KSK scadono, si riceveranno messaggi di errore se si tenta di
    richiedere un token.
-   **Certificati di crittografia dei token**. Rinnovare periodicamente i certificati di
    crittografia dei token per evitare situazioni di Denial of Service. La crittografia
    dei token è necessaria se un'applicazione relying party è un servizio
    Web che usa token di prova di possesso tramite il protocollo WS-Trust.
    Negli altri casi è invece facoltativa. Quando i certificati
    di crittografia scadono, si riceveranno messaggi di errore se si tenta di richiedere un
    token.
-   **Certificati di decrittografia dei token**. Rinnovare periodicamente i certificati
    di decrittografia dei token per evitare situazioni di Denial of Service. Azure
    AD Access Control può accettare token crittografati di provider di identità
    WS-Federation, ad esempio ADFS 2.0. Per la decrittografia viene usato un certificato X.509
    ospitato in Azure AD Access Control.
    Quando i certificati di crittografia scadono, si riceveranno messaggi di errore se si
    tenta di richiedere un token.
-   **Credenziali per l'identità del servizio**. Rinnovare periodicamente le credenziali
    per l'identità del servizio per evitare situazioni di Denial of Service. Le identità del servizio usano
    credenziali configurate a livello globale per lo spazio dei nomi di Azure AD
    Access Control, che consentono ad applicazioni o client di
    eseguire l'autenticazione direttamente con Azure AD Access Control e
    di ricevere un token. All'identità del servizio Azure AD
    Access Control è possibile associare tre tipi di credenziali, ossia chiave
    simmetrica, password e certificato X.509. Quando le credenziali scadono, si inizieranno a
    ricevere eccezioni.
-   **Credenziali per l'account del servizio di gestione
    Azure AD Access Control**. Rinnovare periodicamente le credenziali del servizio di gestione per
    evitare situazioni di Denial of Service. Il servizio di gestione di
    Azure AD Access Control è un componente chiave che consente di gestire e configurare a
    livello di codice le impostazioni dello spazio dei nomi di
    Azure AD Access Control. All'account del servizio di gestione è possibile associare tre
    tipi di credenziali, ossia chiave
    simmetrica, password e certificato X.509. Quando le credenziali scadono, si
    inizieranno a ricevere eccezioni.
-   **Certificati di crittografia e firma del provider di identità
    WS-Federation**. Eseguire una query per verificare la validità del certificato del provider
    di identità WS-Federation ed evitare situazioni di Denial of Service. Il certificato del provider di identità
    WS-Federation è disponibile tramite i relativi metadati.
    Quando si configura il provider di identità WS-Federation, ad esempio ADFS, il
    certificato di firma di WS-Federation viene configurato tramite
    i metadati di WS-Federation disponibili via URL o come file. Dopo aver configurato il
    provider di identità WS-Federation, usare il servizio di gestione di Azure AD
    Access Control per eseguire una query e verificare la validità
    dei relativi certificati. Quando il certificato scade, si inizieranno a ricevere
    eccezioni.

## Hosting condiviso tramite Siti Web di Azure

Tutti gli scenari e le soluzioni illustrati in questo argomento sono validi quando l'applicazione è ospitata in Siti Web di Azure.

## Macchine virtuali di Azure

Tutti gli scenari e le soluzioni illustrati in questo argomento sono validi quando l'applicazione è ospitata in Macchine virtuali di Azure.

## Risorse

-   [Identity Developer Training Kit][13]
-   [Corso di formazione per sviluppatori di identità ospitato su MSDN][14]
-   [Guida all'identità basata sulle attestazioni e al controllo di accesso][15]
-   [Servizio di controllo di accesso][Servizio di controllo di accesso]
-   [Procedure di ACS][Procedure di ACS]
-   [Applicazione Web ASP.NET con ruolo Web di Azure protetta tramite Servizio di controllo di accesso v2.0][Applicazione Web ASP.NET con ruolo Web di Azure protetta tramite Servizio di controllo di accesso v2.0]
-   [Video su Azure AD Access Control Service (ACS) Academy][Video su Azure AD Access Control Service (ACS) Academy]
-   [Microsoft Security Development Lifecycle][Microsoft Security Development Lifecycle]
-   [SDL Threat Modeling Tool 3.1.8][SDL Threat Modeling Tool 3.1.8]
-   [Blog su sicurezza e privacy][Blog su sicurezza e privacy]
-   [Security Response Center][Security Response Center]
-   [Security Intelligence Report][Security Intelligence Report]
-   [Security Development Lifecycle][Microsoft Security Development Lifecycle]
-   [Centro per sviluppatori sulla sicurezza (MSDN)][Centro per sviluppatori sulla sicurezza (MSDN)]

  [PDF di note sulla sicurezza]: http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx
  [Protezione dell'applicazione]: ./media/SecurityRX/01_SecuringTheApplication.gif
  [foglio informativo: sull'infrastruttura di sicurezza delle applicazioni Web]: http://msdn.microsoft.com/it-it/library/ff649461.aspx
  [indice di linee guida per la sicurezza delle applicazioni]: http://msdn.microsoft.com/it-it/library/ff650760.aspx
  [Minacce, vulnerabilità e attacchi]: ./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
  [Esempi di Windows Identity Foundation 4.5]: http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication
  [Strumenti di Windows Identity Foundation 4.5 per Visual Studio 11 Beta]: http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e
  [Runtime di Windows Identity Foundation (.NET 3.5/4.0)]: http://www.microsoft.com/it-it/download/details.aspx?id=17331
  [Esempi di Windows Identity Foundation 3.5/4.0 e modelli di Visual Studio 2008/2010]: http://www.microsoft.com/it-it/download/details.aspx?displaylang=en&id=4451
  [Servizio di controllo di accesso 2.0]: http://msdn.microsoft.com/library/gg429786.aspx
  [Scenari e soluzioni supportati da ACS]: http://msdn.microsoft.com/it-it/library/gg185920.aspx
  [Procedure di ACS]: http://msdn.microsoft.com/it-it/library/windowsazure/gg185939.aspx
  [Guida all'identità basata sulle attestazioni e al controllo di accesso]: http://msdn.microsoft.com/it-it/library/ff423674.aspx
  [Identity Developer Training Kit]: http://www.microsoft.com/it-it/download/details.aspx?id=14347
  [Corso di formazione per sviluppatori di identità ospitato su MSDN]: http://msdn.microsoft.com/it-it/IdentityTrainingCourse
  [Mappa di contenuti AD FS 2.0]: http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx
  [Progettazione di Web SSO]: http://technet.microsoft.com/it-it/library/dd807033(WS.10).aspx
  [Progettazione di Web SSO federativo]: http://technet.microsoft.com/it-it/library/dd807050(WS.10).aspx
  [Gestire l'accesso alle risorse di archiviazione di Azure]: http://msdn.microsoft.com/it-it/library/ee393343.aspx
  [Nuova funzionalità di archiviazione: firme di accesso condiviso]: http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures
  [Come usare facilmente le firme di accesso condiviso]: http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days
  [Azure AD Access Control]: ./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
  [Procedura: Creare mediante ACS la prima applicazione ASP.NET in grado di riconoscere attestazioni]: http://msdn.microsoft.com/it-it/library/gg429779.aspx
  [Procedura: Ospitare pagine di accesso nell'applicazione Web ASP.NET]: http://msdn.microsoft.com/it-it/library/gg185926.aspx
  [Procedura: Implementare mediante WIF e ACS l'autorizzazione delle attestazioni in un'applicazione ASP.NET in grado di riconoscere attestazioni]: http://msdn.microsoft.com/it-it/library/gg185907.aspx
  [Procedura: Implementare mediante WIF e ACS il controllo di accesso basato sui ruoli (RBAC) in un'applicazione ASP.NET in grado di riconoscere attestazioni]: http://msdn.microsoft.com/it-it/library/gg185914.aspx
  [Procedura: Configurare i criteri di attendibilità tra ACS e le applicazioni Web ASP.NET usando certificati X.509]: http://msdn.microsoft.com/it-it/library/gg185947.aspx
  [Esempio di codice: Web Form ASP.NET semplice]: http://msdn.microsoft.com/it-it/library/gg185938.aspx
  [Servizio WCF (SOAP)]: ./media/SecurityRX/04_WCF(SOAP)Service.gif
  [Procedura: Aggiungere identità del servizio con un certificato X.509, una password o una chiave simmetrica]: http://msdn.microsoft.com/it-it/library/gg185924.aspx
  [Procedura: Eseguire l'autenticazione con un certificato client per l'accesso a un servizio WCF protetto da ACS]: http://msdn.microsoft.com/it-it/library/hh289316.aspx
  [Procedura: Eseguire l'autenticazione con un nome utente e una password per l'accesso a un servizio WCF protetto da ACS]: http://msdn.microsoft.com/it-it/library/gg185954.aspx
  [Esempio di codice: autenticazione del certificato per WCF]: http://msdn.microsoft.com/it-it/library/gg185952.aspx
  [Esempio di codice: autenticazione del nome utente per WCF]: http://msdn.microsoft.com/it-it/library/gg185927.aspx
  [Servizio WCF (SOAP) con AD]: ./media/SecurityRX/05_AzureADAccessControl.gif
  [Procedura: Configurare ADFS 2.0 come provider di identità]: http://msdn.microsoft.com/it-it/library/gg185961.aspx
  [Procedura: Usare il servizio di gestione per configurare ADFS 2.0 come provider di identità enterprise]: http://msdn.microsoft.com/it-it/library/gg185905.aspx
  [Esempio di codice: autenticazione federata WCF con ADFS 2.0]: http://msdn.microsoft.com/it-it/library/hh127796.aspx
  [Servizio REST]: ./media/SecurityRX/06_RESTService.gif
  [Procedura: Configurare i criteri di attendibilità tra ACS e il servizio WCF usando chiavi simmetriche]: http://msdn.microsoft.com/it-it/library/gg185958.aspx
  [Procedura: Eseguire l'autenticazione a un servizio WCF REST distribuito in Azure tramite ACS]: http://msdn.microsoft.com/it-it/library/hh289317.aspx
  [Esempio di codice: servizio Web ASP.NET]: http://msdn.microsoft.com/it-it/library/gg983271.aspx
  [WCF REST con token WCF rilasciato da Servizio di controllo di accesso di Azure (ACS)]: http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0
  [WIF è facoltativo]: ./media/SecurityRX/07_WIFisOptional.gif
  [Procedura: Configurare Google come provider di identità]: http://msdn.microsoft.com/it-it/library/gg185976.aspx
  [Procedura: Configurare Facebook come provider di identità]: http://msdn.microsoft.com/it-it/library/gg185919.aspx
  [Procedura: Configurare Yahoo! come provider di identità]: http://msdn.microsoft.com/it-it/library/gg185977.aspx
  [Applicazione Web ASP.NET]: ./media/SecurityRX/08_ASPNETWebApptoREST.gif
  [App Web ASP.NET per delega del servizio WCF REST con token SWT condiviso]: http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82
  []: ./media/SecurityRX/09_RBAC.gif
  [1]: ./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
  [2]: ./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
  [3]: ./media/SecurityRX/12_CustomRoleManager.gif
  [Procedura: Implementare la logica di trasformazione dei token mediante regole]: http://msdn.microsoft.com/it-it/library/gg185955.aspx
  [Autorizzazione con RoleManager per le applicazioni Web ASP.NET (WIF) in grado di riconoscere attestazioni]: http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx
  [Windows Identity Foundation SDK]: http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504
  [4]: ./media/SecurityRX/13_ClaimsAuthorizationManager.gif
  [5]: ./media/SecurityRX/14_WindowsAzurestorage.gif
  [6]: ./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
  [Linee guida e limitazioni sulla sicurezza (database SQL)]: http://msdn.microsoft.com/it-it/library/windowsazure/ff394108.aspx#authentication
  [Procedura: Connettersi al database SQL tramite sqlcmd]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336280.aspx
  [Procedura: Connettersi al database SQL tramite ADO.NET]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336243.aspx
  [Procedura: Connettersi al database SQL tramite ASP.NET]: http://msdn.microsoft.com/it-it/library/windowsazure/ee621781.aspx
  [Procedura: Connettersi al database SQL tramite WCF Data Services]: http://msdn.microsoft.com/it-it/library/windowsazure/ee621789.aspx
  [Procedura: Connettersi al database SQL tramite PHP]: http://msdn.microsoft.com/it-it/library/windowsazure/ff394110.aspx
  [Procedura: Connettersi al database SQL tramite JDBC]: http://msdn.microsoft.com/it-it/library/windowsazure/gg715284.aspx
  [Procedura: Connettersi al database SQL tramite ADO.NET Entity Framework]: http://msdn.microsoft.com/it-it/library/windowsazure/ff951633.aspx
  [7]: ./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
  [Sicurezza del bus di servizio con ACS]: http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS
  [8]: https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849
  [Autenticazione e autorizzazione di Service Bus con il Servizio di controllo di accesso]: http://msdn.microsoft.com/it-it/library/hh403962.aspx
  [9]: ./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
  [Procedura: Configurare un client della cache a livello di codice(Memorizzazione nella cache condivisa di Azure)]: http://msdn.microsoft.com/it-it/library/windowsazure/gg618003.aspx
  [Procedura: Configurare un client della cache mediante il file di configurazione dell'applicazione(Memorizzazione nella cache condivisa di Azure)]: http://msdn.microsoft.com/it-it/library/windowsazure/gg278346.aspx
  [Esempi di bus di servizio e memorizzazione nella cache di Azure]: http://msdn.microsoft.com/it-it/library/ee706741.aspx
  [10]: ./media/SecurityRX/18_IAccessMyDataset.gif
  [Implementare l'autenticazione di base HTTP nell'applicazione per Marketplace]: http://msdn.microsoft.com/it-it/library/gg193417.aspx
  [11]: ./media/SecurityRX/19_UsersAccessMyDatasets.gif
  [Esempio di client Web OAuth]: http://go.microsoft.com/fwlink/?LinkId=219162
  [Esempio di rich client Web OAuth]: http://go.microsoft.com/fwlink/?LinkId=219163
  [12]: ./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif
  [Download del kit di pubblicazione di app]: http://go.microsoft.com/fwlink/?LinkId=221323
  [Introduzione ad Azure Marketplace per le applicazioni]: https://datamarket.azure.com/
  [Considerazione sulla progettazione WIF]: http://msdn.microsoft.com/it-it/library/ee517298.aspx
  [Sicurezza di Windows Identity Foundation (WIF) per applicazioni Web ASP.NET - Minacce e contromisure]: http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx
  [Windows Identity Foundation (WIF): individuato un valore Request.Form potenzialmente pericoloso proveniente dal client.]: http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx
  [Linee guida per la sicurezza di ACS]: http://msdn.microsoft.com/it-it/library/gg185962.aspx
  [Linee guida per la gestione di certificati e chiavi]: http://msdn.microsoft.com/it-it/library/hh204521.aspx
  [13]: http://go.microsoft.com/fwlink/?LinkId=214555
  [14]: http://go.microsoft.com/fwlink/?LinkId=214561
  [15]: http://go.microsoft.com/fwlink/?LinkId=214562
  [Servizio di controllo di accesso]: http://msdn.microsoft.com/it-it/library/windowsazure/gg429786.aspx
  [Applicazione Web ASP.NET con ruolo Web di Azure protetta tramite Servizio di controllo di accesso v2.0]: http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx
  [Video su Azure AD Access Control Service (ACS) Academy]: http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx
  [Microsoft Security Development Lifecycle]: http://www.microsoft.com/security/sdl/default.aspx
  [SDL Threat Modeling Tool 3.1.8]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955
  [Blog su sicurezza e privacy]: http://www.microsoft.com/about/twc/en/us/blogs.aspx
  [Security Response Center]: http://www.microsoft.com/security/msrc/default.aspx
  [Security Intelligence Report]: http://www.microsoft.com/security/sir/
  [Centro per sviluppatori sulla sicurezza (MSDN)]: http://msdn.microsoft.com/security/
