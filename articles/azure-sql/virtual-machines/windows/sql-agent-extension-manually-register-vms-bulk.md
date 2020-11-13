---
title: Registrare più macchine virtuali SQL in Azure con l'estensione SQL IaaS Agent
description: Registrare in blocco SQL Server VM con l'estensione SQL IaaS Agent per migliorare la gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 208df7ad53049598255ce358f2db128ba84fea9a
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557784"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>Registrare più macchine virtuali SQL in Azure con l'estensione SQL IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come registrare in blocco le macchine virtuali di SQL Server (VM) in Azure con l' [estensione SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) usando il `Register-SqlVMs` cmdlet Azure PowerShell. 


Questo articolo illustra come registrare manualmente SQL Server VM in blocco. In alternativa, è possibile registrare manualmente [tutte le macchine virtuali SQL Server](sql-agent-extension-automatic-registration-all-vms.md) o [singole SQL Server VM](sql-agent-extension-manually-register-single-vm.md). 

## <a name="overview"></a>Panoramica

Il cmdlet `Register-SqlVMs` può essere usato per registrare tutte le macchine virtuali in un elenco specifico di sottoscrizioni, gruppi di risorse o un elenco di macchine virtuali specifiche. Il cmdlet registrerà le macchine virtuali in [modalità di gestione lightweight_](sql-server-iaas-agent-extension-automate-management.md#management-modes), quindi genererà un [report e un file di log](#output-description). 

Il processo di registrazione non comporta alcun rischio, non ha tempi di inattività e non riavvia il servizio SQL Server o la macchina virtuale. 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale SQL Server con l'estensione, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/) che è stata [registrata con il provider **Microsoft. SqlVirtualMachine**](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp) e contiene macchine virtuali SQL Server non registrate. 
- Le credenziali client usate per registrare le macchine virtuali sono disponibili in uno dei seguenti ruoli di Azure: **collaboratore macchina virtuale** , **collaboratore** o **proprietario**. 
- La versione più recente di [AZ PowerShell (5,0 minimum)](/powershell/azure/new-azureps-module-az). 


## <a name="get-started"></a>Introduzione

Prima di procedere, è necessario creare una copia locale dello script, importarla come modulo di PowerShell e connettersi ad Azure. 

### <a name="create-the-script"></a>Creare lo script

Per creare lo script, copiare lo [script completo](#full-script) alla fine di questo articolo e salvarlo localmente come `RegisterSqlVMs.psm1`. 

### <a name="import-the-script"></a>Importare lo script

Dopo aver creato lo script, è possibile importarlo come modulo nel terminale di PowerShell. 

Aprire un terminale amministrativo di PowerShell e passare al percorso in cui è stato salvato il file `RegisterSqlVMs.psm1`. Eseguire quindi il seguente cmdlet di PowerShell per importare lo script come modulo: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Connettersi ad Azure

Usare il seguente cmdlet di PowerShell per connettersi ad Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>Tutte le macchine virtuali in un elenco di sottoscrizioni 

Usare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in un elenco di sottoscrizioni:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Output di esempio: 

```
Number of subscriptions registration failed for 
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

Usare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in una singola sottoscrizione: 

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

Usare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in più gruppi di risorse all'interno di una singola sottoscrizione:

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

Usare il cmdlet seguente per registrare tutte le macchine virtuali di SQL Server in un singolo gruppo di risorse: 

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

## <a name="specific-vms-in-a-single-resource-group"></a>Macchine virtuali specifiche in un singolo gruppo di risorse

Usare il cmdlet seguente per registrare macchine virtuali di SQL Server specifiche in un singolo gruppo di risorse:

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

## <a name="a-specific-vm"></a>Una macchina virtuale specifica

Usare il cmdlet seguente per registrare una macchina virtuale di SQL Server specifica: 

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

Ogni volta che viene usato il cmdlet `Register-SqlVMs` vengono generati un report e un file di log. 

### <a name="report"></a>Report

Il report viene generato come file `.txt` denominato `RegisterSqlVMScriptReport<Timestamp>.txt` dove il timestamp è l'ora in cui è stato avviato il cmdlet. Il report contiene i dettagli seguenti:

| **Valore di output** | **Descrizione** |
| :--------------  | :-------------- | 
| La registrazione del numero di sottoscrizioni non è riuscita perché l'utente non può accedere o le credenziali non sono corrette | Vengono indicati il numero e l'elenco delle sottoscrizioni che presentano problemi relativi all'autenticazione. L'errore dettagliato si può reperire nel log cercando l'ID sottoscrizione. | 
| Numero di sottoscrizioni che non è stato possibile provare perché non sono registrate nel provider di risorse | Questa sezione contiene il conteggio e l'elenco delle sottoscrizioni che non sono state registrate nell'estensione SQL IaaS Agent. |
| Numero totale di macchine virtuali trovate | Numero di macchine virtuali trovate nell'ambito dei parametri passati al cmdlet. | 
| Macchine virtuali già registrate | Numero di macchine virtuali ignorate come già registrate con l'estensione. |
| Numero di macchine virtuali registrate correttamente | Numero di macchine virtuali registrate correttamente dopo l'esecuzione del cmdlet. Le macchine virtuali registrate sono riportate nel formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| Numero di VM che non è stato possibile registrare a causa di un errore | Numero di macchine virtuali che non sono state registrate a causa di un errore. I dettagli dell'errore si trovano nel file di log. | 
| Numero di VM ignorate perché la VM o l'agente guest nella VM non è in esecuzione | Numero ed elenco delle macchine virtuali che non è stato possibile registrare perché la macchina virtuale o l'agente guest nella macchina virtuale non era in esecuzione. Queste operazioni possono essere ritentate dopo l'avvio della macchina virtuale o dell'agente guest. I dettagli sono disponibili nel file di log. |
| Numero di VM ignorate poiché non eseguono SQL Server in Windows | Numero di macchine virtuali ignorate perché non eseguono SQL Server o non sono macchine virtuali Windows. Le macchine virtuali sono elencate nel formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>File di log 

Gli errori vengono registrati nel file di log denominato `VMsNotRegisteredDueToError<Timestamp>.log`, dove timestamp è l'ora di inizio dello script. Se l'errore è a livello di sottoscrizione, il log contiene l'ID sottoscrizione delimitato da virgole e il messaggio di errore. Se l'errore riguarda la registrazione della macchina virtuale, il log contiene l'ID sottoscrizione, il nome del gruppo di risorse, il nome della macchina virtuale, il codice di errore e il messaggio separati da virgole. 

## <a name="remarks"></a>Osservazioni

Quando si registrano SQL Server VM con l'estensione tramite lo script specificato, tenere presente quanto segue:

- Per la registrazione con l'estensione è necessario un agente guest in esecuzione nella macchina virtuale SQL Server. Le immagini di Windows Server 2008 non hanno un agente guest, pertanto queste macchine virtuali avranno esito negativo e dovranno essere registrate manualmente usando la [modalità di gestione NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes).
- Per superare gli errori trasparenti, è incorporata una logica di ripetizione dei tentativi. Se la macchina virtuale è stata registrata correttamente, si tratta di un'operazione rapida. Tuttavia, se la registrazione ha esito negativo, viene eseguito un nuovo tentativo per ogni macchina virtuale.  Di conseguenza, è necessario concedere un tempo significativo per il completamento del processo di registrazione, sebbene il requisito di tempo effettivo dipenda dal tipo e dal numero di errori. 

## <a name="full-script"></a>Script completo

Per lo script completo su GitHub, vedere [registrare in blocco SQL Server VM con AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copiare lo script completo e salvarlo come `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)
