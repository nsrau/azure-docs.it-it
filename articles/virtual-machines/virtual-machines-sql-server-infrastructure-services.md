<properties 
	pageTitle="Panoramica di SQL Server in macchine virtuali | Microsoft Azure"
	description="In questo articolo viene fornita una panoramica di SQL Server ospitato in macchine virtuali di Azure. Sono inclusi i collegamenti al contenuto di profondità." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="11/12/2015"
	ms.author="jroth"/>

# Panoramica di SQL Server in Macchine virtuali di Azure

## Introduzione
È possibile ospitare [SQL Server in macchine virtuali Azure](http://azure.microsoft.com/services/virtual-machines/sql-server/) in una vasta gamma di configurazioni, da un singolo server di database a una configurazione con più computer utilizzando i gruppi di disponibilità AlwaysOn e una rete virtuale di Azure.

>[AZURE.NOTE]In esecuzione SQL Server in una macchina virtuale di Azure è un'opzione per l'archiviazione dei dati relazionali in Azure. È inoltre possibile usare il servizio Database SQL Azure. Per altre informazioni, vedere [Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

Per creare una macchina virtuale di SQL Server in Azure, è necessario prima ottenere una sottoscrizione della piattaforma Azure. È possibile acquistare una sottoscrizione di Azure in [Opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/). Per una prova gratuita, visitare la pagina [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

### Distribuire un'istanza di SQL Server in una singola macchina virtuale

Dopo l'iscrizione a una sottoscrizione, il modo più semplice per distribuire una macchina virtuale di SQL Server in Azure consiste nell'[eseguire il provisioning di un'immagine della raccolta di macchine virtuali di SQL Server nel portale di gestione di Azure](virtual-machines-provision-sql-server.md). Tali immagini includono licenze di SQL Server in cui i prezzi per la macchina virtuale.

>[AZURE.NOTE]Utilizzare il [nuovo portale](https://manage.windowsazure.com) per eseguire il provisioning e gestire macchine virtuali di SQL Server. Utilizza l'archiviazione Premium per impostazione predefinita e offre l'applicazione automatizzata di patch, il backup automatizzato e configurazioni AlwaysOn.

La tabella seguente descrive una matrice di immagini di SQL Server disponibili nella raccolta di macchine virtuali.

|Versione di SQL Server|Sistema operativo|Edizione di SQL Server|
|---|---|---|
|SQL Server 2008 R2 SP2|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2008 R2 SP3|Windows Server 2008 R2|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012|Enterprise, Standard, Web|
|SQL Server 2012 SP2|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2014 SP1|Windows Server 2012 R2|Enterprise, Standard, Web|
|SQL Server 2016 CTP|Windows Server 2012 R2|Versione di valutazione|

>[AZURE.NOTE]Le immagini della raccolta di macchine virtuali per data warehouse e carichi di lavoro transazionali (non illustrati di seguito) sono deprecate e presto verranno rimosse dalla raccolta. Utilizzare le immagini standard nella tabella precedente e ottimizzare le prestazioni per il carico di lavoro specifico.

Oltre a queste immagini preconfigurate, è anche possibile [creare una macchina virtuale di Azure](virtual-machines-windows-tutorial.md) senza SQL Server preinstallato. È possibile installare qualsiasi istanza di SQL Server per cui si dispone di una licenza. Si esegue la migrazione della licenza in Azure per l'esecuzione di SQL Server in una macchina virtuale di Azure usando la [mobilità delle licenze tramite Software Assurance in Azure](http://azure.microsoft.com/pricing/license-mobility/). In questo scenario si pagano solo i [costi](http://azure.microsoft.com/pricing/details/virtual-machines) di archiviazione e calcolo di Azure associati alla macchina virtuale.

Per determinare le impostazioni di configurazione migliori della macchina virtuale per l'immagine di SQL Server, esaminare [Procedure ottimali per le prestazioni per SQL Server in macchine virtuali Azure](virtual-machines-sql-server-performance-best-practices.md). Per i carichi di lavoro di produzione, **DS3** è la dimensione minima consigliata della macchina virtuale per SQL Server Enterprise edition e **DS2** è la dimensione minima consigliata della macchina virtuale per l'edizione Standard.

Oltre all'analisi delle procedure ottimali per le prestazioni, altre attività iniziali includono:

- [Esaminare le procedure consigliate relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-security-considerations.md)
- [Configurare la connettività](virtual-machines-sql-server-connectivity.md)

### Migrare i dati

Quando la macchina virtuale SQL Server è attiva e in esecuzione, è possibile eseguire la migrazione di database esistenti alla macchina. Esistono diverse tecniche, ma la procedura guidata per la distribuzione in SQL Server Management Studio è adatta alla maggior parte degli scenari. Per una descrizione degli scenari e un'esercitazione per la procedura guidata, vedere [Migrazione di un database a SQL Server su una VM di Azure](virtual-machines-migrate-onpremises-database.md).

## Disponibilità elevata

Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità AlwaysOn di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale di anteprima di Azure dispone di un modello che consente di impostare questa configurazione. Per ulteriori informazioni, vedere [SQL Server AlwaysOn offerta in Microsoft Azure portale Gallery](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

Se si desidera configurare manualmente il gruppo di disponibilità e il listener associato, vedere gli articoli seguenti:

- [Configurare i gruppi di disponibilità AlwaysOn in Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [Distribuire SQL Server AlwaysOn con un modello di Gestione risorse di Azure](virtual-machines-workload-template-sql-alwayson.md)
- [Estendere i gruppi di disponibilità AlwaysOn locali ad Azure](virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups.md)

Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Backup e ripristino
Per i database in locale, Azure può fungere da un centro dati secondario per archiviare i file di backup di SQL Server. Per una panoramica delle opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-backup-and-restore.md).

[Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx) archivia i file di backup di Azure nell'archiviazione blob di Azure. [Backup per la gestione di SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) consente di pianificazione di backup e memorizzazione in Azure. Questi servizi sono utilizzabili con istanze di SQL Server locale o SQL Server in esecuzione su macchine virtuali di Azure. Macchine virtuali di Azure possono inoltre sfruttare [Backup automatizzato](virtual-machines-sql-server-automated-backup.md) e [automatizzata patch](virtual-machines-sql-server-automated-patching.md) per SQL Server.

## Dettagli di configurazione dell'immagine di macchina virtuale di SQL Server

Le tabelle seguenti descrivono la configurazione delle immagini di macchina virtuale di SQL Server fornite dalla piattaforma.

### Windows Server

L'installazione di Windows Server nell'immagine della piattaforma contiene i componenti e le impostazioni di configurazione seguenti:

|Funzionalità|Configurazione
|---|---|
|Desktop remoto|Abilitato per l'account amministratore|
|Windows Update|Enabled|
|Account utente|Per impostazione predefinita, l'account utente specificato durante il provisioning è membro del gruppo Administrators locale. L'account amministratore è anche membro del ruolo server sysadmin di SQL Server|
|Gruppi di lavoro|La macchina virtuale è membro del gruppo di lavoro denominato GRUPPO DI LAVORO|
|Account Guest|Disabled|
|Windows Firewall con sicurezza avanzata|Attivato|
|.NET Framework|Versione 4|
|Dischi|Le dimensioni selezionate limitano il numero di dischi dati che è possibile configurare. Vedere [Dimensioni delle macchine virtuali per Azure](virtual-machines-size-specs.md)|

### SQL Server

L'installazione di SQL Server nell'immagine della piattaforma contiene i componenti e le impostazioni di configurazione seguenti:

|Funzionalità|Configurazione|
|---|---|
|Motore di database|Installato|
|Analysis Services|Installato|
|Integration Services|Installato|
|Reporting Services|Configurato in modalità nativa|
|Gruppi di disponibilità AlwaysOn|Disponibile in SQL Server 2012 o versione successiva. Richiede [configurazione aggiuntiva](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
|Replica|Installato|
|Estrazioni full-text e semantiche per la ricerca|Installato (estrazioni semantiche solo in SQL Server 2012 o versione successiva)|
|Data Quality Services|Installato (solo SQL Server 2012 o versione successiva)|
|Master Data Services|Installato (solo SQL Server 2012 o versione successiva) Richiede [configurazione e componenti aggiuntivi](https://msdn.microsoft.com/library/ee633752.aspx)
|PowerPivot per SharePoint|Disponibile (solo SQL Server 2012 o versione successiva) Richiede configurazione e componenti aggiuntivi (incluso SharePoint)|
|Client Riesecuzione distribuita|Disponibile (solo SQL Server 2012 o versione successiva), ma non installato. Vedere [Esecuzione dell'installazione di SQL Server dall'immagine di SQL Server fornita dalla piattaforma](#run-sql-server-setup-from-the-platform-provided-sql-server-image)|
|Strumenti|Tutti gli strumenti, inclusi SQL Server Management Studio, Gestione configurazione SQL Server, Business Intelligence Development Studio, il programma di installazione di SQL Server, Connettività strumenti client, SDK di strumenti client, SDK di connettività client SQL e gli strumenti di aggiornamento e migrazione, ad esempio applicazioni livello dati, backup, ripristino, collegamento e scollegamento|
|Documentazione online di SQL Server|Installata, ma richiede la configurazione tramite il visualizzatore della Guida|

### Configurazione del motore di database

Vengono configurate le seguenti impostazioni del motore di database. Per ulteriori impostazioni, esaminare l'istanza di SQL Server.

|Funzionalità|Configurazione|
|---|---|
|Istanza|Contiene un'istanza (senza nome) predefinita del motore di database di Server SQL, in ascolto solo nel protocollo Shared Memory|
|Autenticazione|Per impostazione predefinita, in Azure viene selezionata l'autenticazione di Windows durante l'installazione della macchina virtuale di SQL Server. Per usare l'account di accesso dell'amministratore di sistema o creare un nuovo account di SQL Server, è necessario modificare la modalità di autenticazione. Per altre informazioni, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-security-considerations.md).|
|sysadmin|L'utente che ha installato la macchina virtuale di Azure è inizialmente l'unico membro del ruolo predefinito del server sysadmin di SQL Server|
|Memoria|La memoria del motore di database è impostata sulla configurazione dinamica della memoria|
|Autenticazione del database contenuta|Off|
|Lingua predefinita|English|
|Concatenamento della proprietà tra database|Off|

### Programma Analisi utilizzo software (CEIP)

Il [programma Analisi utilizzo software](https://technet.microsoft.com/library/cc730757.aspx) è abilitato. È possibile disabilitare il programma Analisi utilizzo software tramite l'utilità Segnalazione errori e utilizzo funzionalità di SQL Server. Per avviare l'utilità Segnalazione errori e utilizzo funzionalità di SQL Server, nel menu Start fare clic su Tutti i programmi, Microsoft SQL Server versione, Strumenti di configurazione e infine su Segnalazione errori e utilizzo funzionalità. Se non si desidera usare un'istanza di SQL Server con Analisi utilizzo software abilitato, è anche possibile distribuire un'immagine di macchina virtuale di proprietà in Azure. Per altre informazioni, vedere [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Windows Server](virtual-machines-create-upload-vhd-windows-server.md).

## Eseguire l'installazione di SQL Server dall'immagine di SQL Server fornita dalla piattaforma

Se si crea una macchina virtuale usando un'immagine di SQL Server fornita dalla piattaforma, è possibile trovare i supporti di installazione di SQL Server salvati nella macchina virtuale nella directory **C:\\SqlServer\_SQLMajorVersion.SQLMinorVersion\_Full**. È possibile eseguire il programma di installazione da questa directory per effettuare tutte le azioni di installazione, ad esempio l'aggiunta o la rimozione di funzionalità, l'aggiunta di una nuova istanza o il ripristino dell'istanza, se lo spazio su disco è sufficiente.

>[AZURE.NOTE]Azure offre più versioni di immagini di SQL Server nel portale. Se la data di rilascio della versione dell'immagine di SQL Server fornita dalla piattaforma è 15 maggio 2014 o successiva, contiene il codice Product Key per impostazione predefinita. Se si esegue il provisioning di una macchina virtuale usando un'immagine di SQL Server fornita dalla piattaforma che è stata pubblicata prima di tale data, la macchina virtuale non contiene il codice Product Key. È consigliabile selezionare sempre la versione di immagine più recente per il provisioning di una nuova macchina virtuale.

## Risorse

- [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md)
- [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-migrate-onpremises-database.md)
- [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)
- [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-application-patterns-and-development-strategies.md)
- [Macchine virtuali di Azure](virtual-machines-about.md) 

<!---HONumber=Nov15_HO4-->