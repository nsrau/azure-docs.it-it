La gestione delle identità è importante tanto nel cloud pubblico quanto in locale. A questo scopo, Azure supporta numerose tecnologie di identità diverse per il cloud, tra cui:

- È possibile eseguire Windows Server Active Directory (comunemente denominato AD) nel cloud utilizzando macchine virtuali create con Macchine virtuali di Azure. Questo approccio è indicato quando si utilizza Azure per estendere il data center locale nel cloud.

- È possibile utilizzare Azure Active Directory per fornire agli utenti l'accesso Single Sign-On alle applicazioni SaaS (Software as a Service). Microsoft Office 365 ad esempio utilizza questa tecnologia che può essere utilizzata anche dalle applicazioni eseguite in Azure o in altre piattaforme cloud.

- Le applicazioni eseguite nel cloud o in locale possono utilizzare Controllo di accesso di Azure Active Directory per consentire agli utenti di eseguire l'accesso utilizzando le identità di Facebook, Google, Microsoft e altri provider di identità.


In questo articolo vengono descritte queste tre opzioni.

## Sommario

- [Esecuzione di Windows Server Active Directory nelle macchine virtuali](#adinvm)

- [Uso di Azure Active Directory](#ad)

- [Uso di Controllo di accesso di Azure Active Directory](#ac)


## <a name="adinvm"></a>Esecuzione di Windows Server Active Directory nelle macchine virtuali

L'esecuzione di Windows Server AD nelle macchine virtuali di Azure è molto simile all'esecuzione in locale. Nella [figura 1](#fig1) è mostrato un esempio tipico.

![Azure Active Directory in macchina virtuale](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figura 1: Windows Server Active Directory può essere eseguito nelle macchine virtuali di Azure connesse al data center locale di un'organizzazione mediante Rete virtuale di Azure.

Nell'esempio riportato, Windows Server AD è eseguito nelle macchine virtuali create mediante Macchine virtuali di Azure, la tecnologia IaaS della piattaforma. Queste macchine virtuali e alcune altre sono raggruppate in una rete virtuale (VNET) connessa a un data center locale tramite Rete virtuale di Azure. La rete virtuale crea un gruppo di macchine virtuali cloud che interagiscono con la rete locale attraverso una connessione VPN (Virtual Private Network). In questo modo, le macchine virtuali di Azure presentano lo stesso aspetto di un'altra subnet nel data center locale. Come illustrato nella figura, due di queste macchine virtuali eseguono controller di dominio di Windows Server AD. Le altre macchine virtuali nella rete virtuale potrebbero eseguire applicazioni, come SharePoint, o essere usate in altro modo, ad esempio per attività di sviluppo e di test. Anche nel data center locale sono in esecuzione due controller di dominio di Windows Server AD.

Per connettere i controller di dominio nel cloud a quelli in esecuzione in locale, sono disponibili numerose opzioni:

- Renderle tutte parte di un unico dominio Active Directory.

- Creare domini AD distinti ma appartenenti alla stessa foresta in locale e nel cloud.

- Creare foreste AD distinte nel cloud e in locale e quindi connettere le foreste mediante trust di un'altra foresta o Windows Server Active Directory Federation Services (AD FS) che può essere eseguito nelle macchine virtuali in Azure.

Indipendentemente dalla scelta, l'amministratore deve assicurarsi che le richieste di autenticazione dagli utenti in locale passino ai controller di dominio del cloud solo se necessario, in quanto è probabile che il collegamento al cloud risulti più lento rispetto alle reti locali. Un altro fattore da considerare per la connessione dei controller di dominio cloud e locali è il traffico generato dalla replica. I controller di dominio nel cloud si trovano in genere nel sito AD dedicato, pertanto l'amministratore può pianificare la frequenza di esecuzione della replica. Poiché Azure addebita il traffico in uscita da un data center di Azure ma non i byte in ingresso, questo potrebbe influire sulle scelte di replica dell'amministratore. È inoltre importante sottolineare che mentre Azure fornisce un proprio supporto Domain Name System (DNS), da questo servizio mancano le funzionalità richieste da Active Directory, ad esempio il supporto per il DNS dinamico e i record SRV. Per questo motivo, l'esecuzione di Windows Server AD in Azure richiede la configurazione dei propri server DNS nel cloud.

L'esecuzione di Windows Server AD nelle macchine virtuali di Azure può risultare indicata in varie situazioni. Di seguito sono riportati alcuni esempi:

- Se si utilizza Macchine virtuali di Azure come un'estensione del data center, possibile che vengano eseguite applicazioni nel cloud che richiedono controller di dominio locali per gestire elementi quali le richieste di Autenticazione integrata di Windows o query LDAP. SharePoint, ad esempio, interagisce spesso con Active Directory e sebbene sia possibile eseguire una farm SharePoint in Azure mediante una directory locale, la configurazione di controller di dominio nel cloud determina un miglioramento significativo delle prestazioni. È importante sottolineare che non si tratta di una scelta obbligatoria, infatti, molte applicazioni vengono eseguite nel cloud utilizzando solo controller di dominio locali.

- Si prenda ad esempio una succursale lontana a cui mancano le risorse per eseguire i propri controller di dominio. Attualmente, gli utenti devono eseguire l'autenticazione a controller di dominio che si trovano dall'altra parte del mondo e l'accesso risulta rallentato. L'esecuzione di Active Directory in Azure in un data center Microsoft più vicino consentirebbe di accelerare l'accesso senza richiedere un maggior numero di server presso la succursale.

- Un'organizzazione che utilizza Azure per il ripristino di emergenza potrebbe mantenere un set di macchine virtuali attive ridotto nel cloud, incluso un controller di dominio. Sarà quindi predisposta per espandere il sito in base alle esigenze per intervenire in caso di errori altrove.

Sono inoltre disponibili altre possibilità. Non è ad esempio necessario connettere Windows Server AD nel cloud a un data center locale. Per eseguire una farm SharePoint per servire un determinato set di utenti che possono eseguire l'accesso esclusivamente con identità basate sul cloud, sarebbe necessario creare una foresta autonoma in Azure. Il modo in cui si utilizza questa tecnologia dipende dagli obiettivi. Per informazioni dettagliate sull'utilizzo di Windows Server AD con Azure, [vedere qui](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).

## <a name="ad"></a>Uso di Azure Active Directory

L'aumento della diffusione delle applicazioni SaaS fa sorgere una domanda ovvia relativa al tipo di servizio directory che deve essere usato da queste applicazioni basate sul cloud. La risposta di Microsoft a questa domanda è Azure Active Directory.

Sono disponibili due opzioni principali per l'utilizzo di questo servizio directory nel cloud:

- I privati e le organizzazioni che utilizzano solo applicazioni SaaS possono utilizzare Azure Active Directory come unico servizio directory.

- Le organizzazioni che eseguono Windows Server Active Directory possono connettere la directory locale ad Azure Active Directory e quindi utilizzarla per concedere agli utenti l'accesso Single Sign-On alle applicazioni SaaS.


[Nella ](#fig2)figura 2 è illustrata la prima di queste due opzioni, per la quale è necessario solo Azure Active Directory.

![Azure Active Directory in macchina virtuale](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figura 2: Azure Active Directory offre agli utenti dell'organizzazione l'accesso Single Sign-On alle applicazioni SaaS, incluso Office 365.

Come mostrato nella figura, Azure AD è un servizio multi-tenant. Questo significa che può supportare contemporaneamente più organizzazioni diverse e archiviare informazioni directory relative agli utenti presso ognuna di esse. In questo esempio, un utente presso l'organizzazione A sta tentando di accedere a un'applicazione SaaS. L'applicazione può essere un'applicazione di Office 365, ad esempio SharePoint Online o un'altra applicazione non Microsoft che utilizza questa stessa tecnologia. Poiché Azure AD supporta il protocollo SAML 2.0, tutto ciò che si richiede a un'applicazione è la possibilità di interagire utilizzando questo standard del settore. In realtà, le applicazioni che utilizzano Azure AD possono essere eseguite in qualsiasi data center e non solo in un data center Azure.

Il processo ha inizio quando l'utente esegue l'accesso a un'applicazione SaaS (passaggio 1). Per utilizzare questa applicazione, l'utente deve presentare un token emesso da Azure AD.

Questo token contiene informazioni che identificano l'utente ed è firmato digitalmente da Azure AD. Per ottenere il token, l'utente esegue l'autenticazione ad Azure AD specificando un nome utente e una password (passaggio 2). Azure AD restituisce quindi il token necessario (passaggio 3).

Questo token viene quindi inviato all'applicazione SaaS (passaggio 4), che ne convalida la firma e ne utilizza il contenuto (passaggio 5). In genere, l'applicazione utilizzerà le informazioni relative all'identità contenute nel token per stabilire le informazioni alle quali l'utente può accedere, forse anche in altri modi.

Se l'applicazione necessita di maggiori informazioni sull'utente rispetto a quelle contenute nel token, può richiederle direttamente da Azure AD mediante l'API Graph di Azure AD (passaggio 6). Nella versione iniziale di Azure AD lo schema della directory è relativamente semplice: contiene semplicemente utenti e gruppi e le relazioni tra di essi. Le applicazioni possono usare questi dati per acquisire altre informazioni sulle connessioni tra gli utenti. Si supponga, ad esempio, che un'applicazione necessiti di conoscere il nome del responsabile dell'utente per decidere se questi è autorizzato ad accedere ad alcuni blocchi di dati. Potrà acquisire questa informazione interrogando Azure AD tramite l'API Graph.

L'API Graph utilizza un protocollo RESTful ordinario che ne semplifica l'uso da parte della maggior parte dei client, inclusi i dispositivi mobili. L'API supporta inoltre le estensioni definite da OData, mediante l'aggiunta di elementi quali un linguaggio di query, per consentire ai client di accedere ai dati con maggiore praticità. Per ulteriori informazioni su OData, vedere [Introduzione a OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf). Poiché l'API Graph può essere utilizzata per acquisire informazioni sulle relazioni tra gli utenti, consente alle applicazioni di comprendere il grafico sociale incorporato nello schema di Azure AD per una determinata organizzazione (da cui il nome, API Graph). Inoltre, per eseguire l'autenticazione ad Azure AD per le richieste dell'API Graph, un'applicazione utilizza OAuth 2.0.

Se un'organizzazione non utilizza Windows Server Active Directory, in quanto non dispone di server locali o domini, e utilizza esclusivamente applicazioni cloud che utilizzano Azure AD, l'uso di questa directory cloud concederebbe agli utenti dell'organizzazione l'accesso Single Sign-On a tutte. Sebbene questo scenario stia diventando sempre più diffuso, la maggior parte delle organizzazioni si avvale ancora di domini locali creati con Windows Server Active Directory. Azure AD può giocare un ulteriore ruolo utile anche in questo caso, come illustrato nella [figura 3](#fig3).

![Azure Active Directory in macchina virtuale](./media/identity/identity_03_AD.png) <a id="fig3"></a>Figura 3: un'organizzazione può federare Windows Server Active Directory con Azure Active Directory per concedere agli utenti l'accesso Single Sign-On alle applicazioni SaaS.

In questo scenario un utente presso l'organizzazione B desidera accedere a un'applicazione SaaS. Prima di poter procedere, gli amministratori della directory dell'organizzazione devono stabilire una relazione di federazione con Azure AD mediante AD FS, come illustrato nella figura. Gli amministratori devono inoltre configurare la sincronizzazione dei dati tra Windows Server AD locale dell'organizzazione e Azure AD. Questa operazione determina la copia automatica delle informazioni relative a utenti e gruppi dalla directory locale ad Azure AD. Grazie a questa operazione, l'organizzazione estende la directory locale nel cloud. La combinazione di Windows Server AD e di Azure AD offre all'organizzazione un servizio directory che può essere gestito come un'entità singola, mantenendo al contempo una superficie sia in locale che nel cloud.

Per utilizzare Azure AD, l'utente esegue innanzitutto l'accesso al proprio dominio Active Directory come di consueto (passaggio 1). Quando tenta di accedere all'applicazione SaaS (passaggio 2), il processo di federazione consente ad Azure AD di emettere un token per l'applicazione (passaggio 3). Per altre informazioni sul funzionamento della federazione, vedere il documento relativo alle [tecnologie e agli scenari per le attestazioni basate sulle identità per Windows](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx). Come in precedenza, questo token contiene informazioni che identificano l'utente ed è firmato digitalmente da Azure AD. Questo token viene quindi inviato all'applicazione SaaS (passaggio 4), che ne convalida la firma e ne utilizza il contenuto (passaggio 5). E, come nello scenario precedente, l'applicazione SaaS può utilizzare l'API Graph per acquisire ulteriori informazioni sull'utente, se necessario (passaggio 6).

Attualmente, Azure AD non rappresenta un'alternativa completa rispetto a Windows Server AD locale. Come indicato in precedenza, la directory cloud presenta uno schema molto più semplice da cui mancano diversi elementi, ad esempio Criteri di gruppo, la possibilità di archiviare informazioni relative ai computer e il supporto per LDAP. Di fatto, non è possibile configurare un computer Windows per consentire agli utenti di eseguirvi l'accesso utilizzando solo Azure AD, in quanto non è uno scenario supportato. Tra gli obiettivi iniziali di Azure AD è inclusa la possibilità di consentire agli utenti aziendali di accedere alle applicazioni nel cloud senza mantenere un accesso separato in modo da evitare agli amministratori della directory locale di eseguire la sincronizzazione manuale della directory locale con ogni applicazione SaaS utilizzata dall'organizzazione. Nel tempo, è tuttavia previsto che il servizio directory cloud copra una più vasta gamma di scenari.

## <a name="ac"></a>Uso di Controllo di accesso di Azure Active Directory

Le tecnologie per la gestione delle identità basate sul cloud possono essere usate per risolvere diversi problemi. Azure Active Directory consente ad esempio di fornire agli utenti di un'organizzazione l'accesso Single Sign-On a più applicazioni SaaS. Le tecnologie di gestione dell'identità nel cloud possono inoltre essere utilizzate in altri modi.

Si supponga ad esempio che un'applicazione desideri consentire agli utenti di accedere mediante token emessi da più *provider di identità*. Sono disponibili numerosi provider di identità, inclusi Facebook, Google, Microsoft e altri, e spesso le applicazioni consentono agli utenti di eseguire l'accesso mediante una di queste identità. Per quale motivo un'applicazione dovrebbe preoccuparsi di mantenere un proprio elenco di utenti e password quando può avvalersi di identità già esistenti? L'accettazione di identità esistenti semplifica le operazioni sia agli utenti che avranno così un nome utente e una password in meno da ricordare, sia a coloro che creano l'applicazione, in quanto non è più necessario mantenere un proprio elenco di nomi utente e password.

Sebbene ogni provider di identità emetta un determinato tipo di token, questi token non sono standard, in quanto ogni provider di identità dispone di un formato personalizzato. Inoltre, anche le informazioni contenute in questi token non sono standard. Un'applicazione che desidera accettare i token emessi da Facebook, Google e Microsoft dovrà prevedere la scrittura di codice univoco per gestire ognuno di questi formati diversi.

A che scopo? Non converrebbe creare un intermediario in grado di generare un unico formato di token con una rappresentazione comune delle informazioni relative all'identità? Questo approccio consentirebbe di semplificare le attività degli sviluppatori che creano applicazioni, in quanto dovrebbero gestire un solo tipo di token. Questo è possibile grazie a Controllo di accesso di Azure Active Directory, che fornisce un intermediario nel cloud per l'utilizzo di diversi tipi di token, come illustrato nella [figura 4](#fig4).

![Azure Active Directory in macchina virtuale](./media/identity/identity_04_IdentityProviders.png) <a id="fig4"></a>Figura 4: Controllo di accesso di Azure Active Directory consente alle applicazioni di accettare token di identità emessi da provider di identità diversi.

Il processo ha inizio quando l'utente tenta di accedere all'applicazione da un browser. L'applicazione reindirizza l'utente a un provider di identità di sua scelta considerato attendibile anche dall'applicazione stessa. L'utente esegue l'autenticazione al provider di identità, ad esempio specificando un nome utente e una password (passaggio 1) e il provider di identità restituisce un token contenente le informazioni relative all'utente stesso (passaggio 2).

Come illustrato nella figura, Controllo di accesso supporta un'ampia varietà di provider di identità basati sul cloud, inclusi gli account creati da Google, Yahoo, Facebook, Microsoft (precedentemente noti come Windows Live ID) e da qualsiasi provider OpenID. Supporta inoltre le identità create mediante Azure Active Directory e attraverso la federazione con AD FS, Windows Server Active Directory. L'obiettivo è gestire i tipi di identità utilizzati più di frequente, indipendentemente dal fatto che siano emessi da provider di identità nel cloud o locali.

Dopo avere ottenuto un token dal provider di identità di sua scelta, il browser dell'utente lo invia a Controllo di accesso (passaggio 3). Controllo di accesso di accesso convalida il token, accertandosi che sia stato realmente emesso da quel provider di identità, quindi crea un nuovo token in base alle regole definite per l'applicazione. Analogamente ad Azure Active Directory, Controllo di accesso è un servizio multi-tenant solo che i tenant sono costituiti da applicazioni anziché da organizzazioni dei clienti. Ogni applicazione può ottenere uno spazio dei nomi personalizzato, come mostrato nella figura, definire varie regole relative all'autenticazione e altro ancora.

Tali regole consentono all'amministratore di ogni applicazione di definire la modalità in cui i token di diversi provider di identità devono essere trasformati in un token di Controllo di accesso. Se ad esempio diversi provider di identità utilizzano tipi differenti per rappresentare i nomi utenti, le regole di Controllo di accesso possono trasformarli in un tipo di nome utente comune. Controllo di accesso invia il nuovo token al browser (passaggio 4) che a sua volta lo invia all'applicazione (passaggio 5). Non appena riceve il token di Controllo di accesso, l'applicazione verifica che questo sia stato realmente emesso da Controllo di accesso e quindi utilizza le informazioni in esso contenute (passaggio 6).

Sebbene questo processo possa sembrare complesso, consente di semplificare le attività per gli sviluppatori di applicazioni. Anziché gestire token eterogenei contenenti informazioni diverse, l'applicazione può accettare identità emesse da più provider di identità ricevendo comunque un singolo token con informazioni note. Inoltre, invece di richiedere che ogni applicazione sia configurata per considerare attendibili diversi provider di identità, tali relazioni di attendibilità sono invece mantenute da Controllo di accesso che dovrà semplicemente essere considerato attendibile dalle applicazioni.

È importante sottolineare che Controllo di accesso non è legato a Windows e che può essere utilizzato da altre applicazioni, ad esempio un'applicazione Linux che accetta solo le identità di Google e Facebook. Sebbene Controllo di accesso appartenga alla famiglia di prodotti Azure Active Directory, deve essere considerato come un servizio completamente distinto, come si evince da quanto descritto nella sezione precedente. Nonostante entrambe le tecnologie riguardino la gestione delle identità, consentono di risolvere problemi diversi (anche se Microsoft ha comunicato che è prevista un'integrazione tra esse in futuro).

L'utilizzo delle identità è importante per la maggior parte delle applicazioni. Controllo di accesso è progettato per consentire agli sviluppatori di creare applicazioni che accettano identità da provider di identità eterogenei. Poiché Microsoft ha reso disponibile questo servizio nel cloud, ora può essere utilizzato da qualsiasi applicazione in esecuzione su qualunque piattaforma.

##Informazioni sull'autore

David Chappell è il titolare della società Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) di San Francisco, California.

<!---HONumber=AcomDC_0720_2016-->