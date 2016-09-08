<properties
   pageTitle="Gestione della sicurezza in Azure | Microsoft Azure"
   description=" Questo articolo illustra i passaggi per il miglioramento della gestione remota della sicurezza durante l'amministrazione degli ambienti di Microsoft Azure, inclusi Servizi cloud, Macchine virtuali e applicazioni personalizzate."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="terrylan"/>

# Gestione della sicurezza in Azure

I sottoscrittori di Azure possono gestire i propri ambienti cloud da più dispositivi, tra cui workstation di gestione, PC per sviluppatori e dispositivi di utenti finali con privilegi elevati con autorizzazioni specifiche per le attività. In alcuni casi le funzioni amministrative vengono eseguite tramite console basate sul Web, ad esempio il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). In altri casi è possibile che vengano usate connessioni dirette ad Azure da sistemi locali su reti private virtuali (VPN), Servizi terminal, protocolli applicativi client oppure, a livello di codice, l'API Gestione dei servizi di Azure (SMAPI). Gli endpoint client possono essere inoltre aggiunti a un dominio o isolati e non gestiti, ad esempio tablet o smartphone.

Anche se le funzionalità multiple di accesso e gestione offrono una vasta gamma di opzioni, questa variabilità può aggiungere rischi significativi a una distribuzione cloud, complicando la gestione, la verifica e il controllo delle azioni amministrative. Questa variabilità potrebbe introdurre anche minacce alla sicurezza tramite accesso non regolamentato agli endpoint client usati per la gestione dei servizi cloud. L'uso di workstation generiche o personali per lo sviluppo e la gestione dell'infrastruttura genera vettori di minaccia imprevedibili, come l'esplorazione del Web (ad esempio attacchi di tipo watering hole) o la posta elettronica (ad esempio "ingegneria sociale" e phishing).

![][1]

Il rischio di attacco aumenta in questo tipo di ambiente perché risulta difficile creare criteri e meccanismi di sicurezza per la gestione appropriata dell'accesso alle interfacce di Azure, ad esempio SMAPI, da endpoint molto diversi.

### Minacce relative alla gestione remota

Gli utenti malintenzionati provano spesso a ottenere l'accesso con privilegi mediante la compromissione delle credenziali dell'account, ad esempio tramite attacchi di forza bruta alle password, phishing e recupero di credenziali, oppure inducendo gli utenti a eseguire codice dannoso, ad esempio da siti Web dannosi con download inconsapevoli o da allegati dannosi in messaggi di posta elettronica. In un ambiente cloud con gestione remota le violazioni degli account possono comportare un aumento del rischio a causa dell'accesso da qualsiasi posizione e in qualsiasi momento.

Anche se sono disponibili controlli rigorosi sugli account amministratore principali, gli account degli utenti a livelli inferiori possono essere usati per sfruttare i punti deboli della strategia di sicurezza. Anche una formazione insufficiente a livello di sicurezza può provocare violazioni tramite diffusione o esposizione accidentale delle informazioni sull'account.

Quando la workstation utente viene usata anche per attività amministrative, è possibile che venga compromessa in molti punti diversi, ad esempio quando un utente esplora il Web, usa strumenti open source o di terze parti oppure apre un documento dannoso contenente un trojan.

In genere, la maggior parte degli attacchi specifici che generano violazioni dei dati può essere ricondotta a exploit del browser, plug-in (ad esempio Flash, PDF, Java) e spear phishing (posta elettronica) nei computer desktop. È possibile che questi computer abbiano autorizzazioni a livello amministrativo o di servizio per l'accesso a server attivi o a dispositivi di rete per operazioni in caso di uso per la distribuzione o la gestione di altre risorse.

### Concetti fondamentali sulla sicurezza operativa

Per ottenere una gestione e operazioni più sicure, è possibile ridurre al minimo la superficie di attacco di un client riducendo il numero di punti di ingresso possibili. Per ottenere questo risultato, è possibile usare le entità di sicurezza di tipo "separazione di compiti" e "separazione di ambienti".

L'isolamento delle diverse funzioni riservate consente di ridurre la probabilità che un errore a un livello provochi una violazione in un altro. Esempi:

- Le attività amministrative non devono quindi essere combinate con attività che potrebbero generare compromissioni, ad esempio malware in un messaggio di posta elettronica dell'amministratore che infetta un server di infrastruttura.
- Una workstation usata per operazioni con riservatezza elevata non deve essere lo stesso sistema usato per finalità ad alto rischio, ad esempio l'esplorazione su Internet.

Ridurre la superficie di attacco di un sistema tramite la rimozione di software non necessario. Esempio:

- Una workstation standard per l'amministrazione, il supporto o la distribuzione non necessita dell'installazione di un client di posta elettronica o di altre applicazioni per la produttività se la finalità principale del dispositivo consiste nella gestione dei servizi cloud.

Ai sistemi client con accesso di amministratore ai componenti dell'infrastruttura è consigliabile applicare i criteri più rigorosi possibile per ridurre i rischi alla sicurezza. Esempi:

- I criteri di sicurezza possono includere le impostazioni di Criteri di gruppo che negano l'accesso aperto a Internet dal dispositivo e l'uso di una configurazione restrittiva per il firewall.
- Usare VPN IPsec (Internet Protocol Security) se l'accesso diretto è necessario.
- Configurare domini di Active Directory separati per la gestione e lo sviluppo.
- Isolare e filtrare il traffico di rete della workstation di gestione.
- Usare software antimalware.
- Implementare l'autenticazione a più fattori per ridurre il rischio di furto delle credenziali.

Il consolidamento delle risorse di accesso e l'eliminazione degli endpoint non gestiti consentono anche di semplificare le attività di gestione.


### Sicurezza per la gestione remota di Azure

Azure offre meccanismi di sicurezza utili per gli amministratori che gestiscono servizi cloud e macchine virtuali di Azure, ad esempio:

- Autenticazione e [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md).
- Monitoraggio, registrazione e controllo.
- Certificati e comunicazioni crittografate.
- Portale di gestione Web.
- Filtri dei pacchetti di rete.

Oltre alla configurazione della sicurezza lato client e alla distribuzione a data center di un gateway di gestione, è possibile limitare e monitorare l'accesso amministrativo alle applicazioni e ai dati sul cloud.

> [AZURE.NOTE] Alcune indicazioni incluse in questo articolo potrebbero comportare un maggiore utilizzo delle risorse di dati, rete o calcolo e un aumento dei costi di licenza o di sottoscrizione.

## Workstation con protezione avanzata per la gestione

L'obiettivo dell'applicazione della protezione avanzata a una workstation consiste nell'eliminare tutte le funzioni, tranne quelle assolutamente essenziali, necessarie per il funzionamento, riducendo al massimo la potenziale superficie di attacco. L'applicazione della protezione avanzata al sistema include la riduzione al minimo del numero di applicazioni e servizi installati, la limitazione dell'esecuzione delle applicazioni, la limitazione dell'accesso di rete solo agli elementi necessari e l'aggiornamento continuo del sistema. L'uso di una workstation con protezione avanzata per la gestione consente anche di separare gli strumenti amministrativi da altre attività dell'utente finale.

In un ambiente aziendale locale è possibile limitare la superficie di attacco dell'infrastruttura fisica tramite reti di gestione dedicate, locali server con accesso controllato da schede e workstation in esecuzione in aree protette della rete. In un modello IT cloud o ibrido l'uso appropriato dei servizi di gestione sicuri può risultare più complesso a causa dell'assenza di accesso fisico alle risorse IT. L'implementazione delle soluzioni di protezione richiede una configurazione attenta del software, processi incentrati sulla sicurezza e criteri completi.

L'uso di un footprint software ridotto al minimo con privilegi minimi in una workstation bloccata per la gestione cloud, oltre che per lo sviluppo di applicazioni, può ridurre il rischio di incidenti relativi alla sicurezza mediante la standardizzazione degli ambienti di gestione remota e di sviluppo. Una configurazione della workstation con protezione avanzata può consentire di evitare la compromissione degli account usati per gestire risorse cloud essenziali, chiudendo molti percorsi comuni usati da malware ed exploit. In particolare, è possibile usare [Windows AppLocker](http://technet.microsoft.com/library/dd759117.aspx) e la tecnologia Hyper-V per controllare e isolare il comportamento del sistema client e ridurre al minimo le minacce, incluse quelle relative a posta elettronica o esplorazione su Internet.

In una workstation con protezione avanzata l'amministratore esegue un account utente standard, che blocca l'esecuzione a livello amministrativo, e le applicazioni associate sono controllate da un elenco di applicazioni consentite. Ecco gli elementi di base di una workstation con protezione avanzata:

- Analisi attiva e applicazione di patch. È possibile distribuire software antimalware, eseguire analisi di vulnerabilità regolari e aggiornare tutte le workstation usando gli aggiornamenti più recenti per la sicurezza in modo tempestivo.
- Funzionalità limitata. È possibile disinstallare le applicazioni non necessarie e disabilitare i servizi non necessari (avvio).
- Protezione avanzata della rete. È possibile usare le regole di Windows Firewall per consentire solo gli indirizzi IP, le porte e gli URL validi correlati alla gestione di Azure. Assicurarsi che le connessioni remote in arrivo alla workstation siano bloccate.
- Restrizione dell'esecuzione. È possibile consentire solo un set di file eseguibili predefiniti necessari per l'esecuzione della gestione. Questo approccio è definito "rifiuto predefinito". Per impostazione predefinita, gli utenti non devono essere autorizzati a eseguire alcun programma, a meno che non sia definito esplicitamente nell'elenco Consenti.
- Privilegi minimi. Gli utenti delle workstation di gestione non devono avere privilegi amministrativi nel computer locale stesso. In questo modo non potranno modificare la configurazione del sistema o i file di sistema, in modo intenzionale o accidentale.

È possibile applicare queste restrizioni usando [Oggetti Criteri di gruppo](https://www.microsoft.com/download/details.aspx?id=2612) in Servizi di dominio Active Directory e applicandoli nel dominio di gestione (locale) a tutti gli account di gestione.

### Gestione di servizi, applicazioni e dati

La configurazione dei servizi cloud di Azure viene eseguita mediante il portale di Azure o SMAPI, tramite l'interfaccia da riga di comando di Windows PowerShell o un'applicazione personalizzata che sfrutta queste interfacce RESTful. I servizi che usano questi meccanismi includono Azure Active Directory (Azure AD), Archiviazione di Azure, Siti Web di Azure, Rete virtuale di Azure e altri ancora.

Le applicazioni distribuite in macchine virtuali forniscono strumenti e interfacce client specifiche in base alla necessità, ad esempio Microsoft Management Console (MMC), una console di gestione aziendale, ad esempio Microsoft System Center o Windows Intune, oppure un'altra applicazione di gestione, ad esempio Microsoft SQL Server Management Studio. Questi strumenti si trovano in genere in un ambiente aziendale o in una rete client. Possono dipendere da protocolli di rete specifici, ad esempio Remote Desktop Protocol (RDP), che richiedono connessioni dirette e con stato. Alcuni strumenti possono avere interfacce abilitate per il Web, che non devono essere pubblicate apertamente o accessibili tramite Internet.

È possibile limitare l'accesso alla gestione dell'infrastruttura e dei servizi della piattaforma in Azure usando l'[autenticazione a più fattori](../multi-factor-authentication/multi-factor-authentication.md), i [certificati di gestione X.509](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/) e le regole del firewall. Il portale di Azure e SMAPI richiedono il protocollo TLS (Transport Layer Security). I servizi e le applicazioni distribuiti in Azure richiedono tuttavia misure di protezione appropriate in base all'applicazione. Questi meccanismi possono essere spesso abilitati con maggiore facilità tramite una configurazione di workstation con protezione avanzata standardizzata.

### Gateway di gestione

Per centralizzare tutto l'accesso amministrativo e semplificare il monitoraggio e la registrazione, è possibile distribuire un server [Gateway Desktop remoto ](https://technet.microsoft.com/library/dd560672) dedicato nella rete locale, connesso all'ambiente Azure.

Un Gateway Desktop remoto è un servizio proxy RDP basato su criteri che applica i requisiti relativi alla sicurezza. L'implementazione di un Gateway Desktop remoto insieme a Protezione accesso alla rete di Windows Server consentono di assicurare che solo i client che soddisfano criteri di integrità specifici definiti dagli oggetti Criteri di gruppo di Servizi di dominio Active Directory riescano a connettersi. Eseguire anche queste operazioni:

- Effettuare il provisioning di un [certificato di gestione di Azure](http://msdn.microsoft.com/library/azure/gg551722.aspx) sul Gateway Desktop remoto in modo che sia l'unico host autorizzato ad accedere al portale di gestione di Azure.
- Aggiungere il Gateway Desktop remoto allo stesso [dominio di gestione](http://technet.microsoft.com/library/bb727085.aspx) delle workstation dell'amministratore. Questa operazione è necessaria quando si usa una VPN IPsec da sito a sito o ExpressRoute in un dominio con un trust unidirezionale verso Azure AD oppure se si esegue la federazione di credenziali tra l'istanza locale di Servizi di dominio Active Directory e Azure AD.
- Configurare un [criterio di autorizzazione di connessione client](http://technet.microsoft.com/library/cc753324.aspx) per consentire al Gateway Desktop remoto di verificare che il nome del computer client sia valido (aggiunto al dominio) e che sia autorizzato ad accedere al portale di gestione di Azure.
- Usare IPsec per la [VPN di Azure](https://azure.microsoft.com/documentation/services/vpn-gateway/) per una maggiore protezione del traffico di gestione da intercettazioni e furto di token oppure prendere in considerazione un collegamento Internet isolato tramite [Azure ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
- Abilitare l'autenticazione a più fattori, tramite [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md), oppure l'autenticazione tramite smart card per gli amministratori che accedono tramite il Gateway Desktop remoto.
- Configurare [restrizioni per l'indirizzo IP](http://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) di origine o [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) in Azure per ridurre al minimo il numero di endpoint di gestione consentiti.

## Linee guida sulla sicurezza

In genere, la configurazione della sicurezza delle workstation dell'amministratore per l'uso con il cloud è molto simile alle procedure usate per qualsiasi workstation locale, ad esempio compilazione ridotta al minimo e autorizzazioni restrittive. Alcuni aspetti unici della gestione cloud sono più simili alla gestione remota o alla gestione aziendale fuori banda, ad esempio l'uso e il controllo delle credenziali, l'accesso remoto con sicurezza avanzata e il rilevamento e la risposta alle minacce.

### Autenticazione

È possibile usare le restrizioni di accesso di Azure per impedire agli indirizzi IP di origine di accedere agli strumenti amministrativi e controllare le richieste di accesso. Per semplificare l'identificazione da parte di Azure dei client di gestione (workstation e/o applicazioni), è possibile configurare SMAPI, tramite strumenti sviluppati dai clienti come i cmdlet di Windows PowerShell, e il portale di gestione di Azure per richiedere l'installazione di certificati di gestione lato client, oltre ai certificati SSL. È anche consigliabile che l'accesso amministratore richieda l'autenticazione a più fattori.

Alcune applicazioni o alcuni servizi distribuiti in Azure possono avere meccanismi di autenticazione specifici per l'accesso degli utenti finali e degli amministratori, mentre altri sfruttano tutte le funzionalità di Azure AD. Indipendentemente dalla federazione di credenziali tramite Active Directory Federation Services (AD FS), dall'uso della sincronizzazione delle directory o dalla gestione degli account utente esclusivamente sul cloud, l'uso di [Microsoft Identity Manager](https://technet.microsoft.com/library/mt218776.aspx), incluso in Azure AD Premium, semplifica la gestione dei cicli di vita delle identità tra le risorse.

### Connettività

Sono disponibili alcuni meccanismi per semplificare la protezione delle connessioni client nelle reti virtuali di Azure. Due di questi meccanismi, [VPN da sito a sito](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) e [VPN da punto a sito](../vpn-gateway/vpn-gateway-point-to-site-create.md) (P2S), consentono l'uso del protocollo IPsec standard di settore (S2S) o del protocollo [SSTP (Secure Socket Tunneling Protocol)](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) (P2S) per la crittografia e il tunneling. Quando si connette alla gestione di servizi di Azure per il pubblico, ad esempio il portale di gestione di Azure, Azure richiede il protocollo HTTPS (Hypertext Transfer Protocol Secure).

Una workstation autonoma con protezione avanzata che non si connette ad Azure tramite un Gateway Desktop remoto deve usare la VPN da punto a sito basata su SSTP per creare la connessione iniziale alla rete virtuale di Azure e quindi deve stabilire una connessione RDP alle singole macchine virtuali dal tunnel VPN.

### Confronto tra il controllo della gestione e l'applicazione dei criteri

In genere sono disponibili due approcci per semplificare la protezione dei processi di gestione, ovvero il controllo e l'applicazione dei criteri. L'uso di entrambi gli approcci offrirà controlli completi, ma potrebbe non essere consentito in tutte le situazioni. Ogni approccio presenta inoltre livelli di rischio, costi e impegno diversi associati alla gestione della sicurezza, in particolare per quanto riguarda il livello di attendibilità attribuito ai singoli utenti e alle architetture di sistema.

Il monitoraggio, la registrazione e il controllo forniscono una base per la verifica e la comprensione delle attività amministrative, ma è possibile che non si riesca a controllare tutte le azioni in modo dettagliato a causa della quantità di dati generata. Il controllo dell'efficacia dei criteri di gestione è tuttavia una procedura consigliata.

L'applicazione di criteri che include controlli di accesso rigorosi implementa meccanismi a livello di codice che consentono di regolamentare le azioni amministrative e di assicurare che vengano usate tutte le misure di protezione possibili. La registrazione fornisce la dimostrazione dell'applicazione, oltre a un record di quali operazioni sono state eseguite, da chi, da dove e quando. La registrazione consente anche di controllare e verificare le informazioni sul rispetto dei criteri da parte degli amministratori e fornisce la dimostrazione delle attività.

## Configurazione del client

Sono consigliabili tre configurazioni primarie per una workstation con protezione avanzata. Le tre configurazioni presentano differenze a livello di costo, usabilità e accessibilità, pur mantenendo un profilo di sicurezza simile in tutte le opzioni. La tabella seguente fornisce una breve analisi dei vantaggi e dei rischi di ogni configurazione. Si noti che con "computer aziendale" si indica una configurazione di computer desktop standard da distribuire a tutti gli utenti del dominio, indipendentemente dai ruoli.

| Configurazione | Vantaggi | Svantaggi |
| ----- | ----- | ----- |
| Workstation autonoma con protezione avanzata | Workstation con controllo rigoroso | Costo più elevato per i desktop dedicati
| | Rischio ridotto di exploit dell'applicazione | Maggiore impegno di gestione |
| | Separazione netta dei compiti | |
| Computer aziendale come macchina virtuale | Costi hardware ridotti | |
| | Separazione di ruolo e applicazioni | |
| Windows da usare con la crittografia di unità BitLocker | Compatibilità con la maggior parte dei computer | Verifica delle risorse |
| | Convenienza economica e portabilità | |
| | Ambiente di gestione isolato | |

È importante che la workstation con protezione avanzata sia l'host e non il guest e che non siano presenti elementi tra il sistema operativo host e l'hardware. Il rispetto del "principio dell'origine pulita", definito anche "origine sicura", comporta l'applicazione della protezione più avanzata sull'host. In caso contrario, la workstation con protezione avanzata (guest) è soggetta ad attacchi sul sistema in cui è ospitata.

È possibile separare ulteriormente le funzioni amministrative tramite immagini di sistema dedicate per ogni workstation con protezione avanzata che ha solo gli strumenti e le autorizzazioni necessarie per la gestione di applicazioni Azure e cloud selezionate, con oggetti Criteri di gruppo locali specifici di Servizi di dominio Active Directory per le attività necessarie.

Per gli ambienti IT privi di infrastruttura locale, ad esempio senza accesso all'istanza locale di Servizi di dominio Active Directory per gli oggetti Criteri di gruppo perché tutti i server sono sul cloud, un servizio come [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) può semplificare la distribuzione e la gestione delle configurazioni delle workstation.

### Workstation autonoma con protezione avanzata per la gestione

Con una workstation autonoma con protezione avanzata gli amministratori hanno un computer o un computer portatile che può essere usato per attività amministrative e un altro computer o computer portatile separato per le attività non amministrative. Una workstation dedicata alla gestione dei servizi di Azure non necessita dell'installazione di altre applicazioni. L'uso di workstation che supportano un [Trusted Platform Module](https://technet.microsoft.com/library/cc766159) (TPM) o una tecnologia analoga per la crittografia a livello hardware semplifica l'autenticazione dei dispositivi e contribuisce a evitare determinati attacchi. Il modulo TPM può supportare anche la protezione dell'intero volume dell'unità di sistema mediante la [Crittografia unità BitLocker](https://technet.microsoft.com/library/cc732774.aspx).

Nello scenario con workstation autonoma con protezione avanzata, illustrato di seguito, l'istanza locale di Windows Firewall o di un firewall client non Microsoft viene configurata in modo da bloccare le connessioni in arrivo, ad esempio RDP. L'amministratore può accedere alla workstation con protezione avanzata e avviare una sessione RDP che si connette ad Azure dopo avere stabilito una connessione VPN con la rete virtuale di Azure, ma non può accedere a un computer aziendale e usare RDP per connettersi alla workstation con protezione avanzata stessa.

![][2]

### Computer aziendale come macchina virtuale

Nei casi in cui una workstation autonoma con protezione avanzata separata risulta troppo costosa o poco pratica, la workstation con protezione avanzata può ospitare una macchina virtuale per l'esecuzione di attività non amministrative.

![][3]

Per evitare alcuni rischi relativi alla sicurezza che possono derivare dall'uso di una workstation per la gestione dei sistemi e per altre attività lavorative quotidiane, è possibile distribuire una macchina virtuale Hyper-V Windows per la workstation con protezione avanzata. Questa macchina virtuale può essere usata come computer aziendale. L'ambiente del computer aziendale può rimanere isolato dall'host, in modo da ridurne la superficie di attacco e rimuovere le attività quotidiane dell'utente, ad esempio la posta elettronica, per evitare la coesistenza con attività amministrative riservate.

La macchina virtuale del computer aziendale è in esecuzione in uno spazio protetto e fornisce applicazioni utente. L'host rimane una "origine pulita" e applica criteri di rete rigorosi nel sistema operativo radice, ad esempio il blocco dell'accesso RDP dalla macchina virtuale.

### Windows To Go

Un'alternativa all'uso di una workstation autonoma con protezione avanzata è costituita dall'uso di un'unità [Windows To Go](https://technet.microsoft.com/library/hh831833.aspx), una funzionalità che supporta l'avvio USB lato client. Windows To Go consente agli utenti di avviare un computer compatibile in un'immagine di sistema isolata in esecuzione da un'unità flash USB crittografata. Fornisce controlli aggiuntivi per endpoint di amministrazione remota, perché l'immagine può essere completamente gestita da un gruppo IT aziendale, con criteri di sicurezza rigorosi, una build minima del sistema operativo e supporto per TPM.

Nella figura seguente l'immagine portatile è un sistema aggiunto a un dominio preconfigurato per connettersi solo ad Azure, che richiede l'autenticazione a più fattori e blocca tutto il traffico non di gestione. Se un utente riavvia lo stesso computer nell'immagine aziendale standard e prova ad accedere al Gateway Desktop remoto per gli strumenti di gestione di Azure, la sessione verrà bloccata. Windows To Go diventa il sistema operativo a livello di radice e non sono necessari altri livelli, ovvero sistema operativo, hypervisor, macchina virtuale, che potrebbero essere più vulnerabili agli attacchi esterni.

![][4]

È importante notare che le unità flash USB possono essere perse più facilmente rispetto a un computer desktop medio. L'uso di BitLocker per crittografare l'intero volume, insieme a una password complessa, ridurrà la probabilità dell'uso dell'immagine dell'unità da parte di un utente malintenzionato per finalità dannose. Se inoltre si perde l'unità flash USB, la revoca e il [rilascio di un nuovo certificato di gestione](https://technet.microsoft.com/library/hh831574.aspx) insieme a una rapida reimpostazione della password possono ridurre l'esposizione. I log di controllo amministrativi si trovano in Azure, non sul client, per ridurre ulteriormente la perdita potenziale di dati.

## Procedure consigliate

Prendere in considerazione le linee guida aggiuntive seguenti quando si gestiscono le applicazioni e i dati in Azure.

### Procedure rischiose e procedure consigliate

Occorre non presupporre che il blocco di una workstation renda superflui gli altri requisiti comuni per la sicurezza. Il rischio potenziale risulta maggiore a causa dei livelli di accesso con privilegi elevati associati in genere agli account di amministratore. La tabella seguente illustra esempi di rischi e le procedure consigliate alternative.

| Procedura rischiosa | Procedura consigliata |
| ----- | ----- |
| Non inviare tramite posta elettronica le credenziali per l'accesso amministratore o altri segreti, ad esempio SSL o certificati di gestione. | Mantenere la riservatezza comunicando a voce i nomi e le password degli account, ma non memorizzandoli nella posta vocale, eseguire un'installazione remota di certificati client/server (tramite una sessione crittografata), eseguire il download da una condivisione di rete crittografata oppure eseguire la distribuzione manuale tramite un supporto rimovibile. |
| | Gestire in modo proattivo i cicli di vita del certificati di gestione. |
| Non memorizzare le password dell'account senza crittografarle o senza hash nella risorsa di archiviazione dell'applicazione, ad esempio in fogli di calcolo, siti di SharePoint o condivisioni di rete. | Definire principi di gestione della sicurezza e criteri di protezione avanzata del sistema e applicarli all'ambiente di sviluppo. |
| | Usare le regole di associazione del certificato di [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751) per assicurare un accesso appropriato ai siti SSL/TLS di Azure. |
| Non condividere account e password tra gli amministratori o riutilizzare le password tra più account utente o servizi, in particolare quelli destinati a social media o ad altre attività non amministrative. | Creare un account Microsoft dedicato per gestire la sottoscrizione di Azure, ovvero un account non usato per la posta elettronica personale. |
| Non inviare file di configurazione tramite posta elettronica. | I file e i profili di configurazione devono essere installati da un'origine attendibile, ad esempio un'unità flash USB crittografata, non da un meccanismo che può essere compromesso facilmente, ad esempio la posta elettronica. |
| Non usare password di accesso vulnerabili o semplici. | Applicare criteri per password complesse, cicli di scadenza (changeon-first-use), timeout della console e blocchi automatici degli account. Usare un sistema di gestione delle password client con autenticazione a più fattori per l'accesso all'insieme di credenziali delle password. |
| Non esporre le porte di gestione su Internet. | Bloccare le porte e gli indirizzi IP di Azure per limitare l'accesso di gestione. Per altre informazioni, vedere il white paper sulla [sicurezza di rete in Azure](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx). |
| | Usare firewall, VPN e Protezione accesso alla rete per tutte le connessioni di gestione. |

## Operazioni di Azure

Nell'ambito della gestione Microsoft di Azure, i responsabili del funzionamento e il personale di supporto che accedono ai sistemi di produzione di Azure usano [computer workstation con protezione avanzata con VM](#stand-alone-hardened-workstation-for-management) sottoposti a provisioning per l'accesso di rete aziendale interno e per le applicazioni, ad esempio posta elettronica, Intranet e così via. Tutti i computer workstation di gestione hanno TPM, l'unità di avvio host è crittografata con BitLocker e i computer sono aggiunti a un'unità organizzativa speciale nel dominio aziendale primario di Microsoft.

La protezione avanzata del sistema viene applicata tramite Criteri di gruppo, con aggiornamento software centralizzato. Per finalità di controllo e analisi, i log eventi, ad esempio relativi alla sicurezza e AppLocker, vengono raccolti dalle workstation di gestione e vengono salvati in una posizione centrale.

Nella rete Microsoft vengono anche usati server jumpbox che richiedono l'autenticazione a due fattori per la connessione alla rete di produzione di Azure.

## Elenco di controllo di sicurezza di Azure

La riduzione al minimo del numero di attività che gli amministratori possono eseguire in una workstation con protezione avanzata consente di ridurre al minimo la superficie di attacco nell'ambiente di sviluppo e di gestione. Usare le tecnologie seguenti per semplificare la protezione della workstation con protezione avanzata:

- Protezione avanzata di Internet Explorer. Il browser Internet Explorer o un Web browser qualsiasi è un punto di ingresso essenziale per codice dannoso a causa delle interazioni estese con i server esterni. Verificare i criteri client e applicare l'esecuzione in modalità protetta, disabilitando i componenti aggiuntivi, disabilitando i download di file e usando i filtri [Microsoft SmartScreen](https://technet.microsoft.com/library/jj618329.aspx). Verificare che gli avvisi di sicurezza vengano visualizzati. Sfruttare i vantaggi delle aree Internet e creare un elenco di siti attendibili per cui è stata configurata una protezione avanzata ragionevole. Bloccare tutti i siti e il codice nel browser, ad esempio ActiveX e Java.
- Utente standard. L'esecuzione come utente standard offre alcuni vantaggi, il più importante dei quali consiste nel rendere più difficile il furto delle credenziali dell'amministratore tramite malware. Un account utente standard, inoltre, non ha privilegi elevati sul sistema operativo radice e molte opzioni di configurazioni e API sono bloccate per impostazione predefinita.
- AppLocker. È possibile usare [AppLocker](http://technet.microsoft.com/library/ee619725.aspx) per limitare i programmi e gli script che possono essere eseguiti dagli utenti. È possibile eseguire AppLocker in modalità di controllo o di imposizione. Per impostazione predefinita, AppLocker include una regola di autorizzazione che consente agli utenti con token di amministratore di eseguire tutto il codice sul client. Questa regola è stata creata per impedire agli amministratori di bloccare se stessi ed è applicabile solo ai token con privilegi elevati. Vedere anche l'integrità del codice come parte della [sicurezza di base](http://technet.microsoft.com/library/dd348705.aspx) di Windows Server.
- Firma del codice. La firma del codice per tutti gli strumenti e gli script usati dagli amministratori offre un meccanismo gestibile per la distribuzione di criteri di blocco dell'applicazione. Gli hash non vengono ridimensionati con le modifiche rapide al codice e i percorsi dei file non forniscono una sicurezza di livello elevato. È consigliabile combinare le regole di AppLocker con un [criterio di esecuzione](http://technet.microsoft.com/library/ee176961.aspx) di PowerShell che consente l'[esecuzione](http://technet.microsoft.com/library/hh849812.aspx) solo di codice firmato e script specifici.
- Criteri di gruppo. Creare criteri amministrativi globali applicati a qualsiasi workstation di dominio usata per la gestione (e bloccare l'accesso da tutte le altre), oltre che agli account utente autenticati su queste workstation.
- Provisioning con miglioramento della sicurezza. Proteggere l'immagine della workstation con protezione avanzata baseline per semplificare la protezione da manomissioni. Usare misure di sicurezza quali la crittografia e l'isolamento per archiviare immagini, macchine virtuali e script e limitare l'accesso, usando ad esempio un processo di archiviazione/estrazione controllabile.
- Applicazione di patch. Mantenere una build coerente o avere a disposizione immagini separate per attività di sviluppo, operative e altre attività amministrative, ricercare regolarmente modifiche e malware, mantenere aggiornata la build a oggi e attivare i computer solo quando sono necessari.
- Crittografia. Assicurarsi che le workstation di gestione abbiano un TPM per abilitare in modo più sicuro [Encrypting File System](https://technet.microsoft.com/library/cc700811.aspx) (EFS) e BitLocker. Se si usa Windows To Go, usare solo chiavi USB crittografate insieme a BitLocker.
- Governance. Usare gli oggetti Criteri di gruppo di Servizi di dominio Active Directory per controllare tutte le interfacce amministrative di Windows, ad esempio la condivisione di file. Includere le workstation di gestione nei processi di controllo, monitoraggio e registrazione. Tenere traccia di tutti gli accessi e gli utilizzi di amministratori e sviluppatori.

## Riepilogo

L'uso di una configurazione di workstation con protezione avanzata per l'amministrazione dei servizi cloud di Azure, delle macchine virtuali e delle applicazione può contribuire a evitare numerosi rischi e minacce derivanti dalla gestione remota dell'infrastruttura IT essenziale. Azure e Windows offrono meccanismi utili per semplificare la protezione e il controllo di comunicazioni, autenticazione e comportamento del client.

## Passaggi successivi
Le risorse seguenti sono disponibili per fornire altre informazioni generali su Azure e sui servizi Microsoft correlati, oltre a elementi specifici a cui si fa riferimento in questo articolo:

- [Protezione dell'accesso con privilegi](https://technet.microsoft.com/library/mt631194.aspx): dettagli tecnici per la progettazione e la creazione di una workstation amministrativa sicura per la gestione di Azure.
- [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Security/AzureSecurity): informazioni sulle funzionalità della piattaforma Azure che proteggono l'infrastruttura e i carichi di lavoro di Azure in esecuzione su Azure.
- [Microsoft Security Response Center](http://www.microsoft.com/security/msrc/default.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo [secure@microsoft.com](mailto:secure@microsoft.com).
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni aggiornate sulla sicurezza in Azure.

<!--Image references-->
[1]: ./media/azure-security-management/typical-management-network-topology.png
[2]: ./media/azure-security-management/stand-alone-hardened-workstation-topology.png
[3]: ./media/azure-security-management/hardened-workstation-enabled-with-hyper-v.png
[4]: ./media/azure-security-management/hardened-workstation-using-windows-to-go-on-a-usb-flash-drive.png

<!---HONumber=AcomDC_0831_2016-->