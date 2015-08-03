<properties 
	pageTitle="SQL Server in Macchine virtuali di Azure" 
	description="Descrive l'estensione agente di SQL Server, che consente alle macchine virtuali che eseguono SQL Server nel cloud di Azure di usare funzionalità di automazione e illustra come installare l'agente se non è già stato installato in modo automatico." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="06/17/2015"
	ms.author="jeffreyg"/>

# Estensione Agente IaaS di SQL Server

Questa estensione consente a SQL Server in una macchina virtuale di Azure di usare determinati servizi, elencati in questo articolo, che altrimenti non potrebbero essere usati. Questa estensione viene installata automaticamente per le immagini della raccolta di SQL Server nel portale di anteprima di Azure. Può essere installata in qualsiasi macchina virtuale SQL Server di Azure con l'agente guest per macchine virtuale di Azure installato.
 
## Prerequisiti
Requisiti per l'uso dei cmdlet di Powershell:

- SDK più recente della riga di comando di Azure [disponibile qui](http://azure.microsoft.com/downloads/)

Requisiti per l'uso dell'estensione nella macchina virtuale:

- Agente guest per macchine virtuale di Azure
- Windows Server 2012, Windows Server 2012 R2 o versioni successive
- SQL Server 2012, SQL Server 2014 o versioni successive
 
## Servizi disponibili con l'estensione

- **Backup automatizzato per SQL**: questo servizio consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella macchina virtuale. Per altre informazioni su questo servizio, vedere [Backup automatizzato per SQL Server in Macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn906091.aspx).
- **Applicazione automatica per SQL**: questo servizio consente di configurare una finestra di manutenzione durante la quale eseguire gli aggiornamenti della macchina virtuale, evitandone l'esecuzione durante i periodi di picco del carico di lavoro. Per altre informazioni su questo servizio, vedere l'articolo sull'[applicazione automatica di patch per SQL Server in Macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn961166.aspx).

## Aggiungere l'estensione con Powershell
Se si esegue il provisioning della macchina virtuale di SQL Server usando il [portale di anteprima di Azure](https://portal.azure.com/), l'estensione verrà installata automaticamente. Nel caso di macchine virtuali di SQL Server di cui è stato eseguito il provisioning tramite il [portale di gestione di Azure](https://manage.windowsazure.com) o di macchine virtuali per le quali si dispone già di una licenza SQL, è possibile aggiungere questa estensione a una macchina virtuale esistente usando il seguente cmdlet di Azure PowerShell.

**Set-AzureVMSqlServerExtension**

### Sintassi

Set-AzureVMSqlServerExtension [-VM] <IPersistentVM> [[-Version] <string>] [-AutoBackupSettings <AutoBackupSettings>] [-AutoPatchingSetttings <AutoPatchingSetttings>] [-Confirm] [-WhatIf] [<CommonParameters>]

> [AZURE.NOTE]È consigliabile omettere il parametro –Version. Se lo si omette, viene impostata la versione predefinita dell'estensione, ovvero quella più recente.

### Esempio
	Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## Controllare lo stato dell'estensione
Se si desidera controllare lo stato dell'estensione e dei servizi ad essa associati, è possibile usare entrambi i portali. Nei dettagli della macchina virtuale esistente, trovare la voce **Estensioni** nell'area **Impostazioni**.

In alternativa, è possibile usare il cmdlet di Azure PowerShell seguente.

**Get-AzureVMSqlServerExtension**

### Sintassi

Get-AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-Version] <string>] [<CommonParameters>]

> [AZURE.NOTE]È possibile omettere il parametro –Version. Se lo si omette, viene impostata la versione predefinita dell'estensione, ovvero quella più recente.

### Esempio
	Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## Rimuovere l'estensione con Powershell   
Se si desidera rimuovere l'estensione da una macchina virtuale, è possibile usare il cmdlet di Azure Powershell seguente.

**Remove-AzureVMSqlServerExtension**

### Sintassi
Remove-AzureVMSqlServerExtension -VM <IPersistentVM> [<CommonParameters>]

<!---HONumber=July15_HO4-->