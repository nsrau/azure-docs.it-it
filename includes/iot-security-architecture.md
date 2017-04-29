# <a name="internet-of-things-security-architecture"></a>Architettura di sicurezza di Internet delle cose
Quando si progetta un sistema, è importante comprendere le potenziali minacce e aggiungere le difese appropriate di conseguenza, perché il sistema è definito da una progettazione e un'architettura specifiche. È particolarmente importante progettare il prodotto tenendo conto della sicurezza, perché comprendere in che modo un utente malintenzionato potrebbe compromettere un sistema aiuta ad implementare le misure appropriate sin dall'inizio. 

## <a name="security-starts-with-a-threat-model"></a>La sicurezza inizia con un modello di rischio
Da tempo Microsoft usa modelli di rischio per i suoi prodotti e ha reso disponibile al pubblico il suo processo aziendale di modellazione delle minacce. L'esperienza aziendale dimostra che la modellazione comporta alcuni vantaggi imprevisti oltre l'immediata individuazione delle minacce più preoccupanti. Ad esempio, crea anche un possibile approccio a una discussione aperta con persone esterne al team di sviluppo, che può condurre a nuove idee e a miglioramenti del prodotto.

L'obiettivo della modellazione delle minacce consiste nel comprendere in che modo un utente malintenzionato potrebbe compromettere un sistema e, di conseguenza, nell'assicurarsi che vengano attuate le misure appropriate. La modellazione delle minacce obbliga il team di progettazione a valutare misure di prevenzione durante la progettazione di un sistema, anziché durante la sua distribuzione. Ciò è di fondamentale importanza, perché l'adeguamento retroattivo delle difese di sicurezza a una varietà di dispositivi non è fattibile, è soggetto a errori e mette a rischio i clienti.

Molti team di sviluppo svolgono un eccellente lavoro di acquisizione dei requisiti funzionali per il sistema da cui i clienti traggono vantaggio. Tuttavia, è molto più difficile identificare i modi impropri, ma non ovvi, in cui qualcuno potrebbe usare il sistema. La modellazione delle minacce può aiutare i team di sviluppo a comprendere cosa potrebbe fare un utente malintenzionato e perché. La modellazione delle minacce è un processo strutturato che favorisce una discussione sulle decisioni in merito alla progettazione di sicurezza nel sistema, nonché sulle modifiche alla progettazione effettuate lungo il percorso e che incidono sulla sicurezza. Mentre un modello di rischio è semplicemente un documento, questa documentazione rappresenta anche un modo ideale per garantire la continuità della conoscenza, la conservazione delle lezioni apprese e il rapido inserimento del nuovo team. Infine, uno dei risultati della modellazione delle minacce consiste nella possibilità di prendere in considerazione altri aspetti della sicurezza, ad esempio quali impegni di sicurezza si vogliono fornire ai clienti. Queste operazioni, in combinazione con la modellazione delle minacce, condurranno all'esecuzione di un test informato della soluzione Internet delle cose (IoT).

### <a name="when-to-threat-model"></a>Quando implementare la modellazione delle minacce
La [modellazione delle minacce](http://www.microsoft.com/security/sdl/adopt/threatmodeling.aspx) offre il massimo valore aggiunto se incorporata nella fase di progettazione. Durante la progettazione, si può contare sulla massima flessibilità nell'apportare modifiche al fine di evitare le minacce. L'eliminazione delle minacce prevista da progettazione è il risultato auspicato. È molto più semplice rispetto all'aggiunta e al test di misure preventive, oltre che al garantire che rimangano correnti; per di più, tale eliminazione non è sempre possibile. È sempre più difficile eliminare minacce con l'evolversi di un prodotto, attività che, a sua volta, in ultima analisi richiederà un impegno maggiore e molti più compromessi rispetto alla modellazione delle minacce nella fase iniziale dello sviluppo.

### <a name="what-to-threat-model"></a>Dove implementare la modellazione delle minacce
È consigliabile applicare la modellazione delle minacce all'intera soluzione e concentrarsi anche nelle aree seguenti:

* Funzionalità di sicurezza e privacy
* Funzionalità con errori di sicurezza
* Funzionalità che incontrano un limite di attendibilità 

### <a name="who-threat-models"></a>Chi implementa la modellazione delle minacce
La modellazione delle minacce è un processo come qualsiasi altro.  È consigliabile considerare il documento di modellazione delle minacce come qualsiasi altro componente della soluzione e convalidarlo. Molti team di sviluppo svolgono un eccellente lavoro di acquisizione dei requisiti funzionali per il sistema da cui i clienti traggono vantaggio. Tuttavia, è molto più difficile identificare i modi impropri, ma non ovvi, in cui qualcuno potrebbe usare il sistema. La modellazione delle minacce può aiutare i team di sviluppo a comprendere cosa potrebbe fare un utente malintenzionato e perché.

### <a name="how-to-threat-model"></a>Come implementare la modellazione delle minacce
Il processo di modellazione delle minacce è composto da quattro passaggi, elencati di seguito:

* Modellazione dell'applicazione
* Enumerazione delle minacce
* Attenuazione delle minacce
* Convalida delle misure preventive

#### <a name="the-process-steps"></a>Passaggi del processo
Ci sono tre regole empiriche da tenere presenti durante la creazione di un modello di rischio:

1. Creare un diagramma basato sull'architettura di riferimento. 
2. Iniziare con la ricerca in ampiezza. Ottenere una panoramica e comprendere il sistema nel suo complesso, prima di addentrarsi.  Ciò garantisce un approfondimento nei posti giusti.
3. È l'utente a guidare il processo, non il contrario. Se si riscontra un problema durante la fase di modellazione e lo si vuole esaminare, procedere pure:  non è necessario seguire pedissequamente questa procedura.  

#### <a name="threats"></a>Minacce
I quattro elementi principali di un modello di rischio sono i seguenti:

* Processi (servizi web, servizi di Win32, * nix daemon e così via). Si noti che alcune entità complesse (ad esempio, i sensori e i gateway sul campo) possono essere astratte come processo quando non è possibile eseguire un drill-down tecnico in queste aree.
* Archivi di dati (qualsiasi punto in cui sono memorizzati dati, ad esempio un file di configurazione o un database)
* Flusso di dati (in cui i dati si spostano tra altri elementi nell'applicazione)
* Entità esterne (tutto ciò che interagisce con il sistema, ma non è sotto il controllo dell'applicazione, ad esempio utenti e immagini satellitari)

Tutti gli elementi nel diagramma dell'architettura sono soggetti a varie minacce; verrà usato l'elemento mnemonico STRIDE. Per altre informazioni sugli elementi STRIDE, leggere il post di blog [Threat Modeling Again, STRIDE](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) (Nuova modellazione delle minacce, STRIDE).

Diversi elementi del diagramma applicazioni sono soggetti a determinate minacce STRIDE:

* I processi sono soggetti a STRIDE
* I flussi di dati sono soggetti a TID
* Gli archivi dati sono soggetti a TID e, talvolta, a R se gli archivi di dati sono file di log.
* Le entità esterne sono soggette a SRD

## <a name="security-in-iot"></a>Sicurezza in IoT
I dispositivi per scopi specifici connessi tra loro sono caratterizzati da un numero significativo di potenziali superfici di attacco e di modelli di interazione, tutti da prendere in considerazione nel fornire un framework per la protezione dell'accesso digitale agli stessi. Il termine "accesso digitale" viene qui usato per distinguerlo da qualsiasi altra operazione che preveda l'interazione diretta con i dispositivi laddove è presente la sicurezza dell'accesso attraverso il controllo di accesso fisico (ad esempio, collocando il dispositivo in una stanza chiusa a chiave). Nonostante non sia possibile negare l'accesso fisico usando software e hardware, è però possibile adottare misure preventive per far sì che l'accesso fisico non conduca a intromissioni nel sistema. 

Esplorando i modelli di interazione verranno presi in esame il controllo dei dispositivi e i dati del dispositivo con lo stesso livello di attenzione. Per controllo dei dispositivi si intende qualsiasi informazione fornita a un dispositivo da qualsiasi parte, con l'obiettivo di cambiarne o influenzarne il comportamento nei riguardi del suo stesso stato o dello stato del relativo ambiente. Per dati del dispositivo si intende qualsiasi informazione emessa da un dispositivo a qualsiasi altra parte riguardo il suo stato e lo stato osservato del relativo ambiente.

Allo scopo di ottimizzare le procedure consigliate di sicurezza, è consigliabile suddividere una tipica architettura IoT in svariati componenti/zone come parte dell'esercizio di modellazione delle minacce. Queste zone sono descritte dettagliatamente in questa sezione e includono:

* Dispositivo
* Gateway sul campo
* Gateway cloud
* Servizi

Le zone rappresentano una segmentazione generica di una soluzione. Ogni zona spesso contiene dati e requisiti di autenticazione e autorizzazione specifici. Le zone possono anche essere usate per isolare i danni e limitare l'impatto delle zone di bassa attendibilità sulle zone di attendibilità superiore.

Ciascuna zona è separata da un limite di attendibilità, indicato dalla linea rossa punteggiata nel diagramma riportato di seguito. Esso rappresenta una transizione di dati/informazioni da un'origine a un'altra. Durante questa transizione, i dati potrebbe essere soggetti a spoofing, manomissione, ripudio, divulgazione di informazioni, rifiuto del servizio ed elevazione dei privilegi, semplificati dall'acronimo STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege).

![Aree di sicurezza IoT](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Anche i componenti rappresentati all'interno di ogni limite sono soggetti a STRIDE, consentendo una visione della soluzione con una modellazione delle minacce a 360 gradi. Le sezioni seguenti introducono ciascuno dei componenti, oltre ai problemi di sicurezza specifici e alle soluzioni che devono essere implementate.

Le sezioni di seguito illustreranno i componenti standard in genere disponibili in queste zone.

### <a name="the-device-zone"></a>Zona Dispositivo
L'ambiente del dispositivo è lo spazio fisico immediato che circonda il dispositivo, in cui l'accesso fisico e/o l'accesso digitale peer-to-peer via "rete locale" al dispositivo è fattibile. Si presuppone che una "rete locale" sia una rete distinta e isolata dalla rete Internet pubblica (ma potenzialmente connessa con bridging alla stessa) che includa tutte le tecnologie wireless a corto raggio per consentire la comunicazione peer-to-peer dei dispositivi. La stessa rete *non* include tecnologie di virtualizzazione delle reti che creino l'illusione di una rete locale, né reti di operatore pubblico che richiedano la comunicazione di una coppia qualsiasi di dispositivi in uno spazio di rete pubblica, qualora dovessero entrare in una relazione di comunicazione peer-to-peer.

### <a name="the-field-gateway-zone"></a>Zona Gateway sul campo
Un gateway sul campo è un dispositivo o un software per server di uso generico che agisce come componente che abilita la comunicazione e, potenzialmente, come sistema di controllo dei dispositivi e hub di elaborazione dei dati dei dispositivi. La zona di un gateway sul campo include il gateway stesso e tutti i dispositivi collegati. Come suggerisce il nome, i gateway sul campo operano all'esterno della struttura di elaborazione dei dati, sono solitamente associati alla posizione, sono potenzialmente soggetti a intrusioni e hanno una limitata ridondanza operativa. Tutto ciò per affermare che un gateway sul campo è in genere un sistema che è possibile toccare e sabotare ben conoscendone la funzione. 

Un gateway sul campo è diverso da un semplice router di traffico, perché ha avuto un ruolo attivo nella gestione dell'accesso e del flusso di informazioni. In altre parole, è un'entità orientata alle applicazioni e un terminale di sessione o di connessione di rete. I dispositivi o i firewall NAT, al contrario, non sono idonei come gateway sul campo perché non sono terminali di sessione o di connessione espliciti, ma piuttosto inoltrano (o bloccano) le connessioni o le sessioni che li attraversano. Il gateway sul campo ha due distinte superfici di attacco: una fronteggia i dispositivi ad esso collegati e rappresenta l'interno della zona e l'altra fronteggia tutte le parti esterne ed è il bordo della zona.   

### <a name="the-cloud-gateway-zone"></a>Zona Gateway cloud
Il gateway cloud è un sistema che consente la comunicazione remota da e verso dispositivi o gateway sul campo da più siti diversi attraverso lo spazio di rete pubblica, in genere verso un controllo basato su cloud e un sistema di analisi dei dati, una federazione di tali sistemi. In alcuni casi, un gateway cloud può immediatamente facilitare l'accesso ai dispositivi per scopi specifici dai terminali, ad esempio tablet o telefoni. Nel contesto presentato in questo argomento, "cloud" fa riferimento a un sistema di elaborazione dati dedicato che non è associato allo stesso sito dei dispositivi o gateway sul campo collegati. In una zona cloud le misure operative impediscono anche l'accesso fisico mirato ed essa non è necessariamente esposta a un'infrastruttura di "cloud pubblico".  

Un gateway cloud potrebbe essere potenzialmente mappata a una sovrapposizione di virtualizzazione rete per isolare da qualsiasi altro traffico di rete il gateway cloud e tutti i relativi dispositivi o gateway sul campo collegati. Il gateway cloud stesso non è né un sistema di controllo del dispositivo né una risorsa di elaborazione o archiviazione per i dati del dispositivo; tali funzionalità si interfacciano con il gateway cloud. La zona di gateway cloud include il gateway cloud stesso assieme a tutti i gateway sul campo e ai dispositivi direttamente o indirettamente a esso collegati. Il bordo della zona è una superficie di attacco distinta in cui tutte le entità esterne comunicano.

### <a name="the-services-zone"></a>Zona Servizi
Un "servizio" è definito per questo contesto come qualsiasi componente software o modulo che si interfaccia con i dispositivi attraverso un gateway sul campo o cloud per la raccolta e l'analisi dei dati, nonché per il controllo e il comando.  I servizi sono mediatori, cioè operano con la loro identità nei gateway e in altri sottosistemi, archiviano e analizzano dati, inviano in maniera autonoma comandi ai dispositivi in base a informazioni dettagliate o pianificazioni dei dati ed espongono informazioni e funzionalità di controllo agli utenti finali autorizzati.

### <a name="information-devices-vs-special-purpose-devices"></a>Confronto tra dispositivi informativi e dispositivi per scopi specifici
PC, telefoni e tablet sono principalmente dispositivi informativi interattivi. Telefoni e tablet sono ottimizzati in modo esplicito per massimizzare la durata della batteria, disattivandosi parzialmente quando non interagiscono nell'immediato con una persona oppure quando non forniscono servizi, come riprodurre musica o guidare il proprietario verso una particolare località. Dal punto di vista sistemico, questi dispositivi IT fungono principalmente da delegato verso le persone: sono "attuatori di persone" che suggeriscono azioni e "sensori di persone" che raccolgono input. 

I dispositivi per scopi specifici, dai semplici sensori di temperatura alle complesse linee di produzione industriale che contengono migliaia di componenti, sono differenti. Questi dispositivi hanno uno scopo molto più mirato e, anche se offrono una sorta di interfaccia utente, sono per lo più destinati a interfacciarsi con le risorse del mondo fisico o a esservi integrati. Tali strumenti misurano e segnalano condizioni ambientali, girano valvole, controllano servomotori, fanno suonare allarmi, accendono luci ed eseguono molti altri compiti. In poche parole, aiutano a eseguire operazioni per cui un dispositivo informativo è troppo generico, troppo costoso, troppo grande o troppo instabile. Lo scopo concreto ne determina immediatamente le tecniche di progettazione, come pure il budget monetario disponibile per la produzione e il periodo di funzionamento pianificato. La combinazione di questi due fattori chiave vincola il budget energetico operativo disponibile, l'impronta fisica e quindi le funzionalità di archiviazione, calcolo e sicurezza disponibili.  

Se si verifica un errore nei dispositivi automatizzati o controllabili a distanza, ad esempio, i difetti fisici o della logica di controllo possono condurre a una deliberata intrusione e manipolazione non autorizzata. Le conseguenze potrebbero essere fatali: lotti di produzione distrutti, edifici saccheggiati o ridotti in cenere, persone gravemente ferite o persino decedute. Si tratta, ovviamente, di una classe di danno del tutto differente dal superamento del limite di spesa con una carta di credito trafugata. Il livello di sicurezza per i dispositivi che fanno muovere le cose, nonché per i dati del sensore che infine generano i comandi che fanno muovere le cose, deve essere maggiore rispetto a qualsiasi scenario bancario o di e-commerce. 

### <a name="device-control-and-device-data-interactions"></a>Interazioni tra controllo del dispositivo e dati del dispositivo
I dispositivi per scopi specifici connessi tra loro sono caratterizzati da un numero significativo di potenziali superfici di attacco e di modelli di interazione, tutti da prendere in considerazione nel fornire un framework per la protezione dell'accesso digitale agli stessi. Il termine "accesso digitale" viene qui usato per distinguerlo da qualsiasi altra operazione che preveda l'interazione diretta con i dispositivi laddove è presente la sicurezza dell'accesso attraverso il controllo di accesso fisico (ad esempio, collocando il dispositivo in una stanza chiusa a chiave). Nonostante non sia possibile negare l'accesso fisico usando software e hardware, è però possibile adottare misure preventive per far sì che l'accesso fisico non conduca a intromissioni nel sistema. 

Esplorando i modelli di interazione verranno presi in esame il controllo dei dispositivi e i dati del dispositivo con lo stesso livello di attenzione mostrato durante la modellazione delle minacce. Per controllo dei dispositivi si intende qualsiasi informazione fornita a un dispositivo da qualsiasi parte, con l'obiettivo di cambiarne o influenzarne il comportamento nei riguardi del suo stesso stato o dello stato del relativo ambiente. Per dati del dispositivo si intende qualsiasi informazione emessa da un dispositivo a qualsiasi altra parte riguardo il suo stato e lo stato osservato del relativo ambiente. 

## <a name="threat-modeling-the-azure-iot-reference-architecture"></a>Modellazione delle minacce nell'architettura di riferimento di Azure IoT
Microsoft usa il framework descritto in precedenza per eseguire la modellazione delle minacce per Azure IoT. Nella sezione seguente verrà quindi usato un esempio concreto di Architettura di riferimento di Azure IoT per illustrare come gestire la modellazione delle minacce per IoT e come affrontare le minacce identificate. In questo caso, sono state identificate quattro aree principali di interesse:

* Dispositivi e origini dati
* Trasporto dei dati
* Elaborazione di dispositivi ed eventi
* Presentazione

![Modellazione delle minacce per Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png) 

Il diagramma seguente offre una vista semplificata dell'Architettura IoT Microsoft con un modello di diagramma di flusso di dati che viene usato dallo strumento di modellazione delle minacce di Microsoft:

![Modellazione delle minacce per Azure IoT con l'apposito strumento Microsoft](media/iot-security-architecture/iot-security-architecture-fig3.png)

È importante notare che l'architettura separa le funzionalità del dispositivo e del gateway. Ciò consente all'utente di sfruttare i dispositivi gateway, che sono più sicuri, perché capaci di comunicare con il gateway cloud usando protocolli di protezione. Ciò in genere richiede un maggiore carico di elaborazione rispetto a quanto un dispositivo nativo (ad esempio un termostato) potrebbe fornire autonomamente. Nella zona servizi di Azure si suppone che il gateway cloud sia rappresentato dal servizio dell'hub IoT di Azure.

### <a name="device-and-data-sourcesdata-transport"></a>Dispositivo e origini dati/trasporto dei dati
Questa sezione illustra l'architettura descritta in precedenza attraverso l'obiettivo della modellazione delle minacce e offre una panoramica su come risolvere alcuni problemi intrinseci. L'articolo si concentra sugli elementi principali di un modello di rischio:

* Processi (quelli sotto il controllo degli utenti e gli elementi esterni)
* Comunicazione (o flusso di dati)
* Archiviazione (o archivi di dati)

#### <a name="processes"></a>Processi
In ognuna delle categorie descritte nell'architettura IoT di Azure, si proverà ad attenuare una serie di diverse minacce nelle varie fasi in cui esistono dati e/o informazioni: processo, comunicazione e archiviazione. Di seguito viene fornita una panoramica di quelle più comuni per la categoria "processo", seguita da una panoramica su come sia meglio attenuarle: 

**Spoofing (S)**: un utente malintenzionato potrebbe estrarre materiale della chiave crittografica da un dispositivo, a livello di software o di hardware, e successivamente accedere al sistema con un diverso dispositivo fisico o virtuale che assume l'identità del dispositivo da cui è stata estratta la chiave. Un buon esempio sono i telecomandi che possono accendere qualsiasi televisore e sono popolari strumenti per burloni.

**Denial of Service (D)**: un dispositivo potrebbe essere messo fuori uso o reso incapace di comunicare tramite interferenza con le frequenze radio o taglio dei cavi. Ad esempio, una videocamera di sorveglianza a cui sia stata intenzionalmente interrotta l'alimentazione o la connessione di rete non segnalerà alcun dato.

**Manomissione (T)**: un utente malintenzionato potrebbe sostituire interamente o in parte il software in esecuzione sul dispositivo, consentendo potenzialmente al software sostituito di sfruttare l'autentica identità del dispositivo se il materiale della chiave o le strutture che lo contengono erano disponibili per il programma illecito. Ad esempio, un utente malintenzionato potrebbe sfruttare il materiale della chiave estratto per intercettare ed eliminare i dati dal dispositivo nel percorso di comunicazione e sostituirli con dati falsi che sono stati autenticati con il materiale della chiave trafugato.

**Diffusione di informazioni (I)**: se sul dispositivo viene eseguito un software manipolato, questo potrebbe potenzialmente far trapelare dati a parti non autorizzate. Ad esempio, un utente malintenzionato potrebbe sfruttare il materiale della chiave estratto per inserirsi automaticamente nel percorso di comunicazione tra il dispositivo e un controller o un gateway sul campo o un gateway di cloud per trafugare informazioni.

**Elevazione dei privilegi (E)**: è possibile forzare un dispositivo che esegue una funzione specifica a eseguire un'altra operazione. Ad esempio, una valvola che è programmata per aprirsi a metà può essere indotta ad aprirsi completamente.

| **Componente** | **Minaccia** | **Attenuazione** | **Rischio** | **Implementazione** |
| --- | --- | --- | --- | --- |
| Dispositivo |S |Assegnazione dell'identità al dispositivo e autenticazione del dispositivo |Sostituzione del dispositivo o di parte dello stesso con un altro dispositivo Come stabilire che si sta comunicando con il dispositivo giusto? |Autenticazione del dispositivo con Transport Layer Security (TLS) o IPSec. L'infrastruttura deve supportare l'uso di una chiave precondivisa (PSK) nei dispositivi che non riescono a gestire la crittografia asimmetrica completa. Usare Azure AD, [OAuth](http://www.rfc-editor.org/in-notes/internet-drafts/draft-ietf-ace-oauth-authz-01.txt) |
| TRID |Applicare meccanismi a prova di manomissione al dispositivo, ad esempio rendendo difficile, se non impossibile, estrarre chiavi e altro materiale crittografico dallo stesso. |Il rischio esiste se un utente sta manomettendo il dispositivo (intromissione fisica). Come è possibile accertarsi che il dispositivo non sia stato manomesso. |La soluzione più efficace è una funzionalità TPM (Trusted Platform Module) che consente l'archiviazione delle chiavi in speciali circuiti su chip da cui non è possibile leggerle, ma solo usarle per le operazioni di crittografia che le adoperano, senza mai divulgarle. Crittografia della memoria del dispositivo. Gestione delle chiavi per il dispositivo. Firma del codice. | |
| E |Avere il controllo di accesso del dispositivo. Schema di autorizzazione. |Se il dispositivo consente di eseguire singole azioni in base ai comandi da un'origine esterna o persino a sensori compromessi, l'attacco potrà eseguire operazioni non altrimenti accessibili. |Avere uno schema di autorizzazione per il dispositivo | |
| Gateway sul campo |S |Autenticazione del gateway sul campo al gateway cloud (basata sul certificato, PSK, basata su attestazione) |Se qualcuno riesce a effettuare lo spoofing del gateway sul campo, questo potrà presentarsi come qualsiasi dispositivo. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Tutti gli stessi principali problemi di archiviazione e attestazione dei dispositivi in generale: il miglior caso è l'uso di TPM. Estensione 6LowPAN per IPSec per supportare le reti WSN (Wireless Sensor Network). |
| TRID |Proteggere il gateway sul campo da eventuali manomissioni (TPM)? |Gli attacchi di spoofing che simulano la comunicazione tra il gateway cloud e il gateway sul campo potrebbero comportare la divulgazione delle informazioni e la manomissione dei dati |Crittografia della memoria, TPM, autenticazione. | |
| E |Meccanismo di controllo di accesso per il gateway sul campo | | | |

Ecco alcuni esempi di minacce in questa categoria:

Spoofing, ovvero il furto di identità: un utente malintenzionato potrebbe estrarre materiale della chiave crittografica da un dispositivo, a livello di software o hardware, e successivamente accedere al sistema con un diverso dispositivo fisico o virtuale che assume l'identità del dispositivo da cui è stata estratta la chiave.

**Denial of Service**: un dispositivo potrebbe essere messo fuori uso o reso incapace di comunicare tramite interferenza con le frequenze radio o taglio dei cavi. Ad esempio, una videocamera di sorveglianza a cui sia stata intenzionalmente interrotta l'alimentazione o la connessione di rete non segnalerà alcun dato.

**Manomissione**: un utente malintenzionato potrebbe sostituire interamente o in parte il software in esecuzione sul dispositivo, consentendo potenzialmente al software sostituito di sfruttare l'autentica identità del dispositivo se il materiale della chiave o le strutture che lo contengono sono disponibili per il programma illecito.

**Manomissione**: una telecamera di sorveglianza che mostra l'immagine dello spettro visibile di un corridoio vuoto potrebbe essere puntata su una foto dello stesso corridoio. Un sensore di fumo o antincendio potrebbe segnalare una persona che tiene un accendino acceso al di sotto dello stesso. In entrambi i casi, il dispositivo potrebbe essere tecnicamente del tutto attendibile nei confronti del sistema, ma segnalerà informazioni manipolate.

**Manomissione**: ad esempio, un utente malintenzionato potrebbe sfruttare il materiale della chiave estratto per intercettare ed eliminare i dati dal dispositivo nel percorso di comunicazione e sostituirli con dati falsi che sono stati autenticati con il materiale della chiave trafugato.

**Manomissione**: un utente malintenzionato potrebbe sostituire completamente o in parte il software in esecuzione nel dispositivo, consentendo potenzialmente al software sostituito di sfruttare l'autentica identità del dispositivo se il materiale della chiave o le strutture che lo contengono sono disponibili per il programma illecito.

**Diffusione di informazioni**: se nel dispositivo viene eseguito un software manipolato, questo potrebbe potenzialmente far trapelare dati a parti non autorizzate.

**Diffusione di informazioni**: un utente malintenzionato potrebbe sfruttare il materiale della chiave estratto per inserirsi automaticamente nel percorso di comunicazione tra il dispositivo e un controller oppure un gateway sul campo o un gateway cloud per trafugare informazioni.

**Denial of Service**: il dispositivo potrebbe essere spento oppure commutato in una modalità in cui la comunicazione non è possibile, il che è intenzionale in molti macchinari industriali.

**Manomissione**: il dispositivo può essere riconfigurato in modo da operare in uno stato sconosciuto al sistema di controllo, al di fuori dei parametri di calibrazione noti, e quindi offrire dati che possono essere interpretati erroneamente.

**Elevazione dei privilegi**: è possibile forzare un dispositivo che esegue una funzione specifica a eseguire un'altra operazione. Ad esempio, una valvola che è programmata per aprirsi a metà può essere indotta ad aprirsi completamente.

**Denial of Service**: il dispositivo può essere commutato in uno stato in cui la comunicazione non è possibile.

**Manomissione**: il dispositivo può essere riconfigurato in modo da operare in uno stato sconosciuto al sistema di controllo, al di fuori dei parametri di calibrazione noti, e quindi offrire dati che possono essere interpretati erroneamente.

**Spoofing/Manomissione/Ripudio**: se un dispositivo non è protetto, il che accade assai raramente con i telecomandi di consumo, un utente malintenzionato può manipolarne lo stato in maniera anonima. Un buon esempio sono i telecomandi che possono accendere qualsiasi televisore e sono popolari strumenti per burloni.

#### <a name="communication"></a>Comunicazione
Minacce presenti nel percorso di comunicazione tra dispositivi, dispositivi e gateway sul campo e dispositivo e gateway cloud. La tabella seguente contiene alcune indicazioni sui socket aperti nel dispositivo/VPN:

| **Componente** | **Minaccia** | **Attenuazione** | **Rischio** | **Implementazione** |
| --- | --- | --- | --- | --- |
| Dispositivo - Hub IoT |TID |(D)TLS (PSK/RSA) per crittografare il traffico |Intercettazione o interferenza nella comunicazione tra il dispositivo e il gateway |Sicurezza a livello di protocollo. Con i protocolli personalizzati, è necessario capire come proteggerli. Nella maggior parte dei casi, la comunicazione avviene dal dispositivo all'hub IoT (connessione avviata dal dispositivo). |
| Dispositivo - dispositivo |TID |(D) TLS (PSK/RSA) per crittografare il traffico. |Lettura dei dati in transito tra i dispositivi. Manomissione dei dati. Sovraccarico del dispositivo con nuove connessioni |Sicurezza a livello di protocollo (MQTT/AMQP/HTTP/CoAP). Con i protocolli personalizzati, è necessario capire come proteggerli. La soluzione per la minaccia Denial of Service consiste nell'eseguire il peering dei dispositivi attraverso un gateway cloud o sul campo e far sì che agiscano solo da client verso la rete. Il peering può comportare una connessione diretta tra i peer dopo che è stata negoziata dal gateway |
| Entità esterna - dispositivo |TID |Associazione complessa dell'entità esterna al dispositivo |Intercettazione della connessione al dispositivo. Interferenza nella connessione al dispositivo |Associazione sicura dell'entità esterna al dispositivo NFC/Bluetooth LE. Controllo del pannello operativo del dispositivo (fisico) |
| Gateway sul campo - Gateway cloud |TID |TLS (PSK/RSA) per crittografare il traffico. |Intercettazione o interferenza nella comunicazione tra il dispositivo e il gateway |Sicurezza a livello di protocollo (MQTT/AMQP/HTTP/CoAP). Con i protocolli personalizzati, è necessario capire come proteggerli. |
| Dispositivo - Gateway cloud |TID |TLS (PSK/RSA) per crittografare il traffico. |Intercettazione o interferenza nella comunicazione tra il dispositivo e il gateway |Sicurezza a livello di protocollo (MQTT/AMQP/HTTP/CoAP). Con i protocolli personalizzati, è necessario capire come proteggerli. |

Ecco alcuni esempi di minacce in questa categoria:

**Denial of Service**: i dispositivi limitati sono in genere a rischio Denial of Service quando restano attivamente in attesa di connessioni in ingresso o datagrammi non richiesti in una rete, perché un utente malintenzionato può aprire molte connessioni in parallelo non rendendole disponibili oppure rallentandole o ancora il dispositivo può essere inondato da traffico non richiesto. In entrambi i casi, il dispositivo può essere effettivamente reso inutilizzabile sulla rete.

**Spoofing, Diffusione di informazioni**: i dispositivi limitati e con scopi specifici spesso prevedono funzionalità di sicurezza universali, come la protezione con PIN o password, oppure dipendono interamente dall'attendibilità della rete, ovvero consentono l'accesso alle informazioni quando un dispositivo si trova nella stessa rete, che spesso è protetta solo da una chiave condivisa. Ciò significa che quando viene divulgato il segreto condiviso al dispositivo o alla rete, è possibile controllare il dispositivo oppure osservare i dati inviati dal dispositivo.  

**Spoofing**: un utente malintenzionato potrebbe intercettare o parzialmente sostituire la trasmissione e rubare l'identità dell'iniziatore (attacco man in the middle)

**Manomissione**: un utente malintenzionato può intercettare o sostituire parzialmente la trasmissione e inviare informazioni false 

**Diffusione di informazioni**: un utente malintenzionato può intercettare una trasmissione e ottenere le informazioni senza autorizzazione **Denial of Service**: un utente malintenzionato può bloccare il segnale di trasmissione e negare la distribuzione di informazioni

#### <a name="storage"></a>Archiviazione
Ogni dispositivo e gateway sul campo prevede un tipo di archiviazione (temporanea per accodamento dei dati, archiviazione di immagini del sistema operativo).

| **Componente** | **Minaccia** | **Attenuazione** | **Rischio** | **Implementazione** |
| --- | --- | --- | --- | --- |
| Archiviazione nel dispositivo |TRID |Crittografia di archiviazione, firma dei log |Lettura dei dati dalla risorsa di archiviazione (dati PII), manomissione dei dati di telemetria. Manomissione dei dati di controllo del comando in coda o memorizzati nella cache. La manomissione di pacchetti di configurazione o aggiornamento del firmware memorizzati nella cache o in coda in locale può compromettere il sistema operativo e/o i componenti del sistema |Crittografia, codice di autenticazione messaggi (MAC) o firma digitale. Laddove possibile, un controllo di accesso complesso attraverso elenchi di controllo di accesso (ACL) o autorizzazioni. |
| Immagine del sistema operativo del dispositivo |TRID | |Manomissione del sistema operativo/sostituzione dei componenti del sistema operativo |Partizione del sistema operativo di sola lettura, immagine del sistema operativo firmata, crittografia |
| Archiviazione Gateway sul campo (accodamento dei dati) |TRID |Crittografia di archiviazione, firma dei log |Lettura dei dati dalla risorsa di archiviazione (dati PII), manomissione dei dati di telemetria, manomissione dei dati di controllo del comando in coda o memorizzati nella cache. La manomissione di pacchetti di configurazione o aggiornamento del firmware (destinati ai dispositivi o al gateway sul campo) memorizzati nella cache o in coda in locale può compromettere il sistema operativo e/o i componenti del sistema |BitLocker |
| Immagine del sistema operativo Gateway sul campo |TRID | |Manomissione del sistema operativo/sostituzione dei componenti del sistema operativo |Partizione del sistema operativo di sola lettura, immagine del sistema operativo firmata, crittografia |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Elaborazione di dispositivi ed eventi/zona gateway cloud
Un gateway cloud è un sistema che consente la comunicazione remota da e verso dispositivi o gateway sul campo da più siti diversi attraverso lo spazio di rete pubblica, in genere verso un controllo basato su cloud e un sistema di analisi dei dati, una federazione di tali sistemi. In alcuni casi, un gateway cloud può immediatamente facilitare l'accesso ai dispositivi per scopi specifici dai terminali, ad esempio tablet o telefoni. Nel contesto presentato in questo argomento, "cloud" fa riferimento a un sistema di elaborazione dati dedicato che non è associato allo stesso sito dei dispositivi o gateway sul campo collegati e in cui le misure operative prevengono l'accesso fisico mirato, ma che non rappresenta necessariamente un'infrastruttura di "cloud pubblico".  Un gateway cloud potrebbe essere potenzialmente mappata a una sovrapposizione di virtualizzazione rete per isolare da qualsiasi altro traffico di rete il gateway cloud e tutti i relativi dispositivi o gateway sul campo collegati. Il gateway cloud stesso non è né un sistema di controllo del dispositivo né una risorsa di elaborazione o archiviazione per i dati del dispositivo; tali funzionalità si interfacciano con il gateway cloud. La zona di gateway cloud include il gateway cloud stesso assieme a tutti i gateway sul campo e ai dispositivi direttamente o indirettamente a esso collegati.

Un gateway cloud è in gran parte un software personalizzato ed eseguito come servizio, con endpoint esposti a cui si connettono i dispositivi e il gateway sul campo. Di conseguenza deve essere progettato tenendo presente la sicurezza. Seguire il processo [SDL](http://www.microsoft.com/sdl) per la progettazione e la creazione di questo servizio. 

#### <a name="services-zone"></a>Zona Servizi
Un sistema di controllo (o controller) è una soluzione software che si interfaccia con un dispositivo o un gateway sul campo o un gateway cloud allo scopo di controllare uno o più dispositivi e/o raccogliere e/o archiviare e/o analizzare i dati del dispositivo per una presentazione o a scopo di controllo successivo. I sistemi di controllo sono le uniche entità nell'ambito di questa discussione che possono immediatamente facilitare l'interazione con altri utenti. L'eccezione è rappresentata dalle superfici di controllo fisiche intermedie nei dispositivi, ad esempio un interruttore che consente di disattivare il dispositivo o modificare altre proprietà e per cui non esiste alcun equivalente funzionale che sia accessibile in maniera digitale. 

Le superfici di controllo fisiche intermedie sono quelle in cui qualsiasi tipo di logica governante vincola la funzione della superficie di controllo fisica, in modo che sia possibile avviare una funzione equivalente in modalità remota o evitare conflitti di input con l'input remoto. Tali superfici di controllo intermedie sono concettualmente collegate a un sistema di controllo locale che sfrutta la stessa funzionalità sottostante di qualsiasi altro sistema di controllo remoto, a cui il dispositivo può essere collegato in parallelo. Le principali minacce al cloud computing sono elencate nella pagina [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/research/top-threats/).

## <a name="additional-resources"></a>Risorse aggiuntive
Per altre informazioni, vedere gli articoli seguenti:

* [SDL Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx) (Strumento per la modellazione di minacce SDL)
* [Architettura di riferimento di Microsoft Azure IoT](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)

