<properties
	pageTitle="Esempi contestuali delle procedure consigliate per l'implementazione di modelli"
	description="Vengono illustrati esempi di modelli di Gestione risorse di Azure che indicano le procedure consigliate."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="mmercuri"/>

# Esempi contestuali delle procedure consigliate per l'implementazione di modelli

In questo argomento vengono forniti 7 esempi contestuali sull’implementazione dei modelli di Gestione risorse di Azure. Per una panoramica dei principi illustrati in questi esempi, vedere [Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure](best-practices-resource-manager-design-templates.md).

Questo argomento fa parte di un white paper di dimensioni maggiori. Per leggere il documento completo, scaricare [World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Spostamento di un modello con ambito di funzionalità in un modello con ambito di soluzione end-to-end

Lo schema per lo sviluppo di un modello con ambito di funzionalità è stato condiviso in precedenza. Ci si potrebbe chiedere se esistono considerazioni da osservare quando si utilizza questo modello con ambito di funzionalità da solo o come parte di un modello di soluzione con ambito end-to-end.

Ad esempio, se si dispone di un modello tecnologico mirato che distribuisce SQL Server come funzionalità, quali sarebbero le considerazioni, se presenti, sull'utilizzo del modello in modo indipendente o come parte di un modello con ambito di soluzione end-to-end più ampio che può utilizzare SQL Server per supportare un'applicazione Web.

Quando si esamina questo scenario, è importante esaminare il numero di risorse probabilmente coinvolte. Per un'implementazione affidabile, il modello con ambito di funzionalità non è solo un account di archiviazione e una singola macchina virtuale con un'installazione di SQL Server. Un modello con ambito di funzionalità affidabile distribuisce più macchine virtuali se SQL Server è distribuito per la disponibilità elevata. Per alcune funzionalità, ad esempio Analysis Services, la topologia probabilmente distribuisce anche Active Directory.

Due aspetti importanti di questo scenario includono il ciclo di vita della modalità di utilizzo di SQL Server e del controllo degli accessi in base al ruolo (RBAC) da applicare. In particolare, SQL Server viene aggiornato ed eliminato con il resto della soluzione o il ciclo di vita differisce dalla soluzione o da altre parti della soluzione. Se il ciclo di vita differisce, è possibile considerare il posizionamento in un altro gruppo di risorse.

Un'altra considerazione riguarda la modalità di applicazione del controllo degli accessi in base al ruolo al modello di soluzione con ambito di funzionalità di SQL Server. In base a come si desidera applicare RBAC all'interno della topologia, è possibile scegliere diversi gruppi di risorse in base per allineare con tali specifiche. È possibile applicare il controllo RBAC a livello di risorse, ma dato il numero di risorse per il modello di soluzione con ambito di funzionalità di SQL Server, un gruppo di risorse distinto con il controllo RBAC applicato è un fattore da considerare.

Un'altra considerazione riguarda una valutazione del modello di soluzione con ambito di funzionalità di SQL Server per identificare se attualmente crea determinate risorse da se o consentendo all’utente di "portare le proprie risorse". In un modello di tipo "Bring Your Own Resources" (BYOR), il modello di soluzione con ambito di funzionalità consentirebbe al modello di riutilizzare le risorse esistenti con esempi tipici da un account di archiviazione, una rete virtuale o un set di disponibilità. Se nel modello con ambito di funzionalità non esiste un approccio BYOR, è possibile modificarlo utilizzando l'approccio definito in precedenza in questo documento per i modelli di risorse facoltative. In questo caso, il modello con ambito di soluzione end-to-end sarebbe un modello di risorsa condivisa con queste risorse comuni e il modello con ambito di funzionalità verrebbe esteso per supportare queste risorse come facoltative. In tal modo viene creato un modello di soluzione con ambito di funzionalità migliore in quanto ora può essere utilizzato in modo indipendente o come parte di una composizione.

Nel valutare se l'account di archiviazione deve essere passato dal modello con ambito di soluzione end-to-end, anche il controllo RBAC deve essere nuovamente valutato. In particolare, è necessario assicurarsi che il controllo RBAC venga applicato a questa risorsa specifica. Se, pertanto, si prevede che la risorsa abbia il controllo applicato quando viene passata, un livello di attendibilità viene inserito non solo nel blocco di soluzione, ma in qualsiasi utente che desidera facoltativamente fornire questa risorsa al modello di ambito funzionalità quando utilizzata in modo indipendente. Se il controllo RBAC è critico, è necessario valutare se creare questo modello facoltativo nel modello di soluzione con ambito di funzionalità o richiederne la creazione con il necessario controllo RBAC dal modello di soluzione con ambito di funzionalità.

Se viene presa la decisione di inserire in gruppi di risorse diversi, è possibile utilizzare anche i collegamenti alle risorse per definire le relazioni tra le risorse, anche quando le risorse sono distribuite tra i gruppi di risorse.

## Creazione di un modello con ambito di soluzione end-to-end con più modelli con ambito di funzionalità

Si tratta fondamentalmente di un superset dell'esempio precedente. In questo scenario, un'organizzazione dispone di più modelli di soluzioni con ambito di funzionalità per un set di tecnologie di dati, ad esempio Kafka, Apache Hadoop, Apache Spark e Apache Storm, che desidera raccogliere in un unico blocco di soluzione. La composizione risultante utilizzerà i modelli di soluzione con ambito di funzionalità nonché un'archiviazione e una rete virtuale condivisa con assegnazioni di subnet specifiche.

All’esterno degli specifici modelli con ambito di funzionalità richiesti, saranno necessarie risorse aggiuntive per la soluzione, anche solo gli script per unire i modelli con ambito di funzionalità insieme e configurarli.

In questo caso, viene identificata una rete virtuale condivisa e un account di archiviazione condivisa. Di conseguenza, è necessario aggiungerli a un modello di risorse condivise nel modello con ambito di soluzione end-to-end e assicurarsi che un approccio di tipo "Bring Your Own Resources" sia supportato nei modelli con ambito di funzionalità. Se non lo è, è possibile modificare di conseguenza i modelli con ambito di funzionalità, come descritto nell'esempio precedente.

Per le altre risorse che verranno aggiunte, si seguirà un superset del modello utilizzato per la creazione di un singolo modello con ambito di funzionalità. In questo caso, si aggiungerà un modello di risorse condivise, uno o più modelli di risorse facoltative, uno o più modelli di nodo membro e la configurazione dello stato desiderata (script, Chef, Puppet, Powershell DSC) per le nuove risorse. Se sono presenti dipendenze, saranno ottimizzate per l'utilizzo di riferimenti impliciti rispetto a dependsOn dove è possibile eliminare il rischio potenziale di dipendenze errate che possono influenzare il parallelismo (e la velocità) della distribuzione. Si esaminerà anche il ciclo di vita di queste risorse, le considerazioni sul controllo RBAC e le dipendenze per determinare se devono essere posizionate in diversi gruppi di risorse.

Durante l'aggiunta di risorse condivise, ad esempio l'account di archiviazione condivisa, è necessario valutare anche se è necessario un blocco di risorsa, in quanto può evitare eliminazioni accidentali.

Quando si aggiungono nuove risorse, è necessario esaminare anche se una delle risorse aggiunta al modello con ambito di soluzione end-to-end potrebbe venire isolata, come i modelli con ambito di funzionalità stessi. In tal caso, si consiglia di promuovere ulteriormente la scomposizione che può fornire vantaggi per il riuso e il testing.

Quando si integrano blocchi di soluzione, le considerazioni successive consistono, come indicato nell'esempio precedente, nell’identificare se il ciclo di vita per i singoli modelli di soluzione con ambito di funzionalità è diverso da quello della soluzione più ampia e se richiedere eventuali requisiti RBAC per la separazione in gruppi di risorse separati.

Infine, è opportuno considerare se si desidera essere in grado di definire ed eseguire query di collegamenti tra le risorse. In questo caso, l’utilizzo di collegamenti a risorse consente di eseguire questa operazione in un modello di soluzione con ambito end-to-end, anche quando si esegue lo spanning di più gruppi di risorse.

## Creazione di un modello con ambito di soluzione end-to-end con modello parziale di attivazione/disattivazione

Questo scenario è una variante di quello precedente. In questo caso, il cliente estrae dati da un sistema locale a intervalli fissi nel corso di una giornata. È disponibile una pipeline di dati per l'elaborazione dei dati in ingresso e un archivio dati relazionali in cui i dati sono sempre disponibili per le query. Il cloud è un modello con pagamento in base al consumo e quindi il cliente desidera la pipeline di dati operativa solo durante tali intervalli, quando i dati vengono presentati per l'elaborazione.

Come parte della pipeline di dati, è disponibile un SQL Server che riceve i dati elaborati e li rende disponibili per le query. Il cliente ha indicato che anche se desidera attivare e disattivare l'operazione di inserimento e l'elaborazione dei componenti della pipeline secondo una pianificazione fissa, desidera disporre sempre di SQL Server.

In questo scenario, esistono differenze esplicite nel ciclo di vita e potenzialmente alcune considerazioni aggiuntive che il cliente non ha sollevato, ma devono essere valutate.

Come descritto, la distribuzione di SQL Server verrà mantenuta attiva mentre le altre risorse verranno create ed eliminate. Verranno distribuite inizialmente insieme, ma quindi verranno eliminate e create in un ciclo di vita diverso da altri membri del modello. Queste possono essere isolate in diversi gruppi di risorse o rimanere nello stesso gruppo di risorse con il blocco applicato alle risorse di SQL Server. Dal momento che SQL Server è in modo specifico, come descritto negli esempi precedenti, probabilmente rappresentato come un set più ampio di risorse, sarebbe appropriato separarlo nel proprio gruppo di risorse.

L’altra considerazione è che mentre il cliente desidera che il resto della pipeline di dati venga attivato e disattivato in base a una pianificazione, potrebbe non prendere in considerazione il comportamento incongruente dei sistemi di segnalazione. Il recapito pianificato dei dati di terze parti non è sempre preciso, la connettività potrebbe non essere disponibile per un periodo di tempo, gli orologi dei server locali o basati sul cloud potrebbero non essere sincronizzati, le modifiche di orario potrebbero o meno verificarsi come previsto e così via. Deve essere valutato se il meccanismo di inserimento deve essere utilizzato anche in un modello di attivazione/disattivazione e in tal caso, se il relativo ciclo di vita è maggiore di quello dei componenti di elaborazione.

Se si utilizza un servizio gestito, ad esempio Data Factory di Azure o Hub eventi, non si tratta di un problema in quanto i modelli operativi e l’approccio di fatturazione associato li rende disponibili per l’inserimento e il posizionamento dei dati nel servizio di archiviazione. Se si utilizza un'altra tecnologia, ad esempio Kafka, distribuita in una macchina virtuale, si potrebbe esaminare il ciclo di vita per stabilire come rendere disponibile le risorse e gli account di archiviazione associati necessari per l'inserimento. Questo può comportare che le risorse di inserimento e di elaborazione vengano posizionate in gruppi di risorse diversi in base del ciclo di vita.

## Supporto di ambienti distinti all'interno di una sottoscrizione

Per recapitare in modo efficace i servizi, molte organizzazioni hanno una serie di esigenze di scala, isolamento di fatturazione, isolamento di responsabilità e isolamento geografico che deve essere soddisfatto. Durante la progettazione dei servizi per Azure, si sarebbe utilizzato cronologicamente il partizionamento della sottoscrizione nell'approccio per soddisfare queste esigenze.

Gestione delle risorse riduce i vincoli per il numero di risorse di un determinato tipo che può essere distribuito all'interno di una sottoscrizione e introduce anche i gruppi di risorse, RBAC e il controllo. La combinazione di questi può consentire alle organizzazioni di utilizzare gruppi di risorse per il partizionamento, consentendo loro di soddisfare le proprie esigenze e ridurre la quantità, se presente, del partizionamento della sottoscrizione che potrebbe essere necessario eseguire.

In questa sezione vengono esaminati i requisiti di questi tipi di ambienti e vengono fornite le indicazioni su come distribuire gli ambienti che li soddisfano con ARM.

### Considerazioni di isolamento

In questa sezione vengono illustrati i driver comuni dei clienti per l'isolamento di ambiente, fatturazione e geografico in modo più dettagliato.

#### Isolamento di ambiente

I proprietari del servizio desiderano isolare i diversi ambienti. Ogni ambiente isolato consente ai team di controllare con maggiore precisione chi può avere accesso agli ambienti. Se gli ambienti di sviluppo possono risultare più aperti in termini di chi può accedervi, come l'ambito dell’ambiente si avvicina alla produzione, il numero di utenti, siano essi risorse umane o account di sistema utilizzati per l'automazione, si riduce per facilitare la conformità e contenere al massimo il rischio complessivo.

#### Isolamento di fatturazione: sviluppo ed esecuzione di un servizio

Per riflettere accuratamente il costo del venduto (COGS) e i costi operativi (OpEx), i proprietari dell’azienda desiderano essere in grado di suddividere i costi di ricerca e creazione del servizio dai costi di esecuzione dei servizi.

Come superset dell’isolamento di ambiente menzionato in precedenza, l'intento sarebbe il consolidamento di sviluppo e test per la fatturazione individuale e/o aggregata per il primo mentre la produzione rimarrebbe indipendente per l'ultimo.

#### Isolamento di fatturazione: aggiunta di trasparenza e responsabilità ai costi di utilizzo del servizio

L’isolamento di fatturazione è inoltre utilizzato per ottenere trasparenza nei costi relativi all’utilizzo della piattaforma da team specifici e introdurre livelli appropriati di responsabilità.

Mentre il cloud è flessibile e consente un modello di pagamento in base al consumo, questo è meno familiare per alcuni sviluppatori provenienti da un modello non cloud in cui l’hardware è acquistato e di proprietà. Nel modello non cloud erano presenti limiti fisici in termini di numero di "computer" attivabili e incentivi limitati per ridurre o disattivare le risorse quando non in uso. L’acquisizione dell’hardware dedicato, in molti casi, non viene effettuato dagli sviluppatori che lo utilizzano.

Isolando le sottoscrizioni e assegnando la responsabilità delle sottoscrizioni a team specifici, i proprietari del servizio ritengono questo tipo di partizionamento della sottoscrizione vantaggioso nella realizzazione e applicazione dei comportamenti desiderati.

#### Isolamento geografico: distribuzioni specifiche e disciplinate dalle leggi di una specifica area geografica

In determinati contesti, sono presenti requisiti per cui i servizi destinati per una specifica area geografica devono prendere in considerazione la modalità di distribuzione per soddisfare specifiche esigenze di conformità.

Mentre un servizio può essere globale in natura, le distribuzioni che si trovano all'interno o forniscono il servizio a determinate aree geografiche possono essere regolate da requisiti operativi del personale. In particolare, stabilire che solo gli utenti che sono cittadini di un paese o un set di paesi specifico e/o passano determinati processi di screening in background possono utilizzare tali servizi.

L’isolamento geografico offre inoltre vantaggi in termini di nuove funzionalità e servizi della piattaforma. Alcune aree geografiche, ad esempio la Cina, potrebbero avere solo un sottoinsieme dei servizi di piattaforma disponibili e/o ritardare la distribuzione dei servizi della piattaforma.

L’isolamento geografico consente ai team di sviluppare i servizi per sfruttare funzionalità e servizi di piattaforma nuovi o migliorati laddove disponibili.

### Considerazioni sulla conformità

I servizi possono essere recapitati in più aree geografiche e più verticali. Questi destinatari hanno spesso dati riservati o processi contenuti all'interno delle applicazioni ed esistono normative di conformità associate progettate per proteggerli e controllarli.

#### Separazione di ruoli e compiti

La separazione di ruoli e compiti è un requisito essenziale per la conformità dei servizi interni ai criteri interni. Anche molti servizi commerciali richiedono la separazione per rimanere in conformità con le normative del settore e governative. È necessario limitare l'accesso ai servizi e alle relative risorse sottostanti ai ruoli autorizzati e in determinate circostanze. Molti servizi hanno creato lo scaffolding per offrire due funzionalità: RBAC e controllo.

#### Casi di utilizzo del controllo degli accessi in base al ruolo (RBAC)

Negli scenari di conformità, è importante limitare l'accesso a determinate risorse.

Ad esempio, quando si esaminano i dati riservati in più scenari in cui la conformità è rilevante (informazioni di integrità, dati finanziari, imposte, record, ecc.), è importante limitare il numero di persone che possono accedere, visualizzare o modificare i dati solo a quelli che richiedono l'accesso per svolgere le attività commerciali dell'organizzazione padre.

RBAC fornisce un singolo utente, sistema o gruppo con accesso a risorse specifiche in condizioni identificate.

#### Controllo

Oltre all'accesso vincolato fornito da RBAC, le organizzazioni devono inoltre controllare l'accesso alle risorse e l’interazione con le risorse.

### Implementazione con Gestione risorse di Azure

In precedenza, le organizzazioni avrebbero utilizzato il partizionamento della sottoscrizione per raggiungere questi obiettivi. Sebbene possibile, non è l’ideale. Poiché la creazione di una sottoscrizione è in effetti un'attività commerciale, l'API di gestione del servizio non espone un meccanismo secondo cui creare o eliminare automaticamente le nuove sottoscrizioni e le sottoscrizioni create manualmente. Il numero di sottoscrizioni risultante può crescere significativamente. Per grandi servizi quali i servizi commerciali di Microsoft, questo numero può estendersi in oltre migliaia di sottoscrizioni. Spesso comporta la creazione dello scaffolding personalizzato per creare e gestire le sottoscrizioni di un'organizzazione.

Con Gestione risorse, la distribuzione di più ambienti all'interno di una sottoscrizione è molto più semplice. Riduce i delimitatori fissi precedenti delle risorse nel modello precedente, che riduce notevolmente la necessità di partizione a causa dei vincoli delle risorse.

Gli ambienti possono essere posizionati in gruppi di risorse che possono avere un controllo specifico RBAC applicato, consentendo di fornire l’isolamento dell'ambiente. In scenari in cui è richiesto l'isolamento geografico, inoltre è possibile utilizzare gruppi di risorse. Poiché i gruppi di risorse possono estendersi su più aree geografiche, è possibile ottenere un isolamento specifico per una o più aree geografiche.

È possibile applicare i tag alle risorse e ai gruppi di risorse che possono essere utilizzati nei rollup di fatturazione e nelle visualizzazioni riepilogative per fornire l'isolamento di fatturazione. È possibile utilizzare i tag per definire il tipo di ambiente (ricerca, formazione, sviluppo, test, produzione), l'organizzazione responsabile o la persona ("HR", "Finance", "John Smith", "Jane Jones").

Il requisito di controllo viene fornito come parte del set di gestione di risorse di Azure sottostante delle funzionalità predefinite e può essere visualizzato in una posizione centrale.

I clienti finali avrebbero gli account registrati in Azure Active Directory che verrebbe utilizzato per l'autenticazione e il controllo degli accessi in base al ruolo per l'ambiente e le risorse.

#### Ottimizzazione per la densità

Sebbene i limiti delle risorse sono flessibili in Gestione risorse di Azure, comunque saranno presenti dei limiti. Oltre a creare gli ambienti stessi, è necessario anche riuscire a raggiungere la densità degli ambienti all'interno delle sottoscrizioni. La fornitura di un ambiente offre capacità a una persona o un’organizzazione ed è necessario valutare quali "dimensioni della maglietta" si intendono distribuire. In particolare, identificare le varianti tra clienti di dimensioni piccole, medie, grandi e molto grandi in termini di risorse necessarie.

È possibile utilizzare diverse sottoscrizioni per diverse dimensioni di maglietta per ottenere una densità maggiore. Ad esempio, si potrebbe essere in grado di ospitare 1000 ambienti di dimensioni di maglietta piccole, 500 distribuzioni di dimensioni medie, 100 distribuzioni di dimensioni grandi e 10 distribuzioni di dimensioni molto grandi in una specifica sottoscrizione. Poiché non esiste alcun costo fatturato nell’avere più sottoscrizioni, è possibile voler isolare le diverse dimensioni in sottoscrizioni differenti per fornire la massima densità. Ciò è possibile, mantenendo il numero di sottoscrizioni relativamente contenuto e facile da gestire.

Un fattore fondamentale consiste nell'individuare se consentire al cliente di aumentare o modificare le dimensioni delle magliette e, in tal caso, come si desidera eseguire l’operazione.

Un approccio consiste nel consentire a un cliente di acquisire capacità aggiuntiva all'interno del gruppo di risorse esistente. Questo può essere facilmente applicato tecnicamente, ma ha implicazioni sulla densità. Invece di dimensioni definite nettamente per tutti i clienti, si introduce un livello di variabilità che aggiunge un overhead maggiore per l'ottimizzazione della densità. Se ogni ambiente di piccole dimensioni è una dimensione X, è facilmente possibile calcolare preventivamente come molti ambienti di piccole dimensioni si inseriscono in una sottoscrizione per la densità ottimale. Quando si consente ai clienti di personalizzare l'ambiente, il risultato è un numero imprevisto di varianti e le quantità di ambienti potrebbero essere X, X+1, X+2 e così via. Con questo livello di variabilità, si ottiene meno densità rispetto a quanto necessario per riservare capacità all'interno di una sottoscrizione per riflettere tali variazioni.

Sebbene possibile, questa soluzione non è decisamente idonea come approccio generale in quanto ottiene meno densità e richiede un overhead maggiore da gestire. Per gli ambienti di grandi dimensioni, potrebbe essere un'opzione più valida. Poiché un numero inferiore di questi ambienti di dimensioni grandi e molto grandi potrebbe essere collocato in una sottoscrizione, è possibile scegliere di inserire un numero inferiore di essi in una sottoscrizione per consentire l'aumento.

Un altro approccio consiste nell’eliminare l'ambiente di dimensione corrente dei clienti e creare un nuovo ambiente di dimensioni diverse. Mentre non è appropriato per alcuni scenari, questo approccio è l’ideale per ambienti che vengono utilizzati temporaneamente, ad esempio ambienti di sviluppo e test.

L'approccio più semplice successivo consiste ne fornire al cliente la possibilità di acquisire un ambiente di dimensioni maggiore e quindi di gestire la migrazione all’ambiente in modo indipendente. Ad esempio, un cliente che ha una distribuzione di SQL Server in un ambiente di piccole dimensioni può acquistare un ambiente di medie dimensioni e sarebbe individualmente responsabile per il trasferimento dei dati e dello stato personalizzato.

Un approccio alternativo consiste nel fornire un servizio gestito dove è agevolata la transizione da una dimensione a un’altra. Questo è ovviamente più complicato, ma in base al carico di lavoro e al cliente, potrebbe trattarsi di un approccio utile per l'organizzazione.

## Distribuzione di ambienti con vincoli di criteri aggiuntivi per il cliente

Alcune organizzazioni hanno requisiti e criteri aggiuntivi per gli ambienti che vengono distribuiti. In particolare, hanno criteri che limitano le porte esposte esternamente e possono avere criteri che richiedono il monitoraggio del traffico in entrata e in uscita per l'ambiente. Per motivi di costi e supporto, possono esistere anche vincoli sulle risorse che un cliente finale può creare, aggiornare o eliminare. Per l'organizzazione che fornisce l’ambiente, sarà inoltre necessario in genere richiedere l'accesso alla sottoscrizione per il supporto.

Come superset dello scenario precedente, qui si richiede l'aggiunta di alcune risorse con ulteriori vincoli su chi può o meno creare risorse di un determinato tipo.

La possibilità per un utente di creare, aggiornare o eliminare determinate risorse può essere vincolata tramite il controllo degli accessi in base al ruolo. Alcuni esempi comprendono un'organizzazione che richiede una determinata rete VNET e potenzialmente le subnet che il cliente finale non può aggiornare o eliminare.

I blocchi di risorse possono essere implementati per stabilire che le risorse sono di sola lettura o non possono essere eliminate. RBAC può essere utilizzato per consentire agli utenti o alle entità servizio di eseguire determinate attività rispetto a una risorsa o un gruppo di risorse.

Se l'organizzazione richiede che quel determinato traffico, ad esempio il traffico tra i livelli dell'applicazione, passa innanzitutto attraverso un intermediario, ad esempio un dispositivo di rete virtuale, è opportuno utilizzare route definite dall'utente.

Un dispositivo virtuale non è altro che una macchina virtuale che esegue un'applicazione utilizzata per gestire il traffico di rete in qualche modo, ad esempio un firewall o un dispositivo NAT. Un numero di terze parti fornisce accessori di rete virtuale in Azure e le organizzazioni possono inoltre trasferirne di propri.

Un approccio di tipo "Bring Your Own" per il dispositivo consente di riutilizzare il codice esistente che è possibile venga utilizzato negli ambienti locali. Questo dispositivo virtuale macchina virtuale deve essere in grado di ricevere traffico in ingresso non viene indirizzato a se stesso. Per consentire una macchina virtuale ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare l'inoltro IP nella macchina virtuale.

Come con gli esempi precedenti, i vincoli del ciclo di vita delle risorse e RBAC devono essere rivisti e considerati come parte della strategia del gruppo di risorse.

## Protezione delle risorse da cattivi attori interni

Un problema per un'organizzazione potrebbe essere la protezione delle risorse e dei modelli che vengono sottoposti al provisioning da cattivi attori.

Un esempio potrebbe essere una banca che vuole verificare che uno sviluppatore di software inaffidabile o un membro del personale IT non apportino modifiche o estraggano informazioni chiave che causerebbero l’invio dei dati a un cattivo attore con finalità penali.

In uno scenario aziendale tipico si dispone di un piccolo gruppo di operatori affidabili che hanno accesso a segreti critici all'interno dei carichi di lavoro distribuiti, con un gruppo più ampio del personale di sviluppo e degli operatori che può creare o aggiornare le distribuzioni VM.

L’insieme di credenziali di chiave di Azure viene utilizzato con ARM per orchestrare e archiviare i certificati e i segreti di macchina virtuale.

Si consiglia di mantenere separati i modelli ARM per la creazione di insiemi di credenziali (che conterranno materiale importante) e la distribuzione di macchine virtuali (con i riferimenti dell’URI alle chiavi contenute negli insiemi di credenziali).

I segreti memorizzati nell'insieme di credenziali chiave sono sotto il controllo RBAC completo di un operatore attendibile. Se l'operatore attendibile lascia la società o si trasferisce in un nuovo gruppo della società, non potrà più accedere alle chiavi che ha creato nell'insieme di credenziali.

I modelli ARM per la distribuzione contengono solo i riferimenti dell’URI ai segreti e pertanto i segreti effettivi non sono inclusi nei repository di codice sorgente, configurazione o codice. In tal modo si riducono le opportunità per il phishing dei segreti e si limita la possibilità per i cattivi attori di apportare modifiche.

Come affermato in precedenza nel documento, non esistono insiemi di credenziali di chiave globali. Dal momento che gli insiemi di credenziali di chiave sono sempre locali, i segreti hanno sempre la località (e la sovranità) con le macchine virtuali.

Un esempio di implementazione di questo approccio viene fornito nella sezione Segreti e certificati disponibile in precedenza in questo documento.

## Abilitazione di un modello "Bring Your Own Subscription"

Il personale IT aziendale, gli integratori di sistema e i fornitori di servizi cloud possono utilizzare un modello "Bring Your Own Subscription" con i clienti. In particolare, l'organizzazione fornisce un servizio a un cliente finale e utilizza in qualche modo la sottoscrizione di Azure del cliente.

Esistono più varianti di questo approccio, ciascuno con requisiti leggermente diversi, come descritto di seguito.

### Abilitazione dell'accesso di terze parti per il monitoraggio delle risorse all'interno di un account

Un'organizzazione con un'applicazione di monitoraggio potrebbe richiedere l'accesso di sola lettura alla sottoscrizione di un cliente per recuperare dati da utilizzare in tale applicazione. Ciò richiede l’accesso in sola lettura per un periodo di tempo. L'accesso deve essere nel controllo del cliente, fornendo la possibilità di terminare l'accesso se la relazione con il provider del servizio di monitoraggio viene interrotta.

#### Implementazione con Gestione risorse di Azure

Le informazioni dettagliate sull'implementazione vengono fornite dettagliatamente nella "Guida per gli sviluppatori ad Auth con l'API di Gestione risorse di Azure" disponibile in [qui](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/). Questo documento fornisce le istruzioni dettagliate per l’implementazione e il codice di esempio.

### Abilitazione dell'accesso di terze parti per singola distribuzione del software

In un altro esempio, un'organizzazione può distribuire e configurare una versione del software in un account del cliente, richiedendo l'accesso in scrittura per il periodo di tempo della distribuzione.

#### Implementazione con Gestione risorse di Azure

Si segue un approccio simile all'esempio precedente.

A seconda delle specifiche esigenze di installazione, il ruolo specifico assegnato all'entità servizio deve consentire solo il livello minimo di accesso necessario per ottenere l'installazione e fare in modo che l'accesso venga revocato immediatamente dopo il completamento dell'installazione.

### Abilitazione dell'accesso di terze parti per utilizzare le sottoscrizioni del cliente per l'archiviazione dei dati

In un altro esempio, un'organizzazione potrebbe voler eseguire il software nel proprio ambiente, ma utilizzare l'account del cliente per l'archiviazione. In questo modo il cliente viene inserito nel controllo dei dati in qualsiasi momento e può utilizzare altre tecnologie nella piattaforma, ad esempio Azure Machine Learning o HDInsight, a propria discrezione senza l'aggiunta di overhead di costo/fatturazione per l'organizzazione IT, l’integratore di sistemi o il CSV che offre la funzionalità. Ciò richiede l'accesso costante all'account di archiviazione per l'organizzazione, con il cliente nel controllo e l’accesso alle informazioni di controllo per gli accessi a tali informazioni.

#### Implementazione con Gestione risorse di Azure

Questa implementazione avviene utilizzando lo stesso modello degli altri esempi. A un'entità servizio viene fornito l'accesso alla risorsa di archiviazione. Poiché in questo scenario è richiesto il ruolo per l’accesso in lettura e scrittura all'account di archiviazione, il ruolo predefinito di collaboratore viene assegnato all'entità servizio per ottenere questo livello di accesso.

In questo scenario è coinvolta una prima e una terza parte con un account di archiviazione condiviso e pertanto si vuole assicurare che l'account di archiviazione non venga eliminato accidentalmente. Per questo aspetto dello scenario, viene applicato un blocco di risorsa all'account di archiviazione.

### Abilitazione della gestione dei servizi da terze parti

In un altro esempio, un'organizzazione desidera distribuire, monitorare e gestire il software nella sottoscrizione dei clienti. Potrebbero esserci vincoli per il cliente in termini di modifiche che possono (o in modo più esplicito non possono) apportare a un ambiente in cui il software viene distribuito.

#### Implementazione con Gestione risorse di Azure

Si segue un superset del modello all'inizio di questa sezione. In particolare, a un'entità di servizio utilizzata da terze parti viene fornito l'accesso completo alle risorse del gruppo di risorse.

Inoltre, poiché sono presenti vincoli per il cliente, agli utenti o ai gruppi del cliente vengono concessi i diritti appropriati per utilizzare l'ambiente. A tale scopo è possibile utilizzare i modelli, come indicato in precedenza in questa sezione.

Infine, si potrebbe voler garantire che determinate risorse non vengano eliminate accidentalmente. Se questo è il caso, si potrebbero considerare i blocchi di risorse anche per le risorse che richiedono tale protezione.

## Passaggi successivi

- Per ulteriori informazioni sulla creazione di modelli, vedere [Creazione di modelli](resource-group-authoring-templates.md).
- Per consigli su come gestire la sicurezza in Gestione risorse di Azure, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md).
- Per ulteriori informazioni sulla condivisione dello stato all’interno e all'esterno dei modelli, vedere [Condivisione dello stato nei modelli di Gestione risorse di Azure](best-practices-resource-manager-state.md)

<!---HONumber=August15_HO8-->