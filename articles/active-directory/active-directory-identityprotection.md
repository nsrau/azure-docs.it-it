<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Informazioni su come Azure AD Identity Protection consente di limitare la possibilità di un utente malintenzionato di sfruttare un'identità o un dispositivo compromesso e di proteggere un'identità o un dispositivo che in precedenza è stato sospettato o ritenuto essere compromesso."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection è un servizio di sicurezza che fornisce una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. Microsoft protegge le identità basate sul cloud da oltre dieci anni. Con Azure AD Identity Protection, questi stessi sistemi di protezione sono ora a disposizione dei clienti aziendali. Identity Protection si avvale delle funzionalità di rilevamento anomalie di Azure AD, disponibili tramite i report di Anomalie dell'attività di Azure AD, e introduce nuovi tipi di eventi di rischio che permettono di rilevare anomalie in tempo reale.

## Limitazioni per l'anteprima corrente
Questa sezione elenca le limitazioni che si applicano all'anteprima corrente di Azure Active Directory Identity Protection.

### Limitazione per paese o area geografica

L'anteprima di Azure Active Directory Identity Protection è attualmente disponibile solo per le directory con il valore **Stati Uniti** per **Paese o area geografica**. <br><br> ![Correzione](./media/active-directory-identityprotection/222.png "Correzione")


### Identity Protection e domini federati

L'anteprima di Azure Active Directory Identity Protection presenta le limitazioni seguenti in combinazione con i domini federati:

- I criteri di protezione non funzionano per i domini federati

- Gli eventi di rischio vengono rilevati solo per le app federate con Azure Active Directory

##Introduzione

La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Gli utenti malintenzionati hanno messo a punto tecniche sempre più efficaci per sfruttare le violazioni di terze parti e sferrare sofisticati attacchi di phishing. L'accesso a un account utente, anche uno con privilegi limitati, permette agli utenti malintenzionati di accedere a risorse aziendali importanti in modo relativamente semplice tramite il movimento laterale. È quindi fondamentale proteggere tutte le identità e prevenire attivamente lo sfruttamento di eventuali identità compromesse.

Trovare le identità compromesse non è un compito facile. Identity Protection è utile a questo scopo, perché usa l'euristica e algoritmi adattivi di Machine Learning per rilevare anomalie ed eventi di rischio che possono indicare la compromissione di un'identità.
 
Sulla base di tali dati, Identity Protection genera report e avvisi che consentono di analizzare gli eventi di rischio e adottare le azioni di correzione o mitigazione appropriate.
 
Ma Azure Active Directory Identity Protection è più di un semplice strumento di monitoraggio e reporting. In base agli eventi di rischio, Identity Protection calcola un livello di rischio utente per ogni utente e permette di configurare criteri basati sul rischio per proteggere automaticamente le identità dell'organizzazione. I criteri basati sul rischio, insieme ad altri controlli di accesso condizionale forniti da Azure Active Directory e da Servizi di gestione emergenze, possono eseguire il blocco automatico o proporre azioni di correzione adattive, incluse la reimpostazione della password e l'applicazione dell'autenticazione a più fattori.

####Funzionalità di Identity Protection 

**Rilevamento di eventi di rischio e account rischiosi:**

- Rilevamento di sei tipi di eventi di rischio tramite regole euristiche e Machine Learning. 

- Calcolo dei livelli di rischio utente.

- Raccomandazioni personalizzate per migliorare il comportamento di sicurezza in generale evidenziando le vulnerabilità.

<br>

**Analisi degli eventi di rischio:**

- Invio di notifiche per gli eventi di rischio.

- Analisi degli eventi di rischio con informazioni rilevanti e contestuali.

- Flussi di lavoro di base per tenere traccia delle analisi.

- Accesso semplificato ad azioni di correzione come la reimpostazione della password.

<br>

**Criteri di accesso condizionale basati sul rischio:**

- Criteri per mitigare gli accessi rischiosi con il blocco degli accessi o le richieste di autenticazione a più fattori.

- Criteri per bloccare o proteggere gli account utente rischiosi.

- Criteri per richiedere la registrazione degli utenti per l'autenticazione a più fattori


## Rilevamento e rischio

### Eventi di rischio

Gli eventi di rischio sono eventi contrassegnati come sospetti da Identity Protection e indicano che un'identità potrebbe essere compromessa. Per un elenco completo degli eventi di rischio, vedere l'articolo [Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md).

Alcuni di questi eventi di rischio sono disponibili nei report di Anomalie dell'attività di Azure AD nel portale di gestione di Azure. La tabella seguente riporta un elenco dei vari tipi di eventi di rischio e i report di **Anomalie dell'attività di Azure AD corrispondenti**. Microsoft continua a investire in questo spazio e intende migliorare la precisione di rilevamento degli eventi di rischio esistenti e aggiungere nuovi tipi di eventi di rischio in modo continuativo.



| Tipo di evento di rischio di Identity Protection | Report di Anomalie dell'attività di Azure AD corrispondente |
| :-- | :-- |
| Credenziali perse | Utenti con credenziali perse |
| Trasferimento impossibile a posizioni atipiche |	Attività di accesso irregolare |
| Accessi da dispositivi infetti | Accessi da dispositivi potenzialmente infetti |
| Accessi da indirizzi IP anonimi | Accessi da origini sconosciute |
| Accessi da indirizzi IP con attività sospette |	Accessi da indirizzi IP con attività sospette |
| Accessi da posizioni non note | - | | Eventi di blocco (non nell'anteprima pubblica) | - |

I report di Anomalie dell'attività di Azure AD seguenti non sono inclusi come eventi di rischio in Azure AD Identity Protection e non sono quindi disponibili in Identity Protection. Questi report sono ancora disponibili nel portale di gestione di Azure, ma in futuro saranno deprecati perché progressivamente sostituiti dagli eventi di rischio di Identity Protection.

- Accessi dopo più errori
- Accessi da più aree geografiche

### Livello di rischio

Il livello di rischio è un'indicazione della gravità, misurata come alta, media o bassa, di un evento di rischio. Il livello di rischio consente agli utenti di Identity Protection di classificare in ordine di priorità le azioni da intraprendere per ridurre il rischio per l'organizzazione. La gravità dell'evento di rischio rappresenta il grado di probabilità che l'identità sia compromessa, unita alla quantità di disturbo che in genere produce.

- **Alta**: evento di rischio con livello di gravità elevato e attendibilità elevata. Questi eventi sono fortemente indicativi di una compromissione dell'identità dell'utente e tutti gli account utente interessati devono essere corretti.

- **Media**: evento di rischio con livello di gravità elevato e minore attendibilità o viceversa. Questi eventi sono potenzialmente rischiosi e tutti gli account utente interessati devono essere corretti.

- **Bassa**: evento di rischio con livello di gravità basso e attendibilità bassa. Questi eventi potrebbero non richiedere un'azione immediata, ma in combinazione con altri eventi di rischio possono essere fortemente indicativi di una compromissione dell'identità.


![Livello di rischio](./media/active-directory-identityprotection/01.png "Livello di rischio")

 

Gli eventi di rischio vengono identificati **in tempo reale** oppure offline in fase di post-elaborazione, dopo che l'evento di rischio si è già verificato. Attualmente, la maggior parte degli eventi di rischio in Identity Protection viene calcolata offline e visualizzata in Identity Protection da due a quattro ore dopo. Durante la valutazione in tempo reale, gli eventi di rischio in tempo reale vengono visualizzati nella console di Identity Protection da cinque a dieci minuti dopo.

Attualmente, diversi client legacy non supportano il rilevamento e la prevenzione degli eventi di rischio in tempo reale. Di conseguenza, non è possibile rilevare o prevenire in tempo reale gli accessi da tali client.


## Analisi
L'esperienza con Identity Protection inizia in genere dal relativo dashboard.

<br><br> ![Correzione](./media/active-directory-identityprotection/29.png "Correzione") <br>

Il dashboard consente di accedere a:
 
- Report, ad esempio **Utenti contrassegnati per il rischio**, **Eventi di rischio** e **Vulnerabilità**
- Impostazioni come la configurazione dei **criteri di sicurezza**, delle **notifiche** e della **registrazione per l'autenticazione a più fattori**
 

Si tratta in genere del punto di partenza dell'analisi, ovvero del processo di analisi di attività, log e altre informazioni rilevanti relative a un evento di rischio per decidere se sono necessarie procedure di correzione o mitigazione, se e come l'identità è stata compromessa e come è stata usata l'identità compromessa.

È possibile collegare le attività di analisi alle [notifiche](active-directory-identityprotection-notifications.md) che Azure Active Directory Protection invia per posta elettronica.

Le sezioni seguenti forniscono altre informazioni e i passaggi relativi a un'analisi.



## Che cos'è un livello di rischio utente?

Il livello di rischio utente può essere Alto, Medio o Basso e indica la probabilità che l'identità dell'utente sia stata compromessa. Viene calcolato in base agli eventi di rischio utente associati all'identità dell'utente.

Lo stato di un evento di rischio può essere **attivo** o **chiuso**. Solo gli eventi di rischio **attivi** vengono conteggiati nel calcolo del rischio utente.

Il livello di rischio utente viene calcolato usando le informazioni seguenti:

- Eventi di rischio attivi che interessano l'utente.
- Livello di rischio di tali eventi. 
- Eventuali azioni di correzione intraprese o meno. 

<br> ![Rischi utente](./media/active-directory-identityprotection/86.png "Rischi utente") <br>



È possibile usare i livelli di rischio utente per creare criteri di accesso condizionale per bloccare l'accesso degli utenti rischiosi o per fare in modo che modifichino la password in modo sicuro.


## Chiusura manuale degli eventi di rischio

Nella maggior parte dei casi, per chiudere automaticamente gli eventi di rischio si procede con azioni di correzione come la reimpostazione della password di protezione. Tuttavia, questo potrebbe non essere sempre possibile. <br> Ad esempio, quando:

- Un utente con eventi di rischio attivi è stato eliminato
- Un'analisi rivela che un evento di rischio segnalato è stato eseguito dall'utente legittimo

Dal momento che gli eventi di rischio **attivi** vengono conteggiati nel calcolo del rischio utente, potrebbe essere necessario ridurre il livello di rischio chiudendo manualmente gli eventi di rischio. <br> Nel corso dell'analisi è possibile eseguire una qualsiasi di queste azioni per modificare lo stato di un evento di rischio:

<br> ![Azioni](./media/active-directory-identityprotection/34.png "Azioni") <br>

- **Risolvi**: se è stata eseguita l'azione di correzione appropriata all'esterno di Identity Protection dopo l'analisi di un evento di rischio e si ritiene che l'evento sia da considerare chiuso, contrassegnarlo come risolto. Gli eventi risolti impostano lo stato dell'evento di rischio come chiuso e l'evento di rischio non viene più conteggiato nel rischio utente.

- **Contrassegna come falso positivo**: in alcuni casi è possibile analizzare un evento di rischio e scoprire che è stato erroneamente contrassegnato come rischioso. È possibile ridurre il numero di tali occorrenze contrassegnando l'evento di rischio come falso positivo. Questo permetterà agli algoritmi di Machine Learning di migliorare la classificazione di eventi simili in futuro. Lo stato dell'evento falso positivo viene impostato come **chiuso** e non viene più conteggiato nel rischio utente.

- **Ignora**: se non sono state intraprese azioni di correzione ma si vuole rimuovere l'evento di rischio dall'elenco attivo, è possibile contrassegnarlo come da ignorare e il relativo stato verrà impostato come chiuso. Gli eventi ignorati non vengono conteggiati nel rischio utente. Questa opzione deve essere usata solo in circostanze particolari.

- **Riattiva**: gli eventi di rischio che sono stati chiusi manualmente, scegliendo **Risolvi**, **Contrassegna come falso positivo** o **Ignora**, possono essere riattivati impostando nuovamente lo stato dell'evento su **attivo**. Gli eventi di rischio riattivati vengono conteggiati nel calcolo del livello di rischio utente. Gli eventi di rischio chiusi tramite correzione, ad esempio la reimpostazione della password di protezione, non possono essere riattivati.

**Per aprire la finestra di dialogo di configurazione correlata**:

1. Nel pannello **Azure AD Identity Protection** fare clic su **Utenti contrassegnati per il rischio**. <br><br> ![Reimpostazione manuale della password](./media/active-directory-identityprotection/408.png "Reimpostazione manuale della password") <br>

2. Fare clic con il pulsante destro del mouse sull'utente interessato. <br><br> ![Reimpostazione manuale della password](./media/active-directory-identityprotection/437.png "Reimpostazione manuale della password") <br>

## Correzione di eventi di rischio utente

Una correzione è un'azione che consente di proteggere un'identità o un dispositivo che in precedenza è stato sospettato o ritenuto essere compromesso. Un'azione di correzione ripristina l'identità o il dispositivo a uno stato sicuro e risolve gli eventi di rischio precedenti associati all'identità o al dispositivo.

Per correggere gli eventi di rischio utente, è possibile:

- Eseguire una reimpostazione della password di protezione per correggere manualmente gli eventi di rischio utente 

- Configurare criteri di sicurezza per il rischio utente per mitigare o correggere automaticamente gli eventi di rischio utente

- Ricreare l'immagine del dispositivo infetto


### Reimpostazione manuale della password di protezione

La reimpostazione della password di protezione un'azione di correzione efficace per molti eventi di rischio. Quando viene eseguita, permette di chiudere automaticamente gli eventi di rischio e ricalcolare il livello di rischio utente. È possibile usare il dashboard di Identity Protection per avviare una reimpostazione della password per un utente rischioso.

La finestra di dialogo correlata fornisce due metodi diversi per reimpostare una password:

**Reimposta password**: selezionare **Richiedi all'utente di reimpostare la password** per consentire all'utente di eseguire il ripristino automatico se è registrato per l'autenticazione a più fattori. Durante l'accesso successivo all'utente verrà richiesto di risolvere correttamente una richiesta di autenticazione a più fattori e quindi di modificare la password. Questa opzione non è disponibile se l'account utente non è già registrato per l'autenticazione a più fattori.

**Password provvisoria**: selezionare **Genera password provvisoria** per annullare immediatamente la password esistente e creare una nuova password provvisoria per l'utente. Inviare la nuova password provvisoria a un indirizzo di posta elettronica alternativo dell'utente o al responsabile dell'utente. La password è provvisoria, quindi verrà richiesto all'utente di modificarla al momento dell'accesso.

<br> ![Criterio](./media/active-directory-identityprotection/71.png "Criterio") <br>

**Per aprire la finestra di dialogo di configurazione correlata**:

1. Nel pannello **Azure AD Identity Protection** fare clic su **Utenti contrassegnati per il rischio**. <br><br> ![Reimpostazione manuale della password](./media/active-directory-identityprotection/408.png "Reimpostazione manuale della password") <br>

2. Fare clic sull'utente interessato <br><br> ![Reimpostazione manuale della password](./media/active-directory-identityprotection/404.png "Reimpostazione manuale della password") <br>

2. Fare clic su Reimposta password <br><br> ![Reimpostazione manuale della password](./media/active-directory-identityprotection/420.png "Reimpostazione manuale della password") <br>





## Criteri di sicurezza per il rischio utente

I criteri di sicurezza per il rischio utente sono criteri di accesso condizionale che valutano il livello di rischio per un utente specifico e applicano azioni di correzione e mitigazione dei rischi in base a regole e condizioni predefinite. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/500.png "Criteri di rischio utente") <br>

Azure AD Identity Protection consente di gestire le azioni di mitigazione e correzione per gli utenti contrassegnati per il rischio seguendo questa procedura:

- Impostare gli utenti e i gruppi a cui vengono applicati i criteri: <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/501.png "Criteri di rischio utente") <br>

- Impostare la soglia del livello di rischio utente, bassa, media o alta, che attiva una modifica della password: <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/502.png "Criteri di rischio utente") <br>

- Impostare la soglia del livello di rischio utente, bassa, media o alta, che attiva il blocco di un utente: <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/503.png "Criteri di rischio utente") <br>

- Cambiare lo stato dei criteri: <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/403.png "Registrazione MFA") <br>

- Esaminare e valutare l'impatto di una modifica prima di attivarla: <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/504.png "Criteri di rischio utente") <br>


La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti. Tuttavia, esclude dai criteri gli utenti contrassegnati per il rischio con una soglia **bassa** o **media**. Questa scelta potrebbe non garantire la protezione delle identità o dei dispositivi in precedenza sospettati o ritenuti essere compromessi.

Quando si impostano i criteri:

- Escludere gli utenti che possono generare molti falsi positivi, ad esempio sviluppatori o analisti della sicurezza

- Escludere gli utenti con impostazioni locali in cui abilitare i criteri non è pratico, ad esempio per la mancanza di accesso al supporto tecnico

- Usare una soglia **alta** durante il rollout iniziale dei criteri o se è necessario ridurre al minimo gli avvisi visualizzati dagli utenti finali.

- Usare una soglia **bassa** se l'organizzazione richiede una maggiore sicurezza. La scelta di una soglia **bassa** introduce altri passaggi di verifica dell'accesso per l'utente, ma garantisce una maggiore sicurezza.

L'impostazione predefinita consigliata per la maggior parte delle organizzazioni è la configurazione di una regola per una soglia **media**, che permette di bilanciare usabilità e sicurezza.

Per una panoramica dell'esperienza utente correlata, vedere:

- [Flusso di ripristino di account compromessi](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Flusso di account compromessi bloccati](active-directory-identityprotection-flows.md#compromised-account-blocked).


**Per aprire la finestra di dialogo di configurazione correlata**:

1. Nel pannello **Azure AD Identity Protection** fare clic su **Impostazioni**. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/401.png "Criteri di rischio utente") <br>

2. Nella sezione **Criteri di sicurezza** fare clic su **Rischio utente**. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/500.png "Criteri di rischio utente") <br>






## Mitigazione di eventi di rischio utente
Gli amministratori possono impostare criteri di sicurezza per il rischio utente per bloccare gli utenti al momento dell'accesso in base al livello di rischio.

Il blocco dell'accesso:
 
- Impedisce la generazione di nuovi eventi di rischio utente per l'utente interessato

- Consente agli amministratori di correggere manualmente gli eventi di rischio che interessano l'identità dell'utente e di ripristinarne lo stato protetto



## Che cos'è un livello di rischio di accesso?

Un livello di rischio di accesso (alto, medio o basso) indica la probabilità che per un accesso specifico altre persone stiano cercando di autenticarsi con l'identità dell'utente. Il livello di rischio di accesso viene valutato al momento dell'accesso e prende in considerazione gli eventi di rischio e gli indicatori rilevati in tempo reale per l'accesso specifico.

## Mitigazione degli eventi di rischio di accesso 
La mitigazione è un'azione che consente di limitare la possibilità che un utente malintenzionato sfrutti un'identità o un dispositivo compromesso senza ripristinare l'identità o il dispositivo a uno stato sicuro. La mitigazione non risolve gli eventi di rischio di accesso precedenti associati all'identità o al dispositivo.

Per mitigare automaticamente gli eventi di rischio di accesso, è possibile usare l'accesso condizionale in Azure AD Identity Protection. Usando questi criteri, si tiene conto del livello di rischio dell'utente o dell'accesso per bloccare gli accessi rischiosi o richiedere all'utente di eseguire l'autenticazione a più fattori. Queste azioni possono impedire a un utente malintenzionato di sfruttare un'identità rubata per causare danni e permettono di guadagnare tempo per proteggere l'identità.


## Criteri di sicurezza per il rischio di accesso

I criteri di rischio di accesso sono criteri di accesso condizionale che valutano il rischio associato a un accesso specifico e applicano le azioni di mitigazione in base a condizioni e regole predefinite. <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/700.png "Criteri di rischio di accesso") <br>

Azure AD Identity Protection consente di gestire le azioni di mitigazione degli accessi rischiosi seguendo questa procedura:

- Impostare gli utenti e i gruppi a cui vengono applicati i criteri: <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/701.png "Criteri di rischio di accesso") <br>

- Impostare la soglia del livello di rischio di accesso, bassa, media o alta, che attiva una verifica di autenticazione a più fattori per gli accessi interessati: <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/702.png "Criteri di rischio di accesso") <br>

- Impostare la soglia del livello di rischio di accesso, bassa, media o alta, che blocca gli accessi interessati: <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/703.png "Criteri di rischio di accesso") <br>

- Cambiare lo stato dei criteri: <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/403.png "Registrazione MFA") <br>

- Esaminare e valutare l'impatto di una modifica prima di attivarla: <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/704.png "Criteri di rischio di accesso") <br>

 
La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti.<br> Tuttavia, esclude dai criteri gli accessi contrassegnati per il rischio con una soglia **bassa** o **media**. Questa scelta potrebbe non impedire a un utente malintenzionato di sfruttare un'identità compromessa.

Quando si impostano i criteri:

- Escludere gli utenti non hanno o non possono avere l'autenticazione a più fattori

- Escludere gli utenti con impostazioni locali in cui abilitare i criteri non è pratico, ad esempio per la mancanza di accesso al supporto tecnico

- Escludere gli utenti che possono generare molti falsi positivi, ad esempio sviluppatori o analisti della sicurezza

- Usare una soglia **alta** durante il rollout iniziale dei criteri o se è necessario ridurre al minimo gli avvisi visualizzati dagli utenti finali.

- Usare una soglia **bassa** se l'organizzazione richiede una maggiore sicurezza. La scelta di una soglia **bassa** introduce altri passaggi di verifica dell'accesso per l'utente, ma garantisce una maggiore sicurezza.

L'impostazione predefinita consigliata per la maggior parte delle organizzazioni è la configurazione di una regola per una soglia **media**, che permette di bilanciare usabilità e sicurezza.

 
I criteri di rischio di accesso:

- Vengono applicati a tutto il traffico tramite browser e agli accessi che usano l'autenticazione moderna.
- Non vengono applicati alle applicazioni che usano protocolli di sicurezza meno recenti disabilitando l'endpoint WS-Trust in corrispondenza dell'IDP federato, ad esempio ADFS.

La pagina **Eventi di rischio** nella console di Identity Protection contiene un elenco di tutti gli eventi:

- Visualizzare a quali eventi sono stati applicati i criteri
- Esaminare l'attività e determinare se l'azione è stata appropriata o meno 

Per una panoramica dell'esperienza utente correlata, vedere:

- [Ripristino di un accesso rischioso](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Accesso rischioso bloccato](active-directory-identityprotection-flows.md#risky-sign-in-blocked)

- [Registrazione per l'autenticazione a più fattori durante un accesso rischioso](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





**Per aprire la finestra di dialogo di configurazione correlata**:

1. Nel pannello **Azure AD Identity Protection** fare clic su **Impostazioni**. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/401.png "Registrazione MFA") <br>

1. Nella sezione **Criteri di sicurezza** fare clic su **Rischio di accesso**. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/700.png "Registrazione MFA") <br>




## Criteri di registrazione per l'autenticazione a più fattori

Azure Multi-Factor Authentication è un metodo di verifica dell'identità dell'utente che richiede l'uso di più fattori, oltre a un nome utente e una password. Fornisce un secondo livello di sicurezza agli accessi e alle transazioni degli utenti. <br> Si consiglia di richiedere Azure Multi-Factor Authentication per l'accesso degli utenti perché:

- Offre un'autenticazione avanzata con una gamma di opzioni di verifica semplici

- Svolge un ruolo chiave nella preparazione dell'organizzazione per le operazioni di protezione e ripristino in caso di compromissione degli account

Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).


Azure AD Identity Protection permette di gestire il rollout della registrazione per l'autenticazione a più fattori con la configurazione di criteri che consentono di:

- Visualizzare lo stato di registrazione corrente: <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/603.png "Registrazione MFA") <br>

- Impostare gli utenti e i gruppi a cui vengono applicati i criteri: <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/601.png "Registrazione MFA") <br>

- Definire per quanto tempo consentire agli utenti di ignorare la registrazione: <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/602.png "Registrazione MFA") <br>

- Cambiare lo stato dei criteri: <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/403.png "Registrazione MFA") <br>

Per una panoramica dell'esperienza utente correlata, vedere:

- [Flusso di registrazione per l'autenticazione a più fattori](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Registrazione per l'autenticazione a più fattori durante un accesso rischioso](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).





**Per aprire la finestra di dialogo di configurazione correlata**:

1. Nel pannello **Azure AD Identity Protection** fare clic su **Impostazioni**. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/401.png "Registrazione MFA") <br>

2. Nella sezione **Multi-Factor Authentication** fare clic su **Registrazione**. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/402.png "Registrazione MFA") <br>




## Vedere anche

 - [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipi di eventi di rischio rilevati da Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilità rilevate da Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)
 - [Notifiche di Azure Active Directory Identity Protection](active-directory-identityprotection-notifications.md)
 - [Flussi di Azure Active Directory Identity Protection](active-directory-identityprotection-flows.md)
 - [Studio di Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md)
 - [Glossario di Azure Active Directory Identity Protection](active-directory-identityprotection-glossary.md)

<!---HONumber=AcomDC_0323_2016-->