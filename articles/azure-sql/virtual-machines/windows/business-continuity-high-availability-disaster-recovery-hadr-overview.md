---
title: Disponibilità elevata, ripristino di emergenza, continuità aziendale
description: Informazioni sulle opzioni di disponibilità elevata, ripristino di emergenza (HADR) e continuità aziendale disponibili per SQL Server nelle macchine virtuali di Azure, ad esempio Gruppi di disponibilità Always On, istanza del cluster di failover, mirroring del database, log shipping e backup & ripristino in archiviazione di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 194c6a5cead400e1bac78ba42cb7238b64bd3b7b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327475"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Continuità aziendale e HADR per la SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

La continuità aziendale significa continuare l'attività in caso di emergenza, pianificare il ripristino e garantire la disponibilità elevata dei dati. SQL Server in macchine virtuali di Azure può ridurre il costo di una soluzione di database a disponibilità elevata e ripristino di emergenza (HADR). 

La maggior parte delle soluzioni SQL Server HADR è supportata nelle macchine virtuali (VM), sia come soluzioni ibride che solo Azure. In una soluzione solo Azure l'intero sistema HADR viene eseguito in Azure. In una configurazione ibrida parte della soluzione viene eseguita in Azure e l'altra parte viene eseguita localmente nell'organizzazione. La flessibilità dell'ambiente Azure consente di passare ad Azure in parte o completamente per rispondere ai requisiti HADR e di budget dei sistemi di database di SQL Server.

Questo articolo mette a confronto e contrasta le soluzioni di continuità aziendale disponibili per SQL Server nelle VM di Azure. 

## <a name="overview"></a>Panoramica

È necessario assicurarsi che il sistema di database disponga delle funzionalità HADR richieste dal contratto di servizio (SLA). Il fatto che Azure fornisca meccanismi di disponibilità elevata, ad esempio la riparazione dei servizi cloud e il rilevamento del ripristino da errore per le macchine virtuali, non garantisce che sia possibile soddisfare il contratto di servizio. Sebbene questi meccanismi proteggano la disponibilità elevata della macchina virtuale, non proteggono la disponibilità di SQL Server in esecuzione all'interno della VM. 

È possibile che l'istanza di SQL Server abbia esito negativo mentre la macchina virtuale è online e integra. Anche i meccanismi di disponibilità elevata forniti da Azure consentono tempi di inattività delle macchine virtuali a causa di eventi quali il ripristino da errori software o hardware e gli aggiornamenti del sistema operativo.

L'archiviazione con ridondanza geografica (GRS) in Azure viene implementata con una funzionalità denominata replica geografica. GRS potrebbe non essere una soluzione di ripristino di emergenza adeguata per i database. Poiché la replica geografica invia dati in modo asincrono, è possibile che gli aggiornamenti recenti vadano persi in caso di emergenza. Altre informazioni sulle limitazioni della replica geografica sono descritte nella sezione [supporto della replica geografica](#geo-replication-support) .

## <a name="deployment-architectures"></a>Architetture di distribuzione
Azure supporta le tecnologie SQL Server per la continuità aziendale:

* [Gruppi di disponibilità AlwaysOn](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Istanze del cluster di failover Always On (failover)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Log shipping](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server backup e ripristino con l'archiviazione BLOB di Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Mirroring del database](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -deprecato in SQL Server 2016

È possibile combinare le tecnologie per implementare una soluzione SQL Server con funzionalità di disponibilità elevata e di ripristino di emergenza. A seconda della tecnologia usata, una distribuzione ibrida potrebbe richiedere un tunnel VPN con la rete virtuale di Azure. Le sezioni seguenti illustrano alcune architetture di distribuzione di esempio.

## <a name="azure-only-high-availability-solutions"></a>Solo Azure: soluzioni a disponibilità elevata

È possibile disporre di una soluzione a disponibilità elevata per SQL Server a livello di database con Gruppi di disponibilità Always On. È inoltre possibile creare una soluzione a disponibilità elevata a livello di istanza con Always On istanze del cluster di failover. Per una protezione aggiuntiva, è possibile creare ridondanza a entrambi i livelli creando gruppi di disponibilità nelle istanze del cluster di failover. 

| Tecnologia | Architetture di esempio |
| --- | --- |
| **Gruppi di disponibilità** |Le repliche di disponibilità in esecuzione nelle macchine virtuali di Azure nella stessa area assicurano disponibilità elevata. È necessario configurare una macchina virtuale da usare come controller di dominio perché il servizio Clustering di failover di Windows richiede un dominio di Active Directory.<br/><br/> Per una maggiore ridondanza e disponibilità, le macchine virtuali di Azure possono essere distribuite in diverse [zone di disponibilità](../../../availability-zones/az-overview.md) , come illustrato nella panoramica del [gruppo di disponibilità](availability-group-overview.md). Se le macchine virtuali SQL Server in un gruppo di disponibilità vengono distribuite in zone di disponibilità, usare [Azure Load Balancer standard](../../../load-balancer/load-balancer-overview.md) per il listener, come documentato nell'interfaccia della riga di comando della [VM SQL di Azure](./availability-group-az-commandline-configure.md) e nei [modelli di avvio rapido di Azure](availability-group-quickstart-template-configure.md) .<br/> ![Diagramma che mostra il "controller di dominio" sopra il "cluster WSFC" composto dalla "replica primaria", "replica secondaria" e "condivisione file di controllo".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Per altre informazioni, vedere [Configurare manualmente un gruppo di disponibilità Always On nelle macchine virtuali di Azure tramite Resource Manager](./availability-group-quickstart-template-configure.md). |
| **Istanze del cluster di failover** |Le istanze del cluster di failover sono supportate in macchine virtuali SQL Server. Poiché la funzionalità FCI richiede l'archiviazione condivisa, cinque soluzioni funzioneranno con SQL Server nelle macchine virtuali di Azure: <br/><br/> -Uso di [dischi condivisi di Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) per Windows Server 2019. Managed disks condiviso è un prodotto di Azure che consente il fissaggio simultaneo di un disco gestito a più macchine virtuali. Le macchine virtuali nel cluster possono leggere o scrivere sul disco collegato in base alla prenotazione scelta dall'applicazione in cluster tramite le prenotazioni permanenti SCSI (SCSI PR). La richiesta pull SCSI è una soluzione di archiviazione standard del settore usata dalle applicazioni in esecuzione in una rete di archiviazione (SAN) in locale. L'abilitazione della richiesta pull SCSI in un disco gestito consente di eseguire la migrazione di queste applicazioni in Azure così come sono. <br/><br/>-Uso [di \( spazi di archiviazione diretta \) S2D](failover-cluster-instance-storage-spaces-direct-manually-configure.md) per fornire una San virtuale basata su software per Windows Server 2016 e versioni successive.<br/><br/>-Uso di una [condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md) per Windows Server 2012 e versioni successive. Le condivisioni file Premium sono basate su SSD, hanno una latenza costantemente bassa e sono completamente supportate per l'uso con FCI.<br/><br/>-Uso dell'archiviazione supportata da una soluzione partner per il clustering. Per un esempio specifico che usa il tipo di elemento di datakeeper, vedere il post di Blog sul [clustering di failover e il tipo di Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>-Uso dell'archiviazione a blocchi condivisa per una destinazione iSCSI remota tramite Azure ExpressRoute. Ad esempio, NetApp Private Storage (NPS) espone una destinazione iSCSI tramite ExpressRoute con Equinix a macchine virtuali di Azure.<br/><br/>Per le soluzioni di replica dei dati e archiviazione condivisa dei partner Microsoft, contattare il fornitore in caso di problemi di accesso ai dati durante il failover.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Solo Azure: soluzioni di ripristino di emergenza
È possibile disporre di una soluzione di ripristino di emergenza per i database SQL Server in Azure usando gruppi di disponibilità, mirroring del database o backup e ripristino con i BLOB di archiviazione.

| Tecnologia | Architetture di esempio |
| --- | --- |
| **Gruppi di disponibilità** |Repliche di disponibilità in esecuzione tra più data center nelle macchine virtuali di Azure per il ripristino di emergenza. Questa soluzione tra aree consente di proteggersi da un'interruzione del sito completa. <br/> ![Diagramma che mostra due aree con una "replica primaria" e "replica secondaria" connesse da un "commit asincrono".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>All'interno di un'area, tutte le repliche devono trovarsi all'interno dello stesso servizio cloud e della stessa rete virtuale. Poiché ogni area disporrà di una rete virtuale separata, queste soluzioni richiedono la connettività da rete a rete. Per ulteriori informazioni, vedere [configurare una connessione da rete a rete utilizzando il portale di Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Per istruzioni dettagliate, vedere [configurare un SQL Server Always on gruppo di disponibilità tra diverse aree di Azure](availability-group-manually-configure-multiple-regions.md).|
| **Mirroring del database** |Tutti i server principali, mirror e di controllo in esecuzione in diversi data center per il ripristino di emergenza. È necessario distribuirli usando i certificati del server. Il mirroring del database SQL Server non è supportato per SQL Server 2008 o SQL Server 2008 R2 in una macchina virtuale di Azure. <br/>![Diagramma che mostra il "principale" in un'area connessa al mirror in un'altra area con "prestazioni elevate".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Eseguire il backup e il ripristino con archiviazione BLOB di Azure** |I database di produzione sono stati sottoposti a backup direttamente nell'archiviazione BLOB in un data center diverso per il ripristino di emergenza.<br/>![Diagramma che mostra un "database" in un'area che esegue il backup in "archiviazione BLOB" in un'altra area.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Per altre informazioni, vedere [backup e ripristino per SQL Server in macchine virtuali di Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Eseguire la replica e il failover SQL Server in Azure con Azure Site Recovery** |L'istanza di SQL Server di produzione in un Data Center di Azure è stata replicata direttamente nell'archiviazione di Azure in un Data Center di Azure diverso per il ripristino di emergenza.<br/>![Diagramma che mostra un "database" in un Data Center di Azure che usa "replica di ASR" per il ripristino di emergenza in un altro Data Center. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Per altre informazioni, vedere [Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>IT ibrido: Soluzioni di ripristino di emergenza
È possibile disporre di una soluzione di ripristino di emergenza per i database SQL Server in un ambiente IT ibrido usando gruppi di disponibilità, mirroring del database, log shipping e backup e ripristino con l'archiviazione BLOB di Azure.

| Tecnologia | Architetture di esempio |
| --- | --- |
| **Gruppi di disponibilità** |Alcune repliche di disponibilità in esecuzione nelle macchine virtuali di Azure e altre in esecuzione in locale per il ripristino di emergenza tra siti. Il sito di produzione può essere locale o trovarsi in un data center di Azure.<br/>![Gruppi di disponibilità](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Poiché tutte le repliche di disponibilità devono trovarsi nello stesso cluster di failover, tale cluster deve essere esteso a entrambe le reti (un cluster di failover su più subnet). Questa configurazione richiede una connessione VPN tra Azure e la rete locale.<br/><br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza.|
| **Mirroring del database** |Un partner in esecuzione in una macchina virtuale di Azure e l'altro in esecuzione in locale per il ripristino di emergenza tra siti usando i certificati server. I partner non devono trovarsi nello stesso dominio Active Directory e non è necessaria alcuna connessione VPN.<br/>![Mirroring del database](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Un altro scenario di mirroring del database include un partner in esecuzione in una VM di Azure e l'altro in esecuzione in locale nello stesso dominio di Active Directory per il ripristino di emergenza tra siti. È necessaria una [connessione VPN tra la rete virtuale di Azure e la rete locale](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).<br/><br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza. Il mirroring del database SQL Server non è supportato per SQL Server 2008 o SQL Server 2008 R2 in una macchina virtuale di Azure. |
| **Log shipping** |Un server in esecuzione in una macchina virtuale di Azure e l'altro in esecuzione in locale per il ripristino di emergenza tra siti. Il log shipping dipende dalla condivisione dei file di Windows, pertanto è richiesta una connessione VPN tra la rete virtuale di Azure e la rete locale.<br/>![Log shipping](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Per il corretto ripristino di emergenza dei database, è inoltre opportuno installare un controller di dominio di replica nel sito di ripristino di emergenza. |
| **Eseguire il backup e il ripristino con archiviazione BLOB di Azure** |I database di produzione locali eseguono il backup direttamente nell'archiviazione BLOB di Azure per il ripristino di emergenza.<br/>![Backup e ripristino](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Per altre informazioni, vedere [backup e ripristino per SQL Server in macchine virtuali di Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Eseguire la replica e il failover SQL Server in Azure con Azure Site Recovery** |L'istanza di produzione locale SQL Server replicata direttamente nell'archiviazione di Azure per il ripristino di emergenza.<br/>![Replica con Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Per altre informazioni, vedere [Proteggere SQL Server con il ripristino di emergenza di SQL Server e Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Replica di ripristino di emergenza gratuita in Azure

Se si dispone di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), è possibile implementare piani di ripristino di emergenza ibrido con SQL Server senza sostenere costi di licenza aggiuntivi per l'istanza di ripristino di emergenza passiva.

Nell'immagine seguente il programma di installazione usa SQL Server in esecuzione in una macchina virtuale di Azure che usa 12 core come replica di ripristino di emergenza per una distribuzione di SQL Server locale che usa 12 core. In passato, è necessario concedere in licenza 12 core di SQL Server per la distribuzione locale e la distribuzione di macchine virtuali di Azure. Il nuovo vantaggio offre vantaggi della replica passiva per l'esecuzione in una macchina virtuale di Azure. A questo punto è necessario concedere in licenza solo 12 core di SQL Server eseguiti in locale, purché siano soddisfatti i criteri di ripristino di emergenza per la replica passiva in macchine virtuali di Azure.

![Replica di ripristino di emergenza gratuita in Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

Per altre informazioni, vedere le [condizioni di licenza per il prodotto](https://www.microsoft.com/licensing/product-licensing/products). 

Per abilitare questo vantaggio, passare alla [risorsa SQL Server macchina virtuale](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Selezionare **Configura** in **Impostazioni**, quindi scegliere l'opzione **ripristino di emergenza** in **SQL Server licenza**. Selezionare la casella di controllo per confermare che questa SQL Server VM verrà usata come replica passiva, quindi selezionare **applica** per salvare le impostazioni. 

![Configurare una replica di ripristino di emergenza in Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerazioni importanti per HADR di SQL Server in Azure
La macchina virtuale, l'archiviazione e la rete di Azure hanno caratteristiche operative diverse rispetto a un'infrastruttura IT locale non virtualizzata. Per una corretta implementazione di una soluzione HADR SQL Server in Azure è necessario comprendere queste differenze e progettare la soluzione per adattarle.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nodi a disponibilità elevata in un set di disponibilità
I set di disponibilità in Azure consentono di collocare i nodi a disponibilità elevata in domini di errore e domini di aggiornamento separati. La piattaforma Azure assegna un dominio di aggiornamento e un dominio di errore a ogni macchina virtuale nel set di disponibilità. Questa configurazione all'interno di un data center garantisce che durante un evento di manutenzione pianificato o non pianificato sia disponibile almeno una macchina virtuale e soddisfi il contratto di sicurezza di Azure del 99,95%. 

Per configurare un'installazione a disponibilità elevata, inserire tutte le macchine virtuali SQL Server coinvolte nello stesso set di disponibilità per evitare perdite di dati o applicazioni durante un evento di manutenzione. Solo i nodi dello stesso servizio cloud possono partecipare allo stesso set di disponibilità. Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali](../../../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nodi a disponibilità elevata in una zona di disponibilità
Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati dagli errori dei data center assicurando che almeno una macchina virtuale sia disponibile e soddisfi il contratto di assistenza di Azure del 99,99%. 

Per configurare la disponibilità elevata, collocare la partecipazione SQL Server macchine virtuali distribuite tra le zone di disponibilità nell'area. Verranno addebitati altri addebiti per i trasferimenti da rete a rete tra le zone di disponibilità. Per altre informazioni, vedere [Zone di disponibilità](../../../availability-zones/az-overview.md). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento del cluster di failover nella rete di Azure
Il servizio DHCP non conforme a RFC in Azure può causare un errore durante la creazione di determinate configurazioni del cluster di failover. Questo errore si verifica perché al nome di rete del cluster viene assegnato un indirizzo IP duplicato, ad esempio lo stesso indirizzo IP di uno dei nodi del cluster. Si tratta di un problema quando si usano i gruppi di disponibilità, che dipendono dalla funzionalità del cluster di failover di Windows.

Si consideri uno scenario in cui viene creato e portato online un cluster a due nodi:

1. Il cluster è online e NODE1 richiede un indirizzo IP assegnato in modo dinamico per il nome di rete del cluster.
2. Il servizio DHCP non fornisce un indirizzo IP diverso da node1, perché il servizio DHCP riconosce che la richiesta deriva da NODE1 stesso.
3. Windows rileva l'assegnazione di un indirizzo duplicato a NODE1 e al nome di rete del cluster di failover e il gruppo cluster predefinito non viene in linea.
4. Il gruppo cluster predefinito passa a NODE2. NODE2 tratta l'indirizzo IP di node1 come indirizzo IP del cluster e porta online il gruppo cluster predefinito.
5. Quando NODE2 tenta di stabilire la connettività con NODE1, i pacchetti indirizzati a NODE1 non lasciano mai NODE2 perché l'indirizzo IP di Node1 viene risolto in se stesso. NODE2 non è in grado di stabilire la connettività con NODE1, quindi perde il quorum e arresta il cluster.
6. NODE1 può inviare pacchetti a NODE2, ma NODE2 non può rispondere. NODE1 perde il quorum e arresta il cluster.

È possibile evitare questo scenario assegnando un indirizzo IP statico inutilizzato al nome di rete del cluster per portare online il nome di rete del cluster. Ad esempio, è possibile usare un indirizzo IP locale del collegamento come 169.254.1.1. Per semplificare il processo, vedere [Configuring Windows failover cluster in Azure for availability groups](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx) (Configurazione del cluster di failover di Windows in Azure per i gruppi di disponibilità).

Per altre informazioni, vedere [Configurare manualmente un gruppo di disponibilità Always On nelle macchine virtuali di Azure tramite Resource Manager](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Supporto per i listener del gruppo di disponibilità
I listener del gruppo di disponibilità sono supportati nelle macchine virtuali di Azure che eseguono Windows Server 2012 e versioni successive. Questo supporto viene fornito attraverso l'uso di endpoint con carico bilanciato abilitato nelle macchine virtuali di Azure che costituiscono nodi del gruppo di disponibilità. È necessario seguire i passaggi di configurazione speciali affinché i listener funzionino sia per le applicazioni client in esecuzione in Azure che per quelli in esecuzione in locale.

Sono disponibili due opzioni principali per la configurazione del listener: esterno (pubblico) o interno. Il listener esterno (pubblico) usa un servizio di bilanciamento del carico con connessione Internet ed è associato a un indirizzo IP virtuale pubblico accessibile tramite Internet. Un listener interno usa un servizio di bilanciamento del carico interno e supporta solo i client all'interno della stessa rete virtuale. Per un tipo di bilanciamento del carico, è necessario abilitare Direct Server Return. 

Se il gruppo di disponibilità si estende su più subnet di Azure, ad esempio una distribuzione che attraversa aree di Azure, la stringa di connessione client deve includere `MultisubnetFailover=True` . Di conseguenza, vengono eseguiti tentativi di connessione paralleli alle repliche nelle diverse subnet. Per istruzioni sulla configurazione di un listener, vedere [configurare un listener ILB per i gruppi di disponibilità in Azure](availability-group-listener-powershell-configure.md).


È comunque possibile connettersi separatamente a ogni replica di disponibilità effettuando la connessione direttamente all'istanza del servizio. Inoltre, poiché i gruppi di disponibilità sono compatibili con le versioni precedenti dei client di mirroring del database, è possibile connettersi alle repliche di disponibilità come partner di mirroring del database purché le repliche siano configurate in modo analogo al mirroring del database:

* Esiste una replica primaria e una replica secondaria.
* La replica secondaria è configurata come non leggibile (opzione **secondaria leggibile** impostata su **No**).

Di seguito è riportato un esempio di stringa di connessione del client che corrisponde a questa configurazione simile al mirroring del database tramite ADO.NET o SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Per altre informazioni sulla connettività client, vedere:

* [Utilizzo delle parole chiave delle stringhe di connessione con SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Connessione di client a una sessione di mirroring del database (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Connessione al listener del gruppo di disponibilità in ambiente IT ibrido](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Listener del gruppo di disponibilità, connettività client e failover dell'applicazione (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Utilizzo delle stringhe di connessione per il mirroring del database con Gruppi di disponibilità](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Latenza di rete in ambiente IT ibrido
Distribuire la soluzione HADR con il presupposto che potrebbero essere presenti periodi di latenza di rete elevata tra la rete locale e Azure. Quando si distribuiscono le repliche in Azure, usare il commit asincrono anziché il commit sincrono per la modalità di sincronizzazione. Quando si distribuiscono server di mirroring del database in locale e in Azure, usare la modalità a prestazioni elevate anziché la modalità a sicurezza elevata.

### <a name="geo-replication-support"></a>Supporto della replica geografica
La replica geografica nei dischi di Azure non supporta l'archiviazione del file di dati e del file di log dello stesso database in dischi separati. L'archiviazione con ridondanza geografica replica le modifiche in ogni disco in modo indipendente e asincrono. Questo meccanismo garantisce l'ordine di scrittura in un disco singolo nella copia replicata geograficamente, ma non in più copie replicate geograficamente di più dischi. Se si configura un database per archiviare il file di dati e il file di log in dischi separati, i dischi recuperati dopo un'emergenza potrebbero contenere una copia più aggiornata del file di dati rispetto al file di log, che interrompe il log write-ahead in SQL Server e le proprietà ACID (atomicità, coerenza, isolamento e durabilità) delle transazioni. 

Se non è possibile disabilitare la replica geografica nell'account di archiviazione, conservare tutti i file di dati e di log per un database nello stesso disco. Se è necessario usare più di un disco a causa delle dimensioni del database, distribuire una delle soluzioni di ripristino di emergenza elencate in precedenza per garantire la ridondanza dei dati.

## <a name="next-steps"></a>Passaggi successivi

Decidere se un [gruppo di disponibilità](availability-group-overview.md) o un' [istanza del cluster di failover](failover-cluster-instance-overview.md) è la migliore soluzione di continuità aziendale per l'azienda. Esaminare quindi le [procedure consigliate](hadr-cluster-best-practices.md) per la configurazione dell'ambiente per la disponibilità elevata e il ripristino di emergenza.