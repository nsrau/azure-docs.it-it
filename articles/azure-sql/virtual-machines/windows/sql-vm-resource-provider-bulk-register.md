---
title: Registrare in blocco macchine virtuali SQL in Azure con il provider di risorse per VM SQL | Microsoft Docs
description: Registrare in blocco VM di SQL Server con il provider di risorse per VM SQL per ottimizzare la gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b83a44db98907f505c7bf0d8302470cf3031a967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761261"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrare più macchine virtuali SQL in Azure con il provider di risorse per VM SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come registrare in blocco le macchine virtuali (VM) di SQL Server in Azure con il provider di risorse per macchine virtuali SQL usando il cmdlet `Register-SqlVMs` di PowerShell.

Questo articolo illustra come registrare SQL Server VM in blocco. In alternativa, è possibile registrare [tutte le vm SQL Server automaticamente](sql-vm-resource-provider-automatic-registration.md) o [singole SQL Server VM](sql-vm-resource-provider-register.md). 

## <a name="overview"></a>Panoramica

Il cmdlet `Register-SqlVMs` può essere usato per registrare tutte le macchine virtuali in un elenco specifico di sottoscrizioni, gruppi di risorse o un elenco di macchine virtuali specifiche. Il cmdlet registrerà le macchine virtuali in modalità di gestione _leggera_, quindi genererà sia un [report che un file di log](#output-description). 

Il processo di registrazione non comporta alcun rischio, non ha tempi di inattività e non riavvia SQL Server o la macchina virtuale. 

Per altre informazioni sul provider di risorse, vedere l'articolo relativo al [provider di risorse per VM SQL](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/) che sia [registrata con il provider di risorse](sql-vm-resource-provider-register.md#register-subscription-with-rp) e contenga macchine virtuali di SQL Server non registrate. 
- Le credenziali client usate per registrare le macchine virtuali sono disponibili in uno dei seguenti ruoli di Azure: **collaboratore macchina virtuale**, **collaboratore**o **proprietario**. 
- La versione più recente di [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- La versione più recente di [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

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


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Registrare tutte le macchine virtuali in un elenco di sottoscrizioni 

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

## <a name="register-all-vms-in-a-single-subscription"></a>Registrare tutte le macchine virtuali in una singola sottoscrizione

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

## <a name="register-all-vms-in-multiple-resource-groups"></a>Registrare tutte le macchine virtuali in più gruppi di risorse

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

## <a name="register-all-vms-in-a-resource-group"></a>Registrare tutte le macchine virtuali in un gruppo di risorse

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

## <a name="register-specific-vms-in-a-single-resource-group"></a>Registrare macchine virtuali specifiche in un singolo gruppo di risorse

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

## <a name="register-a-specific-vm"></a>Registrare una macchina virtuale specifica

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
| Numero di sottoscrizioni che non è stato possibile provare perché non sono registrate con il provider di risorse | Questa sezione contiene il conteggio e l'elenco delle sottoscrizioni che non sono state registrate con il provider di risorse per le VM SQL. |
| Numero totale di macchine virtuali trovate | Numero di macchine virtuali trovate nell'ambito dei parametri passati al cmdlet. | 
| Macchine virtuali già registrate | Numero di macchine virtuali ignorate perché erano già registrate con il provider di risorse. |
| Numero di macchine virtuali registrate correttamente | Numero di macchine virtuali registrate correttamente dopo l'esecuzione del cmdlet. Le macchine virtuali registrate sono riportate nel formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| Numero di VM che non è stato possibile registrare a causa di un errore | Numero di macchine virtuali che non sono state registrate a causa di un errore. I dettagli dell'errore si trovano nel file di log. | 
| Numero di VM ignorate perché la VM o l'agente guest nella VM non è in esecuzione | Numero ed elenco delle macchine virtuali che non è stato possibile registrare perché la macchina virtuale o l'agente guest nella macchina virtuale non era in esecuzione. Queste operazioni possono essere ritentate dopo l'avvio della macchina virtuale o dell'agente guest. I dettagli sono disponibili nel file di log. |
| Numero di VM ignorate poiché non eseguono SQL Server in Windows | Numero di macchine virtuali ignorate perché non eseguono SQL Server o non sono macchine virtuali Windows. Le macchine virtuali sono elencate nel formato `SubscriptionID, Resource Group, Virtual Machine`. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>File di log 

Gli errori vengono registrati nel file di log denominato `VMsNotRegisteredDueToError<Timestamp>.log`, dove timestamp è l'ora di inizio dello script. Se l'errore è a livello di sottoscrizione, il log contiene l'ID sottoscrizione delimitato da virgole e il messaggio di errore. Se l'errore riguarda la registrazione della macchina virtuale, il log contiene l'ID sottoscrizione, il nome del gruppo di risorse, il nome della macchina virtuale, il codice di errore e il messaggio separati da virgole. 

## <a name="remarks"></a>Osservazioni

Quando si registrano le VM di SQL Server con il provider di risorse usando lo script a disposizione, tenere presente quanto segue:

- La registrazione con il provider di risorse richiede un agente guest in esecuzione nella VM di SQL Server. Le immagini di Windows Server 2008 non hanno un agente guest, pertanto queste macchine virtuali avranno esito negativo e dovranno essere registrate manualmente usando la [modalità di gestione NoAgent](sql-vm-resource-provider-register.md#management-modes).
- Per superare gli errori trasparenti, è incorporata una logica di ripetizione dei tentativi. Se la macchina virtuale è stata registrata correttamente, si tratta di un'operazione rapida. Tuttavia, se la registrazione ha esito negativo, viene eseguito un nuovo tentativo per ogni macchina virtuale.  Di conseguenza, è necessario concedere un tempo significativo per il completamento del processo di registrazione, sebbene il requisito di tempo effettivo dipenda dal tipo e dal numero di errori. 

## <a name="full-script"></a>Script completo

Per lo script completo su GitHub, vedere l'articolo sulla [registrazione in blocco delle VM SQL con AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Copiare lo script completo e salvarlo come `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)
