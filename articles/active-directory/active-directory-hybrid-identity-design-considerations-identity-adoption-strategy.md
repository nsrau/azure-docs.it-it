<properties
	pageTitle="Considerazioni di progettazione per la soluzione ibrida di gestione delle identità di Azure Active Directory: Definire la strategia di adozione della soluzione ibrida di gestione delle identità | Microsoft Azure"
	description="Il controllo di accesso condizionale consente ad Azure Active Directory di controllare le condizioni specifiche definite durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/08/2016"
	ms.author="billmath"/>  


# Definire una strategia di adozione della soluzione ibrida di gestione delle identità

In questa attività verrà definita la strategia di adozione della soluzione ibrida di gestione delle identità in modo che soddisfi i requisiti aziendali definiti in:

- [Determinare le esigenze aziendali](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Determinare i requisiti di sincronizzazione della directory](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Determinare i requisiti di autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Definire la strategia in base alle esigenze aziendali
Questa prima attività prevede la determinazione delle esigenze aziendali dell'organizzazione. È necessario prestare particolare attenzione per non correre il rischio di ampliare eccessivamente l'ambito. Iniziare da un progetto semplice che possa tuttavia essere adattato e modificato con facilità in futuro. Indipendentemente dal tipo di progetto, semplice o estremamente complesso, Azure Active Directory è la piattaforma di gestione delle identità Microsoft che supporta Office 365, Microsoft Online Services e applicazioni compatibili con il cloud.

## Definire una strategia di integrazione
Microsoft ha previsto tre principali scenari di integrazione, ovvero identità cloud, identità sincronizzate e identità federate. È necessario valutare l'adozione di una di queste strategie di integrazione. La strategia scelta può variare e le decisioni che intervengono nel processo decisionale possono basarsi sul tipo di esperienza utente che si vuole offrire, sull'eventuale infrastruttura esistente e sulla soluzione più conveniente in termini di costi.
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Gli scenari definiti nella figura precedente sono:

- **Identità cloud**: si tratta di identità che esistono solo nel cloud. Nel caso di Azure AD, risiedono nella directory di Azure AD dell'organizzazione.
- **Identità sincronizzate**: si tratta di identità che esistono sia in locale che nel cloud. Grazie ad Azure AD Connect, questi utenti vengono creati o associati ad account Azure AD esistenti. L'hash della password dell'utente viene sincronizzato dall'ambiente locale al cloud in ciò che viene chiamato un hash della password. Lo svantaggio di questa soluzione è che se un utente viene disabilitato nell'ambiente locale, la visualizzazione dello stato dell'account aggiornato in Azure AD può richiedere fino a 3 ore. Questo è dovuto all'intervallo di tempo del processo di sincronizzazione.
- **Identità federate**: si tratta di identità che esistono sia in locale che nel cloud. Grazie ad Azure AD Connect, questi utenti vengono creati o associati ad account Azure AD esistenti.
 
>[AZURE.NOTE]
Per altre informazioni sulle opzioni di sincronizzazione, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

La tabella seguente consente di determinare i vantaggi e gli svantaggi di ognuna delle strategie seguenti:

| Strategia | Vantaggi | Svantaggi: |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Identità cloud** | Più facile da gestire per le organizzazioni di piccole dimensioni. <br> Nessun software da installare in locale, non è previsto hardware aggiuntivo <br> Facile da disabilitare se l'utente lascia la società | Gli utenti devono eseguire l'accesso per usare i carichi di lavoro nel cloud <br> Le password per le identità cloud e locale possono essere uguali oppure no |
| **Identità sincronizzate** | La password locale consente di eseguire l'autenticazione sia alla directory locale che alla directory cloud <br> Più facile da gestire per le organizzazione di piccole, medie o grandi dimensioni <br> Gli utenti possono usufruire dell'accesso Single Sign-On anche solo per alcune risorse <br> Si tratta del metodo preferito da Microsoft per la sincronizzazione <br> Più facile da gestire | Alcuni clienti possono essere riluttanti a sincronizzare le directory con il cloud a causa di criteri specifici della società |
| **Federato** | Gli utenti possono usufruire dell'accesso Single Sign-On <br> Se un utente viene licenziato o lascia la società, è possibile disabilitare immediatamente l'account e revocare l'accesso <br> Supporta scenari avanzati che non sono disponibili con le identità sincronizzate | Maggior numero di attività di installazione e configurazione <br> Manutenzione più elevata <br> Può richiedere hardware aggiuntivo per l'infrastruttura del servizio token di sicurezza <br> Può richiedere hardware aggiuntivo per l'installazione del server federativo. È necessario software aggiuntivo se si usa AD FS <br> Richiede attività di configurazione estese per l'accesso Single Sign-On <br> Punto di errore critico in caso di inattività del server federativo, perché gli utenti non potranno eseguire l'autenticazione |

### Esperienza client
La strategia scelta determinerà l'esperienza di accesso per gli utenti. Le tabelle seguenti forniscono informazioni su ciò che gli utenti dovranno aspettarsi dall'esperienza di accesso. Si noti che non tutti i provider di identità federate supportano l'accesso Single Sign-On in tutti gli scenari.

**Applicazioni di rete privata e aggiunte a un dominio**:
 

| | Identità sincronizzata | Identità federata |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Web browser | Autenticazione basata su form | Single Sign-On, potrebbe essere richiesto di fornire l'ID aziendale |
| Outlook | Richiesta di credenziali | Richiesta di credenziali |
| Skype for Business (Lync) | Richiesta di credenziali | Single Sign-On per Lync, richiesta di credenziali per Exchange |
| OneDrive for Business | Richiesta di credenziali | Single Sign-On |
| Abbonamento a Office Pro Plus | Richiesta di credenziali | Single Sign-On |

**Risorse esterne o non attendibili**:

| | Identità sincronizzata | Identità federata |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Web browser | Autenticazione basata su form | Autenticazione basata su form |
| Outlook, Skype for Business (Lync), OneDrive for Business, abbonamento a Office| Richiesta di credenziali | Richiesta di credenziali |
| Exchange ActiveSync | Richiesta di credenziali | Single Sign-On per Lync, richiesta di credenziali per Exchange |
| App per dispositivi mobili | Richiesta di credenziali | Richiesta di credenziali |

Se nel corso dell'attività 1 è stato determinato che è in uso un provider di identità di terze parti o che ne verrà usato uno per la federazione con Azure AD, è necessario essere a conoscenza delle funzionalità supportate seguenti:
- Qualsiasi provider SAML 2.0 conforme per il profilo SP-Lite può supportare l'autenticazione ad Azure AD e alle applicazioni associate
- Supporta l'autenticazione passiva che semplifica l'autenticazione a Outlook Web App, SharePoint Online e così via
- I client di Exchange Online possono essere supportati usando il profilo Enhanced Client Profile per SAML 2.0

È anche importante conoscere quali funzionalità non saranno disponibili:

- Senza WS-Trust/Federation tutti gli altri client attivi verranno interrotti
 - Questo significa che non sarà possibile usare le versioni del client Lync, del client OneDrive, dell'abbonamento a Office e di Office Mobile precedenti a Office 2016
- La transizione di Office all'autenticazione passiva consentirà di supportare solo provider di identità SAML 2.0, ma il supporto sarà garantito esclusivamente client per client


>[AZURE.NOTE]
Per l'elenco più aggiornato, vedere l'articolo http://aka.ms/ssoproviders.

## Definire la strategia di sincronizzazione
Questa attività permette di definire gli strumenti che verranno usati per sincronizzare i dati locali dell'organizzazione con il cloud e la topologia da adottare. Poiché la maggior parte delle organizzazioni usa Active Directory, vengono fornite informazioni più dettagliate su come usare Azure AD Connect per rispondere alle domande precedenti. Per gli ambienti in cui non è presente Active Directory, vengono fornite informazioni sull'uso di FIM 2010 R2 o MIM 2016 per poter pianificare questa strategia. Tuttavia, le versioni future di Azure AD Connect supporteranno le directory LDAP, quindi, a seconda dei tempi previsti per l'adozione, è possibile che queste informazioni risultino utili.

###Strumenti di sincronizzazione
Negli anni sono stati creati numerosi strumenti di sincronizzazione per diversi scenari. Al momento, Azure AD Connect è lo strumento preferito per tutti gli scenari supportati. Anche AAD Sync e DirSync sono ancora in circolazione e potrebbero ancora essere presenti nell'ambiente in uso.

>[AZURE.NOTE]
Per le informazioni più recenti relative alle funzionalità supportate per ogni strumento, vedere l'articolo [Confronto degli strumenti di integrazione directory](active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### Topologie supportate
Quando si definisce una strategia di sincronizzazione, è necessario determinare la topologia da usare. A seconda delle informazioni definite nel passaggio 2, è possibile stabilire la topologia corretta da usare. La topologia singola di Azure AD a foresta singola è quella più comune ed è costituita da una singola foresta Active Directory e una singola istanza di Azure AD. Questa topologia verrà usata nella maggior parte degli scenari ed è la topologia prevista quando si usa l'installazione rapida di Azure AD Connect come illustrato nella figura seguente.
 
![](./media/hybrid-id-design-considerations/single-forest.png) Scenario a foresta singola Le organizzazioni di grandi e piccole dimensioni hanno spesso più foreste, come illustrato nella figura 5.

>[AZURE.NOTE]
Per altre informazioni sulle diverse topologie locali e di Azure AD con il servizio di sincronizzazione Azure AD Connect, vedere l'articolo [Topologie per Azure AD Connect](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png)

Scenario a più foreste

In questo caso, se le affermazioni seguenti sono vere, sarà necessario valutare la topologia singola di Azure AD a più foreste:

- Gli utenti hanno una sola identità in tutte le foreste. La sezione relativa all'identificazione univoca degli utenti riportata di seguito descrive in dettaglio questo aspetto.
- L'utente esegue l'autenticazione alla foresta in cui risiede la propria identità
- Il nome dell'entità utente e l'ancoraggio di origine (ID immutabile) provengono da questa foresta
- Azure AD Connect può accedere a tutte le foreste. Questo significa che non è necessario che venga aggiunto a un dominio e che può essere collocato in una rete perimetrale, se risulta più semplice
- Gli utenti hanno una sola cassetta postale
- La foresta che ospita la cassetta postale dell'utente garantisce la migliore qualità dei dati per gli attributi visibili nell'Elenco indirizzi globale di Exchange
- Se non è presente una cassetta postale per l'utente, è possibile usare qualsiasi foresta per recuperare questi valori
- Se è disponibile una cassetta postale collegata, è presente anche un altro account in una foresta diversa usato per l'accesso.

>[AZURE.NOTE]
Gli oggetti presenti sia in locale sia nel cloud sono "connessi" tramite un identificatore univoco. Nel contesto della sincronizzazione della directory questo identificatore univoco viene indicato come SourceAnchor. Nel contesto del servizio Single Sign-On invece questo identificatore viene indicato come ImmutableId. Per altre considerazioni sull'uso dell'attributo SourceAnchor, vedere [Concetti di progettazione per Azure AD Connect](active-directory-aadconnect-design-concepts.md#sourceanchor).

Se le affermazioni precedenti non sono vere e sono presenti più account attivi o più cassette postali, Azure AD Connect selezionerà un account o una cassetta postale e ignorerà gli altri. Se sono presenti cassette postali collegate ma non sono presenti altri account, gli account non verranno esportati in Azure AD e l'utente non sarà membro di alcun gruppo. Questo comportamento è diverso da quanto accadeva in passato con DirSync ed è intenzionale, al fine di garantire un supporto migliore degli scenari con più foreste. La figura seguente mostra uno scenario con più foreste.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png)
 
**Topologia multipla di Azure AD a più foreste**

È opportuno che in Azure AD sia presente una singola directory per ogni organizzazione ma è supportato lo scenario multiplo se si mantiene una relazione 1:1 tra il server del servizio di sincronizzazione Azure AD Connect e una directory Azure AD. Per ogni istanza di Azure AD, sarà necessaria un'installazione di Azure AD Connect. Per impostazione predefinita, Azure AD è isolato e gli utenti in una istanza di Azure AD non potranno visualizzare gli utenti in un'altra istanza.

È invece consentita e supportata la connessione di un'istanza locale di Active Directory a più directory di Azure AD, come illustrato nella figura seguente:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png)
 

**Scenario di filtro a singola foresta**

Per questo scenario, devono essere vere le affermazioni seguenti:

- I server del servizio di sincronizzazione Azure AD Connect devono essere configurati per l'applicazione di filtri, in modo che ogni server includa un set di oggetti che si escludono a vicenda su cui operare, ad esempio definendo l'ambito di ogni server in modo che corrisponda a un dominio o a una unità organizzativa specifica.
- Un dominio DNS può essere registrato solo in una singola directory di Azure AD, in modo che anche i nomi dell'entità utente (UPN) degli utenti nell'istanza locale di AD possano usare spazi dei nomi separati
- Gli utenti in un'istanza di Azure AD potranno visualizzare solo gli utenti inclusi nella stessa istanza, ma non quelli in altre istanze
- Solo una delle directory di Azure AD può abilitare la distribuzione ibrida di Exchange con l'istanza locale di Active Directory
- L'esclusione reciproca si applica anche al writeback. Alcune funzionalità di writeback non sono quindi supportate con questa topologia, perché presuppongono una singola configurazione locale. Sono inclusi:
 - Writeback dei gruppi con la configurazione predefinita
 - Writeback dei dispositivi


Tenere presente che le funzionalità seguenti non sono supportate e non devono essere scelte come implementazione:

- Non è supportato lo scenario che prevede più server del servizio di sincronizzazione Azure AD Connect connessi alla stessa directory di Azure AD, anche se sono configurati per sincronizzare set di oggetti che si escludono a vicenda
- Non è supportata la sincronizzazione dello stesso utente in più directory di Azure AD.
- Non è inoltre supportata la modifica della configurazione per fare in modo che gli utenti di un'istanza di Azure AD vengano visualizzati come contatti in un'altra directory di Azure AD.
- Non è consentito modificare il servizio di sincronizzazione Azure AD Connect per la connessione a più directory di Azure AD.
- Come previsto da progettazione, le directory di Azure AD sono isolate. Non è supportata la modifica della configurazione del servizio di sincronizzazione Azure AD Connect in modo da leggere dati da un'altra directory di Azure AD nel tentativo di creare un elenco di indirizzi globale comune e unificato tra le directory. Non è inoltre supportata l'esportazione di utenti come contatti in un'altra istanza locale di AD mediante il servizio di sincronizzazione Azure AD Connect.


>[AZURE.NOTE]
Se l'organizzazione impedisce ai computer inclusi nella rete di connettersi a Internet, questo articolo elenca gli endpoint (FQDN e intervalli di indirizzi IPv4 e IPv6) da includere nell'elenco delle connessioni in uscita e nell'area siti attendibili di Internet Explorer dei computer client per garantire che i computer possano usare Office 365. Per altre informazioni, vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=it-IT&rs=it-IT&ad=US).

## Definire la strategia di autenticazione a più fattori
In questa attività verrà definita la strategia di autenticazione a più fattori da usare. Azure Multi-Factor Authentication è disponibile in due diverse versioni. Una è basata sul cloud mentre l'altra è locale e usa il server Azure MFA. In base alla valutazione effettuata in precedenza, è possibile stabilire qual è la soluzione corretta per la strategia scelta. Usare la tabella seguente per determinare l'opzione di progettazione che meglio soddisfa i requisiti di sicurezza della società:

Opzioni di progettazione per l'autenticazione a più fattori:

| Asset da proteggere | Autenticazione a più fattori nel cloud | Autenticazione a più fattori in locale |
|---------------------------------------------------------------|------------------|----------------|
| App Microsoft | sì | sì |
| App SaaS nella Raccolta di app | sì | sì |
| Le applicazioni IIS pubblicate tramite proxy app per Azure AD | sì | sì |
| Applicazioni IIS non pubblicate tramite il proxy applicazione Azure AD | no | sì |
| Accesso remoto, ad esempio VPN, Gateway Desktop remoto | no | sì |

Anche se è già stata scelta una soluzione per la strategia, è comunque necessario riesaminare la valutazione riportata in precedenza a seconda di dove risiedono gli utenti ed eventualmente cambiare il tipo di soluzione. A questo scopo, usare la tabella seguente:

| Posizione degli utenti | Opzione di progettazione preferita |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory | Multi-Factor Authentication nel cloud |
| Azure AD e AD locale usando la federazione con AD FS | Entrambi |
| Azure AD e Active Directory locale con Azure AD Connect, senza sincronizzazione delle password | Entrambi |
| Azure AD e Active Directory locale con Azure AD Connect, con sincronizzazione delle password | Entrambi |
| Active Directory locale | Server Multi-Factor Authentication |

>[AZURE.NOTE]
È anche necessario assicurarsi che l'opzione di progettazione per l'autenticazione a più fattori selezionata supporti le funzionalità richieste. Per altre informazioni, leggere [Scegliere la soluzione di sicurezza a più fattori più adatta](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## Provider di Multi-Factor Authentication
La modalità Multi-Factor Authentication è disponibile per impostazione predefinita per gli amministratori globali che dispongono di un tenant Azure Active Directory. Tuttavia, se si intende estendere l'autenticazione a più fattori a tutti gli utenti e/o si vuole consentire agli amministratori globali di sfruttare i vantaggi offerti da funzionalità quali il portale di gestione, i messaggi di saluto personalizzati e i report, sarà necessario acquistare e configurare un provider Multi-Factor Authentication.

>[AZURE.NOTE]
È anche necessario assicurarsi che l'opzione di progettazione per l'autenticazione a più fattori selezionata supporti le funzionalità richieste.

##Passaggi successivi
[Determinare i requisiti di protezione dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0810_2016-->