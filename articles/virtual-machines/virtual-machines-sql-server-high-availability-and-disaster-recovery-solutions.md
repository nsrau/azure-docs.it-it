<properties 
   pageTitle="Disponibilità elevata e ripristino di emergenza di SQL Server | Microsoft Azure"
   description="Questa esercitazione sfrutta le risorse create con il modello di distribuzione classica e illustra i vari tipi di strategie HADR per SQL Server in esecuzione nelle macchine virtuali di Azure."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" 
   tags="azure-service-management"/>
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/17/2015"
   ms.author="jroth" />

# Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure

## Panoramica

Le macchine virtuali di Microsoft Azure con SQL Server possono consentire di ridurre il costo di una soluzione di database a disponibilità elevata e con ripristino di emergenza (HADR). Molte soluzioni HADR di SQL Server sono supportate nelle macchine virtuali di Azure, sia come soluzioni solo Azure sia come soluzioni ibride. In una soluzione solo Azure l'intero sistema HADR viene eseguito in Azure. In una configurazione ibrida parte della soluzione viene eseguita in Azure e l'altra parte viene eseguita localmente nell'organizzazione. La flessibilità dell'ambiente Azure consente di passare ad Azure in parte o completamente per rispondere ai requisiti HADR e di budget dei sistemi di database di SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione di una risorsa con il modello di distribuzione classica.

## Comprendere la necessità di una soluzione HADR

L'amministratore del database ha il compito di garantire che il sistema di database disponga delle funzionalità HADR richieste dal contratto di servizio. I meccanismi di disponibilità elevata di Azure, ad esempio la riparazione per i servizi cloud e il rilevamento del ripristino da errore per le macchine virtuali, non garantiscono di per sé la soddisfazione dei requisiti del contratto di servizio desiderato. Questi meccanismi proteggono la disponibilità elevata delle macchine virtuali, ma non la disponibilità elevata di SQL Server che viene eseguito nelle macchine virtuali. È possibile che l'istanza di SQL Server restituisca un errore mentre la macchina virtuale è online e integra. Inoltre, anche i meccanismi di disponibilità elevata forniti in Azure non evitano tempi di inattività delle macchine virtuali a causa di eventi quali il ripristino da errori software o hardware e gli aggiornamenti del sistema operativo.

Neppure l'archiviazione con ridondanza geografica di Azure, implementata con una funzionalità denominata replica geografica, costituisce un'adeguata soluzione di ripristino di emergenza per i database. Poiché la replica geografica invia dati in modo asincrono, è possibile che gli aggiornamenti recenti vadano persi in caso di errore grave. Per altre informazioni relative alle limitazioni della replica geografica, consultare la sezione [Replica geografica non supportata per file di dati e log in dischi separati](#geo-replication-support).

## Architetture di distribuzione HADR

Le tecnologie HADR di SQL Server supportate in Azure includono:

- [Gruppi di disponibilità AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
- [Mirroring del database](https://technet.microsoft.com/library/ms189852.aspx)
- [Log shipping](https://technet.microsoft.com/library/ms187103.aspx)
- [Backup e ripristino con il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/jj919148.aspx)

È possibile combinare le tecnologie per implementare una soluzione di SQL Server che disponga di funzionalità di disponibilità elevata e ripristino di emergenza. A seconda della tecnologia in uso, una distribuzione ibrida può richiedere un tunnel VPN con la rete virtuale di Azure. Le sezioni seguenti illustrano alcune architetture di distribuzione di esempio.

## Solo Azure: soluzioni a disponibilità elevata

È possibile disporre di una soluzione a disponibilità elevata per i database di SQL Server in Azure tramite i gruppi di disponibilità AlwaysOn o il mirroring del database.

|Tecnologia|Architetture di esempio|
|---|---|
|**Gruppi di disponibilità AlwaysOn**|Tutte le repliche di disponibilità in esecuzione nelle macchine virtuali di Azure per la disponibilità elevata nella stessa area. È necessario configurare un controller di dominio oltre alle macchine virtuali di SQL Server perché Windows Server Failover Clustering (WSFC) richiede un dominio di Active Directory.<br/> ![Gruppi di disponibilità AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_always_on.gif)<br/>Per altre informazioni, vedere l'articolo relativo alla [configurazione di gruppi di disponibilità AlwaysOn in Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md).|
|**Mirroring del database**|Tutti i server principali, mirror e di controllo in esecuzione nello stesso data center di Azure per la disponibilità elevata. È possibile eseguire la distribuzione usando un controller di dominio.<br/>![Mirroring del database](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring1.gif)<br/>È inoltre possibile distribuire la stessa configurazione di mirroring del database senza un controller di dominio usando in alternativa i certificati del server.<br/>![Mirroring del database](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring2.gif)|

## Solo Azure: soluzioni di ripristino di emergenza

È possibile disporre di una soluzione di ripristino di emergenza per i database di SQL Server in Azure tramite i gruppi di disponibilità AlwaysOn, il mirroring del database o funzionalità di backup e ripristino con i BLOB di archiviazione.

|Tecnologia|Architetture di esempio|
|---|---|
|**Gruppi di disponibilità AlwaysOn**|Repliche di disponibilità in esecuzione tra più data center nelle macchine virtuali di Azure per il ripristino di emergenza. Questa soluzione per più aree protegge dalla completa interruzione dell'alimentazione del sito. <br/> ![Gruppi di disponibilità AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_alwayson.png)<br/>Nell'ambito di un'area tutte le repliche dovranno risiedere nello stesso servizio cloud e nella stessa rete virtuale. Poiché ogni area sarà caratterizzata da una rete virtuale separata, queste soluzioni richiedono la connettività da rete virtuale a rete virtuale. Per altre informazioni, vedere [Creare una rete virtuale con una connessione VPN da sito a sito nel portale di gestione](../vpn-gateway/vpn-gateway-site-to-site-create.md).|
|**Mirroring del database**|Tutti i server principali, mirror e di controllo in esecuzione in diversi data center per il ripristino di emergenza. È necessario eseguire la distribuzione usando certificati del server perché un dominio di Active Directory non può essere esteso a più data center.<br/>![Mirroring del database](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_dbmirroring.gif)|
|**Backup e ripristino con il servizio di archiviazione BLOB di Azure**|Il backup dei database di produzione viene eseguito direttamente nell'archiviazione BLOB in un data center diverso per il ripristino di emergenza.<br/>![Backup e ripristino](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_backup_restore.gif)<br/>Per altre informazioni, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-backup-and-restore.md).|

## IT ibrido: soluzioni di ripristino di emergenza

È possibile disporre di una soluzione di ripristino di emergenza per i database di SQL Server in un ambiente IT ibrido tramite i gruppi di disponibilità AlwaysOn, il mirroring del database, il log shipping e funzionalità di backup e ripristino con l'archiviazione BLOB di Azure.

|Tecnologia|Architetture di esempio|
|---|---|
|**Gruppi di disponibilità AlwaysOn**|Alcune repliche di disponibilità in esecuzione nelle macchine virtuali di Azure e altre in esecuzione in locale per il ripristino di emergenza tra siti. Il sito di produzione può essere locale o trovarsi in un data center di Azure.<br/>![Gruppi di disponibilità AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_alwayson.gif)<br/>Poiché tutte le repliche di disponibilità devono trovarsi nello stesso cluster WSFC, tale cluster deve essere esteso a entrambe le reti (un cluster WSFC su più subnet). Questa configurazione richiede una connessione VPN tra Azure e la rete locale.<br/><br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza.<br/><br/>È possibile usare la procedura guidata Aggiungi replica in SSMS per aggiungere una replica di Azure a un gruppo di disponibilità AlwaysOn esistente. Per altre informazioni, vedere l'esercitazione relativa all'estensione della soluzione Gruppi di disponibilità AlwaysOn ad Azure.|
|**Mirroring del database**|Un partner in esecuzione in una macchina virtuale di Azure e l'altro in esecuzione in locale per il ripristino di emergenza tra siti usando certificati del server. I partner non devono essere nello stesso dominio di Active Directory e non è richiesta alcuna connessione VPN.<br/>![Mirroring del database](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_dbmirroring.gif)<br/>Un altro scenario di mirroring del database include un partner in esecuzione in una VM di Azure e l'altro in esecuzione in locale nello stesso dominio di Active Directory per il ripristino di emergenza tra siti. È richiesta una [connessione VPN tra la rete virtuale di Azure e la rete locale](../vpn-gateway/vpn-gateway-site-to-site-create.md).<br/><br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza.|
|**Log shipping**|Un server in esecuzione in una macchina virtuale di Azure e l'altro in esecuzione in locale per il ripristino di emergenza tra siti. Il log shipping dipende dalla condivisione dei file di Windows, pertanto è richiesta una connessione VPN tra la rete virtuale di Azure e la rete locale.<br/>![Log Shipping](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_log_shipping.gif)<br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza.|
|**Backup e ripristino con il servizio di archiviazione BLOB di Azure**|Il backup dei database di produzione locali viene eseguito direttamente nell'archiviazione BLOB di Azure per il ripristino di emergenza.<br/>![Backup e ripristino](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_backup_restore.gif)<br/>Per altre informazioni, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-backup-and-restore.md).|

## Considerazioni importanti per HADR di SQL Server in Azure

La macchina virtuale, l'archiviazione e la rete di Azure hanno caratteristiche operative diverse rispetto a un'infrastruttura IT locale non virtualizzata. Per una corretta implementazione di una soluzione HADR di SQL Server in Azure è necessario capire queste differenze e progettare una soluzione adeguata.

### Nodi a disponibilità elevata in un set di disponibilità

I set di disponibilità in Azure consentono di collocare i nodi a disponibilità elevata in domini di errore (FD, Fault Domain) e domini di aggiornamento (UD, Update Domain) separati. Per collocare le macchine virtuali di Azure nello stesso set di disponibilità, è necessario distribuirle nello stesso servizio cloud. Solo i nodi dello stesso servizio cloud possono partecipare allo stesso set di disponibilità. Per altre informazioni, vedere [Gestione della disponibilità delle macchine virtuali](virtual-machines-manage-availability.md).

### Comportamento del cluster WSFC nella rete di Azure

Il servizio DHCP non conforme a RFC di Azure può provocare la non riuscita della creazione di determinate configurazioni del cluster WSFC, a causa dell'assegnazione al nome di rete del cluster di un indirizzo IP duplicato (lo stesso indirizzo IP di uno dei nodi del cluster). Ciò costituisce un problema quando si implementano i gruppi di disponibilità AlwaysOn, che dipendono dalla funzionalità WSFC.

Si consideri uno scenario in cui viene creato e portato online un cluster a due nodi:

1. Il cluster è online e NODE1 richiede un indirizzo IP assegnato in modo dinamico per il nome di rete del cluster.

1. Dal servizio DHCP non viene assegnato un indirizzo IP diverso da quello dello stesso NODE1, poiché il servizio DHCP riconosce che la richiesta proviene da NODE1.

1. Windows rileva l'assegnazione di un indirizzo duplicato a NODE1 e al nome di rete del cluster e il gruppo cluster predefinito non può essere portato online.

1. Il gruppo cluster predefinito passa a NODE2, che tratta l'indirizzo IP di NODE1 come l'indirizzo IP del cluster e porta online il gruppo cluster predefinito.

1. Quando NODE2 tenta di stabilire la connessione con NODE1, i pacchetti indirizzati a NODE1 non lasciano mai NODE2 perché l'indirizzo IP di NODE1 viene risolto in se stesso. NODE2 non è in grado di stabilire la connessione con NODE1, quindi perde il quorum e arresta il cluster.

1. Nel frattempo, NODE1 può inviare i pacchetti a NODE2, ma NODE2 non può rispondere. NODE1 perde il quorum e arresta il cluster.

È possibile evitare questo scenario assegnando un indirizzo IP statico inutilizzato, ad esempio un indirizzo IP locale rispetto al collegamento come 169.254.1.1, al nome di rete del cluster per portarlo online. Per semplificare il processo, vedere la pagina relativa alla [configurazione del cluster di failover di Windows in Azure per i gruppi di disponibilità AlwaysOn](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Per altre informazioni, vedere l'articolo relativo alla [configurazione di gruppi di disponibilità AlwaysOn in Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md).

### Supporto del listener del gruppo di disponibilità

I listener del gruppo di disponibilità sono supportati nelle macchine virtuali di Azure che eseguono Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Questo supporto viene fornito attraverso l'uso di endpoint con carico bilanciato con Direct Server Return (DSR) abilitato nelle macchine virtuali di Azure che costituiscono nodi del gruppo di disponibilità. È necessario eseguire passaggi di configurazione specifici affinché i listener funzionino sia per le applicazioni client eseguite in Azure sia per quelle eseguite in locale.

I client devono connettersi al listener da un computer che non si trova nello stesso servizio cloud dei nodi del gruppo di disponibilità AlwaysOn. Se il gruppo di disponibilità si estende su più subnet di Azure (ad esempio una distribuzione che attraversa aree di Azure), la stringa di connessione client deve includere "MultisubnetFailover=True". Di conseguenza, vengono eseguiti tentativi di connessione paralleli alle repliche nelle diverse subnet. Per istruzioni sull'impostazione di un listener, vedere l'articolo relativo alla [configurazione di un listener di ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

È comunque possibile connettersi separatamente a ogni replica di disponibilità effettuando la connessione direttamente all'istanza del servizio. Inoltre, poiché i gruppi di disponibilità AlwaysOn sono compatibili con le versioni precedenti dei client di mirroring del database, è possibile connettersi alle repliche di disponibilità come partner di mirroring del database purché le repliche siano configurate in modo analogo al mirroring del database:

- Una replica primaria e una replica secondaria

- La replica secondaria è configurata come non leggibile (opzione **Secondario leggibile** impostata su **No**)

Di seguito è riportata una stringa di connessione client di esempio corrispondente a questa configurazione simile al mirroring del database usando ADO.NET o SQL Server Native Client:

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Per altre informazioni sulla connettività client, vedere:

- [Utilizzo delle parole chiave delle stringhe di connessione con SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Connessione di client a una sessione di mirroring del database (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Connessione al listener del gruppo di disponibilità in ambiente IT ibrido](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Listener del gruppo di disponibilità, connettività client e failover dell'applicazione (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Utilizzo delle stringhe di connessione per il mirroring del database con Gruppi di disponibilità](https://technet.microsoft.com/library/hh213417.aspx)

### Latenza di rete in ambiente IT ibrido

È opportuno distribuire la soluzione HADR con il presupposto che potrebbero verificarsi lunghi periodi di tempo con latenza di rete elevata tra la rete locale e Azure. Quando si distribuiscono le repliche in Azure, occorre usare il commit asincrono anziché quello sincrono per la modalità di sincronizzazione. Per la distribuzione dei server di mirroring del database in locale e in Azure, usare la modalità a prestazioni elevate anziché la modalità a protezione elevata.

### Supporto della replica geografica

La replica geografica nei dischi di Azure non supporta l'archiviazione del file di dati e del file di log dello stesso database in dischi separati. L'archiviazione con ridondanza geografica replica le modifiche in ogni disco in modo indipendente e asincrono. Questo meccanismo garantisce l'ordine di scrittura in un disco singolo nella copia replicata geograficamente, ma non in più copie replicate geograficamente di più dischi. Se si configura un database per l'archiviazione del file di dati e del file di log in dischi separati, i dischi recuperati dopo un'emergenza potranno contenere una copia più aggiornata del file di dati che del file di log, creando un'interruzione nel log write-ahead in SQL Server e nelle proprietà ACID delle transazioni. Se non è possibile disabilitare la replica geografica nell'account di archiviazione, è consigliabile mantenere tutti i file di dati e i file di log per un determinato database nello stesso disco. Se è necessario usare più dischi a causa delle dimensioni del database, distribuire una delle soluzioni di ripristino di emergenza elencate in precedenza per garantire la ridondanza dei dati.

## Passaggi successivi

Se è necessario creare una macchina virtuale di Azure con SQL Server, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md).

Per ottenere le prestazioni migliori di SQL Server in esecuzione su una VM di Azure, vedere le indicazioni fornite in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

### Altre risorse:

- [Installare una nuova foresta Active Directory in Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Configurare un listener ILB per gruppi di disponibilità AlwaysOn nella macchina virtuale di Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=Sept15_HO4-->