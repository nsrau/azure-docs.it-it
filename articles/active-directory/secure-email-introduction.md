---
title: Proteggere le configurazioni e i criteri di posta elettronica con Azure AD | Microsoft Docs
description: Descrive i suggerimenti di Microsoft e i concetti di base per la distribuzione sicura di criteri e configurazioni di posta elettronica.
author: jeffgilb
ms.service: guidance
ms.topic: article
ms.date: 06/14/2017
ms.author: pnp
pnp.series.title: Best Practices
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: bb869e67340aa7a9f645c0b51f5a44d91ab51ce8
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---

# <a name="secure-email-policies-and-configurations"></a>Proteggere le configurazioni e i criteri di posta elettronica
 
## <a name="introduction"></a>Introduzione

I consigli riportati in questo documento illustrano le linee guida generali di Microsoft per l'applicazione di criteri e configurazioni di posta elettronica in tutta l'organizzazione. Questi consigli assicurano ai dipendenti la sicurezza e la produttività.  In particolare, questo documento è incentrato sulla protezione della posta elettronica aziendale riducendo al minimo l'impatto di usabilità per gli utenti.

### <a name="intended-audience"></a>Destinatari

Questo documento è destinato ad architetti e professionisti IT dell'infrastruttura aziendale che hanno familiarità con i prodotti [Exchange Online](https://technet.microsoft.com/library/jj200580.aspx) (Office 365) e [Microsoft Enterprise Mobility + Security](http://microsoft.com/ems). Questi prodotti includono, ma non in via esclusiva, Azure Active Directory per l'identità, Microsoft Intune per la gestione dei dispositivi e Azure Information Protection per la protezione dei dati. 

### <a name="customer-environment"></a>Ambiente del cliente

I criteri consigliati in questo documento sono applicabili alle organizzazioni aziendali che operano interamente nell'ambito del cloud di Microsoft e per i clienti con infrastrutture distribuite tra cloud locali e di Microsoft. 

### <a name="assumptions"></a>Presupposti

La maggior parte dei consigli forniti in questo documento usa i servizi disponibili solo con le sottoscrizioni Enterprise Mobility + Security (EMS) E5. I consigli presentati implicano la completa funzionalità della sottoscrizione EMS E5. 

Questo documento descrive come applicare la configurazione consigliata alla posta elettronica protetta in un ambiente appena distribuito.  I documenti futuri possono contenere informazioni aggiuntive su come eseguire la migrazione dai criteri e dalle configurazioni esistenti a quelli indicati successivamente.

### <a name="caveats"></a>Avvertenze

L'organizzazione potrebbe essere soggetta a requisiti normativi o di conformità, che includono indicazioni specifiche per cui potrebbe essere necessario applicare i criteri diversi dalle configurazioni consigliate.  Per tali configurazioni è consigliabile servirsi di controlli non disponibili in precedenza.  È consigliabile usare tali controlli in quanto si ritiene che rappresentino un compromesso tra sicurezza e produttività.  

Microsoft si è impegnata al massimo per rispettare un'ampia gamma di requisiti per la protezione aziendali. Tuttavia, non è possibile rispettare requisiti o tutti gli aspetti specifici di ogni organizzazione. È possibile usare questo documento come guida per le corrette modalità di applicazione dei criteri nell'organizzazione ideati da Microsoft e dal team Secure Productive Enterprise. 

>[!NOTE]
>Per una panoramica dei concetti principali necessari per comprendere le funzionalità di protezione descritte in questi suggerimenti, vedere [EMS and Office 365 Service Descriptions](secure-email-ems-office365-service-descriptions.md) (Descrizioni dei servizi EMS e Office 365).
>

## <a name="core-concepts"></a>Concetti principali

Tutte le misure di sicurezza esistenti diventano inutili quando gli utenti che nel tentare di portare a termine il proprio lavoro incontrano ostacoli e ignorano i criteri di sicurezza aziendale. Single Sign-On di Azure AD tenta di ridurre al minimo il carico degli utenti. Questo consente agli utenti di mantenere la produttività mentre applicano i criteri di controllo di accesso dell'organizzazione. 

### <a name="single-sign-on-authentication"></a>Autenticazione Single Sign-On

Il diagramma seguente illustra un tipico flusso di autenticazione SSO:

![Esperienza dell'utente finale di Single Sign-On](./media/secure-email/typical-authentication-flow.png)

Per avviare l'autenticazione, il client invia le credenziali, ad esempio nome utente e password, e/o gli elementi SSO ottenuti in precedenza in Azure AD. Un elemento SSO può essere un token di sessione del browser o un token di aggiornamento per applicazioni complesse. 

Azure AD verifica le credenziali e/o l'elemento SSO e valuta tutti i criteri applicabili. Rilascia quindi un token di accesso per il provider di risorse, Exchange Online nella figura. Azure AD rilascia anche un elemento SSO, come parte della risposta per consentire al client ottenere l'SSO da richieste future. 

Il client archivia l'elemento SSO e invia il token di accesso come prova di identità al provider di risorse. Dopo aver verificato il token di accesso ed eseguito i controlli necessari, Exchange Online concede l'accesso al client ai messaggi di posta elettronica.

#### <a name="single-sign-on-sso-refresh-tokens"></a>Token di aggiornamento Single sign-on (SSO)

È possibile ottenere SSO in diversi modi. Ad esempio, i cookie di un provider di identità possono essere usati come elemento SSO per archiviare in un browser lo stato di accesso di un utente. Dono possibili successivi tentativi di accesso automatico, ovvero senza richiesta di credenziali, alle applicazioni tramite lo stesso provider di identità. 

Quando un utente esegue l'autenticazione con Azure AD, viene stabilita una sessione Single Sign-On con Azure AD e il browser dell'utente. Questa sessione è rappresentata dal token di sessione SSO, sotto forma di cookie. Azure AD usa due tipi di token di sessione SSO, ovvero permanenti e non permanenti. I token di sessione permanenti vengono archiviati come cookie permanenti dai browser quando è selezionata la casella di controllo "Mantieni l'accesso" durante l'accesso. I token di sessione non permanenti vengono archiviati come cookie di sessione e vengono eliminati quando si chiude il browser. 

Per le applicazioni affidabili in grado di usare protocolli di autenticazione moderni, come [OpenId Connect](http://openid.net/specs/openid-connect-core-1_0.html) e [OAuth 2.0](https://tools.ietf.org/html/rfc6749), SSO viene abilitato con i token di aggiornamento come elementi SSO, oltre ai cookie SSO descritti in precedenza. I token di aggiornamento vengono presentati a un server di autorizzazione quando un'applicazione richiede un nuovo token di accesso. 

Il token di aggiornamento contiene le attestazioni e gli attributi sul tipo di metodo di autenticazione usata per autenticare gli utenti. Ad esempio, se un utente ha eseguito correttamente l'autenticazione con più metodi, ad esempio con nome utente e password e l'autenticazione basata su telefono, nel token di aggiornamento è presente un'attestazione di autenticazione a più fattori (MFA). Potrebbero esserci anche altre attestazioni che contengono dati, ad esempio la durata della validità dell'autenticazione a più fattori e così via. 

I token di aggiornamento consentono al client di ottenere un nuovo token di accesso, senza la necessità di eseguire una nuova autenticazione interattiva. I token di aggiornamento durano più a lungo rispetto ai token di accesso e possono essere riscattati per ottenere un nuovo accesso e aggiornare una coppia di token. I nuovi token di aggiornamento ottenuti possono quindi essere sempre usati per recuperare un altro set di token di accesso e aggiornamento. 

Il client continua il processo SSO fino alla scadenza dell'impostazione relativa al tempo di inattività massimo per il token di aggiornamento, alla scadenza della durata del token di aggiornamento o alla modifica dei requisiti dei criteri di autenticazione e autorizzazione. Questa modifica si verifica al momento dell'emissione del token di aggiornamento originale. Anche per importanti modifiche dell'attributo utente, ad esempio la reimpostazione della password, è necessario generare un nuovo token di autenticazione. Il client deve eseguire una nuova autenticazione interattiva per proseguire. Ciò significa essenzialmente che si verifica un'interruzione del processo SSO mai successa fino a ora. 

#### <a name="conditional-access"></a>Accesso condizionale

Azure AD, come server di autorizzazione per le applicazioni, determina se rilasciare i token di accesso in base a una valutazione dei criteri di accesso condizionali applicati alla risorsa a cui si sta tentando di accedere. Se vengono soddisfatti i requisiti dei criteri, vengono rilasciati un token di accesso e un token di aggiornamento aggiornato. Se i criteri non vengono soddisfatti, l'utente a riceve istruzioni su come soddisfarli e/o gli viene richiesti di completare i passaggi aggiuntivi, tra cui l'autenticazione a più fattori (MFA).  Dopo aver completato l'autenticazione a più fattori, la relativa attestazione viene aggiunta al token di aggiornamento risultante.  

Le attestazioni nel token di aggiornamento vengono accumulate nel tempo. Alcune attestazioni hanno scadenze, superate le quali non sono più considerate durante i controlli di autorizzazione. In alcuni casi, ciò può causare risultati imprevisti. Ad esempio, se i criteri di accesso condizionale sono configurati in modo che l'autenticazione a più fattori sia necessaria per i tentativi di autenticazione provenienti da posizioni extranet. In questo caso, gli utenti potrebbero talvolta non ricevere la richiesta di autenticazione a più fattori prevista quando accedono a una risorsa extranet. Un possibile spiegazione è che l'utente potrebbe avere eseguito l'autenticazione a più fattori poco prima di lasciare intranet. Pertanto, il token di accesso contiene un'attestazione di autenticazione a più fattori valida. L'attestazione di autenticazione a più fattori soddisfa i requisiti del criterio e pertanto Azure AD non richiede all'utente un'autenticazione a più fattori aggiuntiva.

#### <a name="token-lifetime-policy"></a>Criteri per la durata dei token

Oltre la scadenza delle singole attestazioni in un token, anche i token stessi hanno una scadenza. Come accennato prima, i token scaduti sono uno dei motivi per cui il servizio SSO potrebbe essere interrotto. È possibile impostare la durata di un token emesso da Azure AD tramite i [criteri di durata del token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes). Come può essere dedotto da quanto esposto sopra, la definizione dei contorni di una sessione SSO è complessa. Ad esempio, quando le app affidabili come vari fattori che sono apparentemente disconnesse possono influire sulla durata di una sessione SSO.

>[!NOTE]
>Gli amministratori globali di Azure AD possono controllare i periodi di inattività e validità dei token di aggiornamento. Per informazioni sui token di accesso e le attestazioni tramite impostazioni consultare l'articolo [Durata dei token configurabili in Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes).
>

#### <a name="primary-refresh-tokens"></a>Token di aggiornamento primari

Finora, questo articolo ha illustrato il funzionamento del Single Sign-On nel contesto di un unico client. Ma ciò non è sufficiente per l'uso di Single Sign-On in un'unica app. Oggi, gli utenti hanno flussi di lavoro interattivo che si estende su più applicazioni all'interno della stessa suite di applicazioni, ad esempio Microsoft Office. Gli utenti devono accedere alle applicazioni non correlate, incluse le applicazioni LOB sviluppate internamente. 

In genere i dispositivi Windows aggiunti a un dominio, che usano l'autenticazione integrata di Windows, ovvero Kerberos, ottengono un Single Sign-On di alto livello in più applicazioni e risorse. Queste app includono i browser supportati, ad esempio Internet Explorer o Edge. Lo stesso accade per Azure AD, sotto forma di un token di aggiornamento primario. 

Questo token di aggiornamento primario con privilegi è disponibile solo per un insieme di entità client, ad esempio componenti di sistema della piattaforma. Le entità possono quindi consentire l'accesso all'autenticazione negoziato per altre applicazioni client, in modo da poter offrire anche un'esperienza SSO semplice. 

Per gli utenti di Office 365 sui dispositivi [iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios) e [Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android), è stato fatto un lavoro aggiuntivo per ridurre il numero di autenticazioni obbligatorie applicando la funzionalità di broker di autenticazione. Questa funzionalità è incorporata nelle app Microsoft Authenticator del Portale aziendale Intune.

>[!NOTE]
>I consigli descritti in questo documento presuppongono che una delle app sia stata distribuita sui dispositivi iOS o Android degli utenti. Ad esempio, un'app come un broker di autenticazione, quale Microsoft Authenticator o il Portale aziendale Intune.
>

### <a name="multi-factor-authentication"></a>Autenticazione a più fattori

L'autenticazione a più fattori (MFA) offre un alto livello di attendibilità per quanto riguarda il soggetto dell'autenticazione, che ha dato diverse prove o evidenze sulla propria identità. Le prove possono essere segreti prestabiliti di cui solo il soggetto e l'autorità sono a conoscenza o un'entità fisica che solo il soggetto dovrebbe possedere. L'autenticazione a più fattori in genere viene eseguita in fasi. Innanzitutto stabilisce l'identità usando le password e come secondo fattore richiede un metodo di autenticazione diverso, meno soggetto ad attacchi dannosi, o viceversa.

Diverse autorità possono interpretare in modo diverso l'autenticazione a più fattori o l'autenticazione avanzata. Ad esempio, alcune autorità, o in particolare gli amministratori che configurano i criteri di tali autorità, possono scegliere di interpretare l'autenticazione basata su smart card fisica come autenticazione a più fattori. Può succedere che, anche nel senso stretto della parola, l'autenticazione tramite smart card si l'autenticazione che prevede una sola fase. 

La combinazione tra la richiesta di una smart card fisica e la necessità di immettere un PIN segreto per usare la smart card può essere interpretata come autenticazione a più fattori. Altri ancora possono scegliere di essere più flessibili per quanto riguarda la frequenza con è necessario eseguire altri metodi di autenticazione più onerosi. Pertanto le autenticazioni tradizionali, che avvengono tra le autenticazioni più avanzate, vengono considerate un metodo valido per le risorse che in genere richiedono l'autenticazione avanzata. Ad esempio, in alcune organizzazioni è possibile che venga richiesto a un utente di effettuare l'autenticazione a più fattori a distanza di poche ore o giorni. Il periodo di tempo dipende dalla riservatezza delle risorse che si protegge e dal periodo di tempo in cui la posizione fisica dell'utente che tenta di accedere a una risorsa non cambia.

Azure AD e AD FS usano l'attestazione dell'autenticazione a più fattori per indicare se l'autenticazione viene eseguita con l'autenticazione a più fattori. Per impostazione predefinita, Azure AD rilascia i token con attestazione di autenticazione a più fattori quando l'autenticazione viene eseguita con [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud) o [Windows Hello for Business](https://docs.microsoft.com/en-us/windows/access-protection/hello-for-business/hello-identity-verification). In uno scenario federativo Azure AD rispetta l'attestazione di autenticazione a più fattori dai provider di identità federative, ad esempio AD FS, e si porta l'attestazione di autenticazione a più fattori nei token. 

### <a name="security-guidelines"></a>Linee guida sulla sicurezza

Questa sezione contiene le linee guida di sicurezza generali da seguire quando si implementa una delle raccomandazioni per la protezione della posta elettronica descritte nelle sezioni successive.

#### <a name="security-and-productivity-trade-offs"></a>Compromessi tra sicurezza e produttività

Tra sicurezza e produttività è necessario fare un compromesso. Per comprendere questi compromessi, viene generalmente usata la triade Sicurezza-Funzionalità-Usabilità/Facilità d'uso (SFU):

![Compromessi tra sicurezza e produttività](./media/secure-email/security-triad.png)

In base al modello SFU, le raccomandazioni in questo documento si basano sui principi seguenti: 

* Conoscere i destinatari: essere flessibile in base alla barra di sicurezza/funzione del processo
* Applicare i criteri di sicurezza al momento giusto e assicurarsi che vengano applicati correttamente

#### <a name="administrators-versus-users"></a>Amministratori e utenti

Si consiglia di creare gruppi di sicurezza che contengano tutti gli utenti che dispongono di account amministrativi o che sono idonei alla ricezione di un account o di privilegi amministrativi temporanei. Questi gruppi di sicurezza vengono usati per definire i criteri di accesso condizionale specifici per gli amministratori di Azure AD e di Office 365.  

Le raccomandazioni per i criteri prendono in considerazione i privilegi associati a un account. I ruoli di [amministratore di Office 365](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) ruoli hanno sostanzialmente più privilegi rispetto a quelli di Exchange Online e Office 365. Pertanto, le raccomandazioni relative ai criteri per questi account sono più rigide per gli account utente normali. Tutti i criteri che fanno riferimento agli amministratori indicano il criterio consigliato per gli account di amministrazione di Office 365.

#### <a name="reduce-the-number-of-accounts-with-persistent-admin-access"></a>Ridurre il numero di account con accesso amministratore permanente

Usare Privileged Identity Management di Azure AD per ridurre il numero di account amministrativi permanenti. È anche consigliabile che gli amministratori di Office 365 abbiano un account utente separato per il normale uso non amministrativo. Questi dovranno usare il proprio account amministrativo solo quando risulti necessario al completamento di un'attività associata alla relativa funzione del processo.

## <a name="next-steps"></a>Passaggi successivi
[Criteri consigliati](secure-email-recommended-policies.md)

