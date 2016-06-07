<properties
	pageTitle="Applicazione automatica delle patch per VM SQL Server (Resource Manager) | Microsoft Azure"
	description="Viene illustrata la funzionalità di applicazione automatica delle patch per le macchine virtuali di SQL Server in esecuzione in Azure che usano Resource Manager."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/18/2016"
	ms.author="jroth" />

# Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-sql-automated-patching.md)
- [Classico](virtual-machines-windows-classic-sql-automated-patching.md)

L'applicazione automatica delle patch stabilisce un periodo di manutenzione per una macchina virtuale di Azure su cui è in esecuzione SQL Server. Gli aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, gli aggiornamenti di sistema e i riavvii associati vengono eseguiti nel momento migliore per il database. L'applicazione automatica delle patch dipende dall'[estensione dell'agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]
Modello di distribuzione classica. Per visualizzare la versione classica di questo articolo, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-sql-automated-patching.md).

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

>[AZURE.NOTE] L'applicazione automatica delle patch si basa sull'estensione dell'agente IaaS di SQL Server. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## Impostazioni

Nella seguente tabella sono descritte le opzioni che possono essere configurate per l'applicazione automatica delle patch. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

|Impostazione|Valori possibili|Descrizione|
|---|---|---|
|**Applicazione automatica delle patch**|Enable/Disable (disabilitato)|Abilita o disabilita l'applicazione automatica delle patch per una macchina virtuale di Azure.|
|**Pianificazione della manutenzione**|Ogni giorno, lunedì, martedì, mercoledì, giovedì, venerdì, sabato, domenica|Pianificazione per il download e l'installazione degli aggiornamenti di Windows, SQL Server e Microsoft per la macchina virtuale.|
|**Ora di inizio manutenzione**|0-24|Ora di inizio locale per aggiornare la macchina virtuale.|
|**Durata dell'intervallo di manutenzione**|30-180|Numero di minuti consentito per completare il download e l'installazione degli aggiornamenti.|
|**Categoria delle patch**|Importante|Categoria degli aggiornamenti da scaricare e installare.|

## Configurazione nel Portale

È possibile usare il [Portale di Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) per configurare l'applicazione automatica delle patch quando si crea una nuova macchina virtuale di SQL Server nel modello di distribuzione di Resource Manager.

Nel pannello **Impostazioni di SQL Server** selezionare **Applicazione automatica delle patch**. Nella seguente schermata del Portale di Azure viene mostrato il pannello **Applicazione automatica delle patch di SQL**.

![Applicazione automatizzata di patch SQL nel portale di Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Per il contesto, vedere l'argomento completo sul [provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per macchine virtuali di SQL Server esistenti, è necessario usare PowerShell per configurare le impostazioni dell'applicazione automatica delle patch.

>[AZURE.NOTE] Quando si abilita l’applicazione automatizzata di patch per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel Portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione dell'applicazione automatica delle patch. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure verranno visualizzate le nuove impostazioni.

## Configurazione con PowerShell

Dopo il provisioning della VM di SQL, usare PowerShell per configurare l'applicazione automatica delle patch.

Nell'esempio seguente, PowerShell viene utilizzato per configurare l'applicazione automatizzata di patch in una macchina virtuale di SQL Server esistente. Il comando **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig** configura un nuovo periodo di manutenzione per gli aggiornamenti automatici.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

In base a questo esempio, nella tabella seguente vengono descritti gli effetti pratici sulla macchina virtuale di Azure di destinazione:

|Parametro|Effetto|
|---|---|
|**DayOfWeek**|Patch installate ogni giovedì.|
|**MaintenanceWindowStartingHour**|Inizio degli aggiornamenti alle ore 11:00.|
|**MaintenanceWindowsDuration**|Le patch devono essere installate entro 120 minuti. In base all'ora di inizio, devono essere completate entro le ore 13:00.|
|**PatchCategory**|L'unica impostazione possibile per questo parametro è "Important".|

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

Per disabilitare l'applicazione automatica delle patch, eseguire lo stesso script senza il parametro **-Enable** per **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig**. L'assenza del parametro **-Enable** segnala il comando per disabilitare la funzionalità.

## Passaggi successivi

Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0525_2016-->