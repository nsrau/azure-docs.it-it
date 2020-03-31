---
title: Azure Backup for SQL Server running in Azure VM
description: In questo articolo viene illustrato come registrare Backup di Azure in SQL Server in esecuzione in una macchina virtuale di Azure.In this article, learn how to register Azure Backup in SQL Server running in an Azure virtual machine.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247385"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup for SQL Server running in Azure VM

Backup di Azure, tra le altre offerte, fornisce supporto per il backup di carichi di lavoro come SQL Server in esecuzione nelle macchine virtuali di Azure.Azure Backup, among other offerings, provides support for backing up workloads such as SQL Server running in Azure VMs. Poiché l'applicazione SQL è in esecuzione all'interno di una macchina virtuale di Azure, il servizio di backup deve disporre dell'autorizzazione per accedere all'applicazione e recuperare i dettagli necessari.
A tale scopo, Backup di Azure installa l'estensione **AzureBackupWindowsWorkload** nella macchina virtuale, in cui è in esecuzione SQL Server, durante il processo di registrazione attivato dall'utente.

## <a name="prerequisites"></a>Prerequisiti

Per l'elenco degli scenari supportati, fare riferimento alla matrice di supportabilità supportata da Backup di Azure.For the list of supported scenarios, refer to the [supportability matrix](../../backup/sql-support-matrix.md#scenario-support) supported by Azure Backup.

## <a name="network-connectivity"></a>Connettività di rete

Backup di Azure supporta i tag del gruppo di sicurezza di rete, la distribuzione di un server proxy o di intervalli IP elencati. per informazioni dettagliate su ciascuno dei metodi, fare riferimento a questo [articolo](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema di estensione e i valori delle proprietà sono i valori di configurazione (impostazioni di runtime) che il servizio sta passando all'API CRP. Questi valori di configurazione vengono utilizzati durante la registrazione e l'aggiornamento. Anche l'estensione **AzureBackupWindowsWorkload** usa questo schema. Lo schema è preimpostato; è possibile aggiungere un nuovo parametro nel campo objectStr

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

Il codice JSON seguente mostra lo schema per l'estensione WorkloadBackup.The following JSON shows the schema for the WorkloadBackup extension.  

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

Nome | Valore/esempio | Tipo di dati
 --- | --- | ---
locale | it-it  |  string
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | string
objectStr <br/> (publicSettings)  | "eyJjb250YWlu FpmVybmVySWQiOjM0NTY3ODg5LCJS-XNvdXJj-UlkImeMDU5NWIwOGEtEtYzI-MFlLWE5ODItOTkwOWOWMyMGVjNjVhIwiU3Vic2NyaXB0aW9uSWQiOJ kNGEz-OTliNy1iYYAyLTQ2MWMtoODddmYS1jNTM5-OD33-TgzNTQiLCJVbmlxdWVDb250YWlu-XJOYW1lIjoiODM4MD yjg3-NhG3OTcyIn0SInN0YW1wTGlzdCi6W3siU2Vydlj-U-5hbWUiOjUsIlNlcn-pY2VdGFtCXVybCI6Imh0dHA6XC9cL015V0xGYJTdMMuY29tIn1dfQ ==" | string
commandStartTimeUTCTicks | "636967192566036845"  | string
vmType (tipo vm)  | "microsoft.compute/virtualmachines"  | string
objectStr <br/> (protectedSettings) | "eyJjb250YWlu FpmVybmVySWQiOjM0NTY3ODg5LCJS-XNvdXJj-UlkImeMDU5NWIwOGEtEtYzI-MFlLWE5ODItOTkwOWOWMyMGVjNjVhIwiU3Vic2NyaXB0aW9uSWQiOJ kNGEz-OTliNy1iYYAyLTQ2MWMtoODddmYS1jNTM5-OD33-TgzNTQiLCJVbmlxdWVDb250YWlu-XJOYW1lIjoiODM4MD yjg3-NhG3OTcyIn0SInN0YW1wTGlzdCi6W3siU2Vydlj-U-5hbWUiOjUsIlNlcn-pY2VdGFtCXVybCI6Imh0dHA6XC9cL015V0xGYJTdMMuY29tIn1dfQ ==" | string
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | string

## <a name="template-deployment"></a>Distribuzione del modello

È consigliabile aggiungere l'estensione AzureBackupWindowsWorkload a una macchina virtuale abilitando il backup di SQL Server nella macchina virtuale. Ciò può essere ottenuto tramite il [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) progettato per l'automazione del backup in una macchina virtuale di SQL Server.This can be achieved through the Resource Manager template designed for automating backup on a SQL Server VM.

## <a name="powershell-deployment"></a>Distribuzione PowerShell

È necessario 'registrare' la macchina virtuale di Azure che contiene l'applicazione SQL con un insieme di credenziali dei servizi di ripristino. Durante la registrazione, l'estensione AzureBackupWindowsWorkload viene installata nella macchina virtuale. Utilizzare il cmdlet [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) per registrare la macchina virtuale.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Il comando restituirà un **contenitore** di backup di questa risorsa e lo stato verrà **registrato.**

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) sulle linee guida per la risoluzione dei problemi di backup della macchina virtuale di Azure SQL ServerLearn more about Azure SQL Server VM backup troubleshooting guidelines
- [Domande comuni](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) sul backup dei database di SQL Server eseguiti nelle macchine virtuali di Azure e sull'uso del servizio Backup di Azure.Common questions about backing up SQL Server databases that run on Azure virtual machines (VMs) and that use the Azure Backup service.
