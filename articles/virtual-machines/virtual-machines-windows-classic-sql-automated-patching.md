<properties
	pageTitle="Applicazione automatica delle patch per macchine virtuali SQL Server (distribuzione classica) | Microsoft Azure"
	description="Informazioni sulla funzionalità di applicazione automatica delle patch per macchine virtuali SQL Server in esecuzione in Azure con il modello di distribuzione classica."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/18/2016"
	ms.author="jroth" />

# Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (distribuzione classica)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-sql-automated-patching.md)
- [Classico](virtual-machines-windows-classic-sql-automated-patching.md)

L'applicazione automatica delle patch stabilisce un periodo di manutenzione per una macchina virtuale di Azure su cui è in esecuzione SQL Server. Gli aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, gli aggiornamenti di sistema e i riavvii associati vengono eseguiti nel momento migliore per il database. L'applicazione automatica delle patch dipende dall'[estensione dell'agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse. Per visualizzare la versione Resource Manager di questo articolo, vedere [Automated Patching for SQL Server in Azure Virtual Machines Resource Manager](virtual-machines-windows-sql-automated-patching.md) (Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (Resource Manager)).

## Prerequisiti

Per usare l'applicazione automatica delle patch, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versione di SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Installare i comandi di Azure PowerShell più recenti](../powershell-install-configure.md) se si prevede di configurare l'applicazione automatica delle patch con PowerShell.

>[AZURE.NOTE] L'applicazione automatica delle patch si basa sull'estensione dell'agente IaaS di SQL Server. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Impostazioni

Nella seguente tabella sono descritte le opzioni che possono essere configurate per l'applicazione automatica delle patch. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

|Impostazione|Valori possibili|Descrizione|
|---|---|---|
|**Applicazione automatica delle patch**|Enable/Disable (disabilitato)|Abilita o disabilita l'applicazione automatica delle patch per una macchina virtuale di Azure.|
|**Pianificazione della manutenzione**|Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica|Pianificazione per il download e l'installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale.|
|**Ora di inizio manutenzione**|0-24|Ora di inizio locale per aggiornare la macchina virtuale.|
|**Durata dell'intervallo di manutenzione**|30-180|Numero di minuti consentito per completare il download e l'installazione degli aggiornamenti.|
|**Categoria delle patch**|Importante|Categoria degli aggiornamenti da scaricare e installare.|

## Configurazione nel portale

È possibile usare il [portale di Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) per configurare l'applicazione automatica delle patch quando si crea una nuova macchina virtuale di SQL Server nel modello di distribuzione classica.

La seguente schermata del portale di Azure mostra queste opzioni in **CONFIGURAZIONE FACOLTATIV** | **APPLICAZIONE AUTOMATICA DELLE PATCH DI SQL**.

![Applicazione automatizzata di patch SQL nel portale di Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

Per le macchine virtuali esistenti di SQL Server 2012 o 2014, selezionare le impostazioni relative all’**applicazione automatizzata di patch** nella sezione di **configurazione** delle proprietà della macchina virtuale Nel periodo dell’**applicazione automatizzata di patch**, è possibile abilitare la funzionalità, impostare la pianificazione della manutenzione e l’ora di inizio e scegliere la durata del periodo di manutenzione. Queste impostazioni sono illustrate nella seguente schermata.

![Configurazione dell’applicazione automatizzata di patch nel portale di Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] Quando si abilita l’applicazione automatizzata di patch per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione dell'applicazione automatica delle patch. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure verranno visualizzate le nuove impostazioni.

## Configurazione con PowerShell

È inoltre possibile utilizzare PowerShell per configurare l'applicazione automatizzata di patch.

Nell'esempio seguente, PowerShell viene utilizzato per configurare l'applicazione automatizzata di patch in una macchina virtuale di SQL Server esistente. Il comando **New-AzureVMSqlServerAutoPatchingConfig** configura un nuovo periodo di manutenzione per gli aggiornamenti automatici.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

In base a questo esempio, nella tabella seguente vengono descritti gli effetti pratici sulla macchina virtuale di Azure di destinazione:

|Parametro|Effetto|
|---|---|
|**DayOfWeek**|Patch installate ogni giovedì.|
|**MaintenanceWindowStartingHour**|Inizio degli aggiornamenti alle ore 11:00.|
|**MaintenanceWindowsDuration**|Le patch devono essere installate entro 120 minuti. In base all'ora di inizio, devono essere completate entro le ore 13:00.|
|**PatchCategory**|L'unica impostazione possibile per questo parametro è "Important".|

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

Per disabilitare l’applicazione automatizzata di patch, eseguire lo stesso script senza il parametro -Enable per New-AzureVMSqlServerAutoPatchingConfig. Come per l'installazione, la disabilitazione dell’applicazione automatizzata di patch può richiedere alcuni minuti.

## Passaggi successivi

Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0525_2016-->