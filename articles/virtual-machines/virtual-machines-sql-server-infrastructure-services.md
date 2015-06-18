<properties 
	pageTitle="SQL Server in Macchine virtuali di Azure" 
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
	ms.date="04/17/2015"
	ms.author="jroth"/>

# SQL Server in Macchine virtuali di Azure

## Panoramica
È possibile ospitare [SQL Server in macchine virtuali Azure][sqlvmlanding] in una vasta gamma di configurazioni, da un singolo server di database a una configurazione con più computer utilizzando i gruppi di disponibilità AlwaysOn e una rete virtuale di Azure.

> [AZURE.NOTE]In esecuzione SQL Server in una macchina virtuale di Azure è un'opzione per l'archiviazione dei dati relazionali in Azure. È inoltre possibile utilizzare il servizio Database SQL Azure. Per ulteriori informazioni, vedere [Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure][sqldbcompared]
 
## Distribuire un'istanza di SQL Server in una singola macchina virtuale
Dopo aver [creare una macchina virtuale di Azure tramite il portale Azure][createvmportal] o di automazione, è possibile installare qualsiasi istanza di SQL Server per cui si dispone di una licenza. Tuttavia, è necessario eseguire passaggi aggiuntivi per [installazione connettività][setupconnectivity] tra il computer SQL Server e altri computer client.
 
È inoltre possibile installare una delle numerose immagini di macchina virtuale di SQL Server diverse dalla raccolta. Tali immagini includono licenze di SQL Server in cui i prezzi per la macchina virtuale. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure][provisionsqlvm].

## Distribuire una configurazione con più macchine virtuali a disponibilità elevata
È possibile ottenere una disponibilità elevata per SQL Server tramite gruppi di disponibilità AlwaysOn di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale Azure Preview dispone di un modello che consente di impostare questa configurazione. Per ulteriori informazioni, vedere [SQL Server AlwaysOn offerta in Microsoft Azure portale Gallery][sqlalwaysonportal]. Oppure è possibile [configurare manualmente un gruppo di disponibilità AlwaysOn][sqlalwaysonmanual]. Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure][sqlhadr].

## Esecuzione di business intelligence, data warehouse e carichi di lavoro OLTP in Azure   
È possibile eseguire i carichi di lavoro comuni di SQL Server in macchine virtuali di Azure. SQL Server sono disponibili diverse immagini di macchina virtuale ottimizzato nella raccolta. Sono incluse le immagini per [Business Intelligence][sqlbi], [Data Warehousing][sqldw], e [OLTP][sqloltp].

## Migrare i dati
Esistono diversi metodi per migrare i dati in macchine virtuali di Azure che esegue SQL Server. Eseguire innanzitutto il provisioning di una macchina virtuale di SQL Server tramite il portale di Azure, automazione di PowerShell o la distribuzione guidata di SQL Server Management Studio. Immagini di SQL Server ottimizzate includono gestione licenze nel modello di tariffazione, ma è inoltre possibile installare SQL Server utilizzando la propria licenza. Per eseguire la migrazione dei dati, sono disponibili diverse opzioni, ad esempio utilizzando la procedura guidata distribuzione o migrazione di un disco dati alla macchina virtuale di destinazione. Per ulteriori informazioni, vedere [Preparazione della migrazione a SQL Server in Macchine virtuali di Azure][migratesql].

## Backup e ripristino
Per i database in locale, Azure può fungere da un centro dati secondario per archiviare i file di backup di SQL Server. [Backup di SQL Server nell'URL][backupurl] archivia i file di backup di Azure nell'archiviazione blob di Azure. [Backup per la gestione di SQL Server][managedbackup] consente di pianificazione di backup e memorizzazione in Azure. Questi servizi sono utilizzabili con istanze di SQL Server locale o SQL Server in esecuzione su macchine virtuali di Azure. Macchine virtuali di Azure possono inoltre sfruttare [Backup automatizzato][autobackup] e [automatizzata patch][autopatching] per SQL Server. Per ulteriori informazioni, vedere [attività di gestione per SQL Server in macchine virtuali Azure][managementtasks].

## Risorse aggiuntive
[SQL Server in una macchina virtuale Azure][sqlmsdnlanding]

[Introduzione a SQL Server in Macchine virtuali di Azure][sqlvmgetstarted]

[Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure￼￼￼￼￼￼￼.￼][sqlperf]

[Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure][sqlsecurity]

[Articoli tecnici relativi a SQL Server in Macchine virtuali di Azure][technicalarticles]

[sqlvmlanding]: http://azure.microsoft.com/services/virtual-machines/sql-server/
[sqldbcompared]: http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas
[createvmportal]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[setupconnectivity]: https://msdn.microsoft.com/library/azure/dn133152.aspx
[provisionsqlvm]: http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/
[sqlalwaysonportal]: http://go.microsoft.com/fwlink/?LinkId=526941
[sqlalwaysonmanual]: https://msdn.microsoft.com/library/azure/dn249504.aspx
[sqlhadr]: https://msdn.microsoft.com/library/azure/jj870962.aspx
[sqlbi]: https://msdn.microsoft.com/library/azure/jj992719.aspx
[sqldw]: https://msdn.microsoft.com/library/azure/dn387396.aspx
[sqloltp]: https://msdn.microsoft.com/library/azure/eb0188e2-5569-48ff-b92c-1f6c0bf79620#about
[migratesql]: https://msdn.microsoft.com/library/azure/dn133142.aspx
[backupurl]: https://msdn.microsoft.com/library/dn435916(v=sql.120).aspx
[managedbackup]: https://msdn.microsoft.com/library/dn449496.aspx
[autobackup]: https://msdn.microsoft.com/library/azure/dn906091.aspx
[autopatching]: https://msdn.microsoft.com/library/azure/dn961166.aspx
[managementtasks]: https://msdn.microsoft.com/library/azure/dn906886.aspx
[sqlmsdnlanding]: https://msdn.microsoft.com/library/azure/jj823132.aspx
[sqlvmgetstarted]: https://msdn.microsoft.com/library/azure/dn133151.aspx
[sqlperf]: https://msdn.microsoft.com/library/azure/dn133149.aspx
[sqlsecurity]: https://msdn.microsoft.com/library/azure/dn133147.aspx
[technicalarticles]: https://msdn.microsoft.com/library/azure/dn248435.aspx
<!--HONumber=52-->
 