<properties 
   pageTitle="Progettazione di soluzioni cloud per il ripristino di emergenza mediante la replica geografica del database SQL | Microsoft Azure"
   description="Informazioni su come progettare la soluzione cloud per il ripristino di emergenza scegliendo il modello di failover appropriato."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/29/2016"
   ms.author="sashan"/>

# Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL 

Nel corso degli anni si è notato che i servizi cloud non sono infallibili e incidenti catastrofici possono verificarsi e si verificheranno. Il database SQL fornisce alcune funzionalità utili per la continuità aziendale dell'applicazione in caso di eventi imprevisti. I pool elastici e i database autonomi supportano lo stesso tipo di funzionalità per il ripristino di emergenza. Questo articolo illustra alcune strategie di ripristino di emergenza per i pool elastici che sfruttano i vantaggi di queste funzionalità per la continuità aziendale del database SQL.

Per le finalità di questo articolo, verrà usato il modello classico di applicazione ISV SaaS:

<i>Un'applicazione Web moderna basata sul cloud effettua il provisioning di un database SQL per ogni utente finale. L'ISV ha un numero elevato di clienti e usa quindi molti database, definiti database tenant. Poiché i database tenant presentano in genere modelli di attività imprevedibili, l'ISV usa un pool elastico per rendere molto prevedibili i costi del database in periodi estesi di tempo. Il pool elastico semplifica anche la gestione delle prestazioni in caso di picchi dell'attività degli utenti. Oltre ai database tenant, l'applicazione usa anche alcuni database per gestire i profili utente e la sicurezza e per raccogliere i modelli di utilizzo e altro ancora. La disponibilità dei singoli tenant non influisce sulla disponibilità complessiva dell'applicazione. La disponibilità e le prestazioni dei database di gestione, tuttavia, sono essenziali per il funzionamento dell'applicazione e se i database di gestione sono offline, l'intera applicazione è offline.</i>

Nella parte restante di questo articolo vengono illustrate le strategie di ripristino di emergenza in un'ampia gamma di scenari, dalle applicazioni start-up attente ai costi a quelle con requisiti di disponibilità rigorosi.

## Scenario 1. Start-up attente ai costi

<i>Una start-up estremamente attenta ai costi vuole semplificare la distribuzione e la gestione dell'applicazione ed è favorevole all'uso di un Contratto di servizio limitato per singoli clienti. Vuole tuttavia assicurarsi che l'applicazione nel suo complesso non sia mai offline.</i>

Per rispettare il requisito relativo alla semplicità, è consigliabile distribuire tutti i database tenant in un pool elastico nell'area di Azure scelta e quindi distribuire i database di gestione come database autonomi con replica geografica. Per il ripristino di emergenza dei tenant, usare il ripristino geografico, disponibile senza costi aggiuntivi. Per assicurare la disponibilità dei database di gestione, è consigliabile configurarne la replica geografica in un'altra area (Passaggio 1). I costi di esercizio della configurazione per il ripristino di emergenza in questo scenario equivalgono al costo totale dei database secondari. Questa configurazione è illustrata nel diagramma seguente.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

In caso di interruzione nell'area primaria, la procedura di ripristino per riportare online l'applicazione è illustrata nel diagramma seguente.

- Eseguire immediatamente il failover dei database di gestione (2) nell'area di ripristino di emergenza. 
- Modificare la stringa di connessione dell'applicazione in modo che faccia riferimento all'area di ripristino di emergenza. Tutti i nuovi account e i database tenant verranno creati nell'area di ripristino di emergenza. I dati risulteranno temporaneamente non disponibili per i clienti esistenti.
- Creare il pool elastico con la stessa configurazione del pool originale (3). 
- Usare il ripristino geografico per creare copie dei database tenant (4). È possibile prendere in considerazione l'attivazione dei singoli ripristini in base alle connessioni degli utenti finali oppure l'uso di un altro schema di priorità specifico dell'applicazione.

A questo punto l'applicazione è di nuovo online nell'area di ripristino di emergenza, ma alcuni clienti noteranno un ritardo nell'accesso ai dati.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se l'interruzione è stata temporanea, è possibile che l'area primaria venga ripristinata da Azure prima del completamento di tutti i ripristini nell'area di ripristino di emergenza. In questo caso, è necessario orchestrare il ritorno dell'applicazione all'area primaria. Il processo eseguirà la procedura illustrata nel diagramma seguente.
 
- Annullare tutte le richieste di ripristino geografico in sospeso.   
- Eseguire il failover dei database di gestione nell'area primaria (5). Nota: dopo il ripristino dell'area, gli elementi primari precedenti sono diventati automaticamente secondari. Ora i ruoli vengono nuovamente invertiti. 
- Modificare la stringa di connessione dell'applicazione in modo che faccia di nuovo riferimento all'area primaria. Tutti i nuovi account e i database tenant verranno ora creati nell'area primaria. I dati risulteranno temporaneamente non disponibili per alcuni clienti esistenti.   
- Impostare tutti i database nel pool di ripristino di emergenza su sola lettura, per assicurare che non possano essere modificati nell'area di ripristino di emergenza (6). 
- Per ogni database nel pool di ripristino di emergenza modificato dopo il ripristino, rinominare o eliminare i database corrispondenti nel pool primario (7). 
- Copiare i database aggiornati dal pool di ripristino di emergenza al pool primario (8). 
- Eliminare il pool di ripristino di emergenza (9).

A questo punto l'applicazione sarà online nell'area primaria con tutti i database tenant disponibili nel pool primario.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Il **vantaggio** principale di questa strategia è costituito dai costi di esercizio ridotti per la ridondanza a livello dati. I backup vengono eseguiti automaticamente dal servizio database SQL, senza riscrittura di applicazioni e senza costi aggiuntivi. I costi vengono addebitati solo quando i database elastici vengono ripristinati. Lo **svantaggio** consiste nel fatto che il ripristino completo di tutti i database tenant richiederà molto tempo, in base al numero totale di ripristini avviati nell'area di ripristino di emergenza e alle dimensioni complessive dei database tenant. Anche se si attribuisce una priorità maggiore ai ripristini di alcuni tenant rispetto ad altri, si verificheranno conflitti con tutti gli altri ripristini avviati nella stessa area, perché il servizio eseguirà l'arbitraggio e applicherà la limitazione per ridurre al minimo l'impatto complessivo sui database dei clienti esistenti. Il ripristino dei database tenant, inoltre, può essere avviato solo dopo la creazione del nuovo pool elastico nell'area di ripristino di emergenza.

## Scenario 2. Applicazione matura con più livelli di servizio 

<i>Un'applicazione SaaS matura con più livelli di offerte del servizio e diversi Contratti di servizio per i clienti delle versioni di valutazione e i clienti delle versioni a pagamento deve ridurre il più possibile i costi per i clienti delle versioni di valutazione. Questi clienti possono accettare i tempi di inattività, ma si vuole ridurne la probabilità. Per i clienti delle versioni a pagamento, i tempi di inattività possono costituire un rischio inaccettabile. Si vuole quindi assicurare che i clienti delle versioni a pagamento siano sempre in grado di accedere ai propri dati.</i>

Per supportare questo scenario, è consigliabile separare i tenant delle versioni di valutazione dai tenant delle versioni a pagamento, inserendoli in pool elastici separati. I clienti delle versioni di valutazione avranno valori eDTU inferiori per ogni tenant e un Contratto di servizio inferiore con tempi di ripristino più lunghi. I clienti delle versioni a pagamento si troveranno in un pool con valori eDTU superiori per ogni tenant e un Contratto di servizio superiore. Per assicurare tempi di ripristino minimi, i database tenant dei clienti delle versioni a pagamento devono prevedere la replica geografica. Questa configurazione è illustrata nel diagramma seguente.

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Analogamente al primo scenario, i database di gestione saranno abbastanza attivi, quindi è consigliabile usare un database autonomo con replica geografica per questo scopo (1). In questo modo si assicureranno prestazioni prevedibili per le sottoscrizioni dei nuovi clienti, per gli aggiornamenti dei profili e altre operazioni di gestione. L'area in cui si trovano gli elementi primari dei database di gestione sarà l'area primaria e l'area in cui si trovano gli elementi secondari dei database di gestione sarà l'area di ripristino di emergenza.

I database tenant dei clienti delle versioni a pagamento avranno database attivi nel pool "a pagamento" sottoposto a provisioning nell'area primaria. È necessario effettuare il provisioning di un pool secondario con lo stesso nome nell'area di ripristino di emergenza. Per ogni tenant è prevista la replica geografica nel pool secondario (2). Ciò consentirà un ripristino rapido di tutti i database tenant mediante il failover.

In caso di interruzione nell'area primaria, la procedura di ripristino per riportare online l'applicazione è illustrata nel diagramma seguente.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Eseguire immediatamente il failover dei database di gestione nell'area di ripristino di emergenza (3).
- Modificare la stringa di connessione dell'applicazione in modo che faccia riferimento all'area di ripristino di emergenza. Tutti i nuovi account e i database tenant verranno ora creati nell'area di ripristino di emergenza. I dati risulteranno temporaneamente non disponibili per i clienti esistenti delle versioni di valutazione.
- Eseguire il failover dei database del tenant a pagamento nel pool nell'area di ripristino di emergenza in modo da ripristinarne immediatamente la disponibilità (4). Poiché il failover è una rapida modifica a livello di metadati, è consigliabile prendere in considerazione un'ottimizzazione in cui i singoli failover vengono attivati su richiesta dalle connessioni dell'utente finale. 
- Se le dimensioni di eDTU del pool secondario sono minori di quelle del pool primario, poiché i database secondari richiedevano solo le funzionalità necessarie per elaborare i log delle modifiche durante l'impostazione come secondari, è necessario aumentare immediatamente la capacità del pool in modo da adeguarla al carico di lavoro completo di tutti i tenant (5). 
- Creare il nuovo pool elastico con lo stesso nome e la stessa configurazione nell'area di ripristino di emergenza per i database dei clienti della versione di valutazione (6). 
- Dopo la creazione del pool dei clienti della versione di valutazione, usare il ripristino geografico per ripristinare i singoli database tenant della versione di valutazione nel nuovo pool (7). È possibile prendere in considerazione l'attivazione dei singoli ripristini in base alle connessioni degli utenti finali oppure l'uso di un altro schema di priorità specifico dell'applicazione.

A questo punto l'applicazione è di nuovo online nell'area di ripristino di emergenza. Tutti i clienti della versione a pagamento possono accedere ai propri dati, mentre i clienti della versione di valutazione noteranno un ritardo nell'accesso ai dati.

Quando l'area primaria viene ripristinata da Azure *dopo* il ripristino dell'applicazione nell'area di ripristino di emergenza, è possibile continuare a eseguire l'applicazione in tale area oppure eseguire il failback nell'area primaria. Se l'area primaria viene ripristinata *prima* del completamento del processo di failover, è necessario eseguire immediatamente il failback. Il failback eseguirà la procedura illustrata nel diagramma seguente.
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Annullare tutte le richieste di ripristino geografico in sospeso.   
- Eseguire il failover dei database di gestione (8). Dopo il ripristino dell'area, l'elemento primario precedente è diventato automaticamente l'elemento secondario. Ora diventa di nuovo primario.  
- Eseguire il failover dei database tenant della versione a pagamento (9). Analogamente, dopo il ripristino dell'area, gli elementi primari precedenti sono diventati automaticamente secondari. Ora diventano di nuovo primari. 
- Impostare su sola lettura i database della versione di valutazione ripristinati che hanno subito modifiche nell'area di ripristino di emergenza (10).
- Per ogni database nel pool di ripristino di emergenza dei clienti della versione di valutazione modificato dopo il ripristino, rinominare o eliminare il database corrispondente nel pool primario dei clienti della versione di valutazione (11). 
- Copiare i database aggiornati dal pool di ripristino di emergenza al pool primario (12). 
- Eliminare il pool di ripristino di emergenza (13). 

> [AZURE.NOTE] L'operazione di failover è asincrona. Per ridurre al minimo il tempo necessario per il ripristino, è importante eseguire il comando di failover dei database tenant in batch di almeno 20 database.

Il **vantaggio** principale di questa strategia consiste nel fatto che offre il Contratto di servizio migliore per i clienti a pagamento. Garantisce anche che le nuove versioni di valutazione vengano sbloccate non appena viene creato il pool di ripristino di emergenza della versione di valutazione. Lo **svantaggio** è costituito dal fatto che questa installazione aumenterà i costi totali dei database tenant in base al costo del pool di database di ripristino secondario per i clienti a pagamento. Se il pool secondario ha inoltre dimensioni diverse, i clienti a pagamento noteranno prestazioni inferiori dopo il failover fino al completamento dell'aggiornamento del pool nell'area di ripristino di emergenza.

## Scenario 3. Applicazione geograficamente distribuita con più livelli di servizio

<i>Un'applicazione SaaS matura con offerte di più livelli di servizio vuole offrire un Contratto di servizio molto aggressivo ai clienti della versione a pagamento e vuole ridurre al minimo il rischio di impatto di eventuali interruzioni, perché anche una breve interruzione può causare l'insoddisfazione dei clienti. È essenziale che i clienti della versione a pagamento possano accedere sempre ai propri dati. Le versioni di valutazione sono gratuite e non è disponibile alcun Contratto di servizio durante il periodo di valutazione. </i>

Per supportare questo scenario, è necessario creare tre pool elastici separati. È necessario effettuare il provisioning di due pool di dimensioni uguali con eDTU elevati per ogni database in due aree diverse per includere i database tenant dei clienti della versione a pagamento. Il terzo pool contenente i tenant della versione di valutazione avrà valori eDTU inferiori per ogni database e viene sottoposto a provisioning in una delle due aree.

Per assicurare i tempi di ripristino più brevi durante le interruzioni, è necessario che i database tenant dei clienti della versione a pagamento siano sottoposti a replica geografica con il 50% dei database primari in ognuna delle due aree. Analogamente, ogni area includerà il 50% dei database secondari. In questo modo, lo stato offline di un'area influirà solo sul 50% dei database dei clienti della versione a pagamento, che dovranno eseguire il failover. Gli altri database rimarranno invariati. Questa configurazione è illustrata nel diagramma seguente:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Analogamente allo scenario precedente, i database di gestione saranno abbastanza attivi, quindi è necessario configurarli come database autonomi con replica geografica (1). In questo modo si assicureranno prestazioni prevedibili per le sottoscrizioni dei nuovi clienti, per gli aggiornamenti dei profili e altre operazioni di gestione. L'area A sarà l'area primaria per i database di gestione e l'area B verrà usata per il ripristino dei database di gestione.

I database tenant dei clienti della versione a pagamento saranno sottoposti anche a replica geografica, ma con gli elementi primari e secondari suddivisi tra area A e area B (2). In questo modo i database tenant primari interessati dall'interruzione possono eseguire il failover nell'altra area e risultare disponibili. L'altra metà dei database tenant non sarà affatto interessata.

Il diagramma seguente illustra la procedura di ripristino da eseguire in caso di interruzione nell'area A.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Eseguire immediatamente il failover dei database di gestione nell'area B (3).
- Modificare la stringa di connessione dell'applicazione in modo che faccia riferimento ai database di gestione nell'area B. Modificare i database di gestione, per assicurarsi che i nuovi account e i database tenant verranno creati nell'area B e che i database tenant esistenti siano disponibili in tale area. I dati risulteranno temporaneamente non disponibili per i clienti esistenti delle versioni di valutazione.
- Eseguire il failover dei database del tenant a pagamento nel pool 2 nell'area B in modo da ripristinarne immediatamente la disponibilità (4). Poiché il failover è una rapida modifica a livello di metadati, è consigliabile prendere in considerazione un'ottimizzazione in cui i singoli failover vengono attivati su richiesta dalle connessioni dell'utente finale. 
- Poiché il pool 2 contiene ora solo database primari, il carico di lavoro totale nel pool aumenterà ed è necessario incrementare immediatamente le rispettive dimensioni eDTU (5). 
- Creare il nuovo pool elastico con lo stesso nome e la stessa configurazione nell'area B per i database dei clienti della versione di valutazione (6). 
- Dopo la creazione del pool, usare il ripristino geografico per ripristinare il singolo database tenant della versione di valutazione nel pool (7). È possibile prendere in considerazione l'attivazione dei singoli ripristini in base alle connessioni degli utenti finali oppure l'uso di un altro schema di priorità specifico dell'applicazione.


> [AZURE.NOTE] L'operazione di failover è asincrona. Per ridurre al minimo il tempo necessario per il ripristino, è importante eseguire il comando di failover dei database tenant in batch di almeno 20 database.

A questo punto l'applicazione è di nuovo online nell'area B. Tutti i clienti della versione a pagamento possono accedere ai propri dati, mentre i clienti della versione di valutazione noteranno un ritardo nell'accesso ai dati.

Al termine del ripristino dell'area A, è necessario decidere se si vuole usare l'area B per i clienti della versione di valutazione o eseguire il failback e usare di nuovo il pool dei clienti della versione di valutazione nell'area A. Un criterio per la decisione potrebbe essere rappresentato dalla percentuale di database tenant della versione di valutazione modificati dopo il ripristino. Indipendentemente da questa decisione, sarà necessario ribilanciare i tenant della versione a pagamento tra i due pool. Il diagramma seguente illustra il processo di failback dei database tenant della versione di valutazione nell'area A.
 
![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Annullare tutte le richieste di ripristino geografico in sospeso verso il pool di ripristino di emergenza della versione di valutazione.   
- Eseguire il failover del database di gestione (8). Dopo il ripristino dell'area, l'elemento primario precedente è diventato automaticamente l'elemento secondario. Ora diventa di nuovo primario.  
- Selezionare i database tenant della versione a pagamento che eseguiranno il failback al pool 1 e avviare il failover negli elementi secondari (9). Dopo il ripristino dell'area, tutti i database nel pool 1 sono diventati automaticamente secondari. Ora il 50% dei database diventa di nuovo primario. 
- Ridurre le dimensioni del pool 2 al valore eDTU originale (10).
- Impostare su sola lettura tutti i database della versione di valutazione ripristinati nell'area B (11).
- Per ogni database nel pool di ripristino di emergenza della versione di valutazione modificato dopo il ripristino, rinominare o eliminare il database corrispondente nel pool primario della versione di valutazione (12). 
- Copiare i database aggiornati dal pool di ripristino di emergenza al pool primario (13). 
- Eliminare il pool di ripristino di emergenza (14). 

Ecco i **vantaggi** principali di questa strategia:

- Supporta il Contratto di servizio più aggressivo per i clienti della versione a pagamento, perché assicura che un'interruzione non possa influire su oltre il 50% dei database tenant. 
- Garantisce che le nuove versioni di valutazione vengano sbloccate non appena viene creato il pool di ripristino di emergenza della versione di valutazione durante il ripristino. 
- Consente un uso più efficiente della capacità del pool, perché il 50% dei database secondari nel pool 1 e nel pool 2 risulta sicuramente meno attiva rispetto ai database primari.

Ecco gli **svantaggi** principali:

- Le operazioni CRUD rispetto ai database di gestione avranno una latenza minore per gli utenti finali connessi all'area A rispetto agli utenti finali connessi all'area B, perché verranno eseguite rispetto ai database di gestione primari.
- Richiede una progettazione più complessa per il database di gestione. Ad esempio, ogni record del tenant deve avere un tag location che deve essere modificato durante il failover e il failback.  
- I clienti della versione a pagamento potrebbero notare prestazioni inferiori al consueto fino al completamento dell'aggiornamento del pool nell'area B. 

## Riepilogo

Questo articolo illustra le strategie di ripristino di emergenza per il livello database usato da un'applicazione multi-tenant ISV SaaS. La scelta della strategia deve essere basata sulle esigenze dell'applicazione, ad esempio il modello aziendale, il contratto di servizio da offrire ai clienti, i vincoli di budget e così via. Ogni strategia descritta illustra i vantaggi e gli svantaggi, per consentire una decisione consapevole. È anche probabile che l'applicazione specifica includa altri componenti di Azure. È quindi necessario esaminare le rispettive indicazioni relative alla continuità aziendale e orchestrare il ripristino del livello database con tali componenti. Per altre informazioni sulla gestione del ripristino di applicazioni di database in Azure, vedere [Progettazione di soluzioni cloud per il ripristino di emergenza](./sql-database-designing-cloud-solutions-for-disaster-recovery.md).

I singoli passaggi necessari per ogni scenario richiedono operazioni su un numero elevato di database. È consigliabile usare i processi elastici del database SQL per gestire queste operazioni su larga scala. Per altre informazioni, vedere [Gestione dei database cloud con scalabilità orizzontale](./sql-database-elastic-jobs-overview.md). Le pagine seguenti consentono di ottenere informazioni sulle operazioni specifiche necessarie per implementare ogni scenario disponibile in questo articolo:

- [Aggiungere un database secondario](https://msdn.microsoft.com/library/azure/mt603689.aspx) 
- [Eseguire il failover del database nell'area secondaria](https://msdn.microsoft.com/library/azure/mt619393.aspx)
- [Eseguire il ripristino geografico del database](https://msdn.microsoft.com/library/azure/mt693390.aspx) 
- [Eliminare un database](https://msdn.microsoft.com/library/azure/mt619368.aspx)
- [Copiare un database](https://msdn.microsoft.com/library/azure/mt603644.aspx)

<!---HONumber=AcomDC_0511_2016-->