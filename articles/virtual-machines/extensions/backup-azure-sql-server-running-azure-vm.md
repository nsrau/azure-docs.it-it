---
title: Backup di Azure per SQL Server in esecuzione nella macchina virtuale di Azure
description: Come registrare SQL Server di backup di Azure in esecuzione nella macchina virtuale di Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: vijayts
ms.openlocfilehash: 25f23078af67b2f80f39faab975cbec54721c560
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871907"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup di Azure per SQL Server in esecuzione nella macchina virtuale di Azure

Backup di Azure, tra le altre offerte, fornisce il supporto per il backup dei carichi di lavoro, ad esempio SQL Server in esecuzione nelle macchine virtuali di Azure. Poiché l'applicazione SQL è in esecuzione in una macchina virtuale di Azure, il servizio di backup richiede l'autorizzazione per accedere all'applicazione e recuperare i dettagli necessari.
A tale scopo, backup di Azure installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale in cui è in esecuzione il SQL Server durante il processo di registrazione attivato dall'utente.

## <a name="prerequisites"></a>Prerequisiti

Per l'elenco degli scenari supportati, vedere la [matrice di supporto](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) supportata da backup di Azure.

## <a name="network-connectivity"></a>Connettività di rete

Backup di Azure supporta i tag NSG, distribuendo un server proxy o intervalli di indirizzi IP elencati; per informazioni dettagliate su ognuno dei metodi, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema di estensione e i valori delle proprietà sono i valori di configurazione (impostazioni di Runtime) passati dal servizio all'API CRP. Questi valori di configurazione vengono utilizzati durante la registrazione e l'aggiornamento. Anche l'estensione **AzureBackupWindowsWorkload** usa questo schema. Lo schema è preimpostato. è possibile aggiungere un nuovo parametro nel campo objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Il codice JSON seguente mostra lo schema per l'estensione WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Valori delle proprietà

Name | Valore/esempio | Tipo di dati
 --- | --- | ---
locale | it-it  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> PublicSettings  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "Microsoft. Compute/VirtualMachines"  | string
objectStr <br/> ProtectedSettings | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | stringa
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | stringa


## <a name="template-deployment"></a>Distribuzione del modello

È consigliabile aggiungere l'estensione AzureBackupWindowsWorkload a una macchina virtuale abilitando SQL Server backup nella macchina virtuale. Questa operazione può essere eseguita tramite il [modello di gestione risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) progettato per automatizzare il backup in una VM SQL Server.


## <a name="powershell-deployment"></a>Distribuzione PowerShell

È necessario registrare la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali di servizi di ripristino. Durante la registrazione, l'estensione AzureBackupWindowsWorkload viene installata nella macchina virtuale. Usare il cmdlet [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) per registrare la macchina virtuale.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
Il comando restituirà un **contenitore di backup** di questa risorsa e lo stato sarà **registrato**.


## <a name="next-steps"></a>Passaggi successivi

- [Altre](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) informazioni sulle linee guida per la risoluzione dei problemi di backup delle VM di Azure SQL Server
- [Domande frequenti](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sul backup di database SQL Server eseguiti in macchine virtuali (VM) di Azure e che usano il servizio backup di Azure.
