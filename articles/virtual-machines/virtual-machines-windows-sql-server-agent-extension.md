<properties
	pageTitle="Estensione di SQL Server Agent per VM di SQL Server (Resource Manager) | Microsoft Azure"
	description="Questo argomento descrive come gestire l'Estensione di SQL Server Agent, che consente di automatizzare attività di amministrazione di SQL Server specifiche. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure. Questo argomento usa il modello di distribuzione Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
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
	ms.date="07/14/2016"
	ms.author="jroth"/>

# Estensione di SQL Server Agent per VM di SQL Server (Resource Manager)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
- [Classico](virtual-machines-windows-classic-sql-server-agent-extension.md)

L'Estensione Agente IaaS di SQL Server (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo argomento fornisce una panoramica dei servizi supportati dall'estensione e istruzioni per l'installazione, lo stato e la rimozione.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica. 
Per visualizzare la versione classica di questo articolo, vedere [Estensione Agente IaaS di SQL Server (distribuzione classica)](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Servizi supportati

L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
|---------------------|-------------------------------|
| **Backup automatico di SQL** | Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella VM. Per altre informazioni, vedere [Backup automatico per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md).|
| **Applicazione automatica delle patch di SQL** | Consente di configurare una finestra di manutenzione durante la quale eseguire gli aggiornamenti della VM, evitandone l'esecuzione durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md).|
| **Integrazione dell'insieme di credenziali delle chiavi di Azure** | Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md).|

## Prerequisiti

Requisiti per l'uso dell'Estensione Agente IaaS di SQL Server nella VM:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versioni di SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Scaricare e configurare i comandi di Azure PowerShell più recenti](../powershell-install-configure.md)

## Installare

L'Estensione Agente IaaS di SQL Server viene installata automaticamente quando si esegue il provisioning di una delle immagini della galleria di macchine virtuali SQL Server.

Se si crea una macchina virtuale con solo sistema operativo della linea Windows Server, è possibile installare l'estensione manualmente tramite il cmdlet di PowerShell **Set-AzureVMSqlServerExtension**. Ad esempio, il comando seguente consente di installare l'estensione in una VM con solo sistema operativo della linea Windows Server, denominandola "SQLIaaSExtension".

	Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Se si esegue l'aggiornamento alla versione più recente dell'estensione dell'agente IaaS di SQL, è necessario riavviare la macchina virtuale dopo l'aggiornamento dell'estensione.

>[AZURE.NOTE] Se si installa manualmente l'estensione dell'agente IaaS di SQL Server in una VM Windows Server, è necessario usare e gestire le funzionalità tramite i comandi di PowerShell. L'interfaccia del portale è disponibile solo per la raccolta immagini di SQL Server.

## Stato

Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nel pannello della macchina virtuale e quindi fare clic su **Estensioni**. Verrà elencata l'estensione **SQLIaaSExtension**.

![Estensione Agente IaaS di SQL Server nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet di Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Il comando precedente conferma l'installazione dell'agente e ne fornisce informazioni generali sullo stato. È inoltre possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatizzata con i comandi seguenti.

	$sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
	$sqlext.AutoPatchingSettings
	$sqlext.AutoBackupSettings

## Rimozione   

Nel portale di Azure è possibile disinstallare l'estensione facendo clic sui puntini di sospensione nel pannello **Estensioni** delle proprietà della macchina virtuale. Fare quindi clic su **Elimina**.

![Disinstallare l'Estensione Agente IaaS di SQL Server nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet di PowerShell **Remove-AzureRmVMSqlServerExtension**.

	Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## Passaggi successivi

Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli argomenti citati nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0720_2016-->