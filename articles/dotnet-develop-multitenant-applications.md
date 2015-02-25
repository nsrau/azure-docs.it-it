<properties urlDisplayName="Multi-Tenant Web Application Pattern" pageTitle="Modello di applicazione Web multi-Tenant - Architettura di Azure" metaKeywords="" description="Sono disponibili informazioni generali sull'architettura e modelli di progettazione che descrivono come implementare un'applicazione Web multi-tenant in Azure." metaCanonical="" services="" documentationCenter=".NET" title="Multitenant Applications in Azure" authors="wpickett" solutions="" manager="wpickett" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wpickett" />


<div>
<div class="left-nav">
<div class="static-nav">
<ul>
<li class="menu-nodejs-compute"><a href="/it-it/develop/net/compute/">Calcolo</a></li>
<li class="menu-nodejs-data"><a href="/it-it/develop/net/data/">Servizi dati</a></li>
<li class="menu-nodejs-appservices"><a href="/it-it/develop/net/app-services/">Servizi app</a></li>
<li><a href="/it-it/develop/net/reference/">Informazioni di riferimento</a></li>
<li><a href="/it-it/develop/net/guidance/">Informazioni aggiuntive</a></li>
<li><a href="/it-it/develop/net/architecture/">Architettura</a></li>
<li><a href="/it-it/develop/net/samples/">Esempi</a></li>
<li><a href="/it-it/develop/net/end-to-end-Apps/">Esercitazioni basate su scenari</a></li>
</ul>
<ul class="links">
<li class="forum"><a href="/it-it/support/forums/">Forum</a></li>
</ul>
</div>
<div class="floating-nav jump-to"><br />
<ul>
<li>In questa sezione (passare a):</li>
<li><a href="/it-it/develop/net/architecture/#overviews">Panoramica dell'architettura dell'applicazione</a></li>
<li><strong>Modello dell'applicazione: App multi-tenant</strong></li>
<li><a href="/it-it/develop/net/architecture/load-testing-pattern/">Modello dell'applicazione: Test di carico</a></li>
<li><a href="/it-it/develop/net/architecture/#designpatterns">Modelli di progettazione</a></li>
</ul>
</div>
</div>
</div>

# Applicazioni multi-tenant in Azure

Un'applicazione multi-tenant è una risorsa condivisa che consente a utenti separati, o "tenant", di visualizzare l'applicazione come se fosse la propria. Uno degli scenari tipici di un'applicazione multi-tenant è quando tutti gli utenti dell'applicazione vogliono personalizzare l'esperienza utente, ma dispongono tutti degli stessi requisiti aziendali di base. Esempi di grandi applicazioni multi-tenant sono Office 365, Outlook.com e visualstudio.com.

 Dal punto di vista del fornitore di applicazioni, i vantaggi della multi-tenancy risiedono principalmente nell'efficienza operativa e dei costi. Una versione dell'applicazione può soddisfare le esigenze di molti tenant/clienti, consentendo il consolidamento di attività amministrative del sistema come il monitoraggio, l'ottimizzazione delle prestazioni, la manutenzione del software e i backup di dati.

Di seguito è riportato un elenco degli obiettivi e dei requisiti più significativi dal punto di vista di un fornitore.

- **Provisioning**: è necessario essere in grado di eseguire il provisioning di nuovi tenant per l'applicazione.  Per le applicazioni multi-tenant che contano un ingente numero di tenant, di solito è necessario automatizzare il processo abilitando il provisioning self-service.
- **Manutenibilità**: è necessario essere in grado di aggiornare l'applicazione ed eseguire altre attività di manutenzione quando la stessa viene usata da più tenant. 
- **Monitoraggio**: è necessario essere in grado di monitorare l'applicazione in ogni momento allo scopo di identificare eventuali problemi e risolverli. Ciò include il monitoraggio della modalità d'uso dell'applicazione da parte di ogni tenant.

Un'applicazione multi-tenant correttamente implementata offre agli utenti i vantaggi seguenti.

- **Isolamento**: le attività dei singoli tenant non incidono sull'uso dell'applicazione da parte degli altri tenant. I tenant non possono accedere ai dati reciproci. Ogni tenant avrà apparentemente l'uso esclusivo dell'applicazione.
- **Disponibilità**: i singoli tenant vogliono che l'applicazione sia sempre disponibile, meglio se con garanzie definite in un contratto di servizio (SLA, Service Level Agreement). Come già accennato, le attività dei singoli tenant non dovrebbero incidere sulla disponibilità dell'applicazione.
- **Scalabilità**: è possibile scalare l'applicazione in modo da soddisfare la domanda dei singoli tenant. La presenza e le azioni degli altri tenant non dovrebbero incidere sulle prestazioni dell'applicazione. 
- **Costi**: i costi sono inferiori rispetto all'esecuzione di un'applicazione single-tenant dedicata, in quanto la multi-tenancy consente la condivisione delle risorse. 
- **Personalizzabilità**. la possibilità di personalizzare l'applicazione per un singolo tenant in vari modi, ad esempio con l'aggiunta o la rimozione di funzionalità, la modifica di colori e loghi o persino l'aggiunta di un proprio codice o script.
 
In breve, benché sia necessario prendere in considerazione vari aspetti al fine di fornire un servizio altamente scalabile, vi sono anche una serie di obiettivi e requisiti comuni a molte applicazioni multi-tenant. Alcuni potrebbero non essere pertinenti in scenari specifici e l'importanza dei singoli obiettivi e requisiti varierà in ogni scenario. Anche il fornitore di un'applicazione multi-tenant avrà obiettivi e requisiti, ad esempio soddisfare obiettivi e requisiti dei tenant, redditività, fatturazione, vari livelli di servizio, provisioning, monitoraggio della manutenibilità e automazione. 

Per altre informazioni sulle considerazioni di progettazione di un'applicazione multi-tenant, vedere [Hosting di un'applicazione multi-tenant in Azure][].

Azure offre molte funzionalità che consentono di risolvere i principali problemi riscontrati durante la progettazione di un sistema multi-tenant. 

**Isolamento** 

- Segmentare i tenant del sito Web in base alle intestazioni host con o senza comunicazione SSL
- Segmentare i tenant del sito Web in base ai parametri della query
- Servizi Web nei ruoli di lavoro
	- Ruoli di lavoro che solitamente elaborano i dati sul back-end di un'applicazione.
	- Ruoli Web che solitamente fungono da front-end per le applicazioni.

**Archiviazione**

La gestione dati come SQL Azure Database o i servizi di Azure Storage come il servizio tabelle, che offre servizi di archiviazione di grandi quantità di dati non strutturati, e il servizio Blob, che offre servizi di archiviazione di grandi quantità di dati testuali o binari non strutturati come video, audio e immagini.

- Protezione di dati multi-tenant nel database SQL appropriati per l'accesso a SQL Server di ogni tenant 
- Uso di tabelle Azure per le risorse dell'applicazione - Specificando criteri di accesso a livello di contenitore, sarà possibile modificare le autorizzazioni senza dover generare nuovi URL per le risorse protette mediante firme di accesso condiviso. 
- Code di Azure per le risorse dell'applicazione - Le code di Azure sono comunemente usate per attivare l'elaborazione per conto dei tenant, ma possono essere usate anche per distribuire il lavoro richiesto per il provisioning o la gestione. 
- Code di bus di servizio - Per le risorse dell'applicazione che effettuano il push del lavoro a un servizio condiviso, è possibile usare una singola coda in cui ogni mittente del tenant dispone solo delle autorizzazioni (in base alle attestazioni generate da ACS) per effettuare il push a quella coda, mentre solo i ricevitori dal servizio dispongono dell'autorizzazione per effettuare il pull dei dati provenienti da più tenant dalla coda. 


**Servizi di connessione e sicurezza**

- Il bus di servizio di Azure, un'infrastruttura di messaggistica che consente di scambiare messaggi tra le applicazioni con approccio "a regime di controllo libero" per migliorare la scalabilità e la resilienza. 

**Servizi di rete**

Azure offre diversi servizi di rete che supportano l'autenticazione e migliorano la gestibilità delle applicazioni ospitate. Di seguito sono indicati alcuni servizi disponibili: 

- La rete virtuale di Azure consente di effettuare il provisioning e la gestione delle reti private virtuali (VPN) in Azure e di collegare queste reti in modo sicuro con l'infrastruttura IT locale. 
- Rete virtuale - Gestione traffico consente di bilanciare il carico del traffico in ingresso tra più servizi di Azure ospitati, in esecuzione sia nello stesso data center sia in data center diversi distribuiti in tutto il mondo. 
- Azure Active Directory (Azure AD) è un servizio moderno basato su REST che fornisce funzionalità di gestione dell'identità e controllo di accesso per le applicazioni cloud. L'uso di Azure AD per le risorse dell'applicazione semplifica le procedure di autenticazione e autorizzazione degli utenti affinché venga loro concesso l'accesso a servizi e applicazioni Web, consentendo al contempo il factoring delle funzionalità di autenticazione e autorizzazione al di fuori del codice. 
- Il bus di servizio di Azure fornisce una funzionalità protetta di messaggistica e flusso dei dati per le applicazioni distribuite e ibride, ad esempio la comunicazione tra le applicazioni ospitate su Azure e le applicazioni e i servizi locali, senza richiedere complesse infrastrutture di sicurezza e firewall. Uso dell'inoltro del bus di servizio per le risorse dell'applicazione - I servizi esposti come endpoint possono appartenere al tenant (ad esempio, ospitati al di fuori del sistema, come in locale) o essere forniti in maniera specifica per il tenant (perché usati per il trasferimento di dati riservati, specifici del tenant). 



**Provisioning delle risorse**

Azure offre diverse modalità per eseguire il provisioning di nuovi tenant per l'applicazione. Per le applicazioni multi-tenant che contano un ingente numero di tenant, di solito è necessario automatizzare il processo abilitando il provisioning self-service.

- I ruoli di lavoro consentono di eseguire il provisioning e il deprovisioning delle risorse per ogni tenant (ad esempio quando un nuovo tenant effettua o annulla l'iscrizione), raccogliendo metriche per misurare l'uso e gestendo la scalabilità in base a una determinata pianificazione o in risposta al superamento di soglie degli indicatori di prestazioni chiave. Questo stesso ruolo può essere usato anche per effettuare il push di aggiornamenti alla soluzione. 
- È possibile usare l'archivio BLOB di Azure per effettuare il provisioning delle risorse di calcolo o di archiviazione preinizializzate per i nuovi tenant, fornendo criteri di accesso a livello di contenitore per proteggere i pacchetti del servizio di calcolo, le immagini del disco rigido virtuale e altre risorse.
- Le opzioni per il provisioning delle risorse del database SQL per un tenant includono:

	- 	DDL in script o incorporate come risorse negli assembly 
	- 	Distribuzione a livello di codice dei pacchetti SQL Server 2008 R2 DAC
	- 	Copia da un database di riferimento master 
	- 	Uso delle funzionalità di importazione ed esportazione del database per il provisioning di nuovi database da un file 



<!--links-->

[Hosting di un'applicazione multi-tenant in Azure]: http://msdn.microsoft.com/it-it/library/hh534480.aspx
[Progettazione di applicazioni multi-tenant in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh689716


