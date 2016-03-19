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
	ms.date="03/02/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection è un servizio di sicurezza che fornisce una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. Microsoft protegge le identità basate sul cloud da oltre dieci anni. Con Azure AD Identity Protection, questi stessi sistemi di protezione sono ora a disposizione dei clienti aziendali. Identity Protection si avvale delle funzionalità di rilevamento anomalie di Azure AD, disponibili tramite i report di Anomalie dell'attività di Azure AD, e introduce nuovi tipi di eventi di rischio che permettono di rilevare anomalie in tempo reale.

> [AZURE.NOTE] L'anteprima di Identity Protection è attualmente disponibile solo per tenant di Azure AD di cui viene effettuato il provisioning negli Stati Uniti.
 

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

- Criteri per richiedere la registrazione degli utenti per l'autenticazione a più fattori.


## Rilevamento e rischio

### Eventi di rischio

Gli eventi di rischio sono eventi contrassegnati come sospetti da Identity Protection e indicano che un'identità potrebbe essere compromessa. Per un elenco completo degli eventi di rischio, vedere la sezione [Tipi di eventi di rischio](#types-of-risk-events). Alcuni di questi eventi di rischio sono disponibili nei report di Anomalie dell'attività di Azure AD nel portale di gestione di Azure. La tabella seguente riporta un elenco dei vari tipi di eventi di rischio e i report di **Anomalie dell'attività di Azure AD** corrispondenti. Microsoft continua a investire in questo spazio e intende migliorare la precisione di rilevamento degli eventi di rischio esistenti e aggiungere nuovi tipi di eventi di rischio in modo continuativo.



| Tipo di evento di rischio di Identity Protection | Report di Anomalie dell'attività di Azure AD corrispondente |
| :-- | :-- |
| Credenziali perse | Utenti con credenziali perse |
| Trasferimento impossibile a posizioni atipiche |	Attività di accesso irregolare |
| Accessi da dispositivi infetti | Accessi da dispositivi potenzialmente infetti |
| Accessi da indirizzi IP anonimi | Accessi da origini sconosciute |
| Accessi da indirizzi IP con attività sospette |	Accessi da indirizzi IP con attività sospette |
| Accessi da posizioni non note | - | 
| Eventi di blocco (non nell'anteprima pubblica) | - |

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

### Tipi di eventi di rischio

Questa sezione fornisce una panoramica dei tipi di eventi di rischio disponibili.

#### Credenziali perse

I ricercatori Microsoft sulla sicurezza trovano le credenziali perse pubblicate nel Dark Web, in genere in formato testo normale. Queste vengono confrontate con le credenziali di Azure AD e, in caso di corrispondenza, vengono segnalate come "credenziali perse" in Identity Protection.

La gravità degli eventi di rischio correlati a credenziali perse è classificata come "alta", perché sono un chiaro indizio del fatto che il nome utente e la password sono a disposizione degli utenti malintenzionati.

#### Trasferimento impossibile a posizioni atipiche

Questo tipo di evento di rischio identifica due accessi provenienti da posizioni geograficamente distanti, almeno una delle quali può anche essere atipica in base al comportamento passato dell'utente. Viene preso in considerazione anche il tempo che intercorre tra i due accessi. Se è inferiore al tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, ciò indica che un altro utente sta usando le stesse credenziali.

Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti che contribuiscono alla condizione di trasferimento impossibile, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente.

Il trasferimento impossibile indica in genere che un pirata informatico è riuscito a ottenere l'accesso. Possono, tuttavia, verificarsi falsi positivi quando un utente è in viaggio e usa un nuovo dispositivo o una VPN che non viene in genere usata da altri utenti nell'organizzazione. Un'altra origine di falsi positivi è costituita dalle applicazioni che passano in modo errato gli indirizzi IP di server come indirizzi IP di client. Questo può dare l'impressione che gli accessi si verifichino dal data center in cui è ospitato il back-end dell'applicazione. Spesso si tratta di data center Microsoft e gli accessi possono risultare eseguiti da indirizzi IP di proprietà di Microsoft. A causa di questi falsi positivi, il livello di rischio per questo evento di rischio è "**medio**".

####Accessi da dispositivi infetti

Questo tipo di evento rischio identifica accessi da dispositivi infettati da malware, che comunicano attivamente con un server bot. Viene determinato mettendo in relazione gli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che sono stati in contatto con un server bot.

Questo evento di rischio identifica gli indirizzi IP, non i dispositivi degli utenti. Se più dispositivi usano un unico indirizzo IP e solo alcuni sono controllati da una rete bot, gli accessi provenienti da altri dispositivi possono attivare l'evento inutilmente. Ecco perché il livello di questo evento di rischio è classificato come "**basso**".

È consigliabile contattare l'utente e di eseguire la scansione di tutti i relativi dispositivi. È anche possibile che un dispositivo personale dell'utente sia stato infettato o, come indicato in precedenza, che qualcun altro stia usando un dispositivo infetto dallo stesso indirizzo IP dell'utente. I dispositivi infetti sono spesso infettati da malware non ancora identificati dai software antivirus e possono anche essere indicativi di abitudini errate dell'utente.

Per altre informazioni su come risolvere problemi correlati alle infezioni malware, vedere [Malware Protection Center](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409).


#### Accessi da indirizzi IP anonimi

Questo tipo di evento di rischio identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP riconosciuto come un indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.

È consigliabile contattare immediatamente l'utente per verificare se sta usando indirizzi IP anonimi. Il livello di rischio per questo tipo di evento di rischio è "**medio**" perché un IP anonimo non è di per sé un'indicazione sicura di una compromissione dell'account.

#### Accessi da indirizzi IP con attività sospette

Questo tipo di evento di rischio identifica gli indirizzi IP da cui si è verificato un numero elevato di tentativi di accesso non riusciti, per più account utente e in un periodo di tempo breve. Questo corrisponde ai modelli di traffico degli indirizzi IP usati da utenti malintenzionati e indica che gli account sono già compromessi o stanno per esserlo. Questo algoritmo di Machine Learning ignora i "*falsi positivi*" evidenti, ad esempio gli indirizzi IP che vengono regolarmente usati da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali apprende il comportamento di accesso di un nuovo utente e di un nuovo tenant.

È consigliabile contattare l'utente per verificare se ha effettivamente eseguito l'accesso da un indirizzo IP contrassegnato come sospetto. Il livello di rischio per questo tipo di evento è "**medio**" perché lo stesso indirizzo IP può essere usato da più dispositivi, mentre solo alcuni di essi potrebbero essere responsabili dell'attività sospetta.


#### Accessi da posizioni non note

Questo tipo di evento di rischio è un meccanismo di valutazione dell'accesso in tempo reale che prende in considerazione le posizioni di accesso precedenti, come l'indirizzo IP, la latitudine, la longitudine e l'ASN, per determinare posizioni nuove o non note. Il sistema archivia le informazioni sulle posizioni usate in precedenza da un utente e le considera posizioni "note". L'evento di rischio viene attivato quando viene eseguito l'accesso da una posizione non inclusa nell'elenco delle posizioni note. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. <br> Le posizioni non note possono indicare che un utente malintenzionato ha la possibilità di provare a usare un'identità rubata. Possono verificarsi falsi positivi quando un utente è in viaggio, sta provando un nuovo dispositivo o usa una nuova VPN. A causa di questi falsi positivi, il livello di rischio per questo tipo di evento è "**Medio**".

### Vulnerabilità

Le vulnerabilità sono punti deboli in un ambiente che possono essere sfruttati da un utente malintenzionato. È consigliabile risolvere le vulnerabilità per migliorare le condizioni di sicurezza dell'organizzazione e impedire che vengano sfruttate da utenti malintenzionati.

#### Registrazione per l'autenticazione a più fattori non configurata 

Questa vulnerabilità consente di controllare la distribuzione di Azure Multi-Factor Authentication all'interno dell'organizzazione.

L'autenticazione a più fattori di Azure fornisce un secondo livello di sicurezza per l'autenticazione utente. Consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre autenticazione avanzata tramite una gamma di semplici opzioni di verifica, ad esempio una telefonata, un SMS, una notifica dell'app mobile o un codice di verifica e token OATH di terze parti.

È consigliabile richiedere l'autenticazione a più fattori per l'accesso degli utenti. L'autenticazione a più fattori svolge un ruolo chiave nei criteri di accesso condizionale basati sul rischio disponibili tramite Identity Protection.

Per altre informazioni, vedere [Informazioni su Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).


#### App per cloud non gestite

Questa vulnerabilità consente di identificare le app per cloud non gestite all'interno dell'organizzazione.
 
Nelle aziende moderne, i reparti IT spesso non sono a conoscenza di tutte le applicazioni cloud usate per lavoro dagli utenti dell'organizzazione. L'accesso non autorizzato ai dati aziendali, le possibili perdite di dati e altri rischi di sicurezza rappresentano una preoccupazione per gli amministratori.

È consigliabile distribuire Cloud App Discovery per identificare le applicazioni cloud non gestite dell'organizzazione e gestirle con Azure Active Directory.

Per altre informazioni, vedere [Ricerca di applicazioni cloud non gestite con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).



####Avvisi di sicurezza di Privileged Identity Management

Questa vulnerabilità permette di identificare e risolvere gli avvisi sulle identità con privilegi all'interno dell'organizzazione.

Per consentire agli utenti di eseguire operazioni con privilegi, le organizzazioni devono concedere agli utenti accessi con privilegi temporanei o permanenti in Azure AD, per le risorse di Azure o Office 365 o per altre app SaaS. Ognuno di questi utenti con privilegi aumenta la superficie di attacco dell'organizzazione. Questa vulnerabilità consente di identificare gli utenti che hanno un accesso con privilegi non necessario e di intraprendere le azioni appropriate per ridurre o eliminare il rischio.

È consigliabile usare Azure AD Privileged Identity Management nell'organizzazione per gestire, controllare e monitorare le identità con privilegi e il relativo accesso alle risorse in Azure AD e in altri Microsoft Online Services, ad esempio Office 365 o Microsoft Intune.

Per altre informazioni, vedere [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md).

## Analisi
L'esperienza con Identity Protection inizia in genere dal relativo dashboard.

<br><br> ![Correzione](./media/active-directory-identityprotection/29.png "Correzione") <br>

Il dashboard consente di accedere a:
 
- Report, ad esempio **Utenti contrassegnati per il rischio**, **Eventi di rischio** e **Vulnerabilità**.
- Impostazioni quali la configurazione dei **criteri di sicurezza**, delle **notifiche** e della **registrazione per l'autenticazione a più fattori**.
 

Si tratta in genere del punto di partenza dell'analisi, ovvero del processo di analisi di attività, log e altre informazioni rilevanti relative a un evento di rischio per decidere se sono necessarie procedure di correzione o mitigazione, se e come l'identità è stata compromessa e come è stata usata l'identità compromessa.


### Notifiche

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatica per la gestione del rischio utente e degli eventi di rischio:

- Messaggio di posta elettronica di avviso utente compromesso

- Messaggio di posta elettronica di riepilogo settimanale

#### Messaggio di posta elettronica di avviso utente compromesso

Quando Azure AD Identity Protection identifica un account come compromesso, viene generato un avviso di posta elettronica utente compromesso. Il messaggio include un collegamento al report relativo agli utenti contrassegnati per il rischio nella console di Identity Protection. È consigliabile analizzare immediatamente le notifiche relative a utenti compromessi.


#### Messaggio di posta elettronica di riepilogo settimanale

Il messaggio di riepilogo settimanale contiene un riepilogo dei nuovi eventi di rischio.<br> Sono inclusi:
- Utenti a rischio.
- Eventi di rischio segnalati.
- Vulnerabilità rilevate.
- Collegamenti ai report correlati in Identity Protection.


<br> ![Correzione](./media/active-directory-identityprotection/400.png "Correzione") <br>

Per impostazione predefinita, l'avviso utente compromesso viene inviato a tutti gli amministratori di Azure Active Directory. Per personalizzare i destinatari, è possibile:

- Selezionare il collegamento alla gestione dei destinatari nella parte inferiore dell'avviso.

- Fare clic su Notifiche nelle impostazioni di Identity Protection.
 
<br> ![Rischi utente](./media/active-directory-identityprotection/62.png "Rischi utente") <br>
 



## Che cos'è un livello di rischio utente?

Il livello di rischio utente può essere Alto, Medio o Basso e indica la probabilità che l'identità dell'utente sia stata compromessa. Viene calcolato in base agli eventi di rischio utente associati all'identità dell'utente.

Lo stato di un evento di rischio può essere **attivo** o **chiuso**. Solo gli eventi di rischio **attivi** vengono presi in considerazione nel calcolo del rischio utente.

Il livello di rischio utente viene calcolato usando le informazioni seguenti:

- Eventi di rischio attivi che interessano l'utente.
- Livello di rischio di tali eventi. 
- Eventuali azioni di correzione intraprese o meno. 

<br> ![Rischi utente](./media/active-directory-identityprotection/86.png "Rischi utente") <br>



È possibile usare i livelli di rischio utente per creare criteri di accesso condizionale per bloccare l'accesso degli utenti rischiosi o per fare in modo che modifichino la password in modo sicuro.


## Chiusura manuale degli eventi di rischio

Nella maggior parte dei casi, per chiudere automaticamente gli eventi di rischio si procede con azioni di correzione come la reimpostazione della password di protezione. Tuttavia, questo potrebbe non essere sempre possibile. <br> Dal momento che gli eventi di rischio **attivi** vengono conteggiati nel calcolo del rischio utente, potrebbe essere necessario ridurre un livello di rischio chiudendo manualmente gli eventi di rischio. <br> Nel corso dell'analisi è possibile eseguire una qualsiasi di queste azioni per modificare lo stato di un evento di rischio:

<br> ![Azioni](./media/active-directory-identityprotection/34.png "Azioni") <br>

- **Risolvi**: se, dopo l'analisi di un evento di rischio, è stata eseguita un'azione di correzione appropriata all'esterno di Identity Protection e si ritiene che l'evento di rischio sia da considerare chiuso, contrassegnare l'evento come risolto. Gli eventi risolti impostano lo stato dell'evento di rischio come chiuso e l'evento di rischio non viene più conteggiato nel rischio utente.

- **Contrassegna come falso positivo**: in alcuni casi è possibile analizzare un evento di rischio e scoprire che è stato erroneamente contrassegnato come rischioso. È possibile ridurre il numero di tali occorrenze contrassegnando l'evento di rischio come falso positivo. Questo permetterà agli algoritmi di Machine Learning di migliorare la classificazione di eventi simili in futuro. Lo stato dell'evento falso positivo viene impostato come **chiuso** e non viene più conteggiato nel rischio utente.

- **Ignora**: se non sono state intraprese azioni di correzione ma si vuole rimuovere l'evento di rischio dall'elenco attivo, è possibile contrassegnarlo come da ignorare e il relativo stato verrà impostato come chiuso. Gli eventi ignorati non vengono conteggiati nel rischio utente. Questa opzione deve essere usata solo in circostanze particolari.

- **Riattiva**: gli eventi di rischio che sono stati chiusi manualmente, scegliendo **Risolvi**, **Contrassegna come falso positivo** o **Ignora**, possono essere riattivati, impostando nuovamente lo stato dell'evento come **attivo**. Gli eventi di rischio riattivati vengono conteggiati nel calcolo del livello di rischio utente. Gli eventi di rischio chiusi tramite correzione, ad esempio la reimpostazione della password di protezione, non possono essere riattivati.



## Correzione di eventi di rischio utente

Una correzione è un'azione che consente di proteggere un'identità o un dispositivo che in precedenza è stato sospettato o ritenuto essere compromesso. Un'azione di correzione ripristina l'identità o il dispositivo a uno stato sicuro e risolve gli eventi di rischio precedenti associati all'identità o al dispositivo.

Per correggere gli eventi di rischio utente, è possibile:

- Eseguire una reimpostazione della password di protezione per correggere manualmente gli eventi di rischio utente. 

- Configurare criteri di sicurezza per il rischio utente per mitigare o correggere automaticamente gli eventi di rischio utente.

- Ricreare l'immagine del dispositivo infetto.


### Reimpostazione della password di protezione

La reimpostazione della password di protezione un'azione di correzione efficace per molti eventi di rischio. Quando viene eseguita, permette di chiudere automaticamente gli eventi di rischio e ricalcolare il livello di rischio utente. È possibile avviare una reimpostazione della password per un utente rischioso dalla console di Identity Protection.

Nella console sono disponibili due modalità diverse per reimpostare una password:

**Reimposta password**: selezionare **Richiedi all'utente di reimpostare la password** per consentire all'utente di eseguire il ripristino automatico se è registrato per l'autenticazione a più fattori. Durante l'accesso successivo all'utente verrà richiesto di risolvere correttamente una richiesta di autenticazione a più fattori e quindi di modificare la password. Questa opzione non è disponibile se l'account utente non è già registrato per l'autenticazione a più fattori.

**Password provvisoria**: selezionare **Genera password provvisoria** per annullare immediatamente la password esistente e creare una nuova password provvisoria per l'utente. Inviare la nuova password provvisoria a un indirizzo di posta elettronica alternativo dell'utente o al responsabile dell'utente. La password è provvisoria, quindi verrà richiesto all'utente di modificarla al momento dell'accesso.

<br> ![Criterio](./media/active-directory-identityprotection/71.png "Criterio") <br>



## Criteri di sicurezza per il rischio utente

I criteri di sicurezza per il rischio utente sono criteri di accesso condizionale che valutano il livello di rischio per un utente specifico e applicano azioni di correzione e mitigazione dei rischi in base a regole e condizioni predefinite. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/500.png "Criteri di rischio utente") <br>

Azure AD Identity Protection consente di gestire le azioni di mitigazione e correzione per gli utenti contrassegnati per il rischio seguendo questa procedura:

- Impostare gli utenti e i gruppi a cui vengono applicati i criteri. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/501.png "Criteri di rischio utente") <br>

- Impostare la soglia del livello di rischio utente, bassa, media o alta, che attiva una modifica della password. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/502.png "Criteri di rischio utente") <br>

- Impostare la soglia del livello di rischio utente, bassa, media o alta, che attiva un blocco dell'utente. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/503.png "Criteri di rischio utente") <br>

- Esaminare e valutare l'impatto di una modifica prima di attivarla. <br><br> ![Criteri di rischio utente](./media/active-directory-identityprotection/504.png "Criteri di rischio utente") <br>


La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti. Tuttavia, esclude dai criteri gli utenti contrassegnati per il rischio con una soglia **bassa** o **media**. Questa scelta può non permettere di proteggere le identità o i dispositivi in precedenza sospettati o ritenuti essere compromessi.

Quando si impostano i criteri:

- Escludere gli utenti che possono generare molti falsi positivi, ad esempio sviluppatori o analisti della sicurezza.

- Escludere gli utenti con impostazioni locali in cui abilitare i criteri non è pratico, ad esempio per la mancanza di accesso al supporto tecnico.

- Usare una soglia **alta** durante il rollout iniziale dei criteri o se è necessario ridurre al minimo le richieste di verifica visualizzate dagli utenti finali.

- Usare una soglia **bassa** se l'organizzazione richiede una maggiore sicurezza. La scelta di una soglia **bassa** introduce richieste di accesso aggiuntive per l'utente, ma garantisce una maggiore sicurezza.

La scelta consigliata come valore predefinito per la maggior parte delle organizzazioni è la configurazione di una regola per una soglia **media**, che permette di bilanciare usabilità e sicurezza.



### Mitigazione di eventi di rischio utente
Gli amministratori possono impostare criteri di sicurezza per il rischio utente per bloccare gli utenti al momento dell'accesso in base al livello di rischio.

Il blocco dell'accesso:
 
- Impedisce la generazione di nuovi eventi di rischio utente per l'utente interessato.

- Consente agli amministratori di correggere gli eventi di rischio che interessano l'identità dell'utente e di ripristinarne lo stato protetto.





### Flussi di correzione e mitigazione del rischio utente  

Le sezioni seguenti forniscono una panoramica dell'esperienza utente per i flussi di correzione e mitigazione del rischio utente.

#### Flusso di ripristino di account compromessi

Dopo che sono stati configurati criteri di sicurezza per il rischio utente, gli utenti che rientrano nel livello di rischio utente specificato nei criteri, e quindi considerati compromessi, devono seguire il flusso di ripristino di utenti compromessi per poter eseguire l'accesso.

Il flusso di ripristino di utenti compromessi è composto da tre passaggi:

1. L'utente viene informato che la sicurezza dell'account è a rischio a causa di attività sospette o di credenziali perse.

<br> ![Correzione](./media/active-directory-identityprotection/101.png "Correzione") <br>

2.	L'utente deve dimostrare la propria identità rispondendo a una richiesta di sicurezza. Se l'utente è registrato per l'autenticazione a più fattori può eseguire il ripristino automatico da eventuali compromissioni. Deve eseguire il round trip di un codice di sicurezza al proprio numero di telefono. 

<br> ![Correzione](./media/active-directory-identityprotection/110.png "Correzione") <br>


3.	Infine, all'utente viene richiesto di modificare la password perché qualcun altro potrebbe aver avuto accesso all'account. Sono incluse per riferimento le screenshot di questa esperienza.
 
<br> ![Correzione](./media/active-directory-identityprotection/111.png "Correzione") <br>







 
#### Reimpostazione delle password
Se viene bloccato l'accesso di un utente, un amministratore può generare una password provvisoria. L'utente dovrà modificare la password all'accesso successivo. Una modifica della password risolve e chiude la maggior parte degli eventi di rischio per l'utente.

<br> ![Correzione](./media/active-directory-identityprotection/160.png "Correzione") <br>


### Mitigazione del livello di rischio utente

### Account compromesso bloccato 

Per sbloccare un utente bloccato da criteri di sicurezza per il rischio utente, è necessario contattare un amministratore o il supporto tecnico. Il ripristino automatico tramite l'autenticazione a più fattori non è possibile in questo caso.

<br> ![Correzione](./media/active-directory-identityprotection/104.png "Correzione") <br>


## Mitigazione degli eventi di rischio di accesso 
La mitigazione è un'azione che consente di limitare la possibilità che un utente malintenzionato sfrutti un'identità o un dispositivo compromesso senza ripristinare l'identità o il dispositivo a uno stato sicuro. La mitigazione non risolve gli eventi di rischio di accesso precedenti associati all'identità o al dispositivo.

Per mitigare automaticamente gli eventi di rischio di accesso, è possibile usare l'accesso condizionale in Azure AD Identity Protection. Usando questi criteri, si tiene conto del livello di rischio dell'utente o dell'accesso per bloccare gli accessi rischiosi o richiedere all'utente di eseguire l'autenticazione a più fattori. Queste azioni possono impedire a un utente malintenzionato di sfruttare un'identità rubata per causare danni e permettono di guadagnare tempo per proteggere l'identità.


## Criteri di sicurezza per il rischio di accesso

I criteri di sicurezza per il rischio di accesso sono criteri di accesso condizionale che valutano il rischio di un accesso specifico e applicano le azioni di mitigazione appropriate in base a condizioni e regole predefinite. <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/700.png "Criteri di rischio di accesso") <br>

Azure AD Identity Protection consente di gestire le azioni di mitigazione degli accessi rischiosi seguendo questa procedura:

- Impostare gli utenti e i gruppi a cui vengono applicati i criteri. <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/701.png "Criteri di rischio di accesso") <br>

- Impostare la soglia del livello di rischio di accesso, bassa, media o alta, che attiva richiesta di autenticazione a più fattori per gli accessi interessati. <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/702.png "Criteri di rischio di accesso") <br>

- Impostare la soglia del livello di rischio di accesso, bassa, media o alta, che blocca gli accessi interessati. <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/703.png "Criteri di rischio di accesso") <br>

- Esaminare e valutare l'impatto di una modifica prima di attivarla. <br><br> ![Criteri di rischio di accesso](./media/active-directory-identityprotection/704.png "Criteri di rischio di accesso") <br>

 
La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti.<br> Tuttavia, esclude dai criteri gli accessi contrassegnati per il rischio con una soglia **bassa** o **media**. Questa scelta può non impedire a un utente malintenzionato di sfruttare un'identità compromessa.

Quando si impostano i criteri:

- Escludere gli utenti non hanno o non possono avere l'autenticazione a più fattori.

- Escludere gli utenti con impostazioni locali in cui abilitare i criteri non è pratico, ad esempio per la mancanza di accesso al supporto tecnico.

- Escludere gli utenti che possono generare molti falsi positivi, ad esempio sviluppatori o analisti della sicurezza.

- Usare una soglia **alta** durante il rollout iniziale dei criteri o se è necessario ridurre al minimo le richieste di verifica visualizzate dagli utenti finali.

- Usare una soglia **bassa** se l'organizzazione richiede una maggiore sicurezza. La scelta di una soglia **bassa** introduce richieste di accesso aggiuntive per l'utente, ma garantisce una maggiore sicurezza.

La scelta consigliata come valore predefinito per la maggior parte delle organizzazioni è la configurazione di una regola per una soglia **media**, che permette di bilanciare usabilità e sicurezza.

 
I criteri di rischio di accesso:

- Vengono applicati a tutto il traffico tramite browser e agli accessi che usano l'autenticazione moderna.
- Non vengono applicati alle applicazioni che usano protocolli di sicurezza meno recenti disabilitando l'endpoint WS-Trust in corrispondenza dell'IDP federato, ad esempio AD FS.

La pagina **Eventi di rischio** nella console di Identity Protection contiene un elenco di tutti gli eventi:

- Visualizzare a quali eventi sono stati applicati i criteri.
- Esaminare l'attività e determinare se l'azione è stata appropriata o meno. 




## Criteri di registrazione per l'autenticazione a più fattori

L'autenticazione a più fattori viene usata per ottenere una garanzia aggiuntiva dell'identità dell'utente.<br> La registrazione per l'autenticazione a più fattori è un passaggio fondamentale nella preparazione dell'organizzazione per la protezione e il ripristino da compromissioni degli account. <br> ![Registrazione MFA](./media/active-directory-identityprotection/600.png "Registrazione MFA") <br>

Azure AD Identity Protection permette di gestire il rollout della registrazione per l'autenticazione a più fattori consentendo di:

- Impostare gli utenti e i gruppi a cui vengono applicati i criteri. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/601.png "Registrazione MFA") <br>

- Definire per quanto tempo consentire agli utenti di ignorare la registrazione. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/602.png "Registrazione MFA") <br>

- Visualizzare lo stato di registrazione corrente degli utenti interessati. <br><br> ![Registrazione MFA](./media/active-directory-identityprotection/603.png "Registrazione MFA") <br>


##Flussi di mitigazione del rischio di accesso 

#### Flusso di ripristino di accesso rischioso

Se un amministratore ha configurato dei criteri per i rischi di accesso, gli utenti interessati ricevono una notifica quando provano ad accedere.

Il flusso per l'accesso rischioso prevede due passaggi:

1. L'utente viene informato che è stato rilevato qualcosa di sospetto nell'accesso, ad esempio l'accesso da un nuovo dispositivo oppure una nuova posizione o app. <br> ![Correzione](./media/active-directory-identityprotection/120.png "Correzione") <br>

2. L'utente deve dimostrare la propria identità rispondendo a una richiesta di sicurezza. Se l'utente è registrato per l'autenticazione a più fattori, deve eseguire il round trip di un codice di sicurezza al proprio numero di telefono. Dal momento che si tratta soltanto di un accesso rischioso e non di un account compromesso, all'utente non viene richiesto di modificare la password in questo flusso. <br> ![Correzione](./media/active-directory-identityprotection/121.png "Correzione") <br>
 
#### Accesso rischioso bloccato
Gli amministratori possono anche impostare criteri di rischio di accesso per bloccare gli utenti al momento dell'accesso in base al livello di rischio. Per essere sbloccati, gli utenti finali devono contattare un amministratore o il supporto tecnico oppure possono provare a eseguire l'accesso da una posizione o un dispositivo noto. Il ripristino automatico tramite l'autenticazione a più fattori non è possibile in questo caso.

<br> ![Correzione](./media/active-directory-identityprotection/130.png "Correzione") <br>
 
#### Registrazione per l'autenticazione a più fattori

Sia per il ripristino di un account compromesso che per l'accesso rischioso, la migliore esperienza utente si ottiene quando l'utente può eseguire il ripristino automatico. Se un utente è registrato per l'autenticazione a più fattori, ha già un numero di telefono associato all'account che può essere usato per passare richieste di sicurezza. Non è necessario coinvolgere il supporto tecnico o l'amministratore per ripristinare un account compromesso. È quindi consigliabile fare in modo che gli utenti siano registrati per l'autenticazione a più fattori.

Gli amministratori possono:

- Impostare criteri che richiedono agli utenti di configurare l'account per la verifica di sicurezza aggiuntiva. 
- Consentire di ignorare la registrazione per l'autenticazione a più fattori per un massimo di 30 giorni, per dare agli utenti un periodo di tolleranza prima della registrazione.

 
 <br> ![Correzione](./media/active-directory-identityprotection/140.png "Correzione") <br> <br> ![Correzione](./media/active-directory-identityprotection/141.png "Correzione") <br> <br> ![Correzione](./media/active-directory-identityprotection/142.png "Correzione") <br>

 

#### Registrazione per l'autenticazione a più fattori durante un accesso rischioso

È importante che gli utenti eseguano la registrazione per l'autenticazione a più fattori perché siano preparati a rispondere alle richieste di sicurezza. Se un utente non è registrato per l'autenticazione a più fattori, ma i criteri richiedono che lo sia, la registrazione potrebbe essere richiesta durante un accesso rischioso. Questo comporta il rischio che il numero di telefono aggiunto sia dell'utente malintenzionato anziché dell'utente valido. <br> Per evitare questa situazione, richiedere agli utenti di registrarsi per l'autenticazione a più fattori appena possibile, in modo che in caso di compromissione sia già disponibile un numero di telefono associato all'account. In alternativa, gli amministratori possono bloccare completamente gli utenti compromessi che non sono registrati per l'autenticazione a più fattori.

 <br> ![Correzione](./media/active-directory-identityprotection/150.png "Correzione") <br> <br> ![Correzione](./media/active-directory-identityprotection/151.png "Correzione") <br>






 
## Studio

### Simulazione di eventi di rischio

Questa sezione offre una panoramica della simulazione dei tipi di eventi di rischio seguenti:

- Accessi da indirizzi IP anonimi (facile)

- Accessi da posizioni non note (intermedio)

- Trasferimento impossibile a posizioni atipiche (difficile)

Non è possibile simulare altri eventi di rischio in modo sicuro.


#### Accessi da indirizzi IP anonimi

Questo tipo di evento di rischio identifica gli utenti che hanno eseguito l'accesso da un indirizzo IP riconosciuto come un indirizzo IP proxy anonimo. Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi.

Per simulare un accesso da un indirizzo IP anonimo, seguire questa procedura:

1.	Scaricare [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en).
2.	Usare Tor Browser per passare a https://myapps.microsoft.com.   
3.	Immettere le credenziali dell'account da visualizzare nel report relativo agli accessi da indirizzi IP anonimi.

L'operazione è completata. L'accesso dovrebbe essere visualizzato in Identity Protection entro cinque minuti.


####Accessi da posizioni non note
L'evento di rischio per gli accessi da posizioni non note è un meccanismo di valutazione dell'accesso in tempo reale che prende in considerazione le posizioni di accesso precedenti, come l'indirizzo IP, la latitudine, la longitudine e l'ASN, per determinare posizioni nuove o non note. Il sistema archivia gli indirizzi IP, la latitudine, la longitudine e gli ASN usati in precedenza da un utente e li considera posizioni "note". Una posizione di accesso viene considerata non nota se non corrisponde a nessuna delle posizioni note esistenti. Il sistema ha un periodo di apprendimento iniziale di 14 giorni, durante i quali le nuove posizioni non vengono contrassegnate come posizioni non note. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota esistente.

Per simulare posizioni non note, è necessario accedere da una posizione e da un dispositivo mai usati prima con l'account. Procedura dettagliata:

1.	Scegliere un account che abbia una cronologia di accesso di almeno 14 giorni. 

2.	È possibile procedere in due modi:
	
    a. Mentre si usa una VPN, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com) e immettere le credenziali dell'account per cui si vuole simulare l'evento di rischio.

    b. Chiedere a un collega in una posizione differente di accedere usando le credenziali dell'account (scelta non consigliata).

L'operazione è completata. L'accesso dovrebbe essere visualizzato in Identity Protection entro cinque minuti.
 
#### Trasferimento impossibile a posizioni atipiche
La condizione di trasferimento impossibile è difficile da simulare perché l'algoritmo usa Machine Learning per eliminare i falsi positivi, ad esempio il trasferimento impossibile da dispositivi noti o l'accesso da VPN usate da altri utenti nella directory. Per iniziare a generare eventi di rischio, l'algoritmo richiede anche una cronologia di accesso da 3 a 14 giorni per l'utente.

1.	Usando il browser standard, passare a [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.	Immettere le credenziali dell'account per cui si vuole generare un evento di rischio trasferimento impossibile.

3.	A questo punto, modificare l'agente utente. Per modificare l'agente utente in Internet Explorer, usare Strumenti di sviluppo. Per modificare l'agente utente in Firefox o Chrome, usare un componente aggiuntivo di selezione dell'agente utente.

4.	A questo punto, modificare l'indirizzo IP. È possibile modificare l'indirizzo IP usando una VPN, un componente aggiuntivo Tor o avviare un nuovo computer in Azure in un data center differente.

5.	Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com) con le stesse credenziali usate in precedenza e nell'arco di pochi minuti dall'accesso precedente.

L'accesso dovrebbe essere visualizzato in Identity Protection da due a quattro ore dopo. Tuttavia, a causa dei complessi modelli di Machine Learning coinvolti, potrebbe non essere rilevato. Potrebbe essere utile ripetere questi passaggi per più account Azure AD.


#### Simulazione di vulnerabilità 
Le vulnerabilità sono punti deboli in un ambiente Azure AD che possono essere sfruttati da un utente malintenzionato. In Azure AD Identity Protection sono attualmente visibili 3 tipi di vulnerabilità che consentono di sfruttare altre funzionalità di Azure AD. Tali vulnerabilità verranno visualizzate automaticamente in Identity Protection dopo aver impostato le funzionalità seguenti.

-	Azure AD [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
-	Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
-	Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 



###Criteri di accesso condizionale basati sul rischio
####Rischio di compromissione dell'utente

**Per testare il rischio di compromissione dell'utente, seguire questa procedura**:

1.	Accedere a [https://portal.azure.com](https://portal.azure.com) con le credenziali di amministratore globale del tenant.

2.	Passare a **Identity Protection**.

3.	Nel pannello principale di Azure AD Identity Protection fare clic su **Impostazioni**.

4.	Nel pannello **Impostazioni del portale** in **Regole di sicurezza** fare clic su **Rischio di compromissione dell'utente**.

5.	Nel pannello **Rischio di accesso** disabilitare **Abilita regola** e quindi fare clic su **Salva** per salvare le impostazioni.

6.	Per un determinato account utente, simulare un evento di rischio IP anonimo o posizione non nota. In questo modo il livello di rischio utente verrà innalzato a **medio** per tale utente.

7.	Attendere alcuni minuti e verificare che il livello utente sia **medio**.

8.	Passare al pannello **Impostazioni del portale**.

9.	Nel pannello **Rischio di compromissione dell'utente** selezionare **Sì** in **Abilita regola**.

10.	Selezionare una delle opzioni seguenti:

    a. Per bloccare, selezionare **Medio** in **Blocca l'accesso**.

    b. Per applicare la modifica della password di protezione, selezionare **Medio** in **Richiedi autenticazione a più fattori**.

13.	Fare clic su **Salva**.

14. A questo punto è possibile testare l'accesso condizionale basato sul rischio eseguendo l'accesso come un utente con un livello di rischio elevato. Se il rischio dell'utente è medio, l'accesso verrà bloccato o verrà richiesto di modificare la password, a seconda dei criteri impostati. <br><br> ![Studio](./media/active-directory-identityprotection/201.png "Studio") <br>

 
####Rischio di accesso

 


Per testare il rischio di accesso, seguire questa procedura:

1.	Accedere a [https://portal.azure.com](https://portal.azure.com) con le credenziali di amministratore globale del tenant.

2.	Passare a **Identity Protection**.

3.	Nel pannello principale di **Azure AD Identity Protection** fare clic su **Impostazioni**.

4.	Nel pannello **Impostazioni del portale** in **Regole di sicurezza** fare clic su **Rischio di accesso**.

5.	Nel pannello **Rischio di accesso** selezionare **Sì** in **Abilita regola**.

7.	Selezionare una delle opzioni seguenti:

    a. Per bloccare, selezionare **Medio** in **Blocca l'accesso**.

    b. Per applicare la modifica della password di protezione, selezionare **Medio** in **Richiedi autenticazione a più fattori**.

8.	Per bloccare, selezionare Medio in Blocca l'accesso...

9.	Per applicare l'autenticazione a più fattori, selezionare Medio in Richiedi autenticazione a più fattori...

10.	Fare clic su **Save**.

11.	A questo punto è possibile testare l'accesso condizionale basato sul rischio simulando gli eventi di rischio posizioni non note o IP anonimo, che sono considerati di livello medio.

<br> ![Studio](./media/active-directory-identityprotection/200.png "Studio") <br>


## Vedere anche

 - [Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Glossario di Identity Protection](active-directory-identityprotection-glossary.md)

<!------HONumber=AcomDC_0302_2016-->