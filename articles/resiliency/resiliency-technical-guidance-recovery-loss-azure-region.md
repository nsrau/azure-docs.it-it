<properties
   pageTitle="Indicazioni tecniche sulla resilienza per il ripristino dopo la perdita di un'area di Azure | Microsoft Azure"
   description="Articolo sulla progettazione di applicazioni resilienti, a disponibilità elevata e a tolleranza di errore e pianificazione per il ripristino di emergenza"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>


#<a name="azure-resiliency-technical-guidance:-recovery-from-a-region-wide-service-disruption"></a>Materiale sussidiario sulla resilienza di Azure: ripristino dopo un'interruzione di servizio di un'area

Dal punto di vista fisico e logico, Azure è suddiviso in unità chiamate aree. Un'area è costituita da uno o più data center che si trovano in posizioni molto vicine. Al momento della stesura di questo articolo, Azure ha ventiquattro aree nel mondo.

In rari casi è possibile che le strutture di un'intera area diventino inaccessibili, ad esempio a causa di errori di rete. È anche possibile che le strutture vadano completamente perse, ad esempio a causa di una calamità naturale. Questa sezione descrive le funzionalità di Azure per la creazione di applicazioni distribuite tra aree. Questa distribuzione riduce al minimo la possibilità che un errore che si verifica in un'area possa interessarne altre.

##<a name="cloud-services"></a>Servizi cloud

###<a name="resource-management"></a>Resource management

È possibile distribuire le istanze di calcolo tra aree creando un servizio cloud separato in ogni area di destinazione e quindi pubblicando il pacchetto di distribuzione in ogni servizio cloud. Si noti tuttavia che la distribuzione del traffico tra i servizi cloud in diverse aree deve essere implementata dallo sviluppatore dell'applicazione o con un servizio di gestione del traffico.

Determinare il numero di istanze del ruolo di riserva da distribuire in anticipo per il ripristino di emergenza è un aspetto importante della pianificazione della capacità. La presenza di una distribuzione secondaria completa assicura che la capacità sia già disponibile quando necessario; i costi tuttavia raddoppiano. Un modello comune consiste nell'avere una piccola distribuzione secondaria sufficiente a eseguire i servizi critici. Questa piccola distribuzione secondaria è un'opzione consigliabile, sia per la capacità di riserva, sia per verificare la configurazione dell'ambiente secondario.

>[AZURE.NOTE]La quota di sottoscrizione non è una garanzia di capacità. La quota è semplicemente un limite di credito. Per garantire la capacità è necessario definire il numero di ruoli nel modello del servizio e distribuire i ruoli.

###<a name="load-balancing"></a>Bilanciamento del carico

Per bilanciare il carico del traffico tra le aree è necessario usare una soluzione di gestione del traffico. Azure offre [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/). È anche possibile usufruire di servizi di terze parti che offrono funzionalità di gestione del traffico simili.

###<a name="strategies"></a>Strategie

Sono disponibili molte strategie alternative per implementare il calcolo distribuito in aree diverse. Queste strategie devono essere adattate agli specifici requisiti aziendali e ai casi di ciascuna applicazione. Gli approcci possono essere in generale suddivisi nelle categorie seguenti:

  * __Ridistribuire in caso di emergenza__: in questo approccio, l'applicazione viene ridistribuita da zero al momento dell'emergenza. Questo approccio è adatto per applicazioni non critiche che non richiedono un tempo di ripristino garantito.

  * __Warm Spare (attiva/passiva)__: viene creato un servizio ospitato secondario in un'area alternativa e i ruoli vengono distribuiti per garantire la capacità minima; i ruoli non ricevono tuttavia il traffico di produzione. Questo approccio è utile per le applicazioni che non sono state progettate per distribuire il traffico tra le aree.

  * __Riserva a caldo (attiva/attiva)__: l'applicazione è progettata per ricevere il carico di produzione in più aree. I servizi cloud di ogni area possono essere configurati per una capacità superiore a quella richiesta per scopi di ripristino di emergenza. In alternativa è possibile aumentare il numero di istanze dei servizi cloud in base alle esigenze al momento di un'emergenza e di un failover. Questo approccio richiede un investimento sostanziale nella progettazione dell'applicazione, ma presenta vantaggi significativi. I vantaggi includono tempo di ripristino garantito e ridotto, test continuo di tutti i percorsi di ripristino e utilizzo efficiente della capacità.

La descrizione completa della progettazione distribuita non rientra nell'ambito di questo documento. Per altre informazioni vedere [Ripristino di emergenza e disponibilità elevata per le applicazioni di Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Macchine virtuali

Il ripristino delle macchine virtuali dell'infrastruttura distribuita come servizio (IaaS) è simile al ripristino del calcolo delle piattaforme distribuite come servizio (PaaS) sotto molti aspetti. Esistono tuttavia alcune differenze importanti dovute al fatto che una macchina virtuale IaaS è costituita dalla macchina stessa e dal relativo disco.

  * __Usare Backup di Azure per creare backup tra le aree coerenti con l'applicazione__.
  [Backup di Azure](https://azure.microsoft.com/services/backup/) consente ai clienti di creare backup coerenti con l'applicazione di più dischi di macchine virtuali e di supportare la replica del backup tra le aree. Questa operazione viene eseguita optando per la replica geografica dell'insieme di credenziali di backup al momento della creazione. Si noti che la replica dell'insieme di credenziali di backup deve essere configurata al momento della creazione. Non può essere impostata in un secondo momento. In caso di perdita di un'area, Microsoft renderà disponibili i backup ai clienti. I clienti potranno eseguire il ripristino da uno dei punti di ripristino configurati.

  * __Separare i dischi dati dal disco del sistema operativo__. Una considerazione importante per le macchine virtuali IaaS è l'impossibilità di modificare il disco del sistema operativo senza creare di nuovo la macchina virtuale. Ciò non costituisce un problema se la strategia di ripristino prevede la ridistribuzione dopo l'emergenza. Può tuttavia rappresentare un problema se si usa l'approccio Warm Spare per la capacità di riserva. Per implementare correttamente questo approccio è necessario che il disco del sistema operativo sia distribuito sulle località primarie e secondarie e che i dati dell'applicazione siano archiviati in un'unità separata. Usare se possibile una configurazione del sistema operativo standard che possa essere messa a disposizione in entrambe le località. Dopo un failover sarà quindi necessario collegare l'unità di dati alle macchine virtuali IaaS esistenti nel controller di dominio secondario. Usare AzCopy per copiare gli snapshot dei dischi di dati in un sito remoto.

  * __Possono verificarsi problemi di coerenza dopo un failover geografico di più dischi delle macchine virtuali__. I dischi delle macchine virtuali vengono implementati come BLOB di archiviazione di Azure e hanno la stessa caratteristica di replica geografica. A meno che non venga usato [Backup di Azure](https://azure.microsoft.com/services/backup/) , non ci sono garanzie di coerenza tra dischi perché la replica geografica è asincrona e viene eseguita in modo indipendente. I singoli dischi delle macchine virtuali sono garantiti come coerenti per arresto anomalo dopo un failover geografico, ma non come coerenti tra loro. Ciò può causare problemi in alcuni casi, ad esempio in caso di striping del disco.

##<a name="storage"></a>Archiviazione

###<a name="recovery-by-using-geo-redundant-storage-of-blob,-table,-queue-and-vm-disk-storage"></a>Ripristino tramite archiviazione con ridondanza geografica di BLOB, tabelle, code e dischi di VM

In Azure, BLOB, tabelle, code e dischi delle macchine virtuali sono tutti con replica geografica per impostazione predefinita. Questo approccio è denominato archiviazione con ridondanza geografica (GRS). L'archiviazione con ridondanza geografica replica i dati di archiviazione in un data center abbinato a centinaia di chilometri di distanza, in una specifica area geografica. L'archiviazione con ridondanza geografica è progettata per offrire un livello di durabilità aggiuntivo in caso di grave emergenza in un data center. Microsoft determina quando si verifica il failover, che è limitato alle emergenze gravi nelle quali la località primaria originale è ritenuta irrecuperabile in un intervallo di tempo ragionevole. In alcuni scenari può trattarsi di più giorni. I dati vengono in genere replicati entro pochi minuti, anche se l'intervallo di sincronizzazione non è ancora coperto da un contratto di servizio.

In caso di failover geografico, la modalità di accesso all'account rimarrà invariata perché l'URL e la chiave dell'account non cambiano. L'account di archiviazione si troverà tuttavia in un'area diversa dopo il failover. Ciò potrebbe influire sulle applicazioni che richiedono affinità di area con l'account di archiviazione. Anche per i servizi e le applicazioni che non richiedono un account di archiviazione nello stesso data center, i costi di larghezza di banda e latenza tra data center possono costituire un valido motivo per trasferire temporaneamente il traffico all'area di failover. Ciò potrebbe essere un elemento da prendere in considerazione in una strategia globale di ripristino di emergenza.

Oltre al failover automatico offerto dall'archiviazione con ridondanza geografica, Azure ha introdotto un servizio che fornisce l'accesso in lettura alla copia dei dati nella località di archiviazione secondaria. Questo approccio è denominato archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

Per altre informazioni sull'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings:"></a>Mapping delle aree di replica geografica:

È importante comprendere dove viene eseguita la replica geografica dei dati, per sapere dove distribuire le altre istanze dei dati che richiedono affinità di area con l'archiviazione. La tabella seguente descrive le associazioni di località primarie e secondarie:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing:"></a>Prezzi della replica geografica:

La replica geografica è inclusa nel prezzo corrente di Archiviazione di Azure. Si tratta del servizio di archiviazione con ridondanza geografica (GRS). Se non si intende usufruire della replica geografica dei dati è possibile disabilitare questa funzionalità per l'account. Il servizio sarà in questo caso un'archiviazione con ridondanza locale e avrà un prezzo scontato rispetto al servizio GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Determinare se si è verificato un failover geografico

Un eventuale failover geografico verrà indicato nel [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/). Le applicazioni possono tuttavia implementare una modalità automatica per rilevare questo evento monitorando l'area geografica dell'account di archiviazione. Questa funzionalità può essere usata per attivare altre operazioni di ripristino, ad esempio l'attivazione di risorse di calcolo nell'area geografica in cui è stata trasferita l'archiviazione. È possibile eseguire una query a questo scopo da Service Management API usando l'operazione [GET sulle proprietà dell'account di archiviazione](https://msdn.microsoft.com/library/ee460802.aspx). Le proprietà specifiche sono:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Dischi delle macchine virtuali e failover geografico

Come descritto nella sezione sui dischi delle macchine virtuali, la coerenza dei dati tra dischi delle macchine virtuali non viene garantita dopo un failover. Per garantire la correttezza dei backup è opportuno usare un prodotto di backup, ad esempio Data Protection Manager, per eseguire il backup e ripristino dei dati dell'applicazione.

##<a name="database"></a>Database

###<a name="sql-database"></a>Database SQL

Il database SQL di Azure offre due tipi di ripristino: il ripristino geografico e la replica geografica attiva.

####<a name="geo-restore"></a>Ripristino geografico

[Ripristino geografico](../sql-database/sql-database-recovery-using-backups.md#geo-restore) è disponibile anche con i database di livello Basic, Standard e Premium. È l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Analogamente al ripristino temporizzato, il ripristino geografico si basa sui backup di database nell'archiviazione di Azure con ridondanza geografica. Esegue il ripristino della copia di backup con replica geografica ed è quindi resiliente alle interruzioni dell'archiviazione nell'area primaria. Per altre informazioni, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Replica geografica attiva

[Replica geografica attiva](../sql-database/sql-database-geo-replication-overview.md) è disponibile per i database a tutti i livelli. È stata progettata per le applicazioni che hanno requisiti di ripristino più elevati di quelli supportati dal ripristino geografico. Usando la funzionalità di replica geografica attiva, è possibile creare fino a quattro database secondari leggibili su server in diverse aree geografiche. È possibile avviare il failover su qualsiasi database secondario. Inoltre, la replica geografica attiva può essere usata per supportare gli scenari di aggiornamento o riposizionamento dell'applicazione e anche di bilanciamento dei carichi di lavoro di sola lettura. Per informazioni dettagliate, vedere [Configurare la replica geografica](../sql-database/sql-database-geo-replication-portal.md) e l'articolo sul [failover al database secondario](../sql-database/sql-database-geo-replication-failover-portal.md). Per informazioni dettagliate su come progettare e implementare applicazioni e relativi aggiornamenti senza tempi di inattività, vedere [Progettare un'applicazione per il ripristino di emergenza cloud tramite la replica geografica attiva nel database SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL](../sql-database/sql-database-manage-application-rolling-upgrade.md).

###<a name="sql-server-on-virtual-machines"></a>SQL Server nelle macchine virtuali

Sono disponibili numerose opzioni per il ripristino e la disponibilità elevata per SQL Server 2012 e versioni successive in esecuzione in macchine virtuali di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Altri servizi della piattaforma Azure

Quando si prova a eseguire il servizio cloud in più aree di Azure è necessario considerare le implicazioni per ognuna delle dipendenze. Nelle sezioni seguenti, il materiale sussidiario specifico del servizio presuppone che sia necessario usare lo stesso servizio Azure in un data center di Azure alternativo. Ciò comporta attività di configurazione e di replica dei dati.

>[AZURE.NOTE]In alcuni casi, questi passaggi consentono di attenuare un'interruzione specifica del servizio anziché un evento dell'intero data center. Dalla prospettiva dell'applicazione, un'interruzione specifica del servizio potrebbe essere altrettanto limitante e richiedere la migrazione temporanea del servizio in un'altra area di Azure.

###<a name="service-bus"></a>Bus di servizio

Il bus di servizio di Azure usa uno spazio dei nomi univoco che non si estende su più aree di Azure. Il primo requisito consiste quindi nel configurare gli spazi dei nomi del bus di servizio nell'area alternativa. Esistono tuttavia anche considerazioni per la durata dei messaggi in coda. Sono disponibili diverse strategie per la replica dei messaggi tra aree di Azure. Per informazioni dettagliate su queste strategie di replica e altre strategie di ripristino di emergenza, vedere [Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio](../service-bus-messaging/service-bus-outages-disasters.md). Per altre considerazioni sulla disponibilità del bus di servizio, vedere [Bus di servizio](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="web-apps"></a>App Web

Per eseguire la migrazione di un'app Web di Azure in un'area di Azure secondaria è necessario avere un backup del sito Web disponibile per la pubblicazione. Se l'interruzione non interessa l'intero data center di Azure, è possibile usare l'FTP per scaricare un backup recente del contenuto del sito. Creare quindi una nuova app Web nell'area alternativa, se non è già stato fatto per riservare la capacità. Pubblicare il sito nella nuova area e apportare le modifiche di configurazione necessarie. Queste modifiche possono riguardare le stringhe di connessione del database o altre impostazioni specifiche dell'area. Aggiungere se necessario il certificato SSL del sito e modificare il record DNS CNAME in modo che il nome di dominio personalizzato punti all'URL dell'app Web di Azure ridistribuita.

###<a name="mobile-services"></a>Servizi mobili

Nell'area secondaria di Azure creare un servizio mobile di backup per l'applicazione. Ripristinare il database SQL di Azure anche nell'area alternativa. Usare quindi gli strumenti da riga di comando di Azure per trasferire il servizio mobile nell'area alternativa. Configurare quindi il servizio mobile per l'uso del database ripristinato. Per altre informazioni su questo processo, vedere [Ripristinare il servizio mobile in caso di emergenza](../mobile-services/mobile-services-disaster-recovery.md). Per altre considerazioni sulla disponibilità dei servizi mobili, vedere [Servizi mobili](./resiliency-technical-guidance-recovery-local-failures.md#mobile-services).

###<a name="hdinsight"></a>HDInsight

I dati associati a HDInsight vengono archiviati per impostazione predefinita nell'archivio BLOB di Azure. HDInsight richiede che un cluster Hadoop che elabora processi MapReduce si trovi nella stessa area dell'account di archiviazione che contiene i dati analizzati. Se si usa la funzionalità di replica geografica disponibile in Archiviazione di Azure è possibile accedere ai dati nell'area secondaria in cui i dati sono stati replicati, se per qualche motivo l'area primaria non è più disponibile. È possibile creare un nuovo cluster Hadoop nell'area in cui i dati sono stati replicati e continuare l'elaborazione. Per altre considerazioni sulla disponibilità di HDInsight, vedere [HDInsight](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Report SQL

Il ripristino dopo la perdita di un'area di Azure richiede al momento più istanze di report SQL in diverse aree di Azure. Queste istanze di report SQL devono accedere agli stessi dati e questi dati devono disporre di un proprio piano di ripristino in caso di emergenza. È anche possibile mantenere copie di backup esterne del file RDL per ogni report.

###<a name="media-services"></a>Servizi multimediali

Servizi multimediali di Azure ha un approccio di ripristino diverso per la codifica e lo streaming. Lo streaming è in genere più critico durante un'interruzione di area. A tale scopo è necessario disporre di un account di Servizi multimediali in due diverse aree di Azure. Il contenuto codificato deve trovarsi in entrambe le aree. In caso di errore è possibile reindirizzare il traffico di streaming nell'area alternativa. La codifica può essere eseguita in qualsiasi area di Azure. Se i tempi di codifica sono importanti, ad esempio durante l'elaborazione di eventi in tempo reale, è necessario predisporre l'invio di processi a un data center alternativo durante gli errori.

###<a name="virtual-network"></a>Rete virtuale

I file di configurazione offrono il modo più rapido per configurare una rete virtuale in un'area alternativa di Azure. Dopo aver configurato la rete virtuale nell'area primaria di Azure, [esportare le impostazioni di rete virtuale](../virtual-network/virtual-networks-create-vnet-classic-portal.md) della rete corrente in un file di configurazione di rete. In caso di interruzione nell'area primaria, [ripristinare la rete virtuale](../virtual-network/virtual-networks-create-vnet-classic-portal.md) dal file di configurazione archiviato. Configurare quindi altri servizi cloud, macchine virtuali o impostazioni cross-premise per l'uso con la nuova rete virtuale.

##<a name="checklists-for-disaster-recovery"></a>Elenchi di controllo per il ripristino di emergenza

##<a name="cloud-services-checklist"></a>Elenco di controllo per i servizi cloud

  1. Vedere la sezione Servizi cloud di questo documento.
  2. Creare una strategia di ripristino di emergenza tra le aree.
  3. Identificare i compromessi quando si riserva capacità in aree alternative.
  4. Usare strumenti di routing del traffico, ad esempio Gestione traffico di Azure.

##<a name="virtual-machines-checklist"></a>Elenco di controllo per le macchine virtuali

  1. Vedere la sezione Macchine virtuali di questo documento.
  2. Usare [Backup di Azure](https://azure.microsoft.com/services/backup/) per creare backup coerenti con l'applicazione tra le aree.

##<a name="storage-checklist"></a>Elenco di controllo per l'archiviazione

  1. Vedere la sezione Archiviazione di questo documento.
  2. Non disabilitare la replica geografica delle risorse di archiviazione.
  3. Identificare l'area alternativa per la replica geografica in caso di failover.
  4. Creare strategie di backup personalizzate per approcci di failover gestiti dall'utente.

##<a name="sql-database-checklist"></a>Elenco di controllo per il database SQL

  1. Vedere la sezione Database SQL di questo documento.
  2. Usare il [ripristino geografico](../sql-database/sql-database-recovery-using-backups.md#geo-restore) o la [replica geografica](../sql-database/sql-database-geo-replication-overview.md) in base alle esigenze.

##<a name="sql-server-on-virtual-machines-checklist"></a>Elenco di controllo per SQL Server nelle macchine virtuali

  1. Vedere la sezione SQL Server nelle macchine virtuali di questo documento.
  2. Usare i gruppi di disponibilità AlwaysOn tra aree o il mirroring del database.
  3. In alternativa usare backup e ripristino con l'archivio BLOB.

##<a name="service-bus-checklist"></a>Elenco di controllo per il bus di servizio

  1. Vedere la sezione Bus di servizio di questo documento.
  2. Configurare uno spazio dei nomi del bus di servizio in un'area alternativa.
  3. Prendere in considerazione strategie di replica personalizzate per i messaggi tra le aree.

##<a name="web-apps-checklist"></a>Elenco di controllo per app Web

  1. Vedere la sezione App Web di questo documento.
  2. Mantenere backup dei siti Web all'esterno dell'area primaria.
  3. Se l'interruzione è parziale, provare a recuperare il sito corrente con l'FTP.
  4. Pianificare la distribuzione del sito Web su un sito Web nuovo o esistente in un'area alternativa.
  5. Pianificare le modifiche di configurazione per i record di applicazione e DNS CNAME.

##<a name="mobile-services-checklist"></a>Elenco di controllo per i servizi mobili

  1. Vedere la sezione Servizi mobili di questo documento.
  2. Creare un servizio mobile di backup in un'area alternativa.
  3. Gestire i backup del database SQL di Azure associato per il ripristino durante il failover.
  4. Usare gli strumenti da riga di comando di Azure per trasferire il servizio mobile.

##<a name="hdinsight-checklist"></a>Elenco di controllo per HDInsight

  1. Vedere la sezione HDInsight di questo documento.
  2. Creare un nuovo cluster Hadoop nell'area con i dati replicati.

##<a name="sql-reporting-checklist"></a>Elenco di controllo per report SQL

  1. Vedere la sezione Report SQL di questo documento.
  2. Mantenere un'istanza di report SQL alternativa in un'area diversa.
  3. Mantenere un piano separato per replicare la destinazione in quell'area.

##<a name="media-services-checklist"></a>Elenco di controllo per Servizi multimediali

  1. Vedere la sezione Servizi multimediali di questo documento.
  2. Creare un account di Servizi multimediali in un'area alternativa.
  3. Codificare lo stesso contenuto in entrambe le aree per supportare il failover dello streaming.
  4. In caso di interruzione del servizio, inviare i processi di codifica a un'area alternativa.

##<a name="virtual-network-checklist"></a>Elenco di controllo per rete virtuale

  1. Vedere la sezione Rete virtuale di questo documento.
  2. Usare le impostazioni di rete virtuale esportate per creare di nuovo la rete in un'altra area.

##<a name="next-steps"></a>Passaggi successivi

Questo articolo fa parte della serie [Materiale sussidiario sulla resilienza di Azure](./resiliency-technical-guidance.md). Il prossimo articolo di questa serie riguarda il [ripristino da un data center locale ad Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).



<!--HONumber=Oct16_HO2-->


