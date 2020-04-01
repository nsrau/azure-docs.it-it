---
title: Funzionamento dei trust per Servizi di dominio Azure AD Documenti Microsoft
description: Altre informazioni sul funzionamento del trust tra foreste con Servizi di dominio Azure ADLearn more about how forest trust work with Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 903881a1d15c1f043e381f50e5b69d661cd08192
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476431"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Funzionamento delle relazioni di trust per le foreste di risorse in Servizi di dominio Azure Active DirectoryHow trust relationships for resource forests in Azure Active Directory Domain Services

Servizi di dominio Active Directory (AD DS) fornisce la sicurezza tra più domini o foreste tramite relazioni di trust tra domini e foreste. Prima che l'autenticazione possa essere eseguita tra trust, Windows deve prima verificare se il dominio richiesto da un utente, un computer o un servizio ha una relazione di trust con il dominio dell'account richiedente.

Per verificare questa relazione di trust, il sistema di protezione di Windows calcola un percorso di trust tra il controller di dominio (DC) per il server che riceve la richiesta e un controller di dominio nel dominio dell'account richiedente.

I meccanismi di controllo di accesso forniti da Servizi di dominio Active Directory e dal modello di sicurezza distribuito di Windows forniscono un ambiente per il funzionamento dei trust di dominio e di foresta. Affinché questi trust funzionino correttamente, ogni risorsa o computer deve disporre di un percorso di trust diretto a un controller di dominio nel dominio in cui si trova.

Il percorso di trust viene implementato dal servizio Accesso rete utilizzando una connessione RPC (Remote Procedure Call) autenticata all'autorità di dominio trusted. Un canale protetto si estende anche ad altri domini di Servizi di dominio Active Directory tramite relazioni di trust tra domini. Questo canale protetto viene utilizzato per ottenere e verificare le informazioni di sicurezza, inclusi gli identificatori di sicurezza (SID) per utenti e gruppi.

## <a name="trust-relationship-flows"></a>Flussi di relazione di trust

Il flusso di comunicazioni protette su trust determina l'elasticità di un trust. La modalità di creazione o configurazione di un trust determina la distanza di estensione della comunicazione all'interno o tra foreste.

Il flusso di comunicazione sui trust è determinato dalla direzione del trust. I trust possono essere unidirezionali o bidirezionali e possono essere transitivi o non transitivi.

Nel diagramma seguente viene illustrato che tutti i domini *nell'albero 1* e *nell'albero 2* hanno relazioni di trust transitive per impostazione predefinita. Di conseguenza, gli utenti *nell'albero 1* possono accedere alle risorse nei domini nella *struttura 2* e gli utenti nell'albero *1* possono accedere alle risorse *nell'albero 2*, quando alla risorsa vengono assegnate le autorizzazioni appropriate.

![Diagramma delle relazioni di trust tra due foreste](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Trust unidirezionali e bidirezionali

Le relazioni di trust consentono l'accesso alle risorse possono essere unidirezionali o bidirezionali.

Un trust unidirezionale è un percorso di autenticazione unidirezionale creato tra due domini. In un trust unidirezionale tra *il dominio A* e il dominio *B*, gli utenti nel *dominio A* possono accedere alle risorse nel dominio *B*. Tuttavia, gli utenti nel *dominio B* non possono accedere alle risorse nel *dominio A*.

Alcuni trust unidirezionali possono essere non transitivi o transitivi a seconda del tipo di trust creato.

In un trust bidirezionale, *il dominio A* considera attendibile il dominio *B* e il *dominio B* considera attendibile il dominio *A*. Questa configurazione significa che le richieste di autenticazione possono essere passate tra i due domini in entrambe le direzioni. Alcune relazioni bidirezionali possono essere non transitive o transitive a seconda del tipo di trust creato.

Tutti i trust di dominio in una foresta di Servizi di dominio Active Directory sono trust transitivi bidirezionali. Quando si crea un nuovo dominio figlio, viene automaticamente creata una relazione di trust transitiva bidirezionale tra il nuovo dominio figlio e il relativo dominio padre.

### <a name="transitive-and-non-transitive-trusts"></a>Trust transitivi e non transitivi

La transitività determina se un trust può essere esteso al di fuori dei due domini con cui è stato formato.

* Un trust transitivo può essere utilizzato per estendere le relazioni di trust con altri domini.
* Un trust non transitivo può essere utilizzato per negare le relazioni di trust con altri domini.

Ogni volta che si crea un nuovo dominio in una foresta, viene creata automaticamente una relazione di trust transitiva bidirezionale tra il nuovo dominio e il relativo dominio padre. Se i domini figlio vengono aggiunti al nuovo dominio, il percorso di trust passa verso l'alto attraverso la gerarchia di domini estendendo il percorso di trust iniziale creato tra il nuovo dominio e il relativo dominio padre. Le relazioni di trust transitive si muovono verso l'alto all'interno di un albero di dominio man mano che esso viene creato, creando trust transitivi tra tutti i domini nell'albero di dominio.

Le richieste di autenticazione seguono questi percorsi di trust, pertanto gli account di qualsiasi dominio dell'insieme di strutture possono essere autenticati da qualsiasi altro dominio dell'insieme di strutture. Con un singolo processo di accesso, gli account con le autorizzazioni adeguate possono accedere alle risorse in qualsiasi dominio nella foresta.

## <a name="forest-trusts"></a>Trust tra foreste

I trust tra foreste consentono di gestire infrastrutture di Servizi di dominio Active Directory segmentate e supportano l'accesso alle risorse e ad altri oggetti in più foreste. I trust forestali sono utili per i fornitori di servizi, le aziende sottoposte a fusioni o acquisizioni, le extranet aziendali collaborative e le aziende che cercano una soluzione per l'autonomia amministrativa.

Utilizzando i trust tra foreste, è possibile collegare due foreste diverse per formare una relazione di trust transitiva unidirezionale o bidirezionale. Un trust tra foreste consente agli amministratori di connettere due foreste di Servizi di dominio Active Directory con un'unica relazione di trust per fornire un'esperienza di autenticazione e autorizzazione senza interruzioni tra le foreste.

È possibile creare un trust tra foreste solo tra un dominio radice della foresta in una foresta e un dominio radice della foresta in un'altra foresta. I trust tra foreste possono essere creati solo tra due foreste e non possono essere estesi in modo implicito a una terza foresta. Questo comportamento significa che se viene creato un trust tra foreste tra *l'insieme di strutture 1* e *l'insieme di*strutture 2 e viene creato un altro trust tra foreste tra *l'insieme di* strutture 2 e *l'insieme*di strutture 3 , la foresta *1* non dispone di un trust implicito con *l'insieme di strutture 3 .*

Nel diagramma seguente vengono illustrate due relazioni di trust tra foreste separate tra tre foreste di Servizi di dominio Active Directory in una singola organizzazione.

![Diagramma delle relazioni tra trust tra foreste all'interno di una singola organizzazione](./media/concepts-forest-trust/forest-trusts.png)

Questa configurazione di esempio fornisce l'accesso seguente:This example configuration provides the following access:

* Gli utenti nell'insieme *di strutture 2* possono accedere alle risorse in qualsiasi dominio nell'insieme di strutture 1 o nell'insieme di strutture *3Users* in Forest 2 can access resources in any domain in either *Forest 1* or Forest 3
* Gli utenti nell'insieme *di strutture 3* possono accedere alle risorse in qualsiasi dominio *nell'insieme di strutture 2Users* in Forest 3 can access resources in any domain in Forest 2
* Gli utenti nell'insieme *di strutture 1* possono accedere alle risorse in qualsiasi dominio *nell'insieme di strutture 2Users* in Forest 1 can access resources in any domain in Forest 2

Questa configurazione non consente agli utenti nell'insieme *di strutture 1* di accedere alle risorse nell'insieme di strutture *3* o viceversa. Per consentire agli utenti sia nell'insieme *di strutture 1* che nell'insieme *3* di condividere le risorse, è necessario creare un trust transitivo bidirezionale tra le due foreste.

Se viene creato un trust tra foreste unidirezionali tra due foreste, i membri della foresta trusted possono utilizzare le risorse che si trovano nella foresta trusting. Tuttavia, il trust funziona in una sola direzione.

Ad esempio, quando viene creato un trust tra foreste unidirezionale, tra la *foresta 1* (la foresta trusted) e la *foresta 2* (la foresta trusting):

* I membri *dell'insieme di strutture 1* possono accedere alle risorse che si trovano nell'insieme *di strutture 2*.
* I membri della *foresta 2* non possono accedere alle risorse che si trovano nell'insieme di *strutture 1* utilizzando la stessa relazione di trust.

> [!IMPORTANT]
> La foresta di risorse di Servizi di dominio Azure AD supporta solo un trust tra foreste unidirezionali per Active Directory locale.

### <a name="forest-trust-requirements"></a>Requisiti di attendibilità della foresta

Prima di poter creare un trust tra foreste, è necessario verificare di disporre dell'infrastruttura DNS (Domain Name System) corretta. I trust tra foreste possono essere creati solo quando è disponibile una delle configurazioni DNS seguenti:

* Un singolo server DNS radice è il server DNS radice per entrambi gli spazi dei nomi DNS della foresta: la zona radice contiene deleghe per ogni spazio dei nomi DNS e i parametri radice di tutti i server DNS includono il server DNS radice.
* Se non è presente alcun server DNS radice condiviso e i server DNS radice per ogni spazio dei nomi DNS della foresta utilizzano server d'inoltro condizionali DNS per ogni spazio dei nomi DNS per instradare le query per i nomi nell'altro spazio dei nomi.

    > [!IMPORTANT]
    > La foresta di risorse di Servizi di dominio Azure AD deve usare questa configurazione DNS. L'hosting di uno spazio dei nomi DNS diverso dallo spazio dei nomi DNS della foresta di risorse non è una funzionalità di Servizi di dominio Azure AD. I server d'inoltro condizionali sono la configurazione corretta.

* Se non è presente alcun server DNS radice condiviso e i server DNS radice per ogni spazio dei nomi DNS della foresta utilizzano le zone secondarie DNS configurate in ogni spazio dei nomi DNS per instradare le query per i nomi nell'altro spazio dei nomi.

Per creare un trust tra foreste, è necessario essere membri del gruppo Domain Admins (nel dominio radice della foresta) o del gruppo Enterprise Admins in Active Directory. A ogni trust viene assegnata una password che gli amministratori di entrambe le foreste devono conoscere. I membri di Enterprise Admins in entrambe le foreste possono creare i trust in entrambe le foreste contemporaneamente e, in questo scenario, una password crittograficamente casuale viene generata e scritta automaticamente per entrambe le foreste.

Il trust tra foreste in uscita per Servizi di dominio Azure AD viene creato nel portale di Azure.The outbound forest trust for Azure AD Domain Services is created in the Azure portal. Non creare manualmente il trust con il dominio gestito stesso. Il trust tra foreste in ingresso deve essere configurato da un utente con i privilegi precedentemente annotati in Active Directory locale.

## <a name="trust-processes-and-interactions"></a>Processi e interazioni di Trust

Molte transazioni tra domini e tra foreste dipendono da trust tra domini o foreste per completare varie attività. In questa sezione vengono descritti i processi e le interazioni che si verificano quando si accede alle risorse tra trust e vengono valutati i riferimenti di autenticazione.

### <a name="overview-of-authentication-referral-processing"></a>Panoramica dell'elaborazione dei riferimenti di autenticazione

Quando una richiesta di autenticazione viene inviata a un dominio, il controller di dominio in tale dominio deve determinare se esiste una relazione di trust con il dominio da cui proviene la richiesta. La direzione del trust e se il trust è transitivo o non transitivo deve essere determinato anche prima di autenticare l'utente per accedere alle risorse nel dominio. Il processo di autenticazione che si verifica tra domini trusted varia a seconda del protocollo di autenticazione in uso. I protocolli Kerberos V5 e NTLM elaborano i riferimenti per l'autenticazione a un dominio in modo diverso

### <a name="kerberos-v5-referral-processing"></a>Elaborazione dei riferimenti Kerberos V5

Il protocollo di autenticazione Kerberos V5 dipende dal servizio Accesso rete nei controller di dominio per le informazioni di autenticazione e autorizzazione client. Il protocollo Kerberos si connette a un Centro distribuzione chiavi (KDC) online e all'archivio account di Active Directory per i ticket di sessione.

Il protocollo Kerberos utilizza anche i trust per i servizi di concessione ticket (TGS) tra e reati e per convalidare i certificati di attributi dei privilegi (PAC) in un canale protetto. Il protocollo Kerberos esegue l'autenticazione tra aree di autenticazione solo con aree di autenticazione Kerberos del sistema operativo non di marca, ad esempio un'area di autenticazione Kerberos MIT e non è necessario interagire con il servizio Accesso rete.

Se il client utilizza Kerberos V5 per l'autenticazione, richiede un ticket al server nel dominio di destinazione da un controller di dominio nel relativo dominio di account. Il KDC Kerberos funge da intermediario attendibile tra il client e il server e fornisce una chiave di sessione che consente alle due parti di autenticarsi reciprocamente. Se il dominio di destinazione è diverso dal dominio corrente, il KDC segue un processo logico per determinare se è possibile fare riferimento a una richiesta di autenticazione:If the target domain is different from the current domain, the KDC follows a logical process to determine whether an authentication request can be referred:

1. Il dominio corrente è attendibile direttamente dal dominio del server richiesto?
    * In caso affermativo, inviare al client un riferimento al dominio richiesto.
    * Se no, andare al passaggio successivo.

2. Esiste una relazione di trust transitiva tra il dominio corrente e il dominio successivo nel percorso di trust?
    * In caso affermativo, inviare al client un riferimento al dominio successivo nel percorso di trust.
    * In caso contrario, inviare al client un messaggio di accesso negato.

### <a name="ntlm-referral-processing"></a>Elaborazione dei riferimenti NTLM

Il protocollo di autenticazione NTLM dipende dal servizio Accesso rete nei controller di dominio per le informazioni di autenticazione e autorizzazione client. Questo protocollo autentica i client che non utilizzano l'autenticazione Kerberos. NTLM utilizza i trust per passare le richieste di autenticazione tra domini.

Se il client utilizza NTLM per l'autenticazione, la richiesta iniziale di autenticazione passa direttamente dal client al server di risorse nel dominio di destinazione. Questo server crea una richiesta di verifica a cui il client risponde. Il server invia quindi la risposta dell'utente a un controller di dominio nel dominio dell'account computer. Questo controller di dominio controlla l'account utente rispetto al relativo database degli account di protezione.

Se l'account non esiste nel database, il controller di dominio determina se eseguire l'autenticazione pass-through, inoltrare la richiesta o rifiutare la richiesta utilizzando la logica seguente:

1. Il dominio corrente ha una relazione di trust diretta con il dominio dell'utente?
    * In caso affermativo, il controller di dominio invia le credenziali del client a un controller di dominio nel dominio dell'utente per l'autenticazione pass-through.
    * Se no, andare al passaggio successivo.

2. Il dominio corrente ha una relazione di trust transitiva con il dominio dell'utente?
    * In caso affermativo, passare la richiesta di autenticazione al dominio successivo nel percorso di trust. Questo controller di dominio ripete il processo controllando le credenziali dell'utente rispetto al proprio database degli account di protezione.
    * In caso contrario, inviare al client un messaggio di accesso negato.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Elaborazione basata su Kerberos delle richieste di autenticazione su trust tra foreste

Quando due foreste sono connesse tramite un trust tra foreste, le richieste di autenticazione effettuate utilizzando i protocolli Kerberos V5 o NTLM possono essere instradate tra foreste per fornire l'accesso alle risorse in entrambe le foreste.

Quando viene stabilito per la prima volta un trust tra foreste, ogni foresta raccoglie tutti gli spazi dei nomi attendibili nella foresta partner e archivia le informazioni in un [oggetto dominio trusted.](#trusted-domain-object) Gli spazi dei nomi attendibili includono nomi di strutture di dominio, suffissi di nome dell'entità utente (UPN), suffissi del nome dell'entità servizio (SPN) e spazi dei nomi degli ID di sicurezza (SID) utilizzati nell'altra foresta. Gli oggetti TDO vengono replicati nel catalogo globale.

Prima che i protocolli di autenticazione possano seguire il percorso di trust della foresta, il nome dell'entità servizio (SPN) del computer della risorsa deve essere risolto in un percorso nell'altra foresta. Un nome SPN può essere uno dei seguenti:

* Il nome DNS di un host.
* Il nome DNS di un dominio.
* Nome distinto di un oggetto punto di connessione del servizio.

Quando una workstation in una foresta tenta di accedere ai dati in un computer di risorse in un'altra foresta, il processo di autenticazione Kerberos contatta il controller di dominio per un ticket di servizio al nome SPN del computer della risorsa. Una volta che il controller di dominio esegue una query nel catalogo globale e determina che il nome SPN non si trova nella stessa foresta del controller di dominio, il controller di dominio invia un riferimento per il dominio padre alla workstation. A questo punto, la workstation esegue una query sul dominio padre per il ticket del servizio e continua a seguire la catena di riferimenti fino a raggiungere il dominio in cui si trova la risorsa.

Il diagramma e i passaggi seguenti forniscono una descrizione dettagliata del processo di autenticazione Kerberos utilizzato quando i computer che eseguono Windows tentano di accedere alle risorse da un computer che si trova in un'altra foresta.

![Diagramma del processo Kerberos su un trust tra foreste](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *User1* accede a *Workstation1* utilizzando le credenziali del *dominio europe.tailspintoys.com.* L'utente tenta quindi di accedere a una risorsa condivisa su *FileServer1* che si trova nella *foresta usa.wingtiptoys.com.*

2. *Workstation1* contatta il KDC Kerberos in un controller di dominio nel relativo dominio, *ChildDC1,* e richiede un ticket di servizio per il nome SPN *FileServer1.*

3. *ChildDC1* non trova il nome SPN nel relativo database di dominio ed esegue una query nel catalogo globale per verificare se i domini della *foresta tailspintoys.com* contengono questo SPN. Poiché un catalogo globale è limitato alla propria foresta, l'SPN non viene trovato.

    Il catalogo globale controlla quindi il database per informazioni su eventuali trust tra foreste stabiliti con la relativa foresta. Se viene trovato, confronta i suffissi di nome elencati nell'oggetto dominio trusted (TDO) di trust tra foreste con il suffisso dell'SPN di destinazione per trovare una corrispondenza. Una volta trovata una corrispondenza, il catalogo globale fornisce un suggerimento di routing a *ChildDC1*.

    Gli hint di routing consentono di indirizzare le richieste di autenticazione verso la foresta di destinazione. I suggerimenti vengono utilizzati solo quando tutti i canali di autenticazione tradizionali, ad esempio il controller di dominio locale e quindi il catalogo globale, non riescono a individuare un SPN.

4. *ChildDC1* invia un riferimento per il relativo dominio padre a *Workstation1*.

5. *Workstation1* contatta un controller di dominio in *ForestRootDC1* (il relativo dominio padre) per un riferimento a un controller di dominio (*ForestRootDC2*) nel dominio radice della foresta della *foresta wingtiptoys.com.*

6. *Workstation1* contatta *ForestRootDC2* nell'insieme di strutture *wingtiptoys.com* per un ticket di servizio per il servizio richiesto.

7. *ForestRootDC2* contatta il catalogo globale per trovare il nome SPN e il catalogo globale trova una corrispondenza per il nome SPN e lo invia a *ForestRootDC2*.

8. *ForestRootDC2* invia quindi il riferimento a *usa.wingtiptoys.com* a *Workstation1*.

9. *Workstation1* contatta il KDC su *ChildDC2* e negozia il ticket per *L'utente 1* per ottenere l'accesso a *FileServer1*.

10. Una volta che *Workstation1* dispone di un ticket di servizio, invia il ticket di servizio a *FileServer1*, che legge le credenziali di sicurezza di *User1*e costruisce un token di accesso di conseguenza.

## <a name="trusted-domain-object"></a>Oggetto dominio trusted

Ogni trust di dominio o foresta all'interno di un'organizzazione è rappresentato da un oggetto dominio trusted (TDO) archiviato nel contenitore *di sistema* all'interno del relativo dominio.

### <a name="tdo-contents"></a>Contenuti TDO

Le informazioni contenute in un TDO variano a seconda che un TDO sia stato creato da un trust di dominio o da un trust tra foreste.

Quando viene creato un trust di dominio, attributi quali il nome di dominio DNS, il SID del dominio, il tipo di trust, la transitività dell'attendibilità e il nome di dominio reciproco vengono rappresentati nel TDO. I TDO trust tra foreste archiviano attributi aggiuntivi per identificare tutti gli spazi dei nomi attendibili dalla foresta partner. Questi attributi includono nomi di struttura di dominio, suffissi di nome dell'entità utente (UPN), suffissi del nome dell'entità servizio (SPN) e spazi dei nomi ID di sicurezza (SID).

Poiché i trust vengono archiviati in Active Directory come TDO, tutti i domini in un insieme di strutture conoscono le relazioni di trust presenti nell'insieme di strutture. Analogamente, quando due o più foreste vengono unite tramite trust tra foreste, i domini radice della foresta in ogni foresta conoscono le relazioni di trust presenti in tutti i domini delle foreste trusted.

### <a name="tdo-password-changes"></a>Modifiche della password TDO

Entrambi i domini in una relazione di trust condividono una password, che viene archiviata nell'oggetto TDO in Active Directory. Come parte del processo di manutenzione dell'account, ogni 30 giorni il controller di dominio trusting modifica la password archiviata nel TDO. Poiché tutti i trust bidirezionali sono in realtà due trust unidirezionali che vanno in direzioni opposte, il processo si verifica due volte per i trust bidirezionali.

Una fiducia ha un lato trusting e un trust. Sul lato attendibile, qualsiasi controller di dominio scrivibile può essere utilizzato per il processo. Sul lato trusting, l'emulatore PDC esegue la modifica della password.

Per modificare una password, i controller di dominio completano il processo seguente:To change a password, the domain controllers complete the following process:

1. L'emulatore del controller di dominio primario (PDC) nel dominio trusting crea una nuova password. Un controller di dominio nel dominio trusted non avvia mai la modifica della password. Viene sempre avviato dall'emulatore PDC del dominio trusting.

2. L'emulatore PDC nel dominio trusting imposta il campo *OldPassword* dell'oggetto TDO sul campo *NewPassword* corrente.

3. L'emulatore PDC nel dominio trusting imposta il campo *NewPassword* dell'oggetto TDO sulla nuova password. La conservazione di una copia della password precedente consente di ripristinare la vecchia password se il controller di dominio nel dominio trusted non riesce a ricevere la modifica o se la modifica non viene replicata prima che venga effettuata una richiesta che utilizza la nuova password di trust.

4. L'emulatore PDC nel dominio trusting effettua una chiamata remota a un controller di dominio nel dominio trusted chiedendogli di impostare la password nell'account trust sulla nuova password.

5. Il controller di dominio nel dominio trusted modifica la password di trust con la nuova password.

6. Su ogni lato del trust, gli aggiornamenti vengono replicati agli altri controller di dominio nel dominio. Nel dominio trusting, la modifica attiva una replica urgente dell'oggetto dominio trusted.

La password viene ora modificata in entrambi i controller di dominio. La replica normale distribuisce gli oggetti TDO agli altri controller di dominio nel dominio. Tuttavia, è possibile che il controller di dominio nel dominio trusting modifichi la password senza aggiornare correttamente un controller di dominio nel dominio trusted. Questo scenario potrebbe verificarsi perché non è stato possibile stabilire un canale protetto necessario per elaborare la modifica della password. È inoltre possibile che il controller di dominio nel dominio trusted non sia disponibile a un certo punto durante il processo e che non riceva la password aggiornata.

Per gestire le situazioni in cui la modifica della password non viene comunicata correttamente, il controller di dominio nel dominio trusting non modifica mai la nuova password a meno che non sia stata autenticata correttamente (impostare un canale protetto) utilizzando la nuova password. Questo è il motivo per cui sia le password vecchie che le nuove password vengono mantenute nell'oggetto TDO del dominio trusting.

La modifica della password non viene finalizzata fino a quando l'autenticazione con la password non riesce. La vecchia password archiviata può essere utilizzata sul canale protetto fino a quando il controller di dominio nel dominio trusted non riceve la nuova password, abilitando così il servizio ininterrotto.

Se l'autenticazione con la nuova password non riesce perché la password non è valida, il controller di dominio trusting tenta di eseguire l'autenticazione utilizzando la vecchia password. Se l'autenticazione viene eseguita correttamente con la vecchia password, riprende il processo di modifica della password entro 15 minuti.

Gli aggiornamenti delle password di trust devono essere replicati nei controller di dominio di entrambi i lati del trust entro 30 giorni. Se la password di trust viene modificata dopo 30 giorni e un controller di dominio ha solo la password N-2, non può utilizzare il trust dal lato trusting e non può creare un canale protetto sul lato attendibile.

## <a name="network-ports-used-by-trusts"></a>Porte di rete utilizzate dai trust

Poiché i trust devono essere distribuiti attraverso vari limiti di rete, potrebbe essere necessario estendersi su uno o più firewall. In questo caso, è possibile eseguire il tunneling del traffico attendibile attraverso un firewall o aprire porte specifiche nel firewall per consentire il passaggio del traffico.

> [!IMPORTANT]
> Servizi di dominio Active Directory non supporta la limitazione del traffico RPC di Active Directory a porte specifiche.

Leggere la sezione **Windows Server 2008 e versioni successive** dell'articolo del supporto tecnico Microsoft Come [configurare un firewall per](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) i domini di Active Directory e trust per informazioni sulle porte necessarie per un trust tra foreste.

## <a name="supporting-services-and-tools"></a>Servizi e strumenti di supporto

Per supportare i trust e l'autenticazione, vengono utilizzati alcuni strumenti di gestione e funzionalità aggiuntivi.

### <a name="net-logon"></a>Accesso rete

Il servizio Accesso rete gestisce un canale protetto da un computer basato su Windows a un controller di dominio. Viene inoltre utilizzato nei seguenti processi correlati al trust:

* Configurazione e gestione dell'attendibilità: Net Logon consente di mantenere le password di attendibilità, raccogliere informazioni sull'attendibilità e verificarne le relazioni tra l'interagimento con il processo LSA e il TDO.

    Per i trust tra foreste, le informazioni di trust includono il record Informazioni trust tra foreste (*FTInfo*), che include il set di spazi dei nomi che una foresta trusted dichiara di gestire, annotato con un campo che indica se ogni attestazione è considerata attendibile dalla foresta trusting.

* Autenticazione: fornisce le credenziali utente tramite un canale protetto a un controller di dominio e restituisce i SID di dominio e i diritti utente per l'utente.

* Posizione controller di dominio: consente di trovare o individuare i controller di dominio in un dominio o tra domini.

* Convalida pass-through: le credenziali degli utenti in altri domini vengono elaborate da Accesso rete. Quando un dominio trusting deve verificare l'identità di un utente, passa le credenziali dell'utente tramite Accesso rete al dominio trusted per la verifica.

* Verifica del certificato di attributo dei privilegi (PAC): quando un server che utilizza il protocollo Kerberos per l'autenticazione deve verificare la PAC in un ticket di servizio, invia la PAC attraverso il canale protetto al controller di dominio per la verifica.

### <a name="local-security-authority"></a>Autorità di sicurezza locale

L'autorità di protezione locale (LSA) è un sottosistema protetto che gestisce informazioni su tutti gli aspetti della sicurezza locale in un sistema. Collettivamente noto come politica di sicurezza locale, l'LSA fornisce vari servizi per la traduzione tra nomi e identificatori.

Il sottosistema di protezione LSA fornisce servizi sia in modalità kernel che in modalità utente per la convalida dell'accesso agli oggetti, il controllo dei privilegi utente e la generazione di messaggi di controllo. LSA è responsabile del controllo della validità di tutti i ticket di sessione presentati dai servizi in domini attendibili o non attendibili.

### <a name="management-tools"></a>Strumenti di gestione

Gli amministratori possono utilizzare *Domini e trust*di Active Directory , *Netdom* e *Nltest* per esporre, creare, rimuovere o modificare trust.

* *Domini e trust* di Active Directory è Microsoft Management Console (MMC) utilizzata per amministrare trust di dominio, livelli di funzionalità di dominio e foresta e suffissi del nome principale utente.
* Gli strumenti della riga di comando *Netdom* e *Nltest* possono essere utilizzati per trovare, visualizzare, creare e gestire trust. Questi strumenti comunicano direttamente con l'autorità LSA su un controller di dominio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle foreste di risorse, vedere Funzionamento dei trust tra foreste in Servizi di dominio Active Directory di [Azure?][concepts-trust]

Per iniziare a creare un dominio gestito di Servizi di dominio Active Directory di Azure con una foresta di risorse, vedere [Creare e configurare un dominio gestito][tutorial-create-advanced]di Servizi di dominio Active Directory di Azure. È quindi possibile [creare un trust tra foreste in uscita a un dominio locale (anteprima).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
