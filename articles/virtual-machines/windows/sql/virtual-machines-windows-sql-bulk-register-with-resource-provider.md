---
title: Registrare in blocco le macchine virtuali SQL in Azure con il provider di risorse VM SQL | Microsoft Docs
description: Registrare in blocco SQL Server VM con il provider di risorse VM SQL per migliorare la gestibilità.
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
ms.openlocfilehash: 3a8cfeb237b0e5e662f8fbaef4347f4346db1787
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045128"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrare in blocco le macchine virtuali SQL in Azure con il provider di risorse VM SQL

Questo articolo descrive come registrare in blocco il SQL Server macchina virtuale (VM) in Azure con il provider di risorse VM SQL usando il cmdlet `Register-SqlVMs` PowerShell.

Il cmdlet `Register-SqlVMs` può essere usato per registrare tutte le macchine virtuali in un elenco specifico di sottoscrizioni, gruppi di risorse o un elenco di macchine virtuali specifiche. Il cmdlet registrerà le macchine virtuali in modalità di gestione _Lightweight_ , quindi genererà un [report e un file di log](#output-description). 

Il processo di registrazione non comporta alcun rischio, non ha tempi di inattività e non riavvia SQL Server o la macchina virtuale. 

Per ulteriori informazioni sul provider di risorse, vedere [SQL VM Resource Provider](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/) che è stata [registrata con il provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) e contiene macchine virtuali SQL Server non registrate. 
- Le credenziali client utilizzate per registrare le macchine virtuali sono disponibili in uno dei ruoli RBAC seguenti: **collaboratore macchina virtuale**, **collaboratore**o **proprietario**. 
- La versione più recente di [AZ PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="getting-started"></a>Inizia ora

Prima di procedere, è necessario creare prima di tutto una copia locale dello script, importarlo come modulo di PowerShell e connettersi ad Azure. 

### <a name="create-script"></a>Crea script

Per creare lo script, copiare lo [script completo](#full-script) alla fine di questo articolo e salvarlo localmente come `RegisterSqlVMs.psm1`. 

### <a name="import-script"></a>Importa script

Una volta creato lo script, è possibile importarlo come modulo nel terminale di PowerShell. 

Aprire un terminale di PowerShell amministrativo e passare al percorso in cui è stato salvato il file di `RegisterSqlVMs.psm1`. Eseguire quindi il cmdlet di PowerShell seguente per importare lo script come modulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Connettersi ad Azure

Usare il cmdlet di PowerShell seguente per connettersi ad Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Tutte le macchine virtuali nell'elenco di sottoscrizioni 

Usare il cmdlet seguente per registrare tutte le macchine virtuali SQL Server in un elenco di sottoscrizioni:

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

## <a name="all-vms-in-a-single-subscription"></a>Tutte le macchine virtuali in una singola sottoscrizione

Usare il cmdlet seguente per registrare tutte le macchine virtuali SQL Server in una singola sottoscrizione: 

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

## <a name="all-vms-in-multiple-resource-groups"></a>Tutte le macchine virtuali in più gruppi di risorse

Usare il cmdlet seguente per registrare tutte le macchine virtuali SQL Server in più gruppi di risorse all'interno di una singola sottoscrizione:

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

## <a name="all-vms-in-a-resource-group"></a>Tutte le macchine virtuali in un gruppo di risorse

Usare il cmdlet seguente per registrare tutte le macchine virtuali SQL Server in un singolo gruppo di risorse: 

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

## <a name="specific-vms-in-single-resource-group"></a>Macchine virtuali specifiche in un gruppo di risorse singolo

Usare il cmdlet seguente per registrare macchine virtuali SQL Server specifiche all'interno di un singolo gruppo di risorse:

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

Usare il cmdlet seguente per registrare una macchina virtuale SQL Server specifica: 

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


## <a name="output-description"></a>Descrizione output

Ogni volta che viene utilizzato il `Register-SqlVMs` cmdlet viene generato un report e un file di log. 

### <a name="report"></a>Documentazione

Il report viene generato come file `.txt` denominato `RegisterSqlVMScriptReport<Timestamp>.txt` dove il timestamp è l'ora in cui è stato avviato il cmdlet. Il report elenca i dettagli seguenti:

| **Valore di output** | **Descrizione** |
| :--------------  | :-------------- | 
| Il numero di registrazioni delle sottoscrizioni non è riuscito perché non si dispone dell'accesso o le credenziali non sono corrette | In questo modo viene fornito il numero e l'elenco delle sottoscrizioni che hanno avuto problemi con l'autenticazione fornita. L'errore dettagliato è reperibile nel log cercando l'ID sottoscrizione. | 
| Numero di sottoscrizioni che non è stato possibile provare perché non sono registrate nel componente | Questa sezione contiene il conteggio e l'elenco delle sottoscrizioni che non sono state registrate nel provider di risorse della macchina virtuale SQL. |
| Totale VM trovate | Numero di macchine virtuali trovate nell'ambito dei parametri passati al cmdlet. | 
| VM già registrate | Numero di macchine virtuali ignorate come già registrate con il provider di risorse. |
| Il numero di macchine virtuali è stato registrato | Numero di macchine virtuali registrate correttamente dopo l'esecuzione del cmdlet. Elenca le macchine virtuali registrate nel formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| Non è stato possibile registrare il numero di macchine virtuali a causa di un errore | Numero di macchine virtuali che non sono state registrate a causa di un errore. I dettagli dell'errore si trovano nel file di log. | 
| Il numero di macchine virtuali ignorate come macchina virtuale o l'agente raffica nella macchina virtuale non è in esecuzione | Il numero e l'elenco delle macchine virtuali che non è stato possibile registrare come macchina virtuale o agente guest nella macchina virtuale non sono in esecuzione. Queste operazioni possono essere ritentate dopo l'avvio della macchina virtuale o dell'agente guest. Per informazioni dettagliate, vedere il file di log. |
| Numero di macchine virtuali ignorate poiché non sono in esecuzione SQL Server in Windows | Numero di macchine virtuali ignorate perché non in esecuzione SQL Server o non sono una macchina virtuale Windows. Le macchine virtuali sono elencate nel formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Gli errori vengono registrati nel file di log denominato `VMsNotRegisteredDueToError<Timestamp>.log` dove timestamp è l'ora di inizio dello script. Se l'errore è a livello di sottoscrizione, il log contiene le SubscriptionID separate da virgole e il messaggio di errore. Se l'errore è relativo alla registrazione della macchina virtuale, il log contiene l'ID sottoscrizione, il nome del gruppo di risorse, il nome della macchina virtuale, il codice di errore e il messaggio separati da virgole. 

## <a name="remarks"></a>Osservazioni

Quando si registrano SQL Server VM con il provider di risorse usando lo script fornito, tenere presente quanto segue:

- Per la registrazione con il provider di risorse è necessario un agente guest in esecuzione nella macchina virtuale SQL Server. Le immagini di Windows Server 2008 non dispongono di un agente guest, pertanto tali macchine virtuali avranno esito negativo e dovranno essere registrate manualmente usando la [modalità di gestione noagent](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).
- La logica di ripetizione dei tentativi è incorporata per superare gli errori trasparenti. Se la macchina virtuale è stata registrata correttamente, si tratta di un'operazione rapida. Tuttavia, se la registrazione ha esito negativo, viene eseguito un nuovo tentativo per ogni macchina virtuale.  Di conseguenza, è necessario consentire un tempo significativo per il completamento del processo di registrazione, sebbene il requisito di tempo effettivo dipenda dal tipo e dal numero di errori. 

## <a name="full-script"></a>Script completo
Per lo script completo su GitHub, vedere [registrare in blocco le VM SQL con AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
