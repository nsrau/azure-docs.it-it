<properties 
	pageTitle="Panoramica di SQL Server in Macchine virtuali di Azure" 
	description="In questo articolo viene fornita una panoramica di SQL Server ospitato in macchine virtuali IaaS di Azure. Sono inclusi i collegamenti al contenuto di profondità." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="08/18/2015"
	ms.author="jroth"/>

# Panoramica di SQL Server in Macchine virtuali di Azure

## Panoramica
È possibile ospitare [SQL Server in macchine virtuali Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) in una vasta gamma di configurazioni, da un singolo server di database a una configurazione con più computer utilizzando i gruppi di disponibilità AlwaysOn e una rete virtuale di Azure. Questo argomento cerca di indirizzare l'utente alle risorse migliori per iniziare a usare SQL Server in una macchina virtuale di Azure.

>[AZURE.NOTE]In esecuzione SQL Server in una macchina virtuale di Azure è un'opzione per l'archiviazione dei dati relazionali in Azure. È inoltre possibile usare il servizio Database SQL Azure. Per altre informazioni, vedere [Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)
 
## Distribuire un'istanza di SQL Server in una singola macchina virtuale

Il modo più semplice per distribuire una macchina virtuale di SQL Server in Azure è [eseguire il provisioning di un'immagine della raccolta della macchina virtuale di SQL Server nel portale di gestione di Azure](virtual-machines-provision-sql-server.md). Tali immagini includono licenze di SQL Server in cui i prezzi per la macchina virtuale.

È tuttavia possibile [creare una macchina virtuale di Azure](virtual-machines-windows-tutorial.md) senza avere preinstallato SQL Server. È possibile installare qualsiasi istanza di SQL Server per cui si dispone di una licenza.

Durante queste fasi iniziali del provisioning e della configurazione, le attività più comuni includono:

- [Esaminare le procedure consigliate relative alle prestazioni per SQL Server in Macchine virtuali di Azure￼￼￼￼￼￼￼.￼](https://msdn.microsoft.com/library/azure/dn133149.aspx)
- [Esaminare le procedure consigliate relative alla sicurezza per SQL Server in Macchine virtuali di Azure￼￼￼￼￼￼￼.￼](https://msdn.microsoft.com/library/azure/dn133147.aspx)
- [Configurare la connettività](virtual-machines-sql-server-connectivity.md)

## Distribuire una configurazione con più macchine virtuali a disponibilità elevata

È possibile ottenere una disponibilità elevata per SQL Server tramite gruppi di disponibilità AlwaysOn di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale Azure Preview dispone di un modello che consente di impostare questa configurazione. Per altre informazioni, vedere il post di blog relativo alle [offerte AlwaysOn di SQL Server nella raccolta del portale di Microsoft Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Se si desidera configurare manualmente il gruppo di disponibilità e il listener associato, vedere gli articoli seguenti:

- [Configurare i gruppi di disponibilità AlwaysOn in Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Estendere i gruppi di disponibilità AlwaysOn locali ad Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Esecuzione di business intelligence, data warehouse e carichi di lavoro OLTP in Azure   
È possibile eseguire i carichi di lavoro comuni di SQL Server in macchine virtuali di Azure. SQL Server sono disponibili diverse immagini di macchina virtuale ottimizzato nella raccolta. Sono incluse immagini per:

- [Business Intelligence](https://msdn.microsoft.com/library/azure/jj992719.aspx)
- [Data Warehouse](https://msdn.microsoft.com/library/azure/dn387396.aspx)
- [OLTP](https://msdn.microsoft.com/library/azure/dn387396.aspx)

## Migrare i dati

Quando la macchina virtuale SQL Server è attiva e in esecuzione, è possibile eseguire la migrazione di database esistenti alla macchina. Esistono diverse tecniche, ma la procedura guidata per la distribuzione in SQL Server Management Studio è adatta alla maggior parte degli scenari. Per una descrizione degli scenari e un'esercitazione della procedura guidata, vedere [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-migrate-onpremises-database.md).

## Backup e ripristino
Per i database in locale, Azure può fungere da un centro dati secondario per archiviare i file di backup di SQL Server. Per una panoramica delle opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-backup-and-restore.md).

[Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) archivia i file di backup di Azure nell'archiviazione blob di Azure. [Backup per la gestione di SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) consente di pianificazione di backup e memorizzazione in Azure. Questi servizi sono utilizzabili con istanze di SQL Server locale o SQL Server in esecuzione su macchine virtuali di Azure. Macchine virtuali di Azure possono inoltre sfruttare [Backup automatizzato](virtual-machines-sql-server-automated-backup.md) e [automatizzata patch](virtual-machines-sql-server-automated-patching.md) per SQL Server.

<!---HONumber=August15_HO8-->