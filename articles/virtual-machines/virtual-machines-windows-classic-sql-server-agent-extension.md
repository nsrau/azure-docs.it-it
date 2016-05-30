<properties
	pageTitle="Estensione di SQL Server Agent per VM di SQL Server (distribuzione classica) | Microsoft Azure"
	description="Questo argomento descrive come gestire l'Estensione di SQL Server Agent, che consente di automatizzare attività di amministrazione di SQL Server specifiche. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure. In questo argomento viene usata la modalità di distribuzione classica."
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
	ms.date="05/16/2016"
	ms.author="jroth"/>

# Estensione di SQL Server Agent per VM di SQL Server (distribuzione classica)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
- [Classico](virtual-machines-windows-classic-sql-server-agent-extension.md)

L'Estensione Agente IaaS di SQL Server (SQLIaaSAgent) viene eseguita sulle macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo argomento fornisce una panoramica dei servizi supportati dall'estensione e delle istruzioni per l'installazione, lo stato e la rimozione.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse. Per visualizzare la versione di Gestione risorse di questo articolo, vedere [SQL Server Agent Extension for SQL Server VMs Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md) (Estensione Agente IaaS per le VM SQL Gestione risorse).

## Servizi supportati

L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
|---------------------|-------------------------------|
| **Backup automatico di SQL** | Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella VM. Per altre informazioni, vedere [Backup automatico per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-sql-automated-backup.md).|
| **Applicazione automatica delle patch di SQL** | Consente di configurare una finestra di manutenzione durante la quale eseguire gli aggiornamenti della VM, evitandone l'esecuzione durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integrazione dell'insieme di credenziali delle chiavi di Azure** | Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## Prerequisiti

Requisiti per l'uso dell'Estensione Agente IaaS di SQL Server nella VM:

- Agente guest per VM di Azure (estensione BGInfo installata automaticamente sulle nuove VM di Azure).
- Windows Server 2012, Windows Server 2012 R2 o versioni successive
- SQL Server 2012, SQL Server 2014 o versioni successive.

Requisiti per l'uso dei cmdlet di Powershell:

- Versione più recente di Azure PowerShell [disponibile qui](../powershell-install-configure.md)

## Installare

L'Estensione Agente IaaS di SQL Server viene installata automaticamente quando si esegue il provisioning di una delle immagini della galleria di macchine virtuali SQL Server.

Se si crea una macchina virtuale con solo sistema operativo della linea Windows Server, è possibile installare manualmente l'estensione tramite il cmdlet di PowerShell **Set-AzureVMSqlServerExtension**. Usare il comando per configurare uno dei servizi dell'agente, ad esempio l'Applicazione automatica delle patch. La VM consente di installare l'agente se non è installato.

>[AZURE.NOTE] Per istruzioni sull'uso di PowerShell **Set-AzureVMSqlServerExtension**, vedere i singoli argomenti nella sezione [Servizi supportati](#supported-services) di questo articolo.

## Stato

Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nel pannello della macchina virtuale e quindi fare clic su **Estensioni**. Verrà elencata l'estensione **SQLIaaSAgent**.

![Estensione Agente IaaS di SQL Server nel portale di Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet di Azure PowerShell **Get-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Rimozione   

Nel portale di Azure è possibile disinstallare l'estensione facendo clic sui puntini di sospensione nel pannello **Estensioni** delle proprietà della macchina virtuale. Fare quindi clic su **Elimina**.

![Disinstallare l'Estensione Agente IaaS di SQL Server nel portale di Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet di PowerShell **Remove-AzureVMSqlServerExtension**.

	Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Passaggi successivi

Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli argomenti citati nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0518_2016-->