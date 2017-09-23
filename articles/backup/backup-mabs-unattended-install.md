---
title: Installazione invisibile del server di Backup di Azure v2 | Microsoft Docs
description: "Usare uno script di PowerShell per installare in modo invisibile il server di Backup di Azure v2. Questo tipo di installazione è chiamato anche installazione automatica."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Eseguire un'installazione automatica del server di Backup di Azure v2

Informazioni su come eseguire un'installazione automatica del server di Backup di Azure v2. 

Questi passaggi non sono applicabili se si installa il server di Backup di Azure v1.

## <a name="install-backup-server-v2"></a>Installare il server di backup v2

1. Nel server che ospita il server di Backup di Azure v2, creare un file di testo. (è possibile creare il file nel Blocco note o in un altro editor di testo). Salvare il file con il nome MABSSetup.ini. 

2. Incollare il codice seguente nel file MABSSetup.ini. Sostituire il testo all'interno delle parentesi (\< \>) con i valori dell'ambiente. Il testo seguente è un esempio:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Salvare il file. Quindi, a un prompt con privilegi elevati nel server di installazione, immettere questo comando:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

È possibile usare questi flag per l'installazione:</br>
**/f**: percorso del file .ini</br>
**/l**: percorso del log</br>
**/i**: percorso di installazione</br>
**/x**: percorso di disinstallazione</br>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato il server di backup, leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro.

- [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
- [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
- [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
- [Aggiungere Modern Backup Storage al server di backup](backup-mabs-add-storage.md)

