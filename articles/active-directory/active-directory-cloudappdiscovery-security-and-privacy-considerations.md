<properties
	pageTitle="Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery | Microsoft Azure"
	description="Questo argomento illustra le considerazioni sulla sicurezza e sulla privacy correlate a Cloud App Discovery."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2016"
	ms.author="markusvi"/>

# Considerazioni sulla sicurezza e sulla privacy in Cloud App Discovery

Microsoft si impegna a proteggere la privacy e i dati degli utenti, mettendo a disposizione software e servizi che aiutino a gestire la sicurezza dell'organizzazione. <br> Microsoft è a conoscenza della necessità di investimenti e competenze per la progettazione di rigorose misure di sicurezza quando si affidano i dati a terzi. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalle procedure consigliate per il ciclo di vita dello sviluppo di software sicuro alla gestione di un servizio. <br> La sicurezza e la protezione dei dati sono altamente prioritarie per Microsoft.

Questo argomento descrive le modalità di raccolta, elaborazione e protezione dei dati in Azure Active Directory Cloud App Discovery.




##Panoramica

Cloud App Discovery è una funzionalità di Azure AD ed è ospitata in Microsoft Azure. <br> Cloud App Discovery Endpoint Agent viene usato per raccogliere dati di individuazione applicazioni da computer gestiti dal reparto IT. <br> I dati raccolti vengono inviati in modo sicuro su un canale crittografato al servizio Azure AD Cloud App Discovery. <br> I dati di Cloud App Discovery per l'organizzazione saranno quindi visibili nel portale di Azure. <br>


<center>![Funzionamento di Cloud App Discovery](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png) </center> <br>


Le sezioni seguenti seguono il flusso di informazioni e illustrano il modo in cui viene protetto durante il trasferimento dall'organizzazione al servizio Cloud App Discovery e infine al portale di Cloud App Discovery.



## Raccolta di dati dall'organizzazione

Per usare la funzionalità Cloud App Discovery di Azure Active Directory per ottenere informazioni approfondite sulle applicazioni usate dai dipendenti dell'organizzazione, sarà prima di tutto necessario distribuire Cloud App Discovery Endpoint Agent di Azure AD nei computer dell'organizzazione.

Gli amministratori del tenant di Azure Active Directory o i rispettivi delegati possono scaricare il pacchetto di installazione dell'agente dal portale di Azure. L'agente può essere installato manualmente oppure può essere installato in più computer dell'organizzazione tramite SCCM o Criteri di gruppo.

Per altre istruzioni sulle opzioni di distribuzione, vedere la [guida alla distribuzione di Criteri di gruppo in Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx). <br>

### Dati raccolti dall'agente

Le informazioni incluse nell'elenco seguente vengono raccolte dall'agente quando viene stabilita una connessione a un'applicazione Web. Le informazioni vengono raccolte solo per le applicazioni configurate dall'amministratore per l'individuazione. <br> È possibile modificare l'elenco di app cloud di cui l'agente effettua il monitoraggio tramite il pannello Cloud App Discovery nel [portale di Microsoft Azure](https://portal.azure.com/), facendo clic su **Impostazioni**->**Raccolta dati**->**Elenco Raccolta di app**. Per informazioni dettagliate, vedere [Introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br> **Categoria delle informazioni**: Informazioni sull'utente<br> **Descrizione**: <br> Nome utente di Windows del processo che ha effettuato una richiesta all'applicazione Web di destinazione (ad esempio DOMINIO\\nomeutente), oltre al SID (Security Identifier) Windows dell'utente.


**Categoria delle informazioni**: Informazioni sul processo <br> **Descrizione**: <br> Nome del processo che ha effettuato la richiesta all'applicazione Web di destinazione (ad esempio "iexplore.exe").

**Categoria delle informazioni**: Informazioni sul computer <br> **Descrizione**: <br> Nome NetBIOS del computer in cui è installato l'agente.

**Categoria delle informazioni**: Informazioni sul traffico dell'app <br> **Descrizione**: <br>

Informazioni sulla connessione

- Indirizzi IP di origine (computer locale) e destinazione e numeri di porta

- Indirizzo IP pubblico dell'organizzazione attraverso cui viene trasmessa la richiesta.

- Ora della richiesta.

- Volume del traffico inviato e ricevuto.

- Versione IP (4 o 6)

- Solo per connessioni TLS: nome host di destinazione dall'estensione Indicazione nome server o dal certificato del server.

Informazioni HTTP seguenti:

- Metodo (GET, POST e così via)

- Protocollo (HTTP/1.1 e così via)

- Stringa agente utente

- Nome host

- URI di destinazione (esclusa la stringa di query)

- Informazioni sul tipo di contenuto

- Informazioni sull'URL di referrer (esclusa la stringa di query)



> [AZURE.NOTE] Le informazioni HTTP precedenti vengono raccolte per tutte le connessioni non crittografate. Per le connessioni TLS queste informazioni vengono acquisite solo se l'impostazione Ispezione approfondita è attivata nel portale. Questa impostazione è attivata per impostazione predefinita. Per informazioni dettagliate, vedere di seguito e vedere [Introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Oltre ai dati sull'attività di rete, l'agente raccoglie anche informazioni anonime sulla configurazione software e hardware, segnalazioni errori e informazioni sull'uso dell'agente.

<br><br>
### Funzionamento dell'agente

L'installazione dell'agente include due componenti:

- Un componente per la modalità utente

- Un componente per il driver della modalità kernel (driver della Piattaforma filtro Windows)



Quando l'agente viene installato per la prima volta, archivia nel computer un certificato attendibile specifico del computer e lo usa quindi per stabilire una connessione sicura al servizio Cloud App Discovery. <br> L'agente recupera periodicamente la configurazione dei criteri dal servizio Cloud App Discovery tramite questa connessione sicura. <br> I criteri includono ad esempio informazioni sulle applicazioni cloud da monitorare e sulla necessità di abilitare o meno l'aggiornamento automatico.

Quando il traffico Web viene inviato e ricevuto nel computer da Chrome o da Internet Explorer, l'agente di Cloud App Discovery analizza il traffico ed estrae i metadati pertinenti (vedere la sezione **Dati raccolti dall'agente** precedente). <br> L'agente carica ogni minuto i metadati raccolti nel servizio Cloud App Discovery usando un canale crittografato.

Il componente driver intercetta il traffico crittografato e si inserisce nel flusso crittografato. Per altri dettagli, vedere la sezione **Intercettazione dei dati da connessioni crittografate (Ispezione approfondita)** più avanti.


### Rispetto della privacy degli utenti

Il nostro obiettivo consiste nell'offrire agli amministratori gli strumenti necessari per trovare un equilibrio tra informazioni dettagliate sull'utilizzo delle applicazioni e la privacy degli utenti, in base alle esigenze dell'organizzazione. Per questo scopo, nella pagina Impostazioni del portale sono disponibili le manopole seguenti:

- **Raccolta dati**: gli amministratori possono scegliere di specificare le applicazioni o le categorie di applicazioni per cui ottenere i dati relativi all'individuazione.

- **Ispezione approfondita**: gli amministratori possono scegliere di specificare se è l'agente a raccogliere i dati relativi al traffico HTTP per le connessioni SSL/TLS. Per altre informazioni, vedere la sezione successiva.

- **Opzioni per il consenso**: gli amministratori possono usare il portale di Cloud app Discovery per scegliere se informare gli utenti che l'agente effettua la raccolta dei dati e se richiedere il consenso dell'utente prima che l'agente inizi a raccogliere i dati utente.

Cloud App Discovery Endpoint Agent raccoglie solo le informazioni indicate nella sezione **Dati raccolti dall'agente** precedente.


### Intercettazione dei dati da connessioni crittografate (Ispezione approfondita)
Come accennato in precedenza, gli amministratori possono configurare l'agente in modo che effettui il monitoraggio dei dati da connessioni crittografate ('Ispezione approfondita'). Il protocollo TLS ([Transport Layer Security](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) è uno dei protocolli Internet usati più di frequente. La crittografia delle comunicazioni tramite protocollo TLS, consente ai clienti di stabilire un canale di comunicazione sicuro e privato con un server Web. Il protocollo TLS fornisce la protezione essenziale per passare le credenziali di autenticazione e impedire la divulgazione di informazioni riservate.

Anche se il canale crittografato protetto end-to-end offerto da TLS consente di tutelare la sicurezza e la privacy, questo protocollo viene spesso usato per scopi dannosi o non autorizzati. È per questo motivo che in realtà TLS viene spesso definito come il "protocollo universale di bypass del firewall". Il problema dipende dal fatto che la maggior parte dei firewall non è in grado di esaminare le comunicazioni TLS perché i dati a livello di applicazione sono crittografati con SSL. Gli autori di attacchi che conoscono questa limitazione sfruttano quindi il protocollo TLS per recapitare payload dannosi agli utenti nella certezza che persino i firewall a livello di applicazione più intelligenti non riconoscono TLS e devono semplicemente inoltrare le comunicazioni TLS tra host. Spesso gli utenti finali sfruttano il protocollo TLS per ignorare i controlli di accesso applicati da firewall e server proxy aziendali, usandolo per connettersi a proxy pubblici e per il tunneling tramite il firewall di protocolli non TLS che altrimenti verrebbero bloccati da appositi criteri.

L'ispezione approfondita consente all'agente di App Cloud Discovery di fungere da intermediario attendibile. Quando viene effettuata una richiesta client per accedere a una risorsa protetta tramite HTTPS, il driver Endpoint Agent intercetta la connessione e stabilisce una nuova connessione al server di destinazione per recuperare il certificato SSL per conto del client. L'agente verifica quindi che il certificato sia attendibile (controllando che non sia stato revocato ed effettuando altre verifiche del certificato) e, se i controlli vengono superati Endpoint Agent copia le informazioni dal certificato del server per crearne uno proprio, noto come certificato di intercettazione, usando tali informazioni. Il certificato di intercettazione viene quindi firmato immediatamente da Endpoint Agent con un certificato radice, che viene infine installato nell'archivio certificati attendibili di Windows. Il certificato radice autofirmato è contrassegnato come non esportabile e viene sottoposto ad ACL per gli amministratori. Non deve mai lasciare il computer in cui è stato creato. Quando l'applicazione client dell'utente finale riceve il certificato di intercettazione, lo considererà attendibile perché riuscirà a convalidare la catena di certificati fino al certificato radice. Questo processo è quasi completamente trasparente dal punto di vista dell'utente finale, ma occorre tenere presente alcuni aspetti, come descritto di seguito.

Quando l'ispezione approfondita è abilitata, Cloud App Discovery Endpoint Agent può decrittografare e ispezionare le comunicazioni crittografate tramite TLS, consentendo così al servizio di ridurre i dati non significativi e fornendo informazioni dettagliate sull'utilizzo delle app cloud crittografate.

#### Precisazione importante
Prima di attivare l'ispezione approfondita si consiglia di comunicare le proprie intenzioni all'ufficio legale e al reparto risorse umane per ottenerne il consenso. Per ovvi motivi l'ispezione delle comunicazioni crittografate private di un utente finale può comportare problemi di tutela della privacy. Prima di implementare l'ispezione approfondita in un ambiente di produzione, assicurarsi che i criteri relativi alla sicurezza e alle modalità di utilizzo delle informazioni accettati in ambito aziendale siano stati aggiornati in modo da indicare che le comunicazioni crittografate verranno ispezionate. Può inoltre essere necessario implementare le notifiche utente ed escludere i siti ritenuti sensibili (ad esempio quelli bancari o medici) se si configura Cloud App Discovery per includerli nel monitoraggio. Come indicato in precedenza, gli amministratori possono usare il portale di Cloud App Discovery per scegliere se informare gli utenti che l'agente effettua la raccolta dei dati e se richiedere il consenso dell'utente prima che l'agente inizi a raccogliere i dati dell'utente.

### Problemi noti e svantaggi
In alcuni casi, l'intercettazione TLS potrebbe influire negativamente sull'esperienza dell'utente finale:

- Con i certificati di convalida estesa, la barra degli indirizzi del Web browser assume un colore verde per indicare che si sta visitando un sito Web attendibile. L'ispezione TLS non può duplicare la convalida estesa del certificato rilasciato al client, quindi i siti Web che usano i certificati di convalida estesa funzionano normalmente ma la barra degli indirizzi non risulterà verde.  

- L'associazione della chiave pubblica, nota anche come associazione del certificato, è progettata per proteggere gli utenti da attacchi man-in-the-middle e autorità di certificazione non autorizzate. Quando il certificato radice di un sito aggiunto non corrisponde a una delle Autorità di certificazione valide note, il browser impedisce la connessione e restituisce un errore. Dal momento che l'intercettazione TLS è di fatto paragonabile a un attacco man-in-the-middle, queste connessioni non riusciranno.

- Se gli utenti fanno clic sull'icona con il lucchetto nella barra degli indirizzi del browser per ispezionare le informazioni del sito, non visualizzeranno una catena che termina con l'autorità di certificazione usata per firmare il certificato del sito Web ma una catena di certificati che termina con l'archivio certificati attendibili di Windows.

Per ridurre il riprodursi di questi problemi, viene tenuta traccia dei servizi cloud e delle applicazioni client che in genere usano la convalida estesa o l'associazione della chiave pubblica e indicano a Endpoint Agent di evitare di intercettare le connessioni interessate. Anche in questi casi, si continueranno a ricevere report sull'utilizzo di queste app cloud e sul volume di dati trasferiti, ma poiché non viene effettuata l'ispezione approfondita, non saranno disponibili dettagli sul modo in cui le app sono state usate.


## Invio di dati a Cloud App Discovery

Dopo la raccolta da parte dell'agente, i metadati vengono memorizzati nella cache del computer per al massimo un minuto o fino a quando i dati memorizzati nella cache non raggiungono una dimensione pari a 5 MB. Vengono quindi compressi e inviati tramite una connessione sicura al servizio Cloud App Discovery.

Se l'agente non riesce a comunicare con il servizio Cloud App Discovery per qualsiasi motivo, i metadati raccolti vengono archiviati in una cache dei file locale, a cui possono accedere solo gli utenti con privilegi elevati nel computer, ad esempio il gruppo Administrators. <br> L'agente prova automaticamente a inviare di nuovo i metadati memorizzati nella cache fino a che non vengono ricevuti correttamente dal servizio Cloud App Discovery.



## Ricezione dei dati sul lato servizio

Gli agenti eseguono l'autenticazione per il servizio Cloud App Discovery usando il certificato di autenticazione client specifico del computer indicato in precedenza e inoltrano i dati su un canale crittografato. <br> La pipeline di analisi del servizio Cloud App Discovery elabora i metadati separatamente per ogni cliente, partizionandoli logicamente in tutte le fasi della pipeline di analisi. I metadati analizzati sono alla base dei diversi report disponibili nel portale.

I metadati non elaborati e i metadati analizzati vengono archiviati per un massimo di 180 giorni. I clienti possono anche scegliere di acquisire i metadati analizzati nell'account di archiviazione BLOB di Azure desiderato. Ciò risulta utile per l'analisi offline dei metadati, oltre che per la conservazione più duratura dei dati.

## Accesso ai dati mediante il portale di Azure

Nel tentativo di proteggere i metadati raccolti, per impostazione predefinita solo gli amministratori globali del tenant possono accedere alla funzionalità Cloud App Discovery del portale di Azure. <br> Gli amministratori possono tuttavia scegliere di delegare l'accesso ad altri utenti o gruppi.



> [AZURE.NOTE] Per informazioni dettagliate, vedere [Introduzione a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br> Qualsiasi utente che accede ai dati nel portale deve avere una licenza per Azure AD Premium.



##Risorse aggiuntive


* [Come individuare app cloud non autorizzate usate nell'organizzazione](active-directory-cloudappdiscovery-whatis.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0504_2016-->