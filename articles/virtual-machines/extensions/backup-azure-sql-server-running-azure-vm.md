---
title: Backup di Azure per SQL Server in esecuzione nella macchina virtuale di Azure
description: Come registrare Azure Backup di SQL Server in esecuzione nella macchina virtuale di Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607611"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Backup di Azure per SQL Server in esecuzione nella macchina virtuale di Azure

Backup di Azure, tra le altre offerte offre supporto per il backup dei carichi di lavoro, ad esempio SQL Server in esecuzione in macchine virtuali di Azure. Poiché l'applicazione del database SQL è in esecuzione all'interno di una VM di Azure, servizio di backup necessita dell'autorizzazione per accedere all'applicazione per ottenere i dettagli necessari.
A tale scopo, Backup di Azure installa il **AzureBackupWindowsWorkload** estensione nella macchina virtuale, in cui è in esecuzione SQL Server, durante il processo di registrazione attivato dall'utente.

## <a name="prerequisites"></a>Prerequisiti

Per l'elenco degli scenari supportati, vedere la [riporta la matrice](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) supportati da Backup di Azure.

## <a name="network-connectivity"></a>Connettività di rete

Backup di Azure supporta i tag di sicurezza di rete, la distribuzione di un server proxy o elencati gli intervalli IP; Per informazioni dettagliate su ciascuno dei metodi, fare riferimento a questo [articolo](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schema dell'estensione

I valori di proprietà e schema di estensione sono i valori di configurazione (impostazioni di runtime) che servizio sta passando all'API CRP. Questi valori di configurazione vengono usati durante la registrazione e aggiornamento. **AzureBackupWindowsWorkload** estensione inoltre utilizzare questo schema. Lo schema è preimpostato; può essere aggiunto un nuovo parametro nel campo objectStr

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

NOME | Valore/esempio | Tipo di dati
 --- | --- | ---
locale | it-it  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = = " | string
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | stringa
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | stringa


## <a name="template-deployment"></a>Distribuzione del modello

È consigliabile aggiungere estensione AzureBackupWindowsWorkload a una macchina virtuale è per l'abilitazione del backup di SQL Server nella macchina virtuale. Ciò può essere ottenuto tramite il [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) progettato per l'automatizzazione di backup in una VM di SQL Server.


## <a name="powershell-deployment"></a>Distribuzione PowerShell

È necessario registrare la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali di servizi di ripristino. Durante la registrazione, estensione AzureBackupWindowsWorkload viene installato nella macchina virtuale. Uso [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet per registrare la macchina virtuale.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
Il comando restituirà un' **contenitore di backup** di questa risorsa e lo stato sarà **registrata**.


## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) su backup di VM di SQL Server la risoluzione dei problemi delle linee guida
- [Domande frequenti](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sul backup dei database di SQL Server in esecuzione su macchine virtuali di Azure (VM) e che usano il servizio Backup di Azure.
