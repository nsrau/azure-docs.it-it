<properties
	pageTitle="Estensione Agente IaaS di SQL Server (distribuzione classica) | Microsoft Azure"
	description="Questo argomento descrive l'estensione di agente SQL Server, che consente a una macchina virtuale che esegue SQL Server in Azure di usare le funzionalità di automazione. Usa la modalità di distribuzione classica."
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
	ms.date="04/08/2016"
	ms.author="jroth"/>

# Estensione Agente IaaS di SQL Server (distribuzione classica)

Questa estensione consente a SQL Server in una macchina virtuale di Azure di usare determinati servizi, elencati in questo articolo, che altrimenti non potrebbero essere usati. Questa estensione viene installata automaticamente per le immagini della raccolta di SQL Server nel portale di Azure. Può essere installata in qualsiasi macchina virtuale SQL Server di Azure con l'agente guest per macchine virtuale di Azure installato.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


## Prerequisiti
Requisiti per l'uso dei cmdlet di Powershell:

- Versione più recente di Azure PowerShell [disponibile qui](../powershell-install-configure.md)

Requisiti per l'uso dell'estensione nella macchina virtuale:

- Agente guest per macchine virtuale di Azure
- Windows Server 2012, Windows Server 2012 R2 o versioni successive
- SQL Server 2012, SQL Server 2014 o versioni successive

## Servizi disponibili con l'estensione

- **Backup automatizzato per SQL**: questo servizio consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella macchina virtuale. Per altre informazioni, vedere [Backup automatizzato per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-sql-automated-backup.md).
- **Applicazione automatica per SQL**: questo servizio consente di configurare una finestra di manutenzione durante la quale eseguire gli aggiornamenti della macchina virtuale, evitandone l'esecuzione durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatizzata di patch per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-sql-automated-patching.md).
- **Integrazione dell'insieme di credenziali delle chiavi di Azure**: questo servizio consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-ps-sql-keyvault.md).

## Aggiungere l'estensione con Powershell
Se si esegue il provisioning della VM di SQL Server usando il [portale di Azure](virtual-machines-windows-portal-sql-server-provision.md), l'estensione verrà installata automaticamente. Nel caso di VM di SQL Server di cui è stato effettuato il provisioning con il portale di Azure classico o di VM per le quali esiste già una licenza SQL, è possibile aggiungere questa estensione usando il cmdlet di Azure PowerShell **Set-AzureVMSqlServerExtension**.

### Sintassi

Set-AzureVMSqlServerExtension [[-ReferenceName] [String]] [-VM] IPersistentVM [[-Version] [String]] [[-AutoPatchingSettings] [AutoPatchingSettings]] [-AutoBackupSettings[AutoBackupSettings]] [-Profile [AzureProfile]] [CommonParameters]

> [AZURE.NOTE] È consigliabile omettere il parametro –Version. Se lo si omette, viene impostata la versione predefinita dell'estensione, ovvero quella più recente.

### Esempio
L'esempio seguente configura le impostazioni di backup automatico usando una configurazione definita in $abs (non illustrata qui). serviceName è il nome del servizio cloud che ospita la macchina virtuale. Per un esempio completo, vedere [Backup automatizzato per SQL Server in macchine virtuali di Azure (distribuzione classica)](virtual-machines-windows-classic-sql-automated-backup.md).

	Get-AzureVM –ServiceName "serviceName" –Name "vmName" | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Controllare lo stato dell'estensione
Se si desidera controllare lo stato dell'estensione e dei servizi ad essa associati, è possibile usare entrambi i portali. Nei dettagli della macchina virtuale esistente, trovare la voce **Estensioni** nell'area **Impostazioni**.

È anche possibile usare il cmdlet di Azure PowerShell **Get-AzureVMSqlServerExtension**.

### Sintassi

Get-AzureVMSqlServerExtension [[-VM] [IPersistentVM]] [-Profile [AzureProfile]] [CommonParameters]

### Esempio
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Rimuovere l'estensione con Powershell   
Per rimuovere l'estensione da una macchina virtuale, è possibile usare il cmdlet di Azure Powershell **Remove-AzureVMSqlServerExtension**.

### Sintassi

Remove-AzureVMSqlServerExtension [-Profile [AzureProfile]] -VM IPersistentVM [CommonParameters]

<!---HONumber=AcomDC_0413_2016-->