---
title: Funzionamento dei trust per Azure AD Domain Services | Microsoft Docs
description: Altre informazioni sul funzionamento del trust tra foreste con Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 8b79e0fb24c15d2e9f16640e90d62f7df5c21f32
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233701"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Come funzionano le relazioni di trust per le foreste di risorse in Azure Active Directory Domain Services

Active Directory Domain Services (AD DS) fornisce sicurezza tra più domini o foreste tramite relazioni di trust tra domini e foreste. Prima di poter eseguire l'autenticazione tra i trust, Windows deve prima verificare se il dominio richiesto da un utente, un computer o un servizio ha una relazione di trust con il dominio dell'account che ha eseguito la richiesta.

Per verificare la presenza di questa relazione di trust, il sistema di sicurezza di Windows calcola un percorso di trust tra il controller di dominio (DC) per il server che riceve la richiesta e un controller di dominio nel dominio dell'account richiedente.

I meccanismi di controllo degli accessi forniti da servizi di dominio Active Directory e il modello di sicurezza distribuita di Windows forniscono un ambiente per il funzionamento dei trust tra domini e foreste. Per il corretto funzionamento di tali Trust, ogni risorsa o computer deve disporre di un percorso di trust diretto a un controller di dominio nel dominio in cui si trova.

Il percorso di trust viene implementato dal servizio Accesso rete utilizzando una connessione RPC (Remote Procedure Call) autenticata all'autorità di dominio attendibile. Un canale protetto si estende anche ad altri domini di servizi di dominio Active Directory tramite relazioni di trust tra domini. Questo canale protetto viene usato per ottenere e verificare le informazioni di sicurezza, inclusi gli identificatori di sicurezza (SID) per utenti e gruppi.

## <a name="trust-relationship-flows"></a>Flussi relazione di trust

Il flusso di comunicazioni protette su trust determina l'elasticità di una relazione di trust. Il modo in cui si crea o si configura un trust determina la distanza di estensione della comunicazione all'interno o tra foreste.

Il flusso di comunicazione su trust è determinato dalla direzione del trust. I trust possono essere unidirezionali o bidirezionali e possono essere transitivi o non transitivi.

Il diagramma seguente mostra che per impostazione predefinita tutti i domini nell' *albero 1* e nell' *albero 2* hanno relazioni di trust transitive. Di conseguenza, gli utenti nell' *albero 1* possono accedere alle risorse nei domini nell' *albero 2* e gli utenti nell' *albero 1* possono accedere alle risorse nell' *albero 2*, quando le autorizzazioni appropriate vengono assegnate alla risorsa.

![Diagramma delle relazioni di trust tra due foreste](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Trust unidirezionali e bidirezionali

Le relazioni di trust abilitano l'accesso alle risorse possono essere unidirezionali o bidirezionali.

Un trust unidirezionale è un percorso di autenticazione unidirezionale creato tra due domini. In un trust unidirezionale tra *il dominio a* e *il dominio b*, gli utenti del *dominio a* possono accedere alle risorse nel *dominio b*. Tuttavia, gli utenti del *dominio B* non possono accedere alle risorse nel *dominio a*.

Alcuni trust unidirezionali possono essere non transitivi o transitivi a seconda del tipo di trust creato.

In un trust bidirezionale, *il dominio a* considera attendibili il dominio *b* e il dominio *b* considera attendibile *il dominio a*. Questa configurazione significa che le richieste di autenticazione possono essere passate tra i due domini in entrambe le direzioni. Alcune relazioni bidirezionali possono essere non transitive o transitive a seconda del tipo di trust creato.

Tutti i trust di dominio in una foresta di Active Directory Domain Services sono trust bidirezionali e transitivi. Quando viene creato un nuovo dominio figlio, viene creato automaticamente un trust transitivo bidirezionale tra il nuovo dominio figlio e il dominio padre.

### <a name="transitive-and-non-transitive-trusts"></a>Trust transitivi e non transitivi

La transitività determina se un trust può essere esteso all'esterno dei due domini con cui è stato creato.

* È possibile utilizzare un trust transitivo per estendere le relazioni di trust con altri domini.
* È possibile utilizzare un trust non transitivo per negare relazioni di trust con altri domini.

Ogni volta che si crea un nuovo dominio in una foresta, viene creata automaticamente una relazione di trust transitiva bidirezionale tra il nuovo dominio e il relativo dominio padre. Se vengono aggiunti domini figlio al nuovo dominio, il percorso di attendibilità passa verso l'alto attraverso la gerarchia di domini che estende il percorso di attendibilità iniziale creato tra il nuovo dominio e il relativo dominio padre. Le relazioni di trust transitive si propagano verso l'alto attraverso un albero di dominio durante il formato, creando trust transitivi tra tutti i domini nell'albero di dominio.

Le richieste di autenticazione seguono questi percorsi di trust, quindi gli account di qualsiasi dominio nella foresta possono essere autenticati da qualsiasi altro dominio nella foresta. Con un unico processo di accesso, gli account con le autorizzazioni appropriate possono accedere alle risorse in qualsiasi dominio della foresta.

## <a name="forest-trusts"></a>Trust tra foreste

I trust tra foreste consentono di gestire un'infrastruttura di servizi di dominio Active Directory segmentata e supportano l'accesso alle risorse e ad altri oggetti tra più insiemi di strutture. I trust tra foreste sono utili per i provider di servizi, le aziende che operano fusioni o acquisizioni, Le extranet aziendali collaborative e le aziende che cercano una soluzione per l'autonomia amministrativa.

Utilizzando i trust tra foreste, è possibile collegare due foreste diverse per formare una relazione di trust transitiva unidirezionale o bidirezionale. Un trust tra foreste consente agli amministratori di connettere due foreste di servizi di dominio Active Directory con una singola relazione di trust per offrire un'esperienza di autenticazione e autorizzazione semplice tra le foreste.

È possibile creare un trust tra foreste solo tra un dominio radice della foresta in una foresta e un dominio radice della foresta in un'altra foresta. I trust tra foreste possono essere creati solo tra due foreste e non possono essere estesi in modo implicito a una terza foresta. Questo comportamento significa che se viene creato un trust tra foreste tra la *foresta 1* e la *foresta 2*e viene creato un altro trust tra foreste tra la foresta *2* e la *foresta 3*, la *foresta 1* non ha una relazione di trust implicita con la *foresta 3*.

Nel diagramma seguente vengono illustrate due relazioni di trust tra foreste separate tra tre foreste di servizi di dominio Active Directory in una singola organizzazione.

![Diagramma delle relazioni trust tra foreste all'interno di una singola organizzazione](./media/concepts-forest-trust/forest-trusts.png)

Questa configurazione di esempio fornisce l'accesso seguente:

* Gli utenti della *foresta 2* possono accedere alle risorse in qualsiasi dominio in una foresta *1* o *foresta 3*
* Gli utenti della *foresta 3* possono accedere alle risorse in qualsiasi dominio nella *foresta 2*
* Gli utenti della *foresta 1* possono accedere alle risorse in qualsiasi dominio nella *foresta 2*

Questa configurazione non consente agli utenti della *foresta 1* di accedere alle risorse nella *foresta 3* o viceversa. Per consentire agli utenti nella *foresta 1* e nella *foresta 3* di condividere le risorse, è necessario creare un trust transitivo bidirezionale tra le due foreste.

Se viene creato un trust tra foreste unidirezionali tra due foreste, i membri della foresta trusted possono utilizzare risorse situate nella foresta trusting. Tuttavia, il trust opera in una sola direzione.

Ad esempio, quando un trust tra foreste unidirezionale viene creato tra la *foresta 1* (foresta trusted) e la *foresta 2* (la foresta trusting):

* I membri della *foresta 1* possono accedere alle risorse presenti nella *foresta 2*.
* I membri della *foresta 2* non possono accedere alle risorse che si trovano nella *foresta 1* usando la stessa relazione di trust.

> [!IMPORTANT]
> Azure AD Domain Services foresta delle risorse supporta solo un trust tra foreste unidirezionali per la Active Directory locale.

### <a name="forest-trust-requirements"></a>Requisiti di trust tra foreste

Prima di poter creare un trust tra foreste, è necessario verificare di disporre dell'infrastruttura di Domain Name System (DNS) corretta. I trust tra foreste possono essere creati solo quando è disponibile una delle configurazioni DNS seguenti:

* Un singolo server DNS radice è il server DNS radice per entrambi gli spazi dei nomi DNS della foresta. la zona radice contiene le deleghe per ogni spazio dei nomi DNS e gli hint radice di tutti i server DNS includono il server DNS radice.
* In assenza di un server DNS radice condiviso e i server DNS radice per ogni spazio dei nomi DNS della foresta usano i server d'inoltro condizionali DNS per ogni spazio dei nomi DNS per instradare le query per i nomi nell'altro spazio dei nomi.

    > [!IMPORTANT]
    > Azure AD Domain Services foresta delle risorse deve usare questa configurazione DNS. L'hosting di uno spazio dei nomi DNS diverso dallo spazio dei nomi DNS della foresta di risorse non è una funzionalità di Azure AD Domain Services. I server d'inoltri condizionali sono la configurazione corretta.

* In assenza di un server DNS radice condiviso e i server DNS radice per ogni spazio dei nomi DNS della foresta usano le zone secondarie DNS sono configurati in ogni spazio dei nomi DNS per instradare le query per i nomi nell'altro spazio dei nomi.

Per creare un trust tra foreste, è necessario essere un membro del gruppo Domain Admins (nel dominio radice della foresta) o del gruppo Enterprise Admins in Active Directory. A ogni trust viene assegnata una password che è necessario che gli amministratori di entrambe le foreste conoscano. I membri di Enterprise Admins in entrambe le foreste possono creare i trust in entrambe le foreste contemporaneamente e, in questo scenario, una password che è crittograficamente casuale viene generata e scritta automaticamente per entrambe le foreste.

Il trust tra foreste in uscita per Azure AD Domain Services viene creato nel portale di Azure. Non viene creata manualmente la relazione di trust con il dominio gestito. Il trust tra foreste in ingresso deve essere configurato da un utente con i privilegi indicati in precedenza nell'Active Directory locale.

## <a name="trust-processes-and-interactions"></a>Processi e interazioni di trust

Molte transazioni tra domini e tra foreste dipendono dai trust di dominio o di foresta per completare varie attività. Questa sezione descrive i processi e le interazioni che si verificano quando si accede alle risorse tra i trust e i riferimenti di autenticazione vengono valutati.

### <a name="overview-of-authentication-referral-processing"></a>Panoramica dell'elaborazione dei riferimenti di autenticazione

Quando si fa riferimento a una richiesta di autenticazione a un dominio, il controller di dominio in tale dominio deve determinare se esiste una relazione di trust con il dominio da cui deriva la richiesta. Prima di autenticare l'utente per accedere alle risorse nel dominio, è necessario determinare la direzione del trust e stabilire se il trust è transitivo o non transitivo. Il processo di autenticazione che si verifica tra domini trusted varia a seconda del protocollo di autenticazione in uso. I protocolli Kerberos V5 e NTLM elaborano i riferimenti per l'autenticazione in un dominio in modo diverso

### <a name="kerberos-v5-referral-processing"></a>Elaborazione dei riferimenti Kerberos V5

Il protocollo di autenticazione Kerberos V5 dipende dal servizio Accesso rete nei controller di dominio per l'autenticazione client e le informazioni di autorizzazione. Il protocollo Kerberos si connette a un Centro distribuzione chiavi online (KDC) e all'archivio account Active Directory per i ticket di sessione.

Il protocollo Kerberos usa inoltre i trust per i servizi di concessione ticket (TGS) e per convalidare i certificati dell'attributo Privilege su un canale protetto. Il protocollo Kerberos esegue l'autenticazione tra aree di autenticazione solo con le aree di autenticazione Kerberos del sistema operativo non Windows, ad esempio un'area di autenticazione Kerberos MIT e non deve interagire con il servizio Accesso rete.

Se il client usa Kerberos V5 per l'autenticazione, richiede un ticket al server nel dominio di destinazione da un controller di dominio nel dominio dell'account. Il KDC Kerberos funge da intermediario attendibile tra il client e il server e fornisce una chiave di sessione che consente alle due parti di eseguire l'autenticazione reciproca. Se il dominio di destinazione è diverso dal dominio corrente, il KDC segue un processo logico per determinare se è possibile fare riferimento a una richiesta di autenticazione:

1. Il dominio corrente è considerato attendibile direttamente dal dominio del server richiesto?
    * In caso affermativo, inviare al client un riferimento al dominio richiesto.
    * In caso di no, andare al passaggio successivo.

2. Esiste una relazione di trust transitiva tra il dominio corrente e il dominio successivo nel percorso di trust?
    * In caso affermativo, inviare al client un riferimento al dominio successivo nel percorso di trust.
    * In caso negativo, inviare al client un messaggio di accesso negato.

### <a name="ntlm-referral-processing"></a>Elaborazione dei riferimenti NTLM

Il protocollo di autenticazione NTLM dipende dal servizio Accesso rete nei controller di dominio per l'autenticazione client e le informazioni di autorizzazione. Questo protocollo autentica i client che non usano l'autenticazione Kerberos. NTLM utilizza i trust per passare le richieste di autenticazione tra domini.

Se il client usa NTLM per l'autenticazione, la richiesta iniziale di autenticazione passa direttamente dal client al server di risorse nel dominio di destinazione. Questo server crea una richiesta di risposta da parte del client. Il server invia quindi la risposta dell'utente a un controller di dominio nel dominio dell'account del computer. Questo controller di dominio controlla l'account utente nel database degli account di sicurezza.

Se l'account non esiste nel database, il controller di dominio determina se eseguire l'autenticazione pass-through, inoltrare la richiesta o rifiutare la richiesta utilizzando la logica seguente:

1. Il dominio corrente ha una relazione di trust diretta con il dominio dell'utente?
    * In caso affermativo, il controller di dominio invia le credenziali del client a un controller di dominio nel dominio dell'utente per l'autenticazione pass-through.
    * In caso di no, andare al passaggio successivo.

2. Il dominio corrente ha una relazione di trust transitiva con il dominio dell'utente?
    * In caso affermativo, passare la richiesta di autenticazione al dominio successivo nel percorso di trust. Il controller di dominio ripete il processo controllando le credenziali dell'utente rispetto al proprio database degli account di sicurezza.
    * In caso negativo, inviare al client un messaggio di accesso negato.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Elaborazione basata su Kerberos di richieste di autenticazione su trust tra foreste

Quando due foreste sono connesse da un trust tra foreste, le richieste di autenticazione effettuate mediante i protocolli Kerberos V5 o NTLM possono essere instradate tra foreste per fornire l'accesso alle risorse in entrambe le foreste.

Quando viene stabilito per la prima volta un trust tra foreste, ogni foresta raccoglie tutti gli spazi dei nomi attendibili nella relativa foresta partner e archivia le informazioni in un [oggetto dominio trusted](#trusted-domain-object). Gli spazi dei nomi attendibili includono nomi di albero di dominio, suffissi del nome dell'entità utente (UPN), suffissi del nome dell'entità servizio (SPN) e spazi dei nomi ID di sicurezza (SID) usati nell'altra foresta. Gli oggetti TDO vengono replicati nel catalogo globale.

Prima che i protocolli di autenticazione possano seguire il percorso di trust della foresta, il nome dell'entità servizio (SPN) del computer risorse deve essere risolto in un percorso nell'altra foresta. Un nome SPN può essere uno dei seguenti:

* Nome DNS di un host.
* Nome DNS di un dominio.
* Nome distinto di un oggetto punto di connessione del servizio.

Quando una workstation in una foresta tenta di accedere ai dati in un computer di risorse in un'altra foresta, il processo di autenticazione Kerberos contatta il controller di dominio per un ticket di servizio per il nome SPN del computer delle risorse. Quando il controller di dominio esegue una query sul catalogo globale e stabilisce che il nome SPN non si trova nella stessa foresta del controller di dominio, il controller di dominio invia un riferimento per il dominio padre alla workstation. A questo punto, la workstation esegue una query sul dominio padre per il ticket di servizio e continua a seguire la catena di riferimenti fino a raggiungere il dominio in cui si trova la risorsa.

Il diagramma e i passaggi seguenti forniscono una descrizione dettagliata del processo di autenticazione Kerberos usato quando i computer che eseguono Windows tentano di accedere alle risorse da un computer che si trova in un'altra foresta.

![Diagramma del processo Kerberos su un trust tra foreste](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *User1* accede a *Workstation1* usando le credenziali del dominio *Europe.tailspintoys.com* . L'utente tenta quindi di accedere a una risorsa condivisa in *FileServer1* che si trova nella foresta *USA.wingtiptoys.com* .

2. *Workstation1* Contatta il KDC Kerberos in un controller di dominio nel dominio, *ChildDC1*, e richiede un ticket di servizio per l'SPN *FileServer1* .

3. *ChildDC1* non trova il nome SPN nel database del dominio ed esegue una query nel catalogo globale per verificare se i domini nella foresta *tailspintoys.com* contengono questo nome SPN. Poiché un catalogo globale è limitato alla propria foresta, il nome SPN non viene trovato.

    Il catalogo globale controlla quindi il database per ottenere informazioni sui trust tra foreste stabiliti con la relativa foresta. Se trovato, confronta i suffissi di nome elencati nell'oggetto di dominio trusted della foresta (TDO) al suffisso dell'SPN di destinazione per trovare una corrispondenza. Una volta trovata una corrispondenza, il catalogo globale fornisce un suggerimento di routing a *ChildDC1*.

    Gli hint di routing consentono di indirizzare le richieste di autenticazione verso la foresta di destinazione. Gli hint vengono utilizzati solo quando tutti i canali di autenticazione tradizionali, ad esempio controller di dominio locale e quindi catalogo globale, non riescono a individuare un nome SPN.

4. *ChildDC1* Invia un riferimento per il dominio padre a *Workstation1*.

5. *Workstation1* Contatta un controller di dominio in *ForestRootDC1* (il relativo dominio padre) per un riferimento a un controller di dominio (*ForestRootDC2*) nel dominio radice della foresta della foresta *wingtiptoys.com* .

6. *Workstation1* Contatta *ForestRootDC2* nella foresta *wingtiptoys.com* per un ticket di servizio per il servizio richiesto.

7. *ForestRootDC2* Contatta il catalogo globale per trovare il nome SPN e il catalogo globale trova una corrispondenza per il nome SPN e lo invia nuovamente a *ForestRootDC2*.

8. *ForestRootDC2* quindi invia il riferimento a *USA.wingtiptoys.com* a *Workstation1*.

9. *Workstation1* Contatta il KDC in *ChildDC2* e negozia il ticket per *User1* per ottenere l'accesso a *FileServer1*.

10. Quando *Workstation1* dispone di un ticket di servizio, invia il ticket di servizio a *FileServer1*, che legge le credenziali di sicurezza di *User1*e costruisce di conseguenza un token di accesso.

## <a name="trusted-domain-object"></a>Oggetto dominio trusted

Ogni trust di dominio o di foresta all'interno di un'organizzazione è rappresentato da un oggetto di dominio trusted (TDO) archiviato nel contenitore di *sistema* all'interno del dominio.

### <a name="tdo-contents"></a>Contenuto di TDO

Le informazioni contenute in un TDO variano a seconda che un TDO sia stato creato da un trust di dominio o da un trust tra foreste.

Quando viene creato un trust di dominio, gli attributi come il nome di dominio DNS, il SID di dominio, il tipo di trust, la transitività dei trust e il nome di dominio reciproco sono rappresentati in TDO. Trust tra foreste TDOs archivia attributi aggiuntivi per identificare tutti gli spazi dei nomi attendibili dalla foresta partner. Questi attributi includono nomi di albero di dominio, suffissi del nome dell'entità utente (UPN), suffissi del nome dell'entità servizio (SPN) e spazi dei nomi ID di sicurezza (SID).

Poiché i trust vengono archiviati in Active Directory come TDOs, tutti i domini in una foresta conoscono le relazioni di trust presenti in tutta la foresta. Analogamente, quando due o più foreste sono unite in join tramite trust tra foreste, i domini radice della foresta in ogni foresta conoscono le relazioni di trust presenti in tutti i domini delle foreste trusted.

### <a name="tdo-password-changes"></a>Modifiche della password di TDO

Entrambi i domini in una relazione di trust condividono una password, archiviata nell'oggetto TDO in Active Directory. Come parte del processo di manutenzione dell'account, ogni 30 giorni il controller di dominio trusting modifica la password archiviata in TDO. Poiché tutti i trust bidirezionali sono in 2 1 realtà in direzioni opposte, il processo si verifica due volte per i trust bidirezionali.

Una relazione di trust ha un trust e un lato attendibile. Sul lato attendibile, qualsiasi controller di dominio scrivibile può essere usato per il processo. Sul lato trusting, l'emulatore PDC esegue la modifica della password.

Per modificare una password, i controller di dominio completano il processo seguente:

1. L'emulatore del controller di dominio primario (PDC) nel dominio trusting crea una nuova password. Un controller di dominio nel dominio trusted non avvia mai la modifica della password. Viene sempre avviato dall'emulatore PDC del dominio trusting.

2. L'emulatore PDC nel dominio trusting imposta il campo *oldPassword* dell'oggetto TDO sul campo *newPassword* corrente.

3. L'emulatore PDC nel dominio trusting imposta il campo *newPassword* dell'oggetto TDO sulla nuova password. Mantenendo una copia della password precedente è possibile ripristinare la vecchia password se il controller di dominio nel dominio trusted non riesce a ricevere la modifica o se la modifica non viene replicata prima che venga eseguita una richiesta che utilizza la nuova password di attendibilità.

4. L'emulatore PDC nel dominio trusting effettua una chiamata remota a un controller di dominio nel dominio trusted che richiede di impostare la password dell'account attendibile sulla nuova password.

5. Il controller di dominio nel dominio trusted imposta la password di attendibilità sulla nuova password.

6. Per ogni lato del trust, gli aggiornamenti vengono replicati negli altri controller di dominio del dominio. Nel dominio trusting, la modifica attiva una replica urgente dell'oggetto dominio trusted.

La password viene ora modificata in entrambi i controller di dominio. La replica normale distribuisce gli oggetti TDO agli altri controller di dominio nel dominio. Tuttavia, è possibile che il controller di dominio nel dominio trusting modifichi la password senza aggiornare correttamente un controller di dominio nel dominio trusted. Questo scenario potrebbe verificarsi perché non è stato possibile stabilire un canale protetto, necessario per elaborare la modifica della password. È anche possibile che il controller di dominio nel dominio trusted non sia disponibile in un determinato momento durante il processo e potrebbe non ricevere la password aggiornata.

Per gestire le situazioni in cui la modifica della password non viene comunicata correttamente, il controller di dominio nel dominio trusting non modifica mai la nuova password a meno che non sia stata eseguita correttamente l'autenticazione (configurazione di un canale protetto) con la nuova password. Questo comportamento è dovuto al fatto che le password precedenti e nuove vengono mantenute nell'oggetto TDO del dominio trusting.

Una modifica della password non viene finalizzata fino a quando non viene eseguita l'autenticazione con la password. La vecchia password archiviata può essere utilizzata sul canale protetto finché il controller di dominio nel dominio trusted non riceve la nuova password, abilitando così il servizio senza interruzioni.

Se l'autenticazione con la nuova password ha esito negativo perché la password non è valida, il controller di dominio trusting prova a eseguire l'autenticazione con la vecchia password. Se l'autenticazione con la vecchia password viene eseguita correttamente, riprende il processo di modifica della password entro 15 minuti.

Gli aggiornamenti delle password di attendibilità devono essere replicati nei controller di dominio di entrambi i lati del trust entro 30 giorni. Se la password di attendibilità viene modificata dopo 30 giorni e un controller di dominio ha solo la password N-2, non può utilizzare il trust dal lato trusting e non può creare un canale sicuro sul lato attendibile.

## <a name="network-ports-used-by-trusts"></a>Porte di rete usate da trust

Poiché i trust devono essere distribuiti in diversi limiti di rete, possono essere necessari per uno o più firewall. In tal caso, è possibile effettuare il tunneling del traffico in un firewall o aprire porte specifiche nel firewall per consentire il passaggio del traffico.

> [!IMPORTANT]
> Active Directory Domain Services non supporta la limitazione del traffico RPC Active Directory a porte specifiche.

Per informazioni sulle porte necessarie per un trust tra foreste, vedere la sezione **Windows Server 2008 e versioni successive** dell'articolo supporto tecnico Microsoft [come configurare un firewall per Active Directory domini e trust](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) .

## <a name="supporting-services-and-tools"></a>Servizi e strumenti di supporto

Per supportare Trust e autenticazione, vengono usate alcune funzionalità aggiuntive e strumenti di gestione.

### <a name="net-logon"></a>Accesso rete

Il servizio Accesso rete gestisce un canale protetto da un computer basato su Windows a un controller di dominio. Viene anche usato nei seguenti processi correlati all'attendibilità:

* Configurazione e gestione dell'attendibilità: l'accesso rete consente di mantenere le password di attendibilità, raccoglie le informazioni di attendibilità e verifica i trust interagendo con il processo LSA e con TDO.

    Per i trust tra foreste, le informazioni di trust includono il record*FTInfo*(Forest Trust Information), che include il set di spazi dei nomi che una foresta trusted dichiara di gestire, annotata con un campo che indica se ogni attestazione è considerata attendibile dalla foresta trusting.

* Autenticazione: fornisce le credenziali utente su un canale protetto a un controller di dominio e restituisce i SID del dominio e i diritti utente per l'utente.

* Posizione del controller di dominio: consente di trovare o individuare i controller di dominio in un dominio o tra domini.

* Convalida pass-through: le credenziali degli utenti in altri domini vengono elaborate dall'accesso netto. Quando un dominio trusting deve verificare l'identità di un utente, passa le credenziali dell'utente tramite l'accesso NET al dominio trusted per la verifica.

* Verifica del certificato dell'attributo Privilege: quando un server che usa il protocollo Kerberos per l'autenticazione deve verificare il PAC in un ticket di servizio, Invia la PAC sul canale sicuro al controller di dominio per la verifica.

### <a name="local-security-authority"></a>Autorità di sicurezza locale

L'autorità di sicurezza locale (LSA) è un sottosistema protetto che mantiene le informazioni su tutti gli aspetti della sicurezza locale in un sistema. Noto come criterio di sicurezza locale, LSA fornisce vari servizi per la conversione tra nomi e identificatori.

Il sottosistema di sicurezza LSA fornisce servizi in modalità kernel e modalità utente per convalidare l'accesso agli oggetti, controllare i privilegi utente e generare messaggi di controllo. LSA è responsabile della verifica della validità di tutti i ticket di sessione presentati dai servizi in domini trusted o non trusted.

### <a name="management-tools"></a>Strumenti di gestione

Gli amministratori possono utilizzare *Active Directory domini e trust*, *netdom* e *nltest* per esporre, creare, rimuovere o modificare i trust.

* *Active Directory domini e trust* è Microsoft Management Console (MMC) usato per amministrare i trust di dominio, i livelli di funzionalità del dominio e della foresta e i suffissi del nome dell'entità utente.
* Gli strumenti da riga di comando *netdom* e *nltest* possono essere utilizzati per trovare, visualizzare, creare e gestire i trust. Questi strumenti comunicano direttamente con l'autorità LSA in un controller di dominio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle foreste di risorse, vedere [come funzionano i trust tra foreste in Azure AD DS?][concepts-trust]

Per iniziare a creare un dominio gestito di Azure AD DS con una foresta di risorse, vedere [creare e configurare un dominio gestito di Azure AD DS][tutorial-create-advanced]. È quindi possibile [creare un trust tra foreste in uscita per un dominio locale (anteprima)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
