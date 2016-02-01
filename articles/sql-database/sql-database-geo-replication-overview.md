<properties
	pageTitle="Replica geografica attiva per il database SQL di Azure"
	description="Questo argomento illustra la Replica geografica attiva per il database SQL e i relativi utilizzi."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/21/2015"
	ms.author="jroth" />

# Replica geografica attiva per il database SQL di Azure

## Panoramica
La funzionalità Replica geografica attiva implementa un meccanismo per garantire la ridondanza dei database nella stessa area di Microsoft Azure o in aree diverse (ridondanza geografica). La Replica geografica attiva replica in modo asincrono le transazioni di cui è stato eseguito il commit da un database a un massimo di quattro copie del database in server diversi. Il database originale diventa il database primario della copia continua. Ogni copia continua invece è un database secondario online. Il database primario replica in modo asincrono le transazioni sottoposte a commit in ognuno dei database secondari online. Anche se a un certo punto i dati del database secondario online possono essere leggermente indietro rispetto al database primario, viene garantito che siano sempre coerenti dal punto di vista transazionale con le modifiche salvate tramite commit nel database primario. La Replica geografica attiva supporta fino a quattro database secondari online o fino a tre database secondari online e un database secondario offline.

Uno dei principali vantaggi offerti dalla Replica geografica attiva è quello di fornire una soluzione di ripristino di emergenza a livello di database. Con la Replica geografica attiva è possibile configurare un database utente nel livello di servizio Premium per la replica delle transazioni in database che si trovano in server del database SQL di Microsoft Azure diversi all'interno della stessa area o in aree diverse. La ridondanza tra aree consente il ripristino delle applicazioni dalla perdita definitiva di un data center causata da calamità naturali, errori umani di portata catastrofica o atti dolosi.

Un altro vantaggio importante è rappresentato dal fatto che i database secondari online sono leggibili, pertanto un database secondario online può fungere da servizio di bilanciamento del carico per i carichi di lavoro di lettura, come ad esempio nel caso di creazione di report. Nonostante sia possibile creare un database secondario online in un'area diversa per scopi di ripristino di emergenza, esiste anche la possibilità di crearne uno nella stessa area, ma in un server diverso. Entrambi i database secondari online possono essere usati per il bilanciamento dei carichi di lavoro di sola lettura che servono client distribuiti in più aree.

Di seguito sono illustrati altri scenari in cui è possibile usare la Replica geografica attiva:

- **Migrazione di un database**: è possibile usare la Replica geografica attiva per eseguire la migrazione di un database da un server a un altro online con un tempo di inattività minimo.
- **Aggiornamenti dell'applicazione**: è possibile usare il database secondario online come opzione di failback.

Per ottenere una reale continuità aziendale, l'aggiunta di ridondanza tra i data center all'archiviazione relazionale rappresenta solo una parte della soluzione. Per ripristinare un'applicazione (servizio) end-to-end dopo un problema grave, è necessario effettuare il ripristino di tutti i componenti del servizio e gli eventuali servizi dipendenti. Esempi di questi componenti includono il software client (ad esempio, un browser con JavaScript personalizzato), front-end Web, spazio di archiviazione e DNS. È fondamentale che tutti i componenti siano resilienti agli stessi problemi e diventino disponibili entro I'obiettivo del tempo di ripristino (RTO) dell'applicazione. È perciò necessario identificare tutti i servizi dipendenti e comprendere quali garanzie e funzionalità vengono fornite. È quindi necessario intraprendere le azioni appropriate per assicurare il funzionamento del servizio durante il failover dei servizi da cui dipende. Per altre informazioni sulla progettazione di soluzioni per il ripristino di emergenza, vedere [Progettazione di soluzioni cloud per il ripristino di emergenza mediante la Replica geografica attiva](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## Funzionalità della replica geografica attiva
La funzionalità di Replica geografica attiva fornisce i seguenti elementi essenziali:

- **Replica asincrona automatica**: dopo il seeding di un database secondario online, gli aggiornamenti relativi al database primario vengono copiati automaticamente in modo asincrono nel database secondario online. Questo significa che le transazioni vengono sottoposte a commit nel database primario prima di essere copiate nel database secondario online. Tuttavia, dopo il seeding, il database secondario online è coerente da un punto di vista transazionale in qualsiasi momento.
	>[AZURE.NOTE]La replica asincrona risulta particolarmente adatta alla latenza delle reti WAN che connettono i data center remoti.

- **Più database secondari online**: la presenza di due o più database secondari online aumenta la ridondanza e la protezione per il database primario e l'applicazione. Se sono disponibili più database secondari online, l'applicazione rimane protetta anche se uno di essi smette di funzionare. Se è presente un solo database secondario online e questo smette di funzionare, l'applicazione rimane esposta a maggiori rischi finché non viene creato un nuovo database secondario online.

- **Database secondari online leggibili**: un'applicazione può accedere a un database secondario online per le operazioni di sola lettura mediante le stesse entità di sicurezza usate per accedere al database primario. Le operazioni di copia continua nel database secondario online hanno la precedenza sull'accesso dell'applicazione. Se le query eseguite sul database secondario online causano anche un blocco prolungato delle tabelle, le transazioni possono avere esito negativo nel database primario.

- **Terminazione controllata dall'utente per il failover**: prima di poter eseguire il failover di un'applicazione a un database secondario online, è necessario terminare la relazione di copia continua con il database primario. Per la terminazione della relazione di copia continua è necessaria un'azione esplicita da parte dell'applicazione o di uno script amministrativo oppure un'azione eseguita manualmente tramite il portale. Dopo la terminazione, il database secondario online diventa un database autonomo. Diventa perciò un database di lettura/scrittura, a meno che il database primario non fosse un database di sola lettura. Più avanti in questo argomento vengono illustrate due forme di [terminazione di una relazione di copia continua](#termination-of-a-continuous-copy-relationship).

>[AZURE.NOTE]La Replica geografica attiva è supportata solo per i database nel livello di servizio Premium. Questa limitazione vale sia per i database primari che per i database secondari online. Il database secondario online deve anche essere configurato in modo da offrire un livello di prestazioni uguale o superiore a quello del database primario. Le modifiche apportate ai livelli di prestazioni del database primario non saranno replicate automaticamente sui database secondari. Eventuali aggiornamenti andranno eseguiti prima sui database secondari e infine sul primario. Per altre informazioni sulla modifica dei livelli di prestazioni, vedere [Modificare il livello di prestazioni](sql-database-scale-up.md). Il database secondario online deve avere almeno le stesse dimensioni di quello primario per due motivi principali. Il database secondario deve disporre di una capacità sufficiente per elaborare le transazioni replicate alla stessa velocità con cui vengono elaborate dal database primario. Se il database secondario non ha almeno la stessa capacità di elaborazione delle transazioni in ingresso, può accumulare ritardo e persino influire sulla disponibilità del database primario. Se il database secondario non ha la stessa capacità del database primario, è probabile che il failover incida negativamente sulle prestazioni e sulla disponibilità dell'applicazione.

## Concetti relativi alla relazione di copia continua
La ridondanza dei dati locali e il ripristino operativo sono funzionalità standard dei database SQL di Azure. Per ogni database sono previsti un database primario e due database di replica locali che risiedono nello stesso data center, garantendo una disponibilità elevata all'interno del data center stesso. Ciò implica che anche i database della Replica geografica attiva hanno repliche ridondanti. Sia il database primario che i database secondari online hanno due repliche secondarie. Tuttavia, per il database secondario, la replica primaria viene aggiornata direttamente dal meccanismo di copia continua e non può accettare aggiornamenti avviati dall'applicazione. La figura seguente illustra il modo in cui la Replica geografica attiva estende la ridondanza dei database tra due aree di Azure. L'area in cui viene ospitato il database primario è nota come area primaria. Quella in cui viene ospitato il database secondario online è nota come area secondaria. In questa figura l'Europa settentrionale corrisponde all'area primaria, mentre l'Europa occidentale corrisponde all'area secondaria.

![Relazione di copia continua](./media/sql-database-active-geo-replication/continuous-copy-relationships.gif)

Se il database primario diventa non disponibile, terminando la relazione di copia continua per un determinato database secondario online, questo diventa un database autonomo. Il database secondario online eredita la modalità di sola lettura o di lettura/scrittura del database primario che resta invariato a seguito della terminazione. Se ad esempio il database primario è di sola lettura, dopo la terminazione il database secondario online diventa un database di sola lettura. A questo punto, l'applicazione può effettuare il failover e continuare a usare il database secondario online. Per garantire la resilienza nell'eventualità di un errore catastrofico del data center o di un'interruzione prolungata nell'area primaria, è necessario che almeno un database online secondario sia situato in un'area diversa.

## Creazione di una copia continua
È possibile creare una copia continua solo di un database esistente. La creazione di una copia continua di un database esistente è utile per aggiungere ridondanza geografica. È possibile creare una copia continua anche per copiare un database esistente in un altro server di database SQL di Azure. Dopo avere creato il database secondario, è necessario popolarlo con i dati copiati dal database primario. Questo processo è noto come seeding. Al termine del seeding, ogni nuova transazione verrà replicata dopo essere stata sottoposta a commit sul database primario.

Per informazioni su come creare una copia continua di un database esistente, vedere [Come abilitare la replica geografica](sql-database-business-continuity-design.md#how-to-enable-geo-replication).

## Evitare la perdita di dati critici
A causa della latenza elevata delle reti WAN, per la copia continua viene usato un meccanismo di replica asincrona. Questo rende inevitabile una perdita parziale dei dati nel caso si verifichi un problema. Tuttavia, alcune applicazione potrebbero non essere soggette alla perdita dei dati. Per proteggere questi aggiornamenti critici, uno sviluppatore di applicazioni può chiamare la procedura di sistema [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) subito dopo il commit della transazione. La chiamata a **sp\_wait\_for\_database\_copy\_sync** blocca il thread chiamante finché l'ultima transazione sottoposta a commit non è stata replicata nel database secondario online. La procedura resterà in attesa fino al riconoscimento di tutte le transazioni in coda da parte del database secondario online. **sp\_wait\_for\_database\_copy\_sync** ha come ambito un collegamento di copia continua specifico. La procedura può essere chiamata da qualsiasi utente che abbia diritti di connessione al database primario.

>[AZURE.NOTE]Il ritardo causato da una chiamata alla procedura **sp\_wait\_for\_database\_copy\_sync** può essere considerevole e dipende dalla lunghezza della coda e dalla larghezza di banda disponibile. Chiamare quindi la procedura solo quando strettamente necessario.

## Terminazione di una relazione di copia continua
La relazione di copia continua può essere terminata in qualsiasi momento. La terminazione di una relazione di copia continua non rimuove il database secondario. Per terminare una relazione di copia continua è possibile procedere in due modi:

- La **terminazione pianificata** è utile per le operazioni pianificate in cui la perdita di dati non è accettabile. È possibile eseguire una terminazione pianificata solo sul database primario, dopo che è stato effettuato il seeding del database secondario online. In una terminazione di questo tipo tutte le transazioni di cui è stato eseguito il commit nel database primario vengono replicate nel database secondario online, quindi la relazione di copia continua viene terminata. In questo modo si evitano perdite di dati nel database secondario.
- La **terminazione non pianificata (forzata)** deve essere usata per rispondere alla perdita del database primario o di uno dei relativi database secondari online. Una terminazione forzata può essere eseguita sul database primario o sul database secondario attivo. Ogni terminazione forzata causa la perdita irreversibile della relazione di replica tra il database primario e il database secondario online associato, nonché la perdita delle eventuali transazioni non replicate dal database primario. Con la terminazione forzata, la relazione di copia continua viene terminata immediatamente, pertanto le transazioni ancora in corso non vengono replicate nel database secondario online. Una terminazione forzata perciò può dare luogo alla perdita definitiva delle transazioni non replicate dal database primario.

>[AZURE.NOTE]Se il database primario ha una sola relazione di copia continua, dopo la terminazione gli aggiornamenti nel database primario non saranno più protetti.

Per altre informazioni su come terminare una relazione di copia continua, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md).

## Passaggi successivi
Per altre informazioni sulla Replica geografica attiva e su altre funzionalità di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0121_2016-->