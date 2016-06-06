<properties
	pageTitle="Panoramica di SQL Server in macchine virtuali | Microsoft Azure"
	description="Introduzione all'esecuzione di database SQL Server nel cloud in Macchine virtuali di Azure. Questo modello IaaS (Infrastructure-as-a-Service, infrastruttura distribuita come servizio) consente di eseguire il carico di lavoro di SQL Server in Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/25/2016"
	ms.author="jroth"/>

# Panoramica di SQL Server in Macchine virtuali di Azure

[SQL Server in esecuzione in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) consente di ospitare i database di SQL Server nel cloud. È ad esempio possibile eseguire la migrazione di un database locale a una VM di Azure preconfigurata con Windows Server 2012 R2 e SQL Server 2014 Enterprise Edition. Ma ci sono molti altri scenari possibili, come le configurazioni con più computer che supportano la disponibilità elevata o le architetture ibride con connettività alle reti locali.

Per una panoramica completa, vedere il video seguente:

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Soluzioni SQL

In esecuzione SQL Server in una macchina virtuale di Azure è un'opzione per l'archiviazione dei dati relazionali in Azure. La tabella seguente riepiloga le diverse soluzioni.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Soluzione SQL | Descrizione |
|---:|---|---|
|![SQL Server in Macchine virtuali di Azure](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Eseguire SQL Server in Macchine virtuali di Azure. Gestire direttamente la macchina virtuale ed eseguire il database in versioni finali di SQL Server. |
|![Database SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[Database SQL](https://azure.microsoft.com/services/sql-database/)|Usare il servizio di database SQL per accedere al database e ridimensionarlo senza dover gestire l'infrastruttura sottostante.|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/it-IT/services/sql-data-warehouse/)|Usare Azure SQL Data Warehouse per elaborare grandi quantità di dati relazionali e non relazionali. Vengono fornite funzionalità di data warehouse scalabili come servizio.|
|![Estensione database di SQL Server](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[Estensione database di SQL Server](https://azure.microsoft.com/it-IT/services/sql-server-stretch-database/)|Estendere dinamicamente dati transazionali locali da Microsoft SQL Server 2016 ad Azure.|

>[AZURE.NOTE] Per un confronto completo tra VM SQL e il database SQL, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

## Distribuire una VM SQL Server

Per creare una macchina virtuale di SQL Server in Azure, è necessario prima ottenere una sottoscrizione della piattaforma Azure. È possibile acquistare una sottoscrizione di Azure in [Opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/). Per una prova gratuita, visitare la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

Dopo aver eseguito la registrazione per una sottoscrizione, il modo più semplice per distribuire una macchina virtuale SQL Server in Azure consiste nell'[eseguire il provisioning di un'immagine della raccolta di macchine virtuali SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md). Tali immagini includono licenze di SQL Server in cui i prezzi per la macchina virtuale.

È importante notare che esistono due modelli per creare e gestire le macchine virtuali di Azure: il modello classico e Gestione risorse. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per altre informazioni, vedere [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../resource-manager-deployment-model.md). Ogni argomento deve indicare chiaramente il modello di destinazione, a meno che non si applichi a entrambi i modelli, classico e di Resource Manager, come questo articolo.

## Scegliere un'immagine di VM SQL
La tabella seguente descrive una matrice di immagini di SQL Server disponibili nella raccolta di macchine virtuali. Fare clic su uno dei collegamenti nella tabella in base alla versione, all'edizione e al sistema operativo. Fare quindi clic sul pulsante **Crea macchina virtuale** nella pagina del Marketplace.

|Versione di SQL Server|Sistema operativo|Edizione di SQL Server|
|---|---|---|
|**SQL Server 2016 RC**|Windows Server 2012 R2|[Versione di valutazione](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rc3evaluationwindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/), [Standard](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/), [Web](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] Il programma Analisi utilizzo software è abilitato per impostazione predefinita. Se necessario, è possibile personalizzare o disabilitare il programma dopo il provisioning della macchina virtuale. Connettersi alla VM con Desktop remoto ed eseguire l'utilità **Segnalazione errori e utilizzo funzionalità di SQL Server**.

Ulteriore assistenza per la creazione di una nuova VM di SQL? Vedere la procedura dettagliata nell'[esercitazione sul provisioning](virtual-machines-windows-portal-sql-server-provision.md).

Oltre a queste immagini preconfigurate, è anche possibile [creare una macchina virtuale di Azure](virtual-machines-windows-hero-tutorial.md) senza SQL Server preinstallato. È possibile installare qualsiasi istanza di SQL Server per cui si dispone di una licenza. Si esegue la migrazione della licenza in Azure per l'esecuzione di SQL Server in macchine virtuale di Azure usando la [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). In questo scenario si pagano solo i [costi](https://azure.microsoft.com/pricing/details/virtual-machines/) di archiviazione e calcolo di Azure associati alla macchina virtuale.

Per determinare le impostazioni di configurazione migliori della macchina virtuale per l'immagine di SQL Server, esaminare [Procedure consigliate per le prestazioni per le macchine virtuali di SQL Server](virtual-machines-windows-sql-performance.md). Per i carichi di lavoro di produzione, **DS3** è la dimensione minima consigliata della macchina virtuale per SQL Server Enterprise Edition. **DS2** è la dimensione minima consigliata della macchina virtuale per i carichi di lavoro di produzione per la Standard Edition.

## Migrare i dati

Quando la macchina virtuale SQL Server è attiva e in esecuzione, è possibile eseguire la migrazione di database esistenti alla macchina. Per le linee guida e un elenco di opzioni per la migrazione, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

## Disponibilità elevata

Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale di Azure dispone di un modello che consente di impostare questa configurazione. Per altre informazioni, vedere [Configurare un gruppo di disponibilità AlwaysOn in macchine virtuali in Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

Se si desidera configurare manualmente il gruppo di disponibilità e il listener associato, vedere [Configurare i gruppi di disponibilità AlwaysOn nelle VM di Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure](virtual-machines-windows-sql-high-availability-dr.md).

## Backup e ripristino
Per i database in locale, Azure può fungere da un centro dati secondario per archiviare i file di backup di SQL Server. Per una panoramica delle opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

[Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) archivia i file di backup di Azure nell'archiviazione blob di Azure. [Backup per la gestione di SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) consente di pianificazione di backup e memorizzazione in Azure. Questi servizi sono utilizzabili con istanze di SQL Server locale o SQL Server in esecuzione su macchine virtuali di Azure. Macchine virtuali di Azure possono inoltre sfruttare [Backup automatizzato](virtual-machines-windows-classic-sql-automated-backup.md) e [automatizzata patch](virtual-machines-windows-classic-sql-automated-patching.md) per SQL Server.

## Passaggi successivi

Prima di tutto, [creare una VM di SQL Server nel Portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

Esaminare quindi le [procedure consigliate per le prestazioni](virtual-machines-windows-sql-performance.md) e le [tecniche di migrazione](virtual-machines-windows-migrate-sql.md) in relazione allo spostamento dei carichi di lavoro di SQL Server nelle VM di Azure.

Altre domande su SQL Server nelle macchine virtuali di Azure? Innanzitutto, vedere le [Domande frequenti su SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md). In alternativa, è possibile aggiungere commenti alla fine di uno degli argomenti relativi alle VM di SQL per interagire con Microsoft e la community.

<!---HONumber=AcomDC_0525_2016-->