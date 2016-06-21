<properties
	pageTitle="Introduzione a SQL Server in Macchine virtuali di Azure | Microsoft Azure"
	description="Spostare i carichi di lavoro del database di SQL Server locali nel cloud con Macchine virtuali di Azure. Adozione rapida con soluzioni immagini di VM di SQL preconfigurate."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="06/13/2016"
	ms.author="jroth"/>

# Introduzione a SQL Server in Macchine virtuali di Azure

Questo argomento descrive le opzioni per eseguire SQL Server in macchine virtuali di Azure e fornisce le indicazioni e le risorse necessarie per iniziare.

Si pensi a un amministratore del database che vuole spostare i carichi di lavoro di SQL Server locali nel cloud. Oppure a uno sviluppatore che valuta le funzionalità di database relazionale di SQL Server per l'applicazione Azure. Qual è il vantaggio di eseguire i carichi di lavoro di SQL Server nelle macchine virtuali di Azure? Il video seguente illustra i vantaggi e offre una panoramica tecnica.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Valutare i vantaggi

Prima di iniziare, valutare cosa si ottiene usando SQL Server nelle macchine virtuali di Azure.

Se si devono spostare altri carichi di lavoro in Azure, ad esempio un'applicazione aziendale, ha senso spostare in Azure anche i database di SQL Server dipendenti per ottenere migliori prestazioni. Ma l'hosting SQL Server nelle VM di Azure offre anche altri vantaggi, ad esempio l'accesso automatico a più data center per la presenza globale e il ripristino di emergenza. Per un elenco completo di scenari e vantaggi, vedere la [pagina del prodotto SQL Server nelle macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] Quando si valuta SQL Server nelle macchine virtuali, esaminare anche le altre opzioni di archiviazione e di SQL in Azure, ad esempio il [database SQL](../sql-database/sql-database-technical-overview.md), [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) ed [Estensione database di SQL Server](../sql -server-stretch-database/sql-server-stretch-database-overview.md). Per un confronto dettagliato, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

Dopo avere stabilito di eseguire SQL Server nelle VM di Azure, una delle prime decisioni da prendere è se usare un'immagine VM che include i costi di licenza di SQL Server. L'altra opzione prevede l'approccio Bring Your Own License (BYOL) e il pagamento della sola VM. Le due sezioni successive descrivono queste opzioni.

## Opzione 1: Distribuire una VM di SQL (licenza al minuto)
La tabella seguente descrive una matrice di immagini di SQL Server disponibili nella raccolta di macchine virtuali. Fare clic su un collegamento per iniziare a creare una nuova VM di SQL con la versione, l'edizione e il sistema operativo specificati. Tutte le immagini includono i [costi di licenza di SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Istruzioni dettagliate sono disponibili nell'esercitazione [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). Vedere anche [Procedure consigliate per le prestazioni per VM di SQL Server](virtual-machines-windows-sql-performance.md) che illustra come selezionare le dimensioni appropriate per i computer e altre funzionalità disponibili durante il provisioning.

|Versione|Sistema operativo|Edizione|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Opzione 2: Distribuire una VM di SQL (BYOL)
L'altra opzione è rappresentata dallo scenario Bring Your Own License (BYOL). In questo scenario si paga solo per la VM senza altre spese per le licenze di SQL Server. Per usare la propria licenza, usare la matrice di versioni, edizioni e sistemi operativi di SQL Server più sotto. Nel portale i nomi di immagine hanno il prefisso **{BYOL}**.

> [AZURE.IMPORTANT] Per usare le immagini di VM BYOL, è necessario avere un contratto Enterprise con [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). È necessaria anche una licenza valida per la versione/edizione di SQL Server che si vuole usare. Si devono [fornire le informazioni BYOL necessarie a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) entro **10** giorni dal provisioning della VM.

Sono valide le indicazioni dell'[esercitazione sul provisioning](virtual-machines-windows-portal-sql-server-provision.md), ma è necessario usare una delle opzioni per le immagini **BYOL** seguenti. Vedere anche [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md) che illustra come selezionare le dimensioni appropriate per i computer e altre funzionalità disponibili durante il provisioning.

|Versione|Sistema operativo|Edizione|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## Gestire la VM di SQL
Dopo il provisioning della VM di SQL Server, è possibile eseguire diverse attività di gestione facoltative. Per alcuni aspetti, la configurazione e la gestione di SQL Server sono identiche a quelle locali, ma alcune attività sono specifiche di Azure. Le sezioni seguenti evidenziano alcuni di questi aspetti e includono i collegamenti ad altre informazioni.

### Migrare i dati

Se esiste già un database, è possibile spostarlo nella nuova VM di SQL di cui viene effettuato il provisioning. Per le linee guida e un elenco di opzioni per la migrazione, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

### Configurare la disponibilità elevata

Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale di Azure dispone di un modello che consente di impostare questa configurazione. Per altre informazioni, vedere [Configurare un gruppo di disponibilità AlwaysOn in macchine virtuali in Azure Resource Manager (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Per configurare manualmente il gruppo di disponibilità e il listener associato, vedere [Configurare i gruppi di disponibilità AlwaysOn nelle VM di Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure](virtual-machines-windows-sql-high-availability-dr.md).

### Eseguire il backup dei dati
Le VM di Azure possono sfruttare il [backup automatizzato](virtual-machines-windows-sql-automated-backup.md), che crea periodicamente backup del database nell'archivio BLOB. È anche possibile usare questa tecnica manualmente. Per altre informazioni, vedere [Usare Archiviazione di Azure per il backup e il ripristino di SQL Server](../sql-database/storage-use-storage-sql-server-backup-restore.md). Per una panoramica di tutte le opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

### Automatizzare gli aggiornamenti
Le VM di Azure possono usare [Applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md) per pianificare una finestra di manutenzione per l'installazione automatica di importanti aggiornamenti di Windows e SQL Server.

### Analisi utilizzo software
Il programma Analisi utilizzo software è abilitato per impostazione predefinita. Non è un'attività di gestione, a meno che non si voglia disabilitare Analisi utilizzo software dopo il provisioning. È possibile personalizzare o disabilitare Analisi utilizzo software connettendosi alla VM con Desktop remoto. Eseguire quindi l'utilità **Segnalazione errori e utilizzo funzionalità di SQL Server**. Seguire le istruzioni per disabilitare il reporting.

## Passaggi successivi
[Esplorare il percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.

Per altre domande, vedere prima di tutto le [Domande frequenti su SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md). In alternativa, è possibile aggiungere commenti alla fine di uno degli argomenti relativi alle VM di SQL per interagire con Microsoft e la community.

<!---HONumber=AcomDC_0615_2016-->