<properties
	pageTitle="Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure"
	description="Visualizzazione dei modelli di progettazione per i modelli di Gestione risorse di Azure"
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
	ms.date="07/15/2015"
	ms.author="mmercuri"/>

# Procedure consigliate per la progettazione di modelli di Gestione risorse di Azure

Nel nostro lavoro con aziende, integratori di sistemi (SI), fornitori di servizi cloud (CSV) e team di progetto per software open source (OSS), è spesso necessario distribuire rapidamente ambienti, carichi di lavoro o unità di scala. Queste distribuzioni devono essere supportate, seguire procedure consolidate ed essere conformi ai criteri identificati. Adottando un approccio flessibile basato su modelli di Gestione risorse di Azure, è possibile distribuire topologie complesse in modo rapido e coerente e adattare facilmente queste distribuzioni all’evolvere delle offerte di core oppure adattare varianti per scenari di outlier o clienti.

I modelli uniscono i vantaggi della Gestione risorse di Azure sottostante con l’adattabilità e la leggibilità di JavaScript Object Notation (JSON). Utilizzando i modelli, è possibile:

- Distribuire in modo coerente le topologie e i relativi carichi di lavoro.
- Gestire tutte le risorse di un’applicazione contemporaneamente utilizzando gruppi di risorse.
- Applicare il controllo dell'accesso basato sui ruoli (RBAC) per concedere l'accesso appropriato a utenti, gruppi e servizi.
- Utilizzare le associazioni di assegnazione di tag per semplificare attività come i rollup di fatturazione.

In questo articolo vengono fornite informazioni dettagliate su scenari di utilizzo, architettura e modelli di implementazione identificati durante le sessioni di progettazione e implementazioni reali dei modelli con i clienti di Azure Customer Advisory Team (AzureCAT). Lungi dall'essere accademiche, queste sono procedure consolidate derivanti dallo sviluppo di modelli per 12 delle principali tecnologie OSS basate su Linux, tra cui: Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, DataStax Enterprise basato su Apache Cassandra, Elasticsearch, Jenkins, MongoDB, Nagios, PostgreSQL, Redis e Nagios. La maggior parte di questi modelli è stata sviluppata con un noto fornitore di una specifica distribuzione e influenzata dai requisiti di clienti aziendali e SI di Microsoft durante progetti recenti.

Nel presente articolo vengono proposte queste procedure consolidate allo scopo di agevolare la progettazione di modelli di Gestione risorse di Azure di elevata qualità.

Nel nostro lavoro con i clienti, abbiamo identificato una serie di esperienze di utilizzo dei modelli di gestione delle risorse tra aziende, integratori di sistemi (SI) e CSV. Le sezioni seguenti forniscono una panoramica di alto livello di scenari e modelli comuni per diversi tipi di clienti.

## Aziende e integratori di sistemi

All'interno delle organizzazioni di grandi dimensioni, si osservano comunemente due consumer di modelli ARM: team di sviluppo di software interno e IT aziendale. Gli scenari relativi agli SI con cui abbiamo lavorato sono mappati a quelli delle imprese, quindi si applicano le stesse considerazioni.

### Team di sviluppo di software interno

Se il team sviluppa software per supportare l’azienda, i modelli forniscono un modo semplice per distribuire rapidamente le tecnologie da utilizzare in soluzioni specifiche per l’azienda. I modelli possono inoltre essere utilizzati per creare rapidamente ambienti di formazione che consentano ai membri del team di acquisire le competenze necessarie.

È possibile utilizzare modelli così come sono oppure estenderli o comporli per adeguarli alle proprie esigenze. Utilizzando l’associazione di tag all’interno dei modelli, è possibile fornire un riepilogo di fatturazione con varie viste quali team, progetto, utente e formazione.

Le aziende spesso desiderano team di sviluppo software per creare un modello per la distribuzione coerente di una soluzione offrendo al contempo vincoli per mantenere fissi alcuni elementi all'interno di tale ambiente e impedire che possano essere sottoposti a override. Ad esempio una banca potrebbe richiedere un modello per includere lo RBAC al fine di evitare che un programmatore possa modificare una soluzione di banking per inviare dati a un account di archiviazione personale.

### IT aziendale

Le organizzazioni IT aziendali in genere utilizzano modelli per la distribuzione di capacità cloud e funzionalità ospitate su cloud.

#### Capacità cloud

Un metodo comune adottato dai gruppi IT aziendali per fornire capacità cloud ai team all'interno dell'organizzazione sono le "taglie", ovvero dimensioni di offerta standard quali small, medium e large. Le offerte basate sulle taglie possono combinare tipi e quantità differenti di risorse fornendo al tempo stesso un livello di standardizzazione che consente di utilizzare i modelli. I modelli offrono capacità in modo coerente permettendo l’applicazione dei criteri aziendali e l’utilizzo dell’associazione di tag per fornire funzionalità di chargeback alle organizzazioni che ne fanno uso.

Ad esempio, potrebbe essere necessario fornire ambienti di sviluppo, test o produzione all'interno dei quali i team di sviluppo software possono distribuire le relative soluzioni. L'ambiente dispone di una topologia di rete predefinita e di elementi non modificabili dai team di sviluppo software, quali le regole che controllano l'accesso alla rete Internet pubblica e l’ispezione dei pacchetti. Per tali ambienti potrebbero anche esistere regole specifiche dell’organizzazione con diritti di accesso distinti per l’ambiente.

#### Funzionalità ospitate nel cloud

È possibile utilizzare modelli per supportare funzionalità ospitate nel cloud, tra cui singoli pacchetti software oppure offerte combinate disponibili per le linee interne di business. Un esempio di offerta combinata è l’analytics-as-a-service, tecnologie di analisi, virtualizzazione e altre tecnologie, fornita in una configurazione ottimizzata e connessa in una topologia di rete predefinita.

Le funzionalità ospitate nel cloud sono influenzate da considerazioni in materia di sicurezza e ruoli, determinate dall’offerta di capacità cloud su cui sono basate come descritto in precedenza. Queste funzionalità sono disponibili così come sono oppure come servizio gestito. Per quest’ultimo, sono richiesti ruoli con limiti di accesso per abilitare l’accesso all’ambiente a scopo di gestione.

## Fornitori di servizi cloud

Dopo avere parlato con molti CSV, abbiamo identificato molteplici approcci adottabili per distribuire servizi per i clienti e i requisiti associati.

### Offerta ospitata su CSV

Quando l’offerta viene ospitata nella propria sottoscrizione Azure, sono comunemente adottati due approcci di hosting: distribuzione di una implementazione distinta per ogni cliente o distribuzione di unità di scala che supportano un'infrastruttura condivisa utilizzata per tutti i clienti.

- **Distribuzioni distinte per ogni cliente.** Le distribuzioni distinte per ogni cliente richiedono topologie fisse di configurazioni note differenti. Possono contenere macchine virtuali (VM) di dimensioni differenti, un numero variabile di nodi e volumi diversi di memoria associata. L’associazione di tag alle distribuzioni è utilizzata per i roll-up di fatturazione per ogni cliente. Lo RBAC può essere abilitato per consentire ai clienti di accedere agli aspetti dell’ambiente cloud.
- **Unità di scala in ambienti multi-tenant condivisi.** Un modello può rappresentare un'unità di scala per ambienti multi-tenant. In questo caso, la stessa infrastruttura viene utilizzata per supportare tutti i clienti. Le distribuzioni rappresentano un gruppo di risorse che forniscono un livello di capacità per le offerte ospitate, ad esempio numero di utenti e numero di transazioni. Tali unità di scala sono aumentate o diminuite secondo necessità.

### Offerta di CSV inserita nella sottoscrizione del cliente

Potrebbe essere necessario distribuire il software in sottoscrizioni appartenenti a clienti finali. È possibile utilizzare modelli per distribuire distribuzioni distinte in un account Azure del cliente.

Queste distribuzioni utilizzano lo RBAC, pertanto è possibile aggiornare e gestire la distribuzione all'interno dell’account cliente.

### Azure Marketplace

Per pubblicizzare e vendere le proprie offerte tramite un marketplace, ad esempio Azure Marketplace, è possibile sviluppare modelli per la fornitura di tipi distinti di distribuzioni che verranno eseguiti nell'account Azure di un cliente. Queste distribuzioni distinte possono essere descritte in genere come taglia (small, medium, large), tipo di prodotto/pubblico (community, sviluppatore, azienda) o tipo di funzionalità (base, alta disponibilità). In alcuni casi, tali tipi consentiranno di specificare determinati attributi di distribuzione, ad esempio il tipo di VM o il numero di dischi.

## Progetti OSS

All'interno dei progetti open source, i modelli di Gestione delle risorse consentono a una community di distribuire rapidamente una soluzione, utilizzando procedure consolidate. I modelli possono essere archiviati in un archivio GitHub in modo che la community possa esaminarli nel tempo. Gli utenti finali possono quindi distribuire questi modelli nelle proprie sottoscrizioni Azure.

Nelle sezioni seguenti vengono identificati gli aspetti da considerare prima di progettare la soluzione.

## Identificare gli elementi esterni e interni di una VM

Quando si progetta il modello, è utile esaminare i requisiti in termini di elementi interni ed esterni delle macchine virtuali (VM):

- Per elementi esterni si intendono le VM e altre risorse della distribuzione, quali la topologia di rete, associazione di tag, riferimenti a certificati o chiavi private e controllo di accesso basato sui ruoli. Tutte queste risorse fanno parte del modello.
- Per elementi interni si intende il software installato e la configurazione dello stato globale desiderato. Altri meccanismi, ad esempio le estensioni di VM o gli script, vengono utilizzati in tutto o in parte. Questi meccanismi possono essere identificati ed eseguiti dal modello ma non al suo interno.

Esempi comuni di attività eseguite "inside the box" sono:

- Installare o rimuovere funzionalità e ruoli del server
- Installare e configurare il software a livello di nodo o cluster
- Distribuire siti Web in un server Web
- Distribuire schemi di database
- Gestire il registro di sistema o altri tipi di impostazioni di configurazione
- Gestire file e directory
- Avviare, arrestare e gestire processi e servizi
- Gestire account utente e gruppi locali
- Installare e gestire i pacchetti (file con estensione .msi, .exe, yum e così via)
- Gestire le variabili di ambiente
- Eseguire gli script nativi (Windows PowerShell, bash e così via)

### Configurazione dello stato desiderato (DSC)

Relativamente allo stato interno delle VM dopo la distribuzione, è opportuno assicurarsi che tale distribuzione non "devii" dalla configurazione definita e verificata nel controllo del codice sorgente. In questo modo gli sviluppatori o il personale addetto alle operazioni potranno apportare manualmente a un ambiente solo le modifiche ad hoc che sono state esaminate, sottoposte a test o registrate nel controllo del codice sorgente. Questo aspetto è importante, poiché le modifiche manuali non sono presenti nel controllo del codice sorgente, inoltre non fanno parte della distribuzione standard e avranno un impatto sulle future distribuzioni automatiche del software.

Oltre che dal punto di vista dei dipendenti interni, la configurazione dello stato desiderato è importante anche in termini di sicurezza. I pirati informatici tentano regolarmente di compromettere e sfruttare i sistemi software. Quando il tentativo riesce, lo scopo è in genere l’installazione di file o altrimenti la modifica dello stato di un sistema compromesso. Utilizzando la configurazione dello stato desiderato, è possibile identificare i delta tra lo stato desiderato e quello effettivo e ripristinare una configurazione nota.

Esistono estensioni di risorsa per i meccanismi più diffusi per DSC - PowerShell DSC, Chef e Puppet. Ognuna di queste può distribuire lo stato iniziale della VM ed essere utilizzata anche per assicurarsi che venga mantenuto lo stato desiderato.

## Ambiti dei modello comuni

Nella nostra esperienza, abbiamo visto emergere tre ambiti principali per i modelli di soluzioni. Questi tre ambiti, ovvero capacità, funzionalità e soluzione end-to-end, sono descritti in dettaglio più avanti.

### Ambito di capacità

Un ambito di capacità offre un set di risorse in una topologia standard preconfigurata per essere conforme a regolamenti e criteri. L'esempio più comune è la distribuzione di un ambiente di sviluppo standard in uno scenario di IT aziendale o SI.

### Ambito di funzionalità

Un ambito di funzionalità è incentrato sulla distribuzione e configurazione di una topologia per una determinata tecnologia. Scenari comuni includono tecnologie quali SQL Server, Cassandra, Hadoop e così via.

### Ambito di soluzione end-to-end

Un ambito di soluzione end-to-end non riguarda una singola funzionalità ma è invece incentrato sulla fornitura di una soluzione end-to-end costituita da più funzionalità.

Un ambito di modello con ambito di soluzione si manifesta come un set di uno o più modelli con ambito di funzionalità con risorse, logica e stato desiderato specifici della soluzione. Un esempio di modello con ambito di soluzione è un modello di soluzione della pipeline di dati end-to-end che potrebbe combinare la topologia e lo stato specifici della soluzione con più modelli di soluzioni con ambito di funzionalità come Kafka, Storm e Hadoop.

## Scelta del formato libero rispetto a configurazioni note

Inizialmente si potrebbe pensare che un modello debba fornire ai fruitori la massima flessibilità, ma sulla scelta di utilizzare configurazioni in formato libero o configurazioni note influiscono molteplici considerazioni. In questa sezione vengono identificati i principali requisiti dei clienti e le considerazioni tecniche che hanno plasmato l’approccio illustrato nel presente documento.

### Configurazioni in formato libero

In apparenza, le configurazioni in formato libero sembrano ideali. Consentono di selezionare un tipo di VM e specificare un numero arbitrario di nodi e dischi collegati per tali nodi, come parametri di un modello. Quando si esaminano con attenzione, però e si prendono in considerazione i modelli che distribuiranno più macchine virtuali di dimensioni diverse, appaiono evidenti altre considerazioni che rendono la scelta meno appropriata in una serie di scenari.

Nell'articolo [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn641267.aspx) presente sul sito Web di Azure, vengono identificati i diversi tipi di VM e le dimensioni disponibili oltre a ciascuna combinazione di dischi permanenti (2, 4, 8, 16 o 32) collegabile. Ogni disco collegato fornisce 500 IOPS ed è possibile raggruppare più dischi per ottenere un multiplo di tale numero di IOPS. Ad esempio, 16 dischi possono essere raggruppati per fornire 8.000 IOPS. Il pooling viene eseguito con la configurazione nel sistema operativo, utilizzando spazi di archiviazione di Microsoft Windows o RAID (Redundant Array of Inexpensive Disks) in Linux.

Una configurazione in formato libero consente di selezionare varie istanze di VM, vari tipi e dimensioni differenti di VM per tali istanze, un numero di dischi che può variare in base al tipo di VM e uno o più script per configurare il contenuto delle VM.

È comune che una distribuzione disponga di più tipi di nodi, ad esempio nodi master e nodi dati, pertanto questa flessibilità viene spesso fornita per ogni tipo di nodo.

Man mano che si inizia a distribuire cluster di una qualsiasi importanza, si inizia a lavorare con multipli di tali elementi. Distribuendo un cluster di Hadoop, ad esempio, con 8 nodi master e 200 nodi dati e 4 dischi collegati in pool in ogni nodo master e 16 dischi collegati in pool per ogni nodo dati, occorrerà gestire 208 VM e 3.232 dischi.

Un account di archiviazione limiterà le richieste che superano il limite identificato di 20.000 transazioni/secondo, è pertanto opportuno prendere in considerazione il partizionamento dell’account di archiviazione e utilizzare calcoli per determinare il numero appropriato di account di archiviazione per adattare questa topologia. Data la grande varietà di combinazioni supportate dall'approccio in formato libero, sono necessari calcoli dinamici per determinare il partizionamento appropriato. Il linguaggio del modello di Gestione risorse di Azure non fornisce attualmente funzioni matematiche, pertanto è necessario eseguire tali calcoli nel codice, generando un modello univoco hardcoded con i dettagli appropriati.

Negli scenari di IT aziendale e SI, un utente deve gestire i modelli e fornire supporto per le topologie distribuite per una o più organizzazioni. Questo sovraccarico aggiuntivo, ovvero configurazioni e modelli differenti per ogni cliente, è tutt’altro che auspicabile.

È possibile utilizzare questi modelli per distribuire gli ambienti nella sottoscrizione Azure del cliente, ma sia i team IT aziendali che i CSV in genere li distribuiscono nelle proprie sottoscrizioni, utilizzando una funzione di chargeback per la fatturazione dei clienti. In questi scenari, l'obiettivo è distribuire capacità per più clienti in un pool di sottoscrizioni e mantenere le distribuzioni densamente popolate nelle sottoscrizioni per ridurne al minimo la proliferazione, vale a dire più sottoscrizioni da gestire. Con dimensioni di distribuzione realmente dinamiche, per ottenere questo tipo di densità occorre un'attenta pianificazione e un ulteriore sviluppo per l’attività di scaffolding effettuato per conto dell'organizzazione.

Inoltre, non è possibile creare sottoscrizioni tramite chiamata all’API ma è necessaria un’operazione manuale tramite il portale. Man mano che aumenta il numero di sottoscrizioni, l’eventuale proliferazione di sottoscrizioni risultate richiede l’intervento degli operatori, non può essere gestita in modo automatico. Con così tanta variabilità nelle dimensioni delle distribuzioni, è necessario il pre-provisioning manuale di varie sottoscrizioni per garantirne la disponibilità.

Considerando tutti questi fattori, l’adozione di una configurazione in formato libero risulta meno accattivante che a prima vista.

### Configurazioni note: l’approccio a taglie

Anziché offrire un modello che fornisce massima flessibilità e innumerevoli variazioni, secondo la nostra esperienza uno schema diffuso è fornire la possibilità di selezionare configurazioni note, ovvero taglie standard quali sandbox, small, medium e large. Altri esempi di taglie sono le offerte di prodotti, come Community Edition o Enterprise Edition. In altri casi, potrebbero essere configurazioni specifiche per i carichi di lavoro di una determinata tecnologia, ad esempio map reduce o no sql.

Molteplici organizzazioni IT aziendali, fornitori di OSS e SI oggi rendono disponibili le loro offerte utilizzando questo approccio in ambienti locali virtualizzati (aziende) o come offerte software-as-a-service (SaaS) (CSV e OSV).

Questo approccio fornisce configurazioni note ed efficienti di dimensioni variabili, preconfigurate per i clienti. Senza configurazioni note, i clienti devono determinare autonomamente la dimensione del cluster, includere i limiti di risorse della piattaforma ed effettuare calcoli matematici per identificare il partizionamento risultante degli account di archiviazione e altre risorse (a causa della dimensione del cluster e dei limiti di risorse). Le configurazioni note consentono ai clienti di selezionare con facilità la taglia appropriata, ovvero una specifica distribuzione. Oltre a migliorare l'esperienza del cliente, un numero limitato di configurazioni note è più facile da supportare e consente di offrire un livello superiore di densità.

In un approccio di configurazione nota incentrato sulle taglie, la taglia può inoltre contenere un numero variabile di nodi. Ad esempio, una taglia small può contenere dai 3 ai 10 nodi. Le taglie verrebbero progettate per contenere fino a 10 nodi e fornire ai clienti la possibilità di effettuare scelte in formato libero fino alla taglia massima identificata.

Una taglia basata sul tipo di carico di lavoro, potrebbe essere per natura un formato più libero in termini di numero di nodi distribuibili ma avrà dimensione dei nodi e configurazione del software nel nodo separate per il carico di lavoro.

Le taglie basate su offerte di prodotti, quali Community o Enterprise, possono disporre di tipi di risorse separati e di un numero massimo di nodi distribuibile, in genere a seguito di considerazioni relative alle licenze o della disponibilità di funzionalità nelle diverse offerte.

Ai clienti è inoltre possibile offrire varianti specifiche utilizzando modelli basati su JSON. Quando si utilizzano gli outlier, è possibile incorporare la pianificazione appropriata e considerazioni relative a sviluppo, supporto e costi.

Sulla base degli scenari di utilizzo dei modelli da parte dei clienti, dei requisiti identificati all’inizio di questo documento e alla nostra esperienza pratica nella creazione di numerosi modelli, abbiamo identificato uno schema per la scomposizione dei modelli.

## Modelli di soluzione con ambito di capacità e funzionalità

La scomposizione offre un approccio modulare allo sviluppo dei modelli in grado di supportare riutilizzo, estendibilità e strumenti. In questa sezione vengono forniti dettagli sulla modalità di applicazione di un approccio di scomposizione a modelli con un ambito di capacità o funzionalità.

In questo approccio, un modello principale riceve i valori dei parametri da un consumer di modello, quindi si collega a vari tipi di modelli e script a valle come illustrato di seguito. Per inserire ed estrarre valori dei modelli collegati vengono utilizzati parametri, variabili statiche e variabili generate.

![Parametri del modello](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**I parametri vengono passati a un modello principale, quindi a modelli collegati**

Nelle seguenti sezioni vengono esaminati i tipi di modelli e script in cui verrebbe scomposto un singolo modello e gli approcci per il passaggio di informazioni sullo stato tra modelli. Ogni modello e i tipi di script nell'immagine sono descritti con esempi. Per un esempio contestuale, vedere "Uso combinato: un'implementazione di esempio", più avanti in questo documento.

### Metadati del modello

I metadati del modello (il file metadata.json) contengono coppie chiave/valore che descrivono un modello in JSON, leggibili da persone e sistemi software.

![Metadati del modello](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**I metadati del modello sono descritti nel file metadata.json**

Gli agenti software possono recuperare il file metadata.json e pubblicare le informazioni e un collegamento al modello in una pagina Web o in una directory. Gli elementi includono *itemDisplayName*, *description*, *summary*, *githubUsername* e *dateUpdated*.

Di seguito è riportato un file di esempio nel suo complesso.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### Modello principale

Il modello principale (il file azuredeploy.json) viene chiamato da un utente finale ed è il modello tramite cui viene presentato un set di parametri definiti dall'utente.

![Modello principale](./media/best-practices-resource-manager-design-templates/main-template.png)

**Il modello principale riceve parametri da un utente**

Il ruolo di questo modello è ricevere i parametri da un utente, utilizzare tali informazioni per popolare un set di variabili oggetto complesse, quindi eseguire il set appropriato di modelli correlati utilizzando il collegamento ai modelli.

Un parametro fornito è un tipo di configurazione nota conosciuto anche come parametro di taglia a causa dei relativi valori standardizzati quali small, medium o large. In pratica è possibile utilizzare questo parametro in diversi modi. Per ulteriori informazioni, vedere "Modello di risorse di configurazione note", più avanti in questo documento.

Alcune risorse vengono distribuite indipendentemente dalla configurazione nota specificata dal parametro di un utente. Queste risorse vengono sottoposte a provisioning utilizzando un unico modello di risorsa condivisa e vengono condivise da altri modelli, in modo che il modello di risorsa condivisa venga eseguito per primo.

Alcune risorse vengono distribuite facoltativamente indipendentemente dalla configurazione nota specificata.

### Modello di risorse condivise

Questo modello offre risorse comuni a tutte le configurazioni note. Contiene la rete virtuale, i set di disponibilità e altre risorse necessarie indipendentemente dal modello di configurazione nota distribuito.

![Risorse del modello](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Modello di risorse condivise**

I nomi delle risorse, ad esempio il nome della rete virtuale, si basano sul modello principale. È possibile specificarli come una variabile all'interno di tale modello o riceverli come parametro dall'utente, secondo quanto richiesto dall'organizzazione.

### Modello di risorse facoltative

Il modello di risorse facoltative contiene risorse distribuite a livello di codice in base al valore di un parametro o di una variabile.

![Risorse facoltative](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Modello di risorse facoltative**

Ad esempio, è possibile utilizzare un modello di risorse facoltative per configurare un jumpbox che consente l'accesso indiretto a un ambiente distribuito dalla rete Internet pubblica. È possibile utilizzare un parametro o una variabile per stabilire se il jumpbox deve essere abilitato o meno e identificare la funzione di *contatto* per compilare il nome di destinazione per il modello, ad esempio*jumpbox_enabled.json*. Il collegamento al modello utilizza la variabile risultante per installare il jumpbox.

È possibile collegare il modello di risorse facoltative da più posizioni:

-	Quando applicabile a ogni distribuzione, creare un collegamento basato su parametro dal modello di risorse condivise.
-	Quando applicabile alle configurazioni note selezionate, ad esempio, installare solo in distribuzioni di grandi dimensioni, creare un collegamento basato su parametro o su variabile dal modello di configurazione nota.

Se una determinata risorsa è facoltativa potrebbe non dipendere dal consumer del modello ma dal fornitore del modello. Ad esempio, potrebbe essere necessario soddisfare un requisito specifico del prodotto o un componente aggiuntivo di prodotto (comune per i CSV) o per applicare i criteri (comune per SI e gruppi IT aziendali). In questi casi, è possibile utilizzare una variabile per stabilire se la risorsa deve essere distribuita o meno.

### Modello di risorse di configurazione note

Nel modello principale, un parametro può essere esposto per consentire al consumer del modello di specificare una configurazione nota desiderata da distribuire. In molti casi, questa configurazione nota utilizza un approccio a taglie con un set di dimensioni di configurazione fisse quali sandbox, small, medium e large.

![Risorse di configurazione note](./media/best-practices-resource-manager-design-templates/known-config.png)

**Modello di risorse di configurazione note**

L'approccio a taglie viene comunemente utilizzato, ma i parametri possono rappresentare qualsiasi set di configurazioni note. Ad esempio, è possibile specificare un set di ambienti per un'applicazione aziendale, ad esempio sviluppo, test e prodotto. In alternativa, è possibile utilizzarlo per un servizio cloud per rappresentare unità di scala, versioni di prodotto o le configurazioni del prodotto differenti, ad esempio Community, Developer o Enterprise.

Come con il modello di risorsa condivisa, le variabili vengono passate al modello di configurazioni note da:

-	Un utente finale, ovvero i parametri inviati al modello principale.
-	Un'organizzazione, ovvero le variabili nel modello principale che rappresentano requisiti o criteri interni.

### Modello di risorse membro

All'interno di una configurazione nota sono spesso inclusi uno o più tipi di nodi membro. Ad esempio, con Hadoop si avrebbero nodi master e nodi dati. Installando MongoDB, si avrebbero nodi dati e un arbitro. Distribuendo DataStax, si avrebbero nodi dati nonché una VM con OpsCenter installato.

![Risorse membro](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Modello di risorse membro**

Ogni tipo di nodo può avere differenti dimensioni di VM, numeri di dischi collegati, script per installare e configurare i nodi, configurazioni delle porte per le VM, numero di istanze e altri dettagli. Quindi ogni tipo di nodo ottiene il proprio modello di risorsa membro, contenente i dettagli per la distribuzione e la configurazione di un'infrastruttura, nonché l'esecuzione di script per distribuire e configurare il software all'interno della VM.

Per le VM, in genere vengono utilizzati due tipi di script, ovvero script ampiamente riutilizzabili e script personalizzati.

### Script ampiamente riutilizzabili

Gli script ampiamente riutilizzabili possono essere impiegati in più tipi di modelli. Uno degli esempi migliori per questi script ampiamente riutilizzabili imposta RAID su Linux per raggruppare dischi in pool e ottenere un numero maggiore di IOPS. Indipendentemente dal software installato nella VM, questo script consente di riutilizzare procedure consolidate per scenari comuni.

![Script riutilizzabili](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**I modelli di risorse membro possono chiamare script ampiamente riutilizzabili**

### Script personalizzati

I modelli chiamano comunemente uno o più script che consentono di installare e configurare il software all'interno delle VM. È stato osservato uno schema comune con le topologie di grandi dimensioni in cui vengono distribuite più istanze di uno o più tipi membro. Per ogni macchina virtuale viene avviato uno script di installazione eseguibile in parallelo, seguito da uno script di configurazione chiamato dopo la distribuzione di tutte le VM (o tutte le VM di un tipo di membro specificato).

![Script personalizzati](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**I modelli di risorse membro possono chiamare script per uno scopo specifico, ad esempio la configurazione della VM**

## Esempio di modello di soluzione con ambito di funzionalità - Redis

Per mostrare come potrebbe funzionare un'implementazione, esaminiamo un esempio pratico di creazione di un modello in grado di agevolare la distribuzione e la configurazione di Redis a taglie standard.

Per la distribuzione, sarà disponibile un set di risorse condivise (rete virtuale, account di archiviazione, set di disponibilità) e una risorsa facoltativa (jumpbox). Esistono più configurazioni note rappresentate come taglie (small, medium, large) ma ciascuna con un tipo di nodo singolo. Esistono inoltre due script specifici per lo scopo (installazione, configurazione).

### Creazione dei file dei modelli

Verrà creato un modello principale denominato azuredeploy.json.

Verrà creato un modello di risorse condivise denominato shared-resources.json

Verrà creato un modello di risorsa facoltativa per abilitare la distribuzione di un jumpbox denominato jumpbox_enabled.json

Redis utilizzerà solo un tipo di nodo singolo, in modo che venga creato un unico modello di risorsa membro denominato node-resources.json.

Con Redis, è opportuno installare ogni singolo nodo e quindi, una volta installati tutti i nodi è opportuno impostare il cluster. Sono disponibili script per soddisfare entrambi questi requisiti: redis-cluster-install.sh e redis-cluster-setup.sh.

### Collegamento dei modelli

Utilizzando il collegamento di modello, il modello principale si collega al modello di risorse condivise, stabilendo in tal modo la rete virtuale.

All’interno del modello principale viene aggiunta la logica per consentire ai consumer del modello di specificare se un jumpbox dovrà essere distribuito o meno. Un valore *abilitato* per il parametro *EnableJumpbox* indica che il cliente desidera distribuire un jumpbox. Quando questo valore viene specificato, il modello concatena *_abilitato* come suffisso al nome di un modello di base per la funzionalità di jumpbox.

Il modello principale utilizza il valore di parametro *large* come suffisso del nome di un modello di base per le taglie, quindi utilizza tale valore nel collegamento di un modello a *technology_on_os_large.json*.

La topologia sarà simile a questa illustrazione.

![Modello di Redis](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Struttura del modello per un modello di Redis**

### Configurazione dello stato

Per i nodi del cluster, esistono due fasi per la configurazione dello stato, entrambe rappresentate da script specifici per lo scopo. “redis-cluster-install.sh” esegue l’installazione di Redis e “redis-cluster-setup.sh” configura il cluster.

### Supporto di distribuzioni di dimensioni diverse

All'interno di variabili, il modello delle taglie specifica il numero di nodi di ogni tipo di distribuzione per la dimensione specificata (*large*). Quindi distribuisce tale numero di istanze di VM utilizzando i cicli di risorse che forniscono nomi univoci per le risorse mediante l'aggiunta di un nome di nodo con un numero di sequenza numerica ottenuto da *copyIndex()*. Questa operazione viene eseguita per le zone VM sia critiche e medie, come definito nel modello di assegnazione del nome

## Modelli con ambito di soluzione end-to-end e di scomposizione

Un modello di soluzione con ambito di soluzione end-to-end è incentrato sulla fornitura di una soluzione end-to-end. Sarà in genere una composizione di più modelli con ambito di funzionalità con risorse aggiuntive, logica e stato.

Come evidenziato nell'immagine riportata di seguito, lo stesso modello utilizzato per i modelli con ambito di funzionalità viene esteso per i modelli con ambito di soluzione End-to-End.

Un modello di risorse condivise e i modelli di risorse facoltative hanno la stessa funzione degli approcci di modelli con ambito di capacità e di funzionalità ma l’ambito è la soluzione end-to-end.

Dato che anche i modelli con ambito di soluzione end-to-end possono in genere avere taglie, il modello di risorse di configurazione note riflette ciò che è necessario per una specifica configurazione nota della soluzione.

Il modello di risorse di configurazione note si collega a uno o più modelli di soluzione con ambito funzionalità pertinenti per la soluzione end-to-end nonché ai modelli di risorse membro necessari per la soluzione end-to-end.

Dato che la taglia della soluzione può differire dal modello con ambito di funzionalità individuale, vengono utilizzate variabili all’interno del modello di risorse di configurazione note per fornire i valori appropriati ai modelli di soluzione con ambito di funzionalità downstream allo scopo di distribuire la taglia appropriata.

![End-to-end](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**Il modello utilizzato per i modelli di soluzioni con ambito di capacità o funzionalità possono essere facilmente estesi per gli ambiti dei modelli di soluzioni end-to-end**

## Preparazione di modelli per Marketplace

L'approccio precedente si adatta facilmente a scenari in cui le aziende, gli SI e i CSV desiderano distribuire autonomamente i modelli o consentire ai propri clienti di distribuire in modo indipendente.

Un altro scenario desiderato è la distribuzione di un modello tramite marketplace. Questo approccio di scomposizione funzionerà anche per marketplace con alcune piccole modifiche.

Come indicato in precedenza, i modelli possono essere utilizzati per tipi di distribuzione separati per la vendita nel marketplace. Tipi di distribuzione separati possono essere taglie (small, medium, large), tipo di prodotto/pubblico (community, sviluppatore, azienda) o tipo di funzionalità (di base, alta disponibilità).

Come illustrato di seguito, la soluzione end-to-end o i modelli con ambito di funzionalità esistenti, possono essere facilmente utilizzati per elencare le diverse configurazioni note nel marketplace.

Per rimuovere il parametro in ingresso denominato tshirtSize vengono innanzitutto modificati i parametri per il modello principale.

Sebbene i tipi di distribuzione separati eseguano il mapping al modello di risorse di configurazione note, sono necessarie anche le risorse comuni e la configurazione trovate nel modello di risorse condivise e potenzialmente quelle dei modelli di risorse facoltative.

Se si desidera pubblicare il modello in marketplace, è sufficiente stabilire copie distinte del modello principale che sostituiscano il parametro in ingresso precedentemente disponibile di tshirtSize in una variabile incorporata all'interno del modello.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Adattamento di un modello con ambito di soluzione per marketplace**

## Passaggi successivi

- Per vedere esempi contestuali di come implementare i principi di progettazione presentati in questo argomento, vedere [Esempi contestuali delle procedure consigliate per l’implementazione di modelli](best-practices-resource-manager-examples.md).
- Per consigli su come gestire la protezione in Gestione risorse di Azure, vedere [Considerazioni sulla sicurezza per Gestione risorse di Azure](best-practices-resource-manager-security.md)
- Per ulteriori informazioni sulla condivisione dello stato interno ed esterno dei modelli, vedere [Condivisione dello stato in modelli di Gestione risorse di Azure](best-practices-resource-manager-state.md).

<!---HONumber=July15_HO4-->