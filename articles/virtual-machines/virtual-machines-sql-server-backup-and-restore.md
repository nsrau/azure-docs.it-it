<properties 
	pageTitle="Backup e ripristino per SQL Server in Macchine virtuali di Azure"
	description="Vengono descritte considerazioni sul backup e sul ripristino per i database di SQL Server in esecuzione in macchine virtuali di Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />

<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/05/2015"
	ms.author="jroth" />

# Backup e ripristino per SQL Server in Macchine virtuali di Azure

## Panoramica

Il backup dei dati nei database di SQL Server è una parte importante della strategia di protezione contro la perdita di dati a causa di errori dell'utente o dell’applicazione. Questo vale anche per SQL Server in esecuzione nelle macchine virtuali di Azure.

Per SQL Server in esecuzione nelle macchine virtuali di Azure, è possibile utilizzare le tecniche di backup nativo e ripristino utilizzando i dischi collegati per la destinazione dei file di backup. Vi è tuttavia un limite nel numero di dischi che è possibile collegare a una macchina virtuale di Azure, a seconda della [dimensione della macchina virtuale](virtual-machines-size-specs.md). È inoltre da prendere in considerazione l'overhead della gestione disco.

A partire da SQL Server 2014, è possibile effettuare il backup e ripristinare in Archiviazione BLOB di Microsoft Azure. In SQL Server 2016 sono disponibili miglioramenti per questa opzione. Inoltre, per i file di database memorizzati nell'archiviazione BLOB di Microsoft Azure, SQL Server 2016 fornisce un'opzione per eseguire backup quasi istantanei e ripristini rapidi tramite gli snapshot di Azure. In questo articolo viene fornita una panoramica di queste opzioni mentre le informazioni aggiuntive sono disponibili in [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Microsoft Azure] (https://msdn.microsoft.com/library/jj919148(v=sql.130).aspx).

>[AZURE.NOTE]Per una descrizione delle opzioni di backup dei database di grandi dimensioni, vedere [Strategie di backup dei database SQL Server a più terabyte per le macchine virtuali di Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

Nelle sezioni seguenti sono incluse informazioni specifiche per le diverse versioni di SQL Server supportate in una macchina virtuale di Azure.

## Considerazioni sul backup quando i file di database vengono archiviati nel servizio BLOB di Microsoft Azure

I motivi per l'esecuzione dei backup di database e la tecnologia di backup sottostante cambiano quando i file di database vengono archiviati nell'Archiviazione BLOB di Microsoft Azure. Per altre informazioni sulla memorizzazione dei file di database in Archiviazione BLOB di Azure, vedere [File di dati di SQL Server in Azure](https://msdn.microsoft.com/library/jj919148.aspx).

- Non è necessario eseguire il backup di database per garantire la protezione contro errori hardware o di contenuti multimediali perché Microsoft Azure fornisce questo tipo di protezione come parte del servizio Microsoft Azure.

- Occorre comunque eseguire il backup dei database per garantire una protezione dagli errori dell'utente o per scopi di archiviazione, normativi o amministrativi.

- È possibile eseguire i backup quasi istantanei e i ripristini rapidi utilizzando la funzionalità di backup di snapshot di file di SQL Server in Microsoft SQL Server 2016 Community Technology Preview 2 (CTP2). Per altre informazioni, vedere [Backup di snapshot di file di database in Azure](https://msdn.microsoft.com/library/mt169363.aspx).

## Backup e ripristino in Microsoft SQL Server 2016 Community Technology Preview 2 (CTP2)

Microsoft SQL Server 2016 Community Technology Preview 2 (CTP2) supporta la funzionalità di [backup e il ripristino con BLOB di Azure](https://msdn.microsoft.com/library/jj919148.aspx) disponibile in SQL Server 2014 e descritta di seguito. Include anche i seguenti miglioramenti:

- **Striping**: per eseguire il backup nell'archiviazione BLOB di Microsoft Azure, SQL Server 2016 supporta il backup di più BLOB per abilitare il backup dei database di grandi dimensioni, fino a un massimo di 12,8 TB.

- **Backup di snapshot**: tramite l'utilizzo di snapshot di Azure, la funzionalità di backup di snapshot di file di SQL Server fornisce backup quasi istantanei e ripristini rapidi per i file di database archiviati utilizzando il servizio di archiviazione BLOB di Azure. Questa funzionalità consente di semplificare i criteri di backup e ripristino. Backup di snapshot di file supporta anche il ripristino temporizzato. Per altre informazioni, vedere [Backup di snapshot di file di database in Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).

- **Pianificazione del backup gestito**: Il backup gestito di SQL Server in Azure supporta ora le pianificazioni personalizzate. Per altre informazioni, vedere [Backup gestito di SQL Server in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).

>[AZURE.NOTE]Per un'esercitazione delle funzionalità di SQL Server 2016 quando si usa l'archiviazione BLOB di Azure, vedere [Esercitazione: Uso del servizio di archiviazione BLOB di Microsoft Azure con i database di SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## Backup e ripristino in SQL Server 2014

SQL Server 2014 include i miglioramenti seguenti:

1. **Backup e ripristino in Azure**:

 - *Backup di SQL Server nell'URL* ora è supportato in SQL Server Management Studio. L'opzione di backup in Azure è ora disponibile quando si utilizza l'attività di backup o ripristino o la creazione guidata del piano di manutenzione in SQL Server Management Studio. Per altre informazioni, vedere [Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *Backup gestito di SQL Server in Azure* ha nuove funzionalità che consentono la gestione automatizzata dei backup. Ciò è particolarmente utile per l'automazione della gestione dei backup per le istanze di SQL Server 2014 in esecuzione in una macchina di Azure. Per altre informazioni, vedere [Backup gestito di SQL Server in Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Backup automatizzato* fornisce l'automazione aggiuntiva per abilitare automaticamente il *Backup gestito di SQL Server in Azure* per tutti i database nuovi e esistenti per una macchina virtuale di SQL Server in Azure. Per altre informazioni, vedere [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-automated-backup.md).
 - Per una panoramica di tutte le opzioni per il backup di SQL Server 2014 in Azure, vedere [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Crittografia**: SQL Server 2014 supporta la crittografia dei dati durante la creazione di un backup. Supporta diversi algoritmi di crittografia e l'utilizzo di un certificato o una chiave asimmetrica. Per altre informazioni, vedere [Crittografia del backup](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## Backup e ripristino in SQL Server 2012

Per informazioni dettagliate sul backup e il ripristino di SQL Server 2012, vedere [Backup e ripristino di database SQL Server (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

A partire da SQL Server 2012 SP1 Aggiornamento cumulativo 2, è possibile eseguire il backup e il ripristino dal servizio di archiviazione BLOB di Azure. Questa funzionalità avanzata può essere utilizzata per eseguire il backup dei database SQL Server di un server SQL Server in esecuzione in una macchina virtuale di Azure o un'istanza locale. Per altre informazioni, vedere [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Alcuni dei vantaggi dell'utilizzo del servizio di archiviazione BLOB di Azure includono la possibilità di ignorare il limite di 16 dischi per i dischi collegati, la facilità di gestione, la disponibilità diretta del file di backup in un'altra istanza di SQL Server in esecuzione in una macchina virtuale di Azure o in istanze locali per la migrazione o il ripristino di emergenza. Per un elenco completo dei vantaggi derivanti dall'uso di un servizio di archiviazione BLOB di Azure per i backup di SQL Server, vedere la sezione relativa ai *vantaggi* in [Backup e ripristino di SQL Server con il servizio di archiviazione BLOB di Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Per informazioni sulla risoluzione dei problemi e sulle procedure consigliate, vedere [Procedure consigliate di backup e ripristino (servizio di archiviazione BLOB di Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## Backup e ripristino in altre versioni di SQL Server supportate in una macchina virtuale di Azure

Per il backup e ripristino in SQL Server 2008 R2, vedere [Backup e ripristino di database in SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Per il backup e ripristino in SQL Server 2008, vedere [Backup e ripristino di database in SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## Passaggi successivi

Se si prevede comunque di distribuire SQL Server in una macchina virtuale di Azure, è possibile trovare le indicazioni sul provisioning nell'esercitazione seguente: [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md).

Sebbene il backup e il ripristino possano essere utilizzati per la migrazione dei dati, esistono percorsi di SQL Server potenzialmente più semplici per la migrazione dati in una macchina virtuale di Azure. Per una descrizione completa delle opzioni di migrazione e i suggerimenti, vedere [Migrazione di un database a SQL Server in una macchina virtuale di Azure](virtual-machines-migrate-onpremises-database.md).

Esaminare altre [risorse per l'esecuzione di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO7-->