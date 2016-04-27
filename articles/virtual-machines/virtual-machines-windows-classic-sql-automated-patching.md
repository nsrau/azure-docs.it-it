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
	ms.date="04/08/2016"
	ms.author="jroth" />

# Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (distribuzione classica)

L’applicazione automatizzata di patch stabilisce un periodo di manutenzione per una macchina virtuale di Azure in cui è in esecuzione SQL Server 2012 o 2014. Gli aggiornamenti automatici possono essere installati solo durante questo periodo di manutenzione. Per SQL Server, gli aggiornamenti di sistema e i riavvii associati vengono eseguiti nel momento migliore per il database. Dipende dall’agente IaaS di SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

## Configurare l’applicazione automatizzata di patch nel portale di Azure

È possibile usare il [portale di Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) per configurare l’applicazione automatizzata di patch quando si crea una nuova macchina virtuale di SQL Server.

>[AZURE.NOTE] L’applicazione automatizzata di patch si basa sull’agente IaaS di SQL Server. Per installare e configurare l'agente, è necessario che nella macchina virtuale di destinazione sia in esecuzione l'agente di macchine virtuali di Azure. Per le immagini della raccolta di macchine virtuali più recenti questa opzione è abilitata per impostazione predefinita, ma è possibile che l'agente di macchine virtuali di Azure non sia presente nelle macchine virtuali esistenti. Se si usa la propria immagine di macchina virtuale, sarà inoltre necessario installare l'agente IaaS di SQL Server. Per ulteriori informazioni, vedere la pagina relativa all’[agente di macchine virtuali e alle estensioni](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

La seguente schermata del portale di Azure mostra queste opzioni in **CONFIGURAZIONE FACOLTATIV** | **APPLICAZIONE AUTOMATICA DELLE PATCH DI SQL**.

![Applicazione automatizzata di patch SQL nel portale di Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

Per le macchine virtuali esistenti di SQL Server 2012 o 2014, selezionare le impostazioni relative all’**applicazione automatizzata di patch** nella sezione di **configurazione** delle proprietà della macchina virtuale Nel periodo dell’**applicazione automatizzata di patch**, è possibile abilitare la funzionalità, impostare la pianificazione della manutenzione e l’ora di inizio e scegliere la durata del periodo di manutenzione. Queste impostazioni sono illustrate nella seguente schermata.

![Configurazione dell’applicazione automatizzata di patch nel portale di Azure](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] Quando si abilita l’applicazione automatizzata di patch per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure non viene visualizzata l'informazione relativa alla configurazione dell’applicazione automatizzata di patch. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure verranno visualizzate le nuove impostazioni.

## Configurare l’applicazione automatizzata di patch con PowerShell

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

## Disabilitazione e disinstallazione dell'agente IaaS di SQL Server

Se si desidera disabilitare l'agente IaaS di SQL Server per il backup e l'applicazione di patch in modalità automatizzata, usare il seguente comando:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Per disinstallare l'agente IaaS di SQL Server, usare la seguente sintassi:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

È anche possibile disinstallare l'estensione usando il comando **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Compatibilità

I seguenti prodotti sono compatibili con le funzionalità dell'agente IaaS di SQL Server per l’applicazione automatizzata di patch.

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## Passaggi successivi

Una funzionalità correlata per Macchine virtuali di SQL Server in Azure è [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-classic-sql-automated-backup.md).

Esaminare altre [risorse per l'esecuzione di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0413_2016-->