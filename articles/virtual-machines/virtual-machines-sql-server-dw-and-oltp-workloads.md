<properties 
	pageTitle="Carichi di lavoro transazionali e di data warehousing di SQL Server in Macchine virtuali di Azure"
	description="Descrive le immagini di macchina virtuale di SQL Server preconfigurate e ottimizzate in Azure per carichi di lavoro di data warehousing e OLTP."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# Carichi di lavoro transazionali e di data warehousing di SQL Server in Macchine virtuali di Azure

Per usare SQL Server per carichi di lavoro transazionali e di data warehousing in una macchina virtuale di Azure, è consigliabile usare una delle immagini preconfigurate di macchine virtuali presenti nella raccolta Macchine virtuali di Azure. Queste immagini vengono ottimizzate in base alle raccomandazioni specificate in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md).

Questo articolo descrive l'esecuzione di carichi di lavoro su macchine virtuali di Azure (nota anche come Infrastructure-as-a-Service o IaaS). È possibile eseguire anche carichi di lavoro transazionali e di data warehousing come servizi di Azure. Per altre informazioni, vedere [Documentazione di SQL Data Warehouse](http://azure.microsoft.com/documentation/services/sql-data-warehouse/) e [Database SQL di Azure](http://azure.microsoft.com/documentation/services/sql-database/).

## Quali immagini di macchina virtuale preconfigurate sono disponibili?

Nella raccolta Macchine virtuali di Azure sono disponibili le immagini di macchina virtuale preconfigurate seguenti:

- [SQL Server 2014 Enterprise con ottimizzazione per carichi di lavoro transazionali su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014fortransactionalworkloadswindowsserver2012r2/)

- [SQL Server 2014 Enterprise con ottimizzazione per il data warehousing su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014datawarehousingwindowsserver2012r2/)

- [SQL Server 2012 SP2 Enterprise con ottimizzazione per carichi di lavoro transazionali su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise con ottimizzazione per il data warehousing su Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012r2)

- [SQL Server 2012 SP2 Enterprise con ottimizzazione per carichi di lavoro transazionali su Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2fortransactionalworkloadswindowsserver2012/)

- [SQL Server 2012 SP2 Enterprise con ottimizzazione per il data warehousing su Windows Server 2012](http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2datawarehousingworkloadswindowsserver2012/)

Attualmente queste immagini sono supportate nelle istanze di macchina virtuale che consentono fino a 16 dischi dati collegati per garantire la massima velocità effettiva (o larghezza di banda aggregata). Queste istanze sono A4, A7, A8, A9, D4, D13, D14, F3, G4 e G5 del livello Standard e A4 del livello Basic. Per altre informazioni sulle dimensioni e le opzioni, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-size-specs.md).

>[AZURE.NOTE]Fino a settembre 2014 erano disponibili nella raccolta anche precedenti immagini transazionali e per il data warehousing. Per poter essere usate, tuttavia, queste immagini dovevano essere collegate a dischi dati. È quindi consigliabile usare le nuove immagini sopra riportate, poiché sono pronte per l'uso tramite provisioning.

## Provisioning di una macchina virtuale SQL dalla raccolta usando le nuove immagini transazionali e per il data warehousing

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).

1. Fare clic su **MACCHINA VIRTUALE** nelle voci di menu di Azure nel riquadro sinistro.

1. Fare clic su **NUOVO** nell'angolo in basso a sinistra, quindi fare clic su **CALCOLO**, **MACCHINA VIRTUALE** e **DA RACCOLTA**.

1. Nella pagina di selezione delle immagini di macchina virtuale selezionare una delle edizioni di SQL Server per le immagini transazionali o per il data warehousing.

	![Raccolta Macchine virtuali di Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814362.png)

1. Nella pagina **Configurazione macchina virtuale**, nell'opzione **DIMENSIONE**, scegliere una delle dimensioni supportate.

	![Configurazione della raccolta Macchine virtuali di Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814363.png)

	>[AZURE.NOTE]Attualmente sono supportate solo le dimensioni A4, A7, A8, A9, D4, D13, D14, G3, G4 e G5 del livello Standard e A4 del livello Basic. Eventuali tentativi di eseguire il provisioning di dimensioni di macchina virtuale non supportate avranno esito negativo.

1. Attendere la fine del provisioning. Durante l'attesa, è possibile visualizzare lo stato del provisioning nella pagina delle macchine virtuali (come nella figura seguente). Al termine del provisioning, lo stato sarà **In esecuzione** con un segno di spunta.

	![Stato della raccolta Macchine virtuali di Azure](./media/virtual-machines-sql-server-dw-and-oltp-workloads/IC814364.png)

## Uso di PowerShell per creare immagini transazionali e per il data warehousing

Per creare la macchina virtuale è possibile anche usare il cmdlet **New-AzureQuickVM** di PowerShell. Come parametri, è necessario specificare il nome del servizio cloud, il nome della macchina virtuale, il nome dell'immagine, il nome utente e la password amministratore e altre informazioni simili. Un metodo semplice per ottenere il nome dell'immagine consiste nell'usare **Get-AzureVMImage** per elencare tutte le immagini di macchina virtuale disponibili.

Ad esempio, il comando PowerShell seguente restituisce la più recente immagine corrispondente all'etichetta immagine **SQL Server 2012 SP2 Enterprise con ottimizzazione per il data warehousing su Windows Server 2012 R2** dell'elenco riportato nella sezione precedente.

	(Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads on Windows Server 2012 R2"} | sort PublishedDate -Descending)[0].ImageName

Per altre informazioni sulla creazione di immagini con PowerShell, vedere [Uso di Azure PowerShell per creare e preconfigurare macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Configurazioni specifiche incluse nelle immagini transazionali e per il data warehousing

Le ottimizzazioni incluse nelle immagini si basano su quanto descritto in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-performance-best-practices.md). In particolare, la configurazione di queste immagini include le ottimizzazioni seguenti.

>[AZURE.NOTE]Se si dispone di una licenza personale e si crea una macchina virtuale transazionale o di data warehousing da zero, è possibile eseguire le ottimizzazioni in base all'articolo sulle prestazioni e alle ottimizzazioni di esempio eseguite nelle immagini preconfigurate della raccolta seguenti.

### Configurazioni dei dischi

|Configurazione|Impostazione|
|---|---|
|Numero di dischi dati collegati|15|
|Spazi di archiviazione|Due pool di archiviazione:<br/>1 pool di dati con 12 dischi dati, dimensioni fisse da 12 TB, Colonna = 12<br/>1 pool di log con 3 dischi dati, dimensioni fisse da 3 TB, Colonna = 3<br/><br/>Un disco dati rimanente per l'utente da collegare e di cui determinare l'uso.<br/><br/>**DATA WAREHOUSING**: dimensione striping = 256 KB<br/>**Transazionale**: dimensione striping = 64 KB|
|Dimensione disco|1 TB ciascuno|
|Memorizzazione nella cache|HostCache = nessuna|
|Dimensioni allocazione|Dimensioni delle unità di allocazione NTFS = 64 KB|

### Configurazioni di SQL Server

|Configurazione|Impostazione|
|---|---|
|Parametri di avvio|-T1117 per mantenere le stesse dimensioni dei file di dati se è necessario l'aumento automatico delle dimensioni del database<br/><br/>-T1118 per supportare la scalabilità di tempdb. Per altre informazioni, vedere l'articolo relativo all'[uso del flag di traccia 1118 (-T1118) di SQL Server (2005 e 2008)](http://blogs.msdn.com/b/psssql/archive/2008/12/17/sql-server-2005-and-2008-trace-flag-1118-t1118-usage.aspx?WT.mc_id=Blog_SQL_Announce_Announce).|
|Modalità di ripristino|**DATA WAREHOUSING**: impostare su SIMPLE per il database modello usando ALTER DATABASE<br/>**Transazionale**: nessuna modifica|
|Percorsi di configurazione predefiniti|Spostare le directory dei file di traccia e dei log degli errori di SQL Server nei dischi dati|
|Percorsi predefiniti per i database|Database di sistema spostati nei dischi dati.<br/><br/>Il percorso per la creazione di database utente è stato modificato in modo da usare dischi dati.|
|Inizializzazione file immediata|Abilitata|
|Blocco di pagine in memoria|Abilitato (per altre informazioni, vedere [Abilitazione dell'opzione Blocco di pagine in memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).|

## Domande frequenti

- Esiste una differenza di prezzo tra le immagini ottimizzate e quelle non ottimizzate?

	No. Le immagini ottimizzate seguono lo stesso piano tariffario (maggiori dettagli sono disponibili [qui](http://azure.microsoft.com/pricing/details/virtual-machines/)), senza alcun costo aggiuntivo. Alle dimensioni delle istanze di macchina virtuale più grandi viene comunque applicata una tariffa maggiore.

- È necessario tenere presenti altre correzioni per le prestazioni?

	Sì, prendere in considerazione l'applicazione delle correzioni per le prestazioni inerenti a SQL Server:

	- [CORREZIONE: Prestazioni di I/O insufficienti quando si esegue l'operazione di selezione nella tabella temporanea in SQL Server 2012](https://support.microsoft.com/kb/2958012)

	- [CORREZIONE: "I contatori delle prestazioni di SQL Server sono disabilitati" quando si sposta la risorsa di SQL Server in SQL Server 2014](http://support.microsoft.com/kb/2973444)

- Come si trovano altre informazioni su Spazi di archiviazione?

	Per altre informazioni su Spazi di archiviazione, vedere le [domande frequenti su Spazi di archiviazione](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)

- Qual è la differenza tra la nuova immagine per il data warehousing e la precedente?

	L'immagine per il data warehousing precedente richiede l'esecuzione di passaggi aggiuntivi da parte dei clienti, come il collegamento dei dischi dati dopo la creazione della macchina virtuale, mentre la nuova immagine è pronta per l'uso dopo la creazione e di conseguenza è disponibile più rapidamente e ha meno probabilità di errore.

- Se è necessario usare l'immagine per il data warehousing precedente, è possibile accedervi in qualche modo?

	Le immagini di macchina virtuale precedenti sono ancora disponibili, ma non sono direttamente accessibili dalla raccolta. Al contrario, è possibile continuare a usare i cmdlet di PowerShell. Ad esempio, è possibile usare **Get-AzureVMImage** per elencare tutte le immagini e quando si individua l'immagine per il data warehousing precedente in base alla descrizione e alla data di pubblicazione, è possibile usare **New-AzureVM** per eseguire il provisioning.

## Passaggi successivi

Dopo l'installazione in una macchina virtuale con SQL Server, sarà possibile:

- [Eseguire la migrazione dei dati](virtual-machines-migrate-onpremises-database.md)
- [Configurare la connettività](virtual-machines-sql-server-connectivity.md)

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->