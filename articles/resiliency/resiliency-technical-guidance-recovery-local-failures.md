<properties
   pageTitle="Ripristino da errori locali in Azure: informazioni tecniche | Microsoft Azure"
   description="Articolo incentrato sugli errori locali in Azure per comprendere e progettare applicazioni resilienti a disponibilità elevata e tolleranza di errore e pianificare il ripristino di emergenza."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Informazioni tecniche sulla resilienza di Azure - Ripristino da errori locali in Azure

Esistono due principali minacce alla disponibilità delle applicazioni: l'errore a livello di dispositivi, come unità e server, e l'esaurimento di risorse critiche, come in caso di calcolo in condizioni di picco di carico. Azure offre una combinazione di gestione delle risorse, elasticità, bilanciamento del carico e partizionamento per garantire disponibilità elevata in tali circostanze. Alcune di queste funzionalità vengono eseguite automaticamente per tutti i servizi cloud, mentre in altri casi lo sviluppatore di applicazioni deve completare alcune operazioni aggiuntive per usufruirne.

##Servizi cloud
Tutti i servizi cloud ospitati da Azure sono raccolte di uno o più ruoli Web o di lavoro. Una o più istanze di uno specifico ruolo possono essere eseguite contemporaneamente. Il numero di istanze è determinato dalla configurazione. Le istanze del ruolo vengono monitorate e gestite con un componente denominato controller di infrastruttura. Il controller di infrastruttura rileva gli errori software e hardware e risponde a tali errori automaticamente.

  * Ogni istanza del ruolo viene eseguita in una macchina virtuale (VM) specifica e comunica con il controller di infrastruttura tramite un agente guest. L'agente guest raccoglie le metriche delle risorse e dei nodi, che includono l'uso della VM, lo stato, i log, l'uso delle risorse, le eccezioni e le condizioni di errore. Il controller di infrastruttura interroga l'agente guest a intervalli configurabili e riavvia la VM se l'agente guest non risponde.
  * In caso di errore hardware, il controller di infrastruttura associato sposta tutte le istanze del ruolo interessate in un nuovo nodo hardware e riconfigura la rete per indirizzare il traffico a tale nodo.

Per usufruire di queste funzionalità, gli sviluppatori devono assicurarsi che tutti i ruoli del servizio evitino di archiviare lo stato nelle istanze del ruolo. Tutti i dati persistenti devono essere invece accessibili da una risorsa di archiviazione durevole, come i servizi di archiviazione di Azure o il database SQL di Azure. In questo modo, le richieste possono essere gestite da qualsiasi ruolo. Le istanze del ruolo possono anche diventare inattive in qualsiasi momento senza che si creino incoerenze nello stato temporaneo o permanente del servizio.

Il requisito dell'archiviazione dello stato all'esterno dei ruoli ha diverse implicazioni. Implica, ad esempio, che tutte le modifiche correlate in una tabella di archiviazione di Azure dovrebbero essere eseguite, se possibile, in una singola transazione del gruppo di entità. Naturalmente, non sempre è possibile apportare tutte le modifiche in una singola transazione. È necessario prestare particolare attenzione per assicurare che gli errori delle istanze del ruolo non causino problemi quando determinano l'interruzione di operazioni con esecuzione prolungata che coprono due o più aggiornamenti dello stato permanente del servizio. Se un altro ruolo ritenta un'operazione di questo tipo, deve prevedere e gestire l'eventualità che sia stata parzialmente completata.

In un servizio che partiziona i dati in più archivi, ad esempio, se un ruolo di lavoro diventa inattivo durante la rilocazione di una partizione, la rilocazione potrebbe non essere completa oppure potrebbe essere ripetuta dall'inizio da un diverso ruolo di lavoro causando potenzialmente dati orfani o un danneggiamento dei dati. Per prevenire problemi, è necessario che le operazioni con esecuzione prolungata siano idempotenti (ovvero ripetibili senza effetto collaterale) e/o riavviabili in modo incrementale (ovvero proseguibili dal punto di errore più recente).

  * Per essere idempotente, un'operazione con esecuzione prolungata deve avere lo stesso effetto a prescindere da quante volte viene eseguita, anche in caso di interruzione durante l'esecuzione.
  * Per essere riavviabile in modo incrementale, un'operazione con esecuzione prolungata deve essere costituita da una sequenza di operazioni atomiche più piccole e registrare il proprio stato di avanzamento in una risorsa di archiviazione durevole, in modo che ogni successiva chiamata riprenda dal punto in cui è stata interrotta la precedente.

Tutte le operazioni con esecuzione prolungata, infine, devono essere richiamate ripetutamente fino a quando non vengono completate. Un'operazione di provisioning, ad esempio, potrebbe essere inserita in una coda di Azure ed essere rimossa dalla coda da un ruolo di lavoro solo quando viene completata. Per pulire i dati creati dalle operazioni interrotte potrebbe essere necessaria un'operazione di Garbage Collection.

###Elasticità
Il numero iniziale di istanze eseguite per ogni ruolo è determinato nella configurazione del ruolo. Gli amministratori devono configurare inizialmente i singoli ruoli per l'esecuzione con due o più istanze in base al carico previsto. Le istanze del ruolo, tuttavia, possono essere facilmente aumentate o ridotte in caso di variazione dei modelli d'uso. Questa operazione può essere eseguita manualmente nel portale di Azure oppure essere automatizzata con Windows PowerShell, l'API Gestione dei servizi o strumenti di terze parti. Per informazioni, vedere [Come eseguire la scalabilità automatica di un servizio cloud](../cloud-services/cloud-services-how-to-scale.md).

###Partizionamento
Il controller di infrastruttura di Azure usa due tipi di partizioni: i domini di aggiornamento e i domini di errore.

  * Un dominio di aggiornamento viene usato per aggiornare le istanze del ruolo di un servizio in gruppi. Azure distribuisce le istanze del servizio in più domini di aggiornamento. Per un aggiornamento sul posto, il controller di infrastruttura arresta tutte le istanze in un dominio di aggiornamento, le aggiorna e quindi le riavvia prima di passare al dominio di aggiornamento successivo. Questo approccio impedisce che l'intero servizio risulti non disponibile durante il processo di aggiornamento.
  * Un dominio di errore definisce i potenziali punti di errore hardware o di rete. Per qualsiasi ruolo con più istanze, il controller di infrastruttura assicura che le istanze vengano distribuite in più domini di errore, in modo da impedire che errori hardware isolati interrompano il servizio. L'esposizione a errori del server o del cluster in Azure è interamente controllata dai domini di errore.

In base al [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/), Microsoft garantisce che in caso di distribuzione di due o più istanze di ruoli Web in domini di errore e di aggiornamento diversi sarà disponibile la connessione esterna per almeno il 99,95% del tempo. A differenza dei domini di aggiornamento, non è possibile controllare il numero di domini di errore. Azure esegue automaticamente l'allocazione dei domini di errore e la distribuzione negli stessi delle istanze del ruolo. Per garantire che qualsiasi ruolo con almeno due istanze soddisfi i requisiti del contratto di servizio, almeno le prime due istanze di ogni ruolo vengono inserite in domini di errore e di aggiornamento diversi, come illustrato nella figura seguente:

![Isolamento dei domini di errore (vista semplificata)](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png "Isolamento dei domini di errore - Vista semplificata")

###Bilanciamento del carico
Tutto il traffico in ingresso verso un ruolo Web passa attraverso un servizio di bilanciamento del carico senza stato che distribuisce le richieste client tra le istanze del ruolo. Le singole istanze di ruoli non dispongono di indirizzi IP pubblici e non sono indirizzabili direttamente da Internet. Poiché i ruoli Web sono senza stato, qualsiasi richiesta client può essere indirizzata a qualsiasi istanza del ruolo. Ogni 15 secondi viene generato un evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx), che può essere usato per indicare se il ruolo è pronto per ricevere traffico oppure è occupato e deve essere escluso dalla rotazione del servizio di bilanciamento del carico.

##Macchine virtuali
In relazione alla disponibilità elevata, le macchine virtuali di Azure si differenziano dai ruoli di calcolo PaaS sotto diversi aspetti. In alcuni casi, per garantire disponibilità elevata sono necessarie operazioni aggiuntive.

###Durabilità dei dischi
A differenza delle istanze del ruolo PaaS, i dati archiviati in unità di macchine virtuali sono persistenti anche in caso di rilocazione della macchina virtuale. Le macchine virtuali di Azure usano dischi di VM esistenti come BLOB in Archiviazione di Azure. Grazie alle caratteristiche di disponibilità di Archiviazione di Azure, anche i dati archiviati nelle unità di una macchina virtuale sono a disponibilità elevata. Si noti che l'unità D: rappresenta un'eccezione a questa regola. L'unità D: è di fatto la risorsa di archiviazione fisica del server del rack che ospita la VM e i relativi dati andranno persi in caso di riciclo della VM. L'unità D: è destinata esclusivamente all'archiviazione temporanea.

###Partizionamento
Azure riconosce in modo nativo i livelli in un'applicazione PaaS (ruolo Web e ruolo di lavoro) e può quindi distribuirli correttamente nei domini di errore e di aggiornamento. I livelli in un'applicazione IaaS, invece, devono essere definiti manualmente con i set di disponibilità. I set di disponibilità sono necessari per un contratto di servizio correlato a IaaS.

![Set di disponibilità per Macchine virtuali di Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png "Set di disponibilità per Macchine virtuali di Microsoft Azure")

Nella figura precedente, il livello IIS (Internet Information Server), che funge da livello app Web, e il livello SQL, che funge da livello dati, sono assegnati a set di disponibilità diversi. Ciò garantisce la ridondanza hardware di tutte le istanze di ogni livello, grazie alla relativa distribuzione nei domini di errore, e assicura che non vengano disattivate durante un aggiornamento.

###Bilanciamento del carico
Se il traffico deve essere distribuito tra le VM, è necessario raggruppare le VM di un servizio cloud e bilanciare il carico in uno specifico endpoint TCP o UDP. Per altre informazioni, vedere[Bilanciamento del carico delle macchine virtuali](../virtual-machines/virtual-machines-linux-load-balance.md). Se le VM ricevono input da un'altra origine (ad esempio, un meccanismo di accodamento), non è necessario un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico usa un controllo dell'integrità di base per determinare se il traffico deve essere inviato al nodo. È anche possibile creare probe personalizzati per implementare metriche sull'integrità specifiche dell'applicazione che determinano se la VM deve ricevere traffico.

##Archiviazione
Archiviazione di Azure è il servizio dati durevoli di base per Azure, che offre archiviazione BLOB, tabelle, code e per dischi di VM. Per garantire disponibilità elevata in un singolo data center, usa una combinazione di replica e gestione delle risorse. Il contratto di servizio per la disponibilità di Archiviazione di Azure garantisce che per almeno il 99,9% del tempo le richieste di aggiunta, aggiornamento, lettura ed eliminazione di dati in formato corretto verranno elaborate correttamente e che gli account di archiviazione disporranno della connettività al gateway Internet.

###Replica
La durabilità dei dati di Archiviazione di Azure è agevolata dal mantenimento di più copie di tutti i dati in diverse unità che risiedono in sottosistemi di archiviazione fisica completamente indipendenti all'interno dell'area. I dati vengono replicati in modo sincrono e prima che la scrittura venga confermata viene eseguito il commit di tutte le copie. L'elevato livello di coerenza di Archiviazione di Azure garantisce che le letture riflettano le scritture più recenti. Le copie dei dati, inoltre, vengono continuamente analizzate per rilevare e riparare il decadimento dei bit, che costituisce una minaccia spesso trascurata all'integrità dei dati archiviati. I servizi usufruiscono della replica semplicemente usando Archiviazione di Azure. Non sono necessarie operazioni aggiuntive da parte dello sviluppatore di servizi per il ripristino da un errore locale.

###Gestione delle risorse
Le dimensioni degli account di archiviazione creati dopo il 7 giugno 2012 possono aumentare fino a 200 TB (in precedenza, il massimo era 100 TB). Se è necessario spazio aggiuntivo, le applicazioni devono essere progettate in modo da sfruttare più account di archiviazione.

###Dischi delle macchine virtuali
Un disco di una VM viene archiviato in Archiviazione di Azure come BLOB di pagine, con le stesse caratteristiche di durabilità e scalabilità dell'archiviazione BLOB. Questa progettazione assicura la persistenza dei dati in un disco di una macchina virtuale anche se si verifica un errore nel server che esegue la VM e la VM deve essere riavviata in un altro server.

##Database

###Database SQL
Il database SQL di Microsoft Azure offre un database distribuito come servizio che consente alle applicazioni di effettuare il provisioning di database relazionali, inserirvi dati ed eseguire query con rapidità. Fornisce molte delle familiari funzioni e funzionalità di SQL Server eliminando l'impegno in termini di hardware, configurazione, applicazione di patch e resilienza.

>[AZURE.NOTE]Il database SQL di Azure Database non offre parità di funzionalità 1 a 1 con SQL Server ed è destinato a soddisfare un diverso set di requisiti adatto specificamente alle applicazioni cloud (scalabilità elastica, database distribuito come servizio per ridurre i costi di manutenzione e così via). Per altre informazioni, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

####Replica
Il database SQL di Azure offre resilienza predefinita agli errori a livello di nodo. Tutte le scritture in un database vengono replicate automaticamente in due o più nodi in background con una tecnica di commit del quorum, in base alla quale il nodo primario e almeno uno secondario devono confermare la scrittura dell'attività nel log delle transazioni prima che la transazione venga considerata riuscita e completata. In caso di errore del nodo, viene eseguito automaticamente il failover del database in una delle due repliche secondarie. Questo causa un'interruzione temporanea della connessione per le applicazioni client. Per questo motivo, tutti i client del database SQL di Microsoft Azure devono implementare qualche forma di gestione temporanea della connessione. Per altre informazioni, vedere l'articolo relativo all'[uso del blocco applicazione di gestione degli errori temporanei con SQL Azure](https://msdn.microsoft.com/library/hh680899.aspx).

####Gestione delle risorse
Ogni database, al momento della creazione, viene configurato con un limite massimo di dimensioni. Le dimensioni massime attualmente disponibili sono 150 GB. Quando raggiunge il proprio limite massimo di dimensioni, il database rifiuta i comandi INSERT o UPDATE aggiuntivi (è ancora possibile eseguire query ed eliminare dati).

All'interno di un database, il database SQL di Microsoft Azure usa un'infrastruttura per gestire le risorse. Per rilevare gli errori, tuttavia, usa una topologia ad anello anziché un controller di infrastruttura. Ogni replica in un cluster ha due vicini e la responsabilità di rilevare quando sono inattivi. Quando una replica diventa inattiva, i vicini attivano un agente di riconfigurazione (RA) per ricrearla in un'altra macchina. La limitazione del motore viene fornita per garantire che un server logico non usi troppe risorse in una macchina o ne superi i limiti fisici.

###Elasticità
Se le esigenze dell'applicazione superano il limite di 150 GB del database, deve essere implementato un approccio di aumento delle istanze. Con il database SQL di Microsoft Azure, la scalabilità orizzontale viene eseguita con il partizionamento manuale dei dati (noto anche come partizionamento orizzontale) in più database SQL di Azure. Questo approccio di aumento delle istanze offre l'opportunità di ottenere una crescita proporzionale quasi lineare dei costi. La crescita elastica consente di aumentare la capacità su richiesta con costi incrementali in base alle esigenze, perché i database vengono fatturati in base alle dimensioni effettive medie usate al giorno, non in base alle dimensioni massime possibili.

##SQL Server nelle macchine virtuali
Installando SQL Server in Macchine virtuali di Azure (versione 2014 o successiva), è possibile sfruttare le funzionalità di disponibilità tradizionali di SQL Server, come Gruppi di disponibilità AlwaysOn o il mirroring del database. Si noti che la VM, l'archiviazione e la rete di Azure hanno caratteristiche operative diverse rispetto a un'infrastruttura IT locale non virtualizzata. Per la corretta implementazione di una soluzione di disponibilità elevata/ripristino di emergenza (HADR) di SQL Server in Azure è necessario conoscere queste differenze e progettare una soluzione adeguata.

###Nodi a disponibilità elevata in un set di disponibilità
Quando si implementa una soluzione a disponibilità elevata in Azure, il set di disponibilità in Azure consente di inserire i nodi a disponibilità elevata in domini di errore e di aggiornamento separati. Per maggiore chiarezza, il set di disponibilità è un concetto di Azure. È una procedura consigliata da seguire per assicurarsi che i database siano effettivamente a disponibilità elevata, indipendentemente dal fatto che si usino Gruppi di disponibilità AlwaysOn, il mirroring del database o altro. Se non si segue questa procedura consigliata, si potrebbe erroneamente presumere che il sistema sia a disponibilità elevata mentre in realtà si possono verificare errori in tutti i nodi simultaneamente perché i nodi sono inseriti nello stesso dominio di errore nel data center di Azure. Questa raccomandazione non si applica con il log shipping, perché con questa funzionalità di ripristino di emergenza è consigliabile assicurarsi che i server siano in esecuzione in posizioni dei data center di Azure separate (aree). Per definizione, queste posizioni dei data center sono domini di errore separati.

Per collocare le macchine virtuali di Azure nello stesso set di disponibilità, è necessario distribuirle nello stesso servizio cloud. Solo i nodi dello stesso servizio cloud possono partecipare allo stesso set di disponibilità. Le VM, inoltre, devono far parte della stessa rete virtuale in modo da mantenere i propri IP anche dopo la correzione del servizio, per evitare i tempi di aggiornamento DNS.

###Solo Azure: soluzioni a disponibilità elevata
È possibile disporre di una soluzione a disponibilità elevata per i database di SQL Server in Azure tramite i gruppi di disponibilità AlwaysOn o il mirroring del database.

La figura seguente illustra l'architettura di Gruppi di disponibilità AlwaysOn in esecuzione in macchine virtuali di Azure. Questa figura è tratta dall'articolo di approfondimento sull'argomento [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Gruppi di disponibilità AlwaysOn in Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png "Gruppi di disponibilità AlwaysOn in Microsoft Azure")

È anche possibile effettuare automaticamente il provisioning della distribuzione di un gruppo di disponibilità AlwaysOn end-to-end in VM di Azure usando il modello AlwaysOn nel portale di Microsoft Azure. Per altre informazioni, vedere l'[offerta di SQL Server AlwaysOn nella raccolta del portale di Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

La figura seguente illustra l'uso del mirroring del database nelle macchine virtuali di Azure. Anche questa figura è tratta dall'argomento di approfondimento [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Mirroring del database in Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png "Mirroring del database in Microsoft Azure")

>[AZURE.NOTE]In entrambe le architetture è necessario un controller di dominio. Con il mirroring del database, tuttavia, è possibile usare certificati del server per eliminare la necessità di un controller di dominio.

##Altri servizi della piattaforma Azure
I Servizi cloud di Azure sono basati su Azure e usufruiscono così delle funzionalità della piattaforma precedentemente descritte per il ripristino da errori locali. In alcuni casi, è possibile eseguire azioni specifiche per aumentare la disponibilità per un determinato scenario.

###Bus di servizio
Per attenuare gli effetti di un'interruzione temporanea del bus di servizio di Azure, può essere opportuno creare una coda durevole sul lato client. In questo modo viene usato temporaneamente un meccanismo di archiviazione locale alternativo per archiviare i messaggi che non possono essere aggiunti alla coda del bus di servizio. L'applicazione può decidere come gestire i messaggi archiviati temporaneamente dopo che è stato ripristinato il servizio. Per altre informazioni, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica negoziata del bus di servizio](../service-bus/service-bus-performance-improvements.md). Per altre informazioni, vedere [Bus di servizio](./resiliency-technical-guidance-recovery-loss-azure-region.md#service-bus) (in relazione al ripristino di emergenza).

###Servizi mobili
Esistono due considerazioni sulla disponibilità in relazione a Servizi mobili di Azure. Per prima cosa, eseguire regolarmente il backup del database SQL di Azure associato al servizio mobile. Eseguire il backup anche degli script del servizio mobile. Per altre informazioni, vedere [Ripristinare il servizio mobile in caso di emergenza](../mobile-services/mobile-services-disaster-recovery.md). In caso di interruzione temporanea di Servizi mobili, potrebbe essere necessario usare temporaneamente un data center di Azure alternativo. Per altre informazioni, vedere [Servizi mobili](./resiliency-technical-guidance-recovery-loss-azure-region.md#mobile-services) (in relazione al ripristino di emergenza).

###HDInsight
Per impostazione predefinita, i dati associati a HDInsight vengono archiviati nell'archivio BLOB di Azure, dotato delle proprietà di disponibilità elevata e durabilità specificate da Archiviazione di Azure. L'elaborazione multinodo associata ai processi Hadoop MapReduce viene eseguita in Hadoop Distributed File System (HDFS), di cui HDInsight effettua il provisioning quando necessario. Anche i risultati di un processo MapReduce vengono archiviati per impostazione predefinita nell'archivio BLOB di Azure, quindi i dati elaborati sono durevoli e mantengono la disponibilità elevata dopo il deprovisioning del cluster Hadoop. Per altre informazioni, vedere [HDInsight](./resiliency-technical-guidance-recovery-loss-azure-region.md#hdinsight) (in relazione al ripristino di emergenza).

##Elenchi di controllo: errori locali
 
##Elenco di controllo per i servizi cloud
  1. Esaminare la sezione [Servizi Cloud](#cloud-services) di questo documento
  2. Configurare almeno due istanze per ogni ruolo
  3. Rendere persistente lo stato nella risorsa di archiviazione durevole, non nelle istanze del ruolo
  4. Gestire correttamente l'evento StatusCheck
  5. Eseguire il wrapping in transazioni delle modifiche correlate, quando possibile
  6. Verificare che le attività dei ruoli di lavoro siano idempotenti e riavviabili
  7. Continuare a richiamare le operazioni finché non vengono completate
  8. Prendere in considerazione strategie di scalabilità automatica

##Elenco di controllo per le macchine virtuali
  1. Esaminare la sezione [Macchine virtuali](#virtual-machines) di questo documento
  2. Non usare l'unità D: per l'archiviazione persistente
  3. Raggruppare le macchine di un livello del servizio in un set di disponibilità
  4. Configurare il bilanciamento del carico e probe facoltativi
 
##Elenco di controllo per l'archiviazione
  1. Esaminare la sezione [Archiviazione](#storage) di questo documento
  2. Quando la larghezza di banda o i dati superano le quote, usare più account di archiviazione

##Elenco di controllo per il database SQL
  1. Esaminare la sezione [Database SQL](#sql-database) di questo documento
  2. Implementare un criterio di ripetizione dei tentativi per gestire gli errori temporanei
  3. Usare il partizionamento orizzontale come strategia di aumento delle istanze
  
##Elenco di controllo per SQL Server nelle macchine virtuali
  1. Esaminare la sezione [SQL Server nelle macchine virtuali](#sql-server-on-virtual-machines) di questo documento
  2. Seguire le raccomandazioni precedenti per le macchine virtuali
  3. Usare le funzionalità di disponibilità elevata di SQL Server, come AlwaysOn
  
##Elenco di controllo per il bus di servizio
  1. Esaminare la sezione [Bus di servizio](#service-bus) di questo documento
  2. Prendere in considerazione la creazione di una coda durevole sul lato client come backup

##Elenco di controllo per HDInsight
  1. Esaminare la sezione [HDInsight](#hdinsight) di questo documento
  2. Per gli errori locali non sono necessari passi aggiuntivi relativi alla disponibilità
 
##Passaggi successivi
Questo articolo fa parte di una serie relativa alle [indicazioni tecniche sulla resilienza di Azure](./resiliency-technical-guidance.md). L'articolo successivo della serie riguarda il [ripristino da un'interruzione del servizio a livello di area](./resiliency-technical-guidance-recovery-loss-azure-region.md).

<!---HONumber=AcomDC_0525_2016-->