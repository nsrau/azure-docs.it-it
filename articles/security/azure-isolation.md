---

title: Isolamento nel cloud pubblico di Azure | Microsoft Docs
description: "Informazioni sui servizi di calcolo basati sul cloud che includono un&quot;ampia gamma di istanze e servizi di calcolo con scalabilità automatica per soddisfare le esigenze dell&quot;applicazione o dell&quot;organizzazione."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 0a83f249bbaba0ae3fd6ebf10863abc398859200
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="isolation-in-the-azure-public-cloud"></a>Isolamento nel cloud pubblico di Azure
## <a name="10-introduction"></a>1.0 Introduzione
### <a name="11-overview"></a>1.1 Panoramica
Per consentire ai clienti attuali e potenziali di Azure di comprendere e usare le diverse funzionalità correlate alla sicurezza disponibili nel contesto della piattaforma Azure, Microsoft ha sviluppato una serie di white paper, panoramiche sulla sicurezza, procedure consigliate ed elenchi di controllo.
Gli argomenti variano per ampiezza e livello di approfondimento e vengono aggiornati periodicamente. Questo documento fa parte di tale serie come descritto nella sezione Sintesi di seguito.

### <a name="12-azure-platform"></a>1.2 Piattaforma Azure
Azure è una piattaforma aperta e flessibile di servizi cloud che supporta la più ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi. Ad esempio, è possibile:
- Eseguire contenitori Linux con l'integrazione Docker;
- Compilare app con JavaScript, Python, .NET, PHP, Java e Node.js e
- Compilare back-end per dispositivi iOS, Android e Windows.

Microsoft Azure supporta le stesse tecnologie che milioni di sviluppatori e professionisti IT considerano affidabili e usano già.

Quando si compilano asset IT o se ne esegue la migrazione in un provider di servizi di cloud pubblico, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli offerti per gestire la sicurezza degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare milioni di clienti contemporaneamente e fornisce alle aziende una solida base per poter soddisfare le esigenze di sicurezza. Azure offre anche un'ampia gamma di opzioni di sicurezza configurabili, con la possibilità di controllarle per poter personalizzare la sicurezza e soddisfare così i requisiti univoci di ogni distribuzione. Questo documento consente di soddisfare questi requisiti.

### <a name="13-abstract"></a>1.3 Sunto

Microsoft Azure consente di eseguire applicazioni e macchine virtuali (VM) in un'infrastruttura fisica condivisa. Uno dei motivi economici principali per l'esecuzione di applicazioni in un ambiente cloud è la possibilità di distribuire il costo delle risorse condivise tra più clienti. Questa pratica di multi-tenancy aumenta l'efficienza grazie al multiplexing delle risorse tra i diversi clienti a costi ridotti, ma introduce purtroppo i rischi correlati alla condivisione di server fisici e altre risorse dell'infrastruttura per l'esecuzione di applicazioni sensibili del cliente insieme a VM che possono appartenere a un utente qualsiasi, potenzialmente malintenzionato.

Questo articolo illustra l'isolamento dagli utenti malintenzionati e non malintenzionati offerto da Microsoft Azure e funge da guida per la progettazione di soluzioni cloud, con diverse opzioni di isolamento per gli architetti. Questo white paper è incentrato sulla tecnologia della piattaforma Azure e sui controlli di sicurezza per i clienti e non tratta contratti di servizio, modelli tariffari e attività DevOps.

## <a name="20-tenant-level-isolation"></a>2.0 Isolamento a livello di tenant
Uno dei vantaggi principali del cloud computing è il concetto di un'infrastruttura comune condivisa tra numerosi clienti contemporaneamente, determinando economie di scala. Questo concetto è denominato "multi-tenancy". Microsoft lavora costantemente per garantire che l'architettura multi-tenant dei servizi cloud di Microsoft Azure supporti gli standard di sicurezza, riservatezza, privacy, integrità e disponibilità.

Nell'area di lavoro abilitata per il cloud, un tenant può essere definito come un client o un'organizzazione che possiede e gestisce un'istanza specifica di tale servizio cloud. Con la piattaforma delle identità fornita da Microsoft Azure, un tenant è semplicemente un'istanza dedicata di Azure Active Directory (Azure AD) che l'organizzazione riceve e di cui diventa proprietaria quando effettua l'iscrizione a un servizio Microsoft Cloud.

Ogni directory di Azure AD è distinta e separata dalle altre directory di Azure AD. Proprio come un edificio di uffici è un asset sicuro specifico solo dell'organizzazione che vi ha sede, anche una directory di Azure AD è stata progettata per essere un asset sicuro usato solo dall'organizzazione proprietaria. L'architettura di Azure AD isola le informazioni relative all'identità e i dati dei clienti evitando che si combinino con altri. Questo significa che gli utenti e gli amministratori di una directory di Azure AD non possono accedere accidentalmente o con dolo ai dati presenti in un'altra directory.

### <a name="21-azure-tenancy"></a>2.1 Tenancy di Azure
Il concetto di tenancy di Azure (sottoscrizione di Azure) si riferisce a una relazione "cliente/fatturazione" e a un [tenant](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) univoco in [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). L'isolamento a livello di tenant in Microsoft Azure si ottiene usando Azure Active Directory e i [controlli in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) messi a disposizione da questa applicazione. Ogni sottoscrizione di Azure è associata a una directory di Azure Active Directory (AD).

Gli utenti, i gruppi e le applicazioni da tale directory possono gestire le risorse nella sottoscrizione di Azure. È possibile assegnare questi diritti di accesso tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API Gestione di Azure. Un tenant di Azure AD viene isolato in modo logico usando limiti di sicurezza in modo che nessun cliente possa accedere agli altri tenant e comprometterli, intenzionalmente o accidentalmente. Azure AD viene eseguito nei server "bare metal" isolati in un segmento di rete separato, in cui il filtraggio dei pacchetti a livello di host e Windows Firewall bloccano connessioni e traffico indesiderati.

- L'accesso ai dati in Azure AD richiede l'autenticazione degli utenti tramite un [servizio token di sicurezza](https://docs.microsoft.com/azure/app-service-web/web-sites-authentication-authorization). Le informazioni relative a esistenza, stato abilitato e ruolo dell'utente vengono usate dal sistema di autorizzazione per determinare se l'accesso richiesto al tenant di destinazione è autorizzato per l'utente corrente in questa sessione.

![Tenancy di Azure](./media/azure-isolation/azure-isolation-fig1.png)


- I tenant sono contenitori discreti che non hanno alcuna relazione tra loro.

- Non è possibile l'accesso da un tenant all'altro, a meno che l'amministratore non lo conceda tramite la federazione o il provisioning degli account utente da altri tenant.

- L'accesso fisico ai server che comprendono il servizio Azure AD e l'accesso diretto ai sistemi back-end di Azure AD sono limitati.

- Gli utenti di Azure AD non hanno accesso a posizioni o risorse fisiche e non possono quindi ignorare le verifiche dei criteri del controllo degli accessi in base al ruolo indicate di seguito.

Per esigenze di diagnostica e manutenzione, è necessario e viene usato un modello operativo che impiega un sistema di elevazione dei privilegi just-in-time. Azure AD Privileged Identity Management (PIM) introduce il concetto di amministratore idoneo. Gli [amministratori idonei](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) devono essere utenti che necessitano dell'accesso con privilegi in modo occasionale, non con cadenza quotidiana. Il ruolo è inattivo fino a quando l'utente che necessita dell'accesso non completa un processo di attivazione e diventa amministratore attivo per un periodo di tempo predeterminato.

![Gestione identità con privilegi di Azure AD](./media/azure-isolation/azure-isolation-fig2.png)

Azure Active Directory ospita ogni tenant nel relativo contenitore protetto, con i criteri e autorizzazioni per il contenitore e gli elementi al suo interno gestiti esclusivamente dal tenant che ne è anche proprietario.

Il concetto dei contenitori di tenant è profondamente radicato nel servizio directory a tutti i livelli, dai portali fino all'archivio permanente.

Anche quando i metadati di più tenant di Azure Active Directory vengono archiviati nello stesso disco fisico, non esistono relazioni tra i contenitori tranne quelle definite dal servizio directory, a sua volta determinato dall'amministratore tenant.

### <a name="22-azure-role-based-access-control-rbac"></a>2.2 Controllo degli accessi in base al ruolo di Azure (RBAC)
Il [controllo degli accessi in base al ruolo di Azure (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) consente di condividere i vari componenti disponibili all'interno di una sottoscrizione di Azure fornendo la gestione degli accessi con granularità fine per Azure. Il controllo degli accessi in base al ruolo di Azure consente di separare i compiti all'interno dell'organizzazione e di concedere l'accesso in base alle attività che i singoli utenti devono svolgere. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni.

Il Controllo degli accessi in base al ruolo di Azure include di tre ruoli di base che si applicano a tutti i tipi di risorsa:

- **Proprietario** ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.

- **Collaboratore** può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri utenti.

- **Lettore** può visualizzare le risorse di Azure esistenti.

![Controllo degli accessi in base al ruolo di Azure](./media/azure-isolation/azure-isolation-fig3.png)

Il resto dei ruoli RBAC in Azure consente la gestione di risorse di Azure specifiche. Ad esempio, il ruolo Collaboratore Macchina virtuale consente all'utente di creare e gestire macchine virtuali, ma non concede l'accesso alla rete virtuale di Azure o alla subnet a cui la macchina virtuale si connette.

[Ruoli predefiniti per il controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) elenca i ruoli disponibili in Azure. Specifica le operazioni e l'ambito che ogni ruolo predefinito concede agli utenti. Per definire ruoli personalizzati per un maggiore controllo, vedere come creare [ruoli personalizzati nel Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Altre funzionalità di Azure Active Directory includono:
- Azure AD abilita SSO nelle applicazioni SaaS, indipendentemente da dove vengono ospitate. Alcune applicazioni sono federate con Azure AD e altre usano SSO basato su password. Le applicazioni federate possono anche supportare il provisioning utenti e l'[insieme di credenziali delle password](https://www.techopedia.com/definition/31415/password-vault).

- L'accesso ai dati in [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) è controllato dall'autenticazione. Ogni account di archiviazione ha una chiave primaria ([chiave dell'account di archiviazione](https://docs.microsoft.com/azure/storage/storage-create-storage-account)) e una chiave privata secondaria (firma di accesso condiviso).

- Azure AD fornisce l'identità come servizio usando la federazione (con [Active Directory Federation Services](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs)), la sincronizzazione e la replica con le directory locali.

- [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) è il servizio di autenticazione a più fattori che richiede agli utenti di verificare l'accesso usando un'app mobile, una chiamata telefonica o un SMS. Può essere usato con Azure AD per la protezione delle risorse locali con il server Azure Multi-Factor Authentication e anche con applicazioni e directory personalizzate che usano l'SDK.

- [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) consente di aggiungere le macchine virtuali di Azure a un dominio di Active Directory senza distribuire controller di dominio. È possibile accedere a queste macchine virtuali con le credenziali di Active Directory aziendali e amministrare le macchine virtuali aggiunte a un dominio usando Criteri di gruppo per applicare le baseline della sicurezza in tutte le macchine virtuali di Azure.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) offre un servizio di gestione delle identità globale a disponibilità elevata per le applicazioni rivolte agli utenti, con scalabilità fino a centinaia di milioni di identità. Il servizio può essere integrato tra piattaforme mobili e Web. Gli utenti possono accedere a tutte le applicazioni attraverso esperienze personalizzabili usando gli account dei propri social network esistenti o creando credenziali.

### <a name="23-isolation-from-microsoft-administrators--data-deletion"></a>2.3 Isolamento da amministratori Microsoft ed eliminazione dei dati
Microsoft adotta misure sicure per proteggere i dati da accessi e usi impropri da parte di persone non autorizzate. Questi processi operativi e controlli sono supportati dalle [condizioni di Microsoft Online Services](http://aka.ms/Online-Services-Terms), che offrono impegni contrattuali che regolano l'accesso ai dati.

-    Gli esperti Microsoft non hanno l'accesso predefinito ai dati nel cloud. L'accesso viene loro concesso sotto supervisione e solo quando necessario. Viene anche attentamente controllato e registrato e revocato quando non è più necessario.

-    Microsoft può ricorrere ad altre società per la fornitura di servizi limitati per suo conto. Le società esterne possono accedere ai dati dei clienti solo per fornire i servizi dei quali sono state incaricate e non sono autorizzate a usare i dati per altri scopi. Sono anche vincolate per contratto a tutelare la riservatezza delle informazioni dei clienti.

I servizi aziendali con certificazioni sottoposte a auditing, ad esempio ISO/IEC 27001, vengono verificati regolarmente da Microsoft e da società di auditing accreditate tramite controlli a campione per attestare che l'accesso avvenga solo per scopi commerciali legittimi. Il cliente può accedere ai propri dati in qualsiasi momento e per qualunque motivo.

Se si eliminano dati, Microsoft Azure elimina i dati, comprese le eventuali copie memorizzate nella cache o di backup. Per i servizi nell'ambito, l'eliminazione verrà eseguita entro 90 giorni dal termine del periodo di conservazione. I servizi nell'ambito sono definiti nella sezione Condizioni per l'elaborazione dei dati delle [condizioni di Microsoft Online Services](http://aka.ms/Online-Services-Terms).

Se in un'unità disco usata per l'archiviazione si verifica un errore hardware, l'unità verrà [cancellata o distrutta](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) in modo sicuro prima che Microsoft la restituisca al produttore per la sostituzione o la riparazione. I dati presenti nell'unità verranno sovrascritti per garantire che non possano essere recuperati in alcun modo.

## <a name="30-compute-isolation"></a>3.0 Isolamento del calcolo
Microsoft Azure offre numerosi servizi di calcolo basati sul cloud che includono un'ampia gamma di istanze e servizi di calcolo con scalabilità automatica per soddisfare le esigenze dell'applicazione o dell'organizzazione. Tali istanze e servizi di calcolo offrono l'isolamento a più livelli per proteggere i dati senza sacrificare la flessibilità di configurazione richiesta dai clienti.

### <a name="31-hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>3.1 Isolamento Hyper-V e del sistema operativo radice tra VM radice e VM guest
La piattaforma di calcolo di Azure si basa sulla virtualizzazione dei computer, ovvero tutto il codice del cliente viene eseguito in una macchina virtuale Hyper-V. In ogni nodo o endpoint di rete di Azure è presente un hypervisor che viene eseguito direttamente sull'hardware e suddivide il nodo in un numero variabile di macchine virtuali guest.


![Isolamento Hyper-V e del sistema operativo radice tra VM radice e VM guest](./media/azure-isolation/azure-isolation-fig4.jpg)


Ogni nodo ha anche un'apposita VM radice che esegue il sistema operativo host. Una delimitazione critica è l'isolamento della VM radice dalle VM guest e delle VM guest l'una dall'altra, gestita dall'hypervisor e dal sistema operativo radice. L'associazione di hypervisor e sistema operativo radice sfrutta decenni di esperienza di Microsoft nella sicurezza dei sistemi operativi e l'esperienza più recente con Microsoft Hyper-V per offrire l'isolamento avanzato delle macchine virtuali guest.

La piattaforma Azure usa un ambiente virtualizzato. Le istanze utente funzionano come macchine virtuali autonome prive di accesso a un server host fisico e questo isolamento viene applicato usando i livelli di privilegi del processore (ring-0/ring-3) fisico.

Ring 0 è il livello con più privilegi e 3 quello con meno privilegi. Il sistema operativo guest viene eseguito in un livello Ring 1 con meno privilegi e le applicazioni vengono eseguite nel livello con privilegi minimi Ring 3. Questa virtualizzazione delle risorse fisiche porta a una netta separazione tra il sistema operativo guest e l'hypervisor, con un'ulteriore separazione della sicurezza tra i due.

L'hypervisor di Azure funge da micro-kernel e passa tutte le richieste di accesso all'hardware dalle macchine virtuali guest all'host per elaborarle usando un'interfaccia di memoria condivisa denominata VMBus. Questo impedisce agli utenti di ottenere l'accesso in lettura/scrittura/esecuzione non elaborato al sistema e riduce il rischio di condividere le risorse di sistema.

### <a name="32-advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>3.2 Algoritmo avanzato di selezione host per le VM e protezione da attacchi side channel
Gli attacchi tra VM prevedono due passaggi: posizionamento di una VM controllata da un antagonista nello stesso host delle VM bersaglio e quindi violazione del limite di isolamento per sottrarre informazioni sensibili alla vittima oppure comprometterne le prestazioni per guadagno o vandalismo. Microsoft Azure fornisce protezione in entrambi i passaggi usando un algoritmo avanzato di selezione host per le VM e la protezione da tutti gli attacchi side channel noti, incluse le VM "noisy neighbor".

### <a name="33-the-azure-fabric-controller"></a>3.3 Controller di infrastruttura di Azure
Il controller di infrastruttura di Azure è responsabile dell'allocazione delle risorse dell'infrastruttura nei carichi di lavoro dei tenant e gestisce le comunicazioni unidirezionali dall'host alle macchine virtuali. L'algoritmo di selezione host per le VM del controller di infrastruttura di Azure è estremamente sofisticato e quasi impossibile da prevedere a livello di host fisico.

![Controller di infrastruttura di Azure](./media/azure-isolation/azure-isolation-fig5.png)

L'hypervisor di Azure impone la separazione di memoria e processi tra le macchine virtuali e instrada in modo sicuro il traffico di rete ai tenant del sistema operativo guest. Si elimina così la possibilità di un attacco side channel a livello di VM.

In Azure, la macchina virtuale radice è particolare: esegue un sistema operativo con protezione avanzata denominato "sistema operativo radice" che ospita un agente dell'infrastruttura. Gli agenti dell'infrastruttura vengono usati loro volta per gestire gli agenti guest all'interno dei sistemi operativi guest nelle macchine virtuali dei clienti. Gestiscono anche i nodi di archiviazione.

L'insieme costituito da hypervisor di Azure, sistema operativo radice/agente dell'infrastruttura e VM del cliente/agenti guest comprende un nodo di calcolo. Gli agenti dell'infrastruttura sono gestiti da un controller di infrastruttura (FC) esterno ai nodi di calcolo e di archiviazione: i cluster di calcolo e di archiviazione vengono gestiti da controller di infrastruttura separati. Se un cliente aggiorna il file di configurazione dell'applicazione mentre questa è in esecuzione, il controller di infrastruttura comunica con l'agente dell'infrastruttura, il quale contatta gli agenti guest che a loro volta segnalano la modifica della configurazione all'applicazione. In caso di errore hardware, il controller di infrastruttura troverà automaticamente hardware disponibile nel quale riavvierà la macchina virtuale.

![Controller di infrastruttura di Azure](./media/azure-isolation/azure-isolation-fig6.jpg)

La comunicazione tra un controller di infrastruttura e un agente è unidirezionale. L'agente implementa un servizio protetto da SSL che risponda solo alle richieste del controller. Non può stabilire connessioni con il controller o altri nodi interni con privilegi. Il controller di infrastruttura considera tutte le risposte come se fossero non attendibili.


![Controller di infrastruttura](./media/azure-isolation/azure-isolation-fig7.png)

L'isolamento si estende dalla VM radice alle VM guest e tra le VM guest. Anche i nodi di calcolo sono isolati dai nodi di archiviazione per aumentare la protezione.


L'hypervisor e il sistema operativo host forniscono filtri dei pacchetti di rete per assicurare che le macchine virtuali non attendibili non possano generare traffico falsificato o ricevere il traffico non indirizzato a loro, indirizzare il traffico a endpoint protetti dell'infrastruttura o inviare/ricevere traffico broadcast inappropriato.


### <a name="34-additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>3.4 Regole aggiuntive configurate dall'agente controller di infrastruttura per isolare la VM
Per impostazione predefinita, tutto il traffico viene bloccato quando viene creata una macchina virtuale e quindi l'agente controller di infrastruttura configura il filtro dei pacchetti per aggiungere regole ed eccezioni che consentono il traffico autorizzato.

Sono previste due categorie di regole:

-    **Regole di configurazione macchina virtuale o di infrastruttura**: per impostazione predefinita, vengono bloccate tutte le comunicazioni. Alcune eccezioni consentono a una macchina virtuale di inviare e ricevere il traffico DHCP e DNS. Le macchine virtuali possono anche inviare il traffico a Internet "pubblico" e ad altre macchine virtuali nella stessa rete virtuale di Azure e nel server di attivazione del sistema operativo. L'elenco di destinazioni in uscita consentite delle macchine virtuali non include subnet di router di Azure, la gestione di Azure e altre proprietà Microsoft.

-    **File di configurazione dei ruoli**: definisce gli elenchi di controllo di accesso (ACL) in ingresso in base al modello di servizio del tenant.

### <a name="35-vlan-isolation"></a>3.5 Isolamento VLAN
In ogni cluster sono presenti tre VLAN:

![Isolamento VLAN](./media/azure-isolation/azure-isolation-fig8.jpg)


-    VLAN principale: crea l'interconnessione tra i nodi non attendibili del cliente

-    VLAN del controller di infrastruttura (FC): contiene controller di infrastruttura attendibili e sistemi di supporto

-    VLAN dei dispositivi: contiene dispositivi di rete attendibili e altri dispositivi dell'infrastruttura

La comunicazione è consentita dalla VLAN del controller di infrastruttura alla VLAN principale, ma non dalla VLAN principale alla VLAN del controller di infrastruttura. La comunicazione è anche bloccata dalla VLAN principale alla VLAN dei dispositivi. Questo assicura che anche se un nodo che esegue il codice del cliente è compromesso, non potrà attaccare nodi sulla VLAN dei dispositivi o sulla VLAN del controller di infrastruttura.

## <a name="40-storage-isolation"></a>4.0 Isolamento dell'archiviazione
### <a name="41-logical-isolation-between-compute-and-storage"></a>4.1 Isolamento logico tra calcolo e archiviazione
Nell'ambito della sua progettazione fondamentale, Microsoft Azure separa il calcolo basato sulle VM dall'archiviazione. Questa separazione consente la scalabilità indipendente di calcolo e archiviazione, semplificando l'uso di multi-tenancy e isolamento.

Il servizio Archiviazione di Azure viene quindi eseguito in hardware separato senza alcuna connettività di rete ai servizi di calcolo di Azure, ad eccezione di quella logica. [Questo](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf) significa che quando viene creato un disco virtuale, lo spazio sul disco non viene allocato per l'intera capacità. Viene invece creata una tabella che associa gli indirizzi nel disco virtuale ad aree del disco fisico. Questa tabella è inizialmente vuota. **La prima volta che un cliente scrive dati nel disco virtuale, viene allocato spazio sul disco fisico e il relativo puntatore viene inserito nella tabella.**
### <a name="42-isolation-using-storage-access-control"></a>4.2 Isolamento tramite il controllo di accesso per l'archiviazione
Il **controllo di accesso in Archiviazione di Azure** ha un modello semplice. Ogni sottoscrizione di Azure può creare uno o più account di archiviazione. Ogni account di archiviazione ha un'unica chiave privata usata per controllare l'accesso a tutti i dati presenti nell'account di archiviazione.

![Isolamento tramite il controllo di accesso per l'archiviazione](./media/azure-isolation/azure-isolation-fig9.png)

L'**accesso ai dati di Archiviazione di Azure, incluse le tabelle**, può essere controllato in un token di [firma di accesso condiviso](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) che concede l'accesso con ambito. La firma di accesso condiviso viene creata tramite un modello di query (URL) firmato con la [chiave dell'account di archiviazione](https://msdn.microsoft.com/library/azure/ee460785.aspx). L'[URL firmato](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) può essere assegnato, ovvero delegato, a un altro processo che può quindi compilare i dettagli della query ed effettuare la richiesta del servizio di archiviazione. Una firma di accesso condiviso consente di concedere l'accesso con scadenza ai client senza rivelare la chiave privata dell'account di archiviazione.

La firma di accesso condiviso consente di concedere a un client autorizzazioni limitate per oggetti nell'account di archiviazione per un periodo di tempo specificato e con un set di autorizzazioni. È possibile concedere queste autorizzazioni limitate senza dover condividere le chiavi di accesso all'account.

### <a name="43-ip-level-storage-isolation"></a>4.3 Isolamento dell'archiviazione a livello IP
È possibile stabilire firewall e definire un intervallo di indirizzi IP per i client attendibili. Con un intervallo di indirizzi IP, solo i client con indirizzo IP compreso nell'intervallo definito possono connettersi ad [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-security-guide).

I dati di archiviazione IP possono essere protetti da utenti non autorizzati tramite un meccanismo di rete che viene usato per allocare un tunnel di traffico dedicato per l'archiviazione IP.

### <a name="44-encryption"></a>4.4 Crittografia
Azure offre i tipi di crittografia seguenti per proteggere i dati:
-    Crittografia in transito

-    Crittografia di dati inattivi

#### <a name="441-encryption-in-transit"></a>4.4.1 Crittografia in transito
La crittografia in transito è un meccanismo di protezione dei dati durante la trasmissione tra le reti. Con Archiviazione di Azure è possibile proteggere i dati con:

-    [Crittografia a livello di trasporto](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), ad esempio HTTPS quando si trasferiscono dati all'interno o all'esterno di Archiviazione di Azure.

-    [Crittografia di rete](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), ad esempio la crittografia SMB 3.0 per le condivisioni file di Azure.

-    [Crittografia lato client](https://docs.microsoft.com/azure/storage/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), per crittografare i dati prima che siano trasferiti nella risorsa di archiviazione e decrittografarli dopo il trasferimento dalla risorsa di archiviazione.

#### <a name="442-encryption-at-rest"></a>4.4.2 Crittografia di dati inattivi
Per molte organizzazioni, [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è un passaggio obbligatorio per assicurare la privacy dei dati, la conformità e la sovranità dei dati. Esistono tre funzionalità di Azure che consentono di crittografare dati inattivi:

-    [Crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-at-rest) consente di richiedere che il servizio di archiviazione crittografi automaticamente i dati durante la scrittura in Archiviazione di Azure.

-    [Client-side Encryption](https://docs.microsoft.com/azure/storage/storage-security-guide#client-side-encryption) offre anche la funzionalità di crittografia dei dati inattivi.

-    [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) consente di crittografare i dischi dati e del sistema operativo usati da una macchina virtuale IaaS.

#### <a name="443-azure-disk-encryption"></a>4.4.3 Crittografia dischi di Azure
[Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) per le macchine virtuali consente di soddisfare i requisiti di conformità e sicurezza dell'organizzazione, grazie alla possibilità di crittografare i dischi delle macchine virtuali, inclusi i dischi di avvio e di dati, con chiavi e criteri gestiti in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

La soluzione Crittografia dischi per Windows è basata su [Crittografia unità BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Microsoft e la soluzione Linux è basata su [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

La soluzione supporta gli scenari seguenti per le macchine virtuali IaaS, se abilitati in Microsoft Azure:
-    Integrazione dell'insieme di credenziali delle chiavi di Azure.

-    Macchine virtuali di livello Standard: serie A, D, DS, G, GS e così via per VM IaaS

-    Abilitazione della crittografia nelle macchine virtuali IaaS Windows e Linux

-    Disabilitazione della crittografia nel sistema operativo e nelle unità dati per le VM IaaS Windows

-    Disabilitazione della crittografia nelle unità dati per le macchine virtuali IaaS Linux

-    Abilitazione della crittografia in macchine virtuali IaaS in esecuzione nel sistema operativo client Windows

-    Abilitazione della crittografia su volumi con percorsi di montaggio

-    Abilitazione della crittografia nelle macchine virtuali Linux configurate con striping del disco (RAID) tramite [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-    Abilitazione della crittografia nelle macchine virtuali Linux usando [LVM (Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) per i dischi di dati

-    Abilitazione della crittografia nelle macchine virtuali Windows configurate usando spazi di archiviazione

-    Sono supportate tutte le aree geografiche pubbliche di Azure

La soluzione non supporta gli scenari, le funzionalità e la tecnologia seguenti in questa versione:

-    VM IaaS del piano Basic

-    Disabilitazione della crittografia in un'unità del sistema operativo per le VM IaaS Linux

-    Macchine virtuali IaaS create usando il metodo di creazione classico per le macchine virtuali

-    Integrazione con il servizio di gestione delle chiavi locale.

-    File di Azure (file system condiviso), file system di rete (NFS, Network File System), volumi dinamici e macchine virtuali Windows configurate con sistemi RAID basati su software

## <a name="50-sql-azure-database-isolation"></a>5.0 Isolamento del database SQL di Azure
Il database SQL è un servizio di database relazionale sul cloud Microsoft basato sul motore Microsoft SQL Server, leader di mercato, e in grado di gestire carichi di lavoro di importanza strategica. Il database SQL di Azure offre isolamento prevedibile dei dati a livello di account, in base all'area geografica e alla rete, con esigenze di amministrazione quasi nulle.

### <a name="51-sql-azure-application-model"></a>5.1 Modello applicativo di SQL Azure

Il database di [Microsoft SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started) è un servizio di database relazionale basato sul cloud che si avvale delle tecnologie SQL Server. Fornisce un servizio di database a disponibilità elevata, scalabile e multi-tenant ospitato da Microsoft nel cloud.

Dal punto di vista dell'applicazione, SQL Azure fornisce la gerarchia seguente. Ogni livello ha una relazione di contenimento uno-a-molti per questi livelli.

![Modello applicativo di SQL Azure](./media/azure-isolation/azure-isolation-fig10.png)

Account e sottoscrizione sono concetti della piattaforma Microsoft Azure per associare fatturazione e gestione.

Database e server logici sono concetti specifici di SQL Azure e vengono gestiti tramite SQL Azure, con le interfacce OData e TSQL fornite oppure tramite il portale SQL Azure integrato nel portale di Azure.

I server SQL Azure non sono istanze di macchine virtuali o fisiche, ma sono raccolte di database, con criteri di sicurezza e gestione condivisi, archiviate nel cosiddetto database "master logico".

![SQL Azure](./media/azure-isolation/azure-isolation-fig11.png)

I database master logici includono:

-    Account di accesso SQL usati per connettersi al server

-    Regole del firewall

Le informazioni sulla fatturazione e l'uso per i database SQL di Azure dello stesso server logico non si trovano necessariamente nella stessa istanza fisica del cluster di SQL Azure, mentre le applicazioni devono fornire il nome del database di destinazione durante la connessione.

Dal punto di vista del cliente, un server logico viene creato in un'area geografica, mentre la creazione effettiva del server viene eseguita in uno dei cluster disponibili nell'area.

### <a name="52-isolation-through-network-topology"></a>5.2 Isolamento tramite la topologia della rete

Quando viene creato un server logico e viene registrato il relativo nome DNS, questo nome punta all'indirizzo denominato "Gateway VIP" nel data center in cui si trova il server.

Dietro l'indirizzo VIP (indirizzo IP virtuale) è presente una raccolta di servizi di gateway senza stato. In generale, i gateway vengono coinvolti quando è necessario coordinare più origini dati, ovvero database master, database utente e così via. I servizi gateway implementano gli elementi seguenti:
-    **Inoltro dei dati tramite connessione TDS.** Comprende l'identificazione del database utente nel cluster back-end, l'implementazione della sequenza di accesso e quindi l'inoltro dei pacchetti TDS al back-end e viceversa.

-    **Gestione database.** Comprende l'implementazione di una raccolta di flussi di lavoro per eseguire operazioni di database CREATE/ALTER/DROP. Le operazioni di database possono essere richiamate analizzando pacchetti TDS o API OData esplicite.

-    Operazioni CREATE/ALTER/DROP per account di accesso/utenti

-    Operazioni di gestione di server logici tramite API OData

![Isolamento tramite la topologia della rete](./media/azure-isolation/azure-isolation-fig12.png)

Il livello dietro il gateway è denominato "back-end". Qui vengono archiviati tutti i dati in una modalità a disponibilità elevata. Ogni dato appartiene a una partizione o unità di failover, ognuna con almeno tre repliche. Le repliche vengono archiviate e replicate dal motore di SQL Server e gestite da un sistema di failover, noto anche come "infrastruttura".

In genere, il sistema back-end non comunica in uscita con altri sistemi come misura di sicurezza. Questo tipo di comunicazione è riservata ai sistemi nel livello front-end (gateway). Le macchine di livello gateway hanno privilegi limitati per le macchine back-end per ridurre al minimo la superficie di attacco come meccanismo di difesa avanzata.

### <a name="53-isolation-by-machine-function-and-access"></a>5.3 Isolamento in base all'accesso e alla funzione della macchina
SQL Azure è composto da servizi in esecuzione in funzioni differenti della macchina. È diviso in ambiente database cloud "back-end" e ambiente "front-end" (gateway/gestione), con il principio generale secondo cui il traffico è diretto al back-end e non all'esterno. L'ambiente front-end può comunicare con altri servizi esterni e in generale ha solo autorizzazioni limitate nel back-end, sufficienti a chiamare i punti di ingresso necessari.

## <a name="60-networking-isolation"></a>6.0 Isolamento della rete
La distribuzione di Azure offre più livelli di isolamento della rete. Il diagramma seguente mostra i vari livelli di isolamento della rete che Azure offre ai clienti. Questi livelli sono costituiti sia da funzionalità native della piattaforma Azure, sia da funzionalità definite dal cliente. Per il traffico in ingresso da Internet, Azure DDoS fornisce l'isolamento da attacchi su larga scala contro Azure. Il livello di isolamento successivo è costituito da indirizzi IP pubblici (endpoint) definiti dall'utente, usati per determinare il traffico che può passare alla rete virtuale attraverso il servizio cloud. L'isolamento nativo della rete virtuale di Azure assicura l'isolamento completo da tutte le altre reti e il flusso di traffico solo tramite percorsi e metodi configurati dall'utente. Questi percorsi e metodi costituiscono il livello successivo, in cui è possibile usare NSG, UDR e appliance virtuali di rete per creare limiti di isolamento e proteggere le distribuzioni delle applicazioni nella rete protetta.

![Isolamento della rete](./media/azure-isolation/azure-isolation-fig13.png)

**Isolamento del traffico**: una [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) è il limite di isolamento del traffico nella piattaforma Azure. Le macchine virtuali (VM) in una rete virtuale non possono comunicare direttamente con le VM in una rete virtuale diversa, anche se entrambe le reti virtuali vengono create dallo stesso cliente. L'isolamento è una proprietà essenziale che assicura che le macchine virtuali e le comunicazioni dei clienti rimangano private entro una rete virtuale.

La [subnet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#subnets) offre un livello di isolamento aggiuntivo nella rete virtuale in base a un intervallo di indirizzi IP. È possibile suddividere la rete virtuale in più subnet per una maggiore organizzazione e sicurezza. Le VM e le istanze del ruolo PaaS distribuite nelle subnet (nella stessa o in diverse) in una rete virtuale possono comunicare tra loro senza nessuna configurazione aggiuntiva. È anche possibile configurare un [gruppo di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview#network-security-groups-nsg) per consentire o negare il traffico di rete verso un'istanza di macchina virtuale in base alle regole configurate nell'elenco di controllo di accesso (ACL) del gruppo di sicurezza di rete. I gruppi di sicurezza di rete possono essere associati a subnet o singole istanze VM in una subnet. Quando un gruppo di sicurezza di rete viene associato a una subnet, le regole ACL si applicano a tutte le istanze di VM in tale subnet.

## <a name="next-steps"></a>Passaggi successivi

- [Opzioni di isolamento della rete per le macchine nelle reti virtuali di Microsoft Azure](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/)

Comprendono il tipico scenario di front-end e back-end in cui le macchine di una particolare rete back-end o subnet possono consentire solo ad alcuni client o altri computer di connettersi a un endpoint specifico in base a un elenco di indirizzi IP consentiti.

- [Isolamento del calcolo](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure offre numerosi servizi di calcolo basati sul cloud che includono un'ampia gamma di istanze e servizi di calcolo con scalabilità automatica per soddisfare le esigenze dell'applicazione o dell'organizzazione.

- [Isolamento dell'archiviazione](https://msenterprise.global.ssl.fastly.net/vnext/PDFs/A01_AzureSecurityWhitepaper20160415c.pdf)

Microsoft Azure separa tra loro il calcolo e l'archiviazione basati sulle VM. Questa separazione consente la scalabilità indipendente di calcolo e archiviazione, semplificando l'uso di multi-tenancy e isolamento. Il servizio Archiviazione di Azure viene quindi eseguito in hardware separato senza alcuna connettività di rete ai servizi di calcolo di Azure, ad eccezione di quella logica. Tutte le richieste vengono eseguite tramite HTTP o HTTPS in base alla scelta del cliente.


