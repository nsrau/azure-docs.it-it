---
title: Registrare in blocco le macchine virtuali SQL in Azure con il provider di risorse della macchina virtuale SQL. Documenti Microsoft
description: Registrare in blocco le macchine virtuali di SQL Server con il provider di risorse della macchina virtuale SQL per migliorare la gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353876"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrare in blocco le macchine virtuali SQL in Azure con il provider di risorse della macchina virtuale SQLBulk register SQL virtual machines in Azure with the SQL VM resource provider

Questo articolo descrive come registrare in blocco la macchina virtuale (VM) di `Register-SqlVMs` SQL Server in Azure con il provider di risorse della macchina virtuale SQL usando il cmdlet PowerShell.This article describes how to bulk register your SQL Server virtual machine (VM) in Azure with the SQL VM resource provider using the PowerShell cmdlet.

Il `Register-SqlVMs` cmdlet può essere utilizzato per registrare tutte le macchine virtuali in un determinato elenco di sottoscrizioni, gruppi di risorse o un elenco di macchine virtuali specifiche. Il cmdlet registrerà le macchine virtuali in modalità di gestione _leggera_ e quindi genererà sia un report che un file di [log.](#output-description) 

Il processo di registrazione non comporta alcun rischio, non ha tempi di inattività e non riavvia SQL Server o la macchina virtuale. 

Per altre informazioni sul provider di risorse, vedere Provider di [risorse VM SQL.](virtual-machines-windows-sql-register-with-resource-provider.md) 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue:To register your SQL Server VM with the resource provider, you'll need the following: 

- Una [sottoscrizione](https://azure.microsoft.com/free/) di Azure registrata con il provider di [risorse](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) e contenente macchine virtuali di SQL Server non registrate. 
- Le credenziali client utilizzate per registrare le macchine virtuali sono presenti in uno dei ruoli RBAC seguenti: **Collaboratore macchina virtuale**, **Collaboratore**o **Proprietario**. 
- La versione più recente di [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- La versione più recente di [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Introduzione

Prima di procedere, è necessario creare una copia locale dello script, importarla come modulo di PowerShell e connettersi ad Azure.Before proceeding, you must first create a local copy of the script, import it as a PowerShell module, and connect to Azure. 

### <a name="create-script"></a>Crea script

Per creare lo script, copiare lo [script completo](#full-script) dalla `RegisterSqlVMs.psm1`fine di questo articolo e salvarlo localmente come . 

### <a name="import-script"></a>Script di importazione

Una volta creato lo script, è possibile importarlo come modulo nel terminale di Powershell.Once the script is created, you can import it as a module in the Powershell terminal. 

Aprire un terminale PowerShell amministrativo `RegisterSqlVMs.psm1` e passare alla posizione in cui è stato salvato il file. Quindi, eseguire il seguente cmdlet PowerShell per importare lo script come modulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Connettersi ad Azure

Usare il cmdlet PowerShell seguente per connettersi ad Azure:Use the following PowerShell cmdlet to connect to Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Tutte le macchine virtuali nell'elenco delle sottoscrizioniAll VMs in list of subscriptions 

Utilizzare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in un elenco di sottoscrizioni:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Output di esempio: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Tutte le macchine virtuali in un'unica sottoscrizioneAll VMs in a single subscription

Utilizzare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in una singola sottoscrizione:Use the following cmdlet to register all SQL Server virtual machines in a single subscription: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Output di esempio:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Tutte le macchine virtuali in più gruppi di risorseAll VMs in multiple resource groups

Utilizzare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in più gruppi di risorse all'interno di una singola sottoscrizione:Use the following cmdlet to register all SQL Server virtual machines in multiple resource groups within a single subscription:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Output di esempio:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Tutte le macchine virtuali in un gruppo di risorseAll VMs in a resource group

Utilizzare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in un singolo gruppo di risorse:Use the following cmdlet to register all SQL Server virtual machines in a single resource group: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Output di esempio:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>Macchine virtuali specifiche in un singolo gruppo di risorseSpecific VMs in single resource group

Utilizzare il cmdlet seguente per registrare macchine virtuali di SQL Server specifiche all'interno di un singolo gruppo di risorse:Use the following cmdlet to register specific SQL Server virtual machines within a single resource group:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Output di esempio:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>VM specifica

Utilizzare il cmdlet seguente per registrare una macchina virtuale DI SQL Server specifica:Use the following cmdlet to register a specific SQL Server virtual machine: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Output di esempio:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Descrizione dell'output

Ogni volta che viene utilizzato il `Register-SqlVMs` cmdlet, vengono generati sia un file di report che un file di log. 

### <a name="report"></a>Report

Il report viene `.txt` generato `RegisterSqlVMScriptReport<Timestamp>.txt` come file denominato timestamp in cui il timestamp è l'ora in cui è stato avviato il cmdlet. Il rapporto elenca i seguenti dettagli:

| **Valore di output** | **Descrizione** |
| :--------------  | :-------------- | 
| Numero di sottoscrizioni di registrazione non riuscite perché non si dispone dell'accesso o le credenziali non sono corrette | Fornisce il numero e l'elenco delle sottoscrizioni che hanno avuto problemi con l'autenticazione fornita. L'errore dettagliato è reperibile nel log cercando l'ID sottoscrizione. | 
| Numero di sottoscrizioni che non è stato possibile provare perché non sono registrate nel componente | Questa sezione contiene il conteggio e l'elenco delle sottoscrizioni che non sono state registrate nel provider di risorse della macchina virtuale SQL. |
| Totale macchine virtuali trovate | Numero di macchine virtuali trovate nell'ambito dei parametri passati al cmdlet. | 
| Macchine virtuali già registrate | Numero di macchine virtuali ignorate perché erano già registrate con il provider di risorse. |
| Numero di macchine virtuali registrate correttamente | Numero di macchine virtuali registrate correttamente dopo l'esecuzione del cmdlet. Elenca le macchine virtuali `SubscriptionID, Resource Group, Virtual Machine`registrate nel formato . | 
| Numero di macchine virtuali non riuscite a causa di un errore | Numero di macchine virtuali che non è stato possibile registrare a causa di un errore. I dettagli dell'errore sono disponibili nel file di registro. | 
| Numero di macchine virtuali ignorate poiché la macchina virtuale o l'agente di una conversione nella macchina virtuale non è in esecuzione | Conteggio e elenco delle macchine virtuali che non è stato possibile registrare come macchina virtuale o l'agente guest nella macchina virtuale non erano in esecuzione. Questi possono essere ritentati una volta che la macchina virtuale o l'agente guest è stato avviato. I dettagli sono disponibili nel file di registro. |
| Numero di macchine virtuali ignorate perché non eseguono SQL Server in Windows | Numero di macchine virtuali che sono state ignorate in quanto non eseguono SQL Server o non sono una macchina virtuale Windows. Le macchine virtuali sono `SubscriptionID, Resource Group, Virtual Machine`elencate nel formato . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>File di log 

Gli errori vengono registrati `VMsNotRegisteredDueToError<Timestamp>.log` nel file di registro denominato timestamp, ovvero l'ora di avvio dello script. Se l'errore è a livello di sottoscrizione, il log contiene il SubscriptionID separato da virgole e il messaggio di errore. Se l'errore riguarda la registrazione della macchina virtuale, il log contiene l'ID sottoscrizione, il nome del gruppo di risorse, il nome della macchina virtuale, il codice di errore e il messaggio separati da virgole. 

## <a name="remarks"></a>Osservazioni

Quando si registrano macchine virtuali di SQL Server con il provider di risorse usando lo script fornito, considerare quanto segue:When you register SQL Server VMs with the resource provider using the provided script, consider the following:

- La registrazione con il provider di risorse richiede un agente guest in esecuzione nella macchina virtuale di SQL Server.Registration with the resource provider requires a guest agent running on the SQL Server VM. Le immagini di Windows Server 2008 non dispongono di un agente guest, pertanto queste macchine virtuali avranno esito negativo e devono essere registrate manualmente utilizzando la modalità di [gestione NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- La logica di ripetizione dei tentativi è incorporata per superare gli errori trasparenti. Se la macchina virtuale è stata registrata correttamente, si tratta di un'operazione rapida. Tuttavia, se la registrazione non riesce, ogni macchina virtuale verrà ritentata.  Di conseguenza, è consigliabile concedere tempo significativo per completare il processo di registrazione, anche se il requisito di tempo effettivo dipende dal tipo e dal numero di errori. 

## <a name="full-script"></a>Script completo

Per lo script completo in GitHub, vedere [Registrazione in blocco delle macchine virtuali SQL con Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copiare lo script completo `RegisterSqLVMs.psm1`e salvarlo come .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale WindowsFAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per SQL Server in una macchina virtuale WindowsPricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale WindowsRelease notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
