---
title: Installazione invisibile all'utente del server di Backup di Azure V2
description: Usare uno script di PowerShell per installare in modo invisibile il server di Backup di Azure V2. Questo tipo di installazione è chiamato anche installazione automatica.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: 66ed5765a91b607bc5b765926c5df87d13ff6a24
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253850"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Eseguire un'installazione automatica del server di Backup di Azure

Informazioni su come eseguire un'installazione automatica del server di Backup di Azure.

Questa procedura non è applicabile se si installa il server di Backup di Azure V1.

## <a name="install-backup-server"></a>Installare il server di Backup

1. Nel server che ospita il server di Backup di Azure V2 o versione successiva, creare un file di testo (è possibile creare il file nel Blocco note o in un altro editor di testo). Salvare il file con il nome MABSSetup.ini.

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
**/f**: percorso del file con estensione ini</br>
**/l**: percorso del log</br>
**/i**: percorso di installazione</br>
**/x**: percorso di disinstallazione</br>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato il server di backup, leggere le informazioni su come preparare il server o iniziare a proteggere un carico di lavoro.

- [Preparare i carichi di lavoro del server di backup](backup-azure-microsoft-azure-backup.md)
- [Usare il server di backup per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md)
- [Usare il server di backup per eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
- [Aggiungere Modern Backup Storage al server di backup](backup-mabs-add-storage.md)
