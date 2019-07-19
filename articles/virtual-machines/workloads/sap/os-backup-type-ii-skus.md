---
title: Backup e ripristino del sistema operativo per SKU di tipo II di istanze Large di SAP HANA in Azure | Microsoft Docs
description: Eseguire il backup e il ripristino del sistema operativo per SAP HANA in SKU di tipo II di Azure (istanze large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3afcd429351a0d988ff0e82ecf09f524ceac70f1
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868976"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Backup e ripristino del sistema operativo per SKU di tipo II di indicatori di revisione 3

Questo documento descrive i passaggi per eseguire un backup e un ripristino a livello di file del sistema operativo per gli **SKU di tipo II** delle istanze large di Hana di revisione 3. 

>[!Important]
> **Questo articolo non si applica alle distribuzioni SKU di tipo II nei timbri delle istanze large di revisione 4 HANA.** I LUN di avvio di tipo II unità di istanze large di HANA distribuite nella revisione 4 gli indicatori di istanze large di HANA possono essere sottoposti a backup con snapshot di archiviazione, come nel caso degli SKU di tipo I già presenti negli indicatori di revisione 3


>[!NOTE]
>Gli script di backup del sistema operativo usano il software ReaR preinstallato nel server.  

Al termine del provisioning da parte del team `Service Management` Microsoft, per impostazione predefinita, il server viene configurato con due pianificazioni di backup per eseguire il backup del file System livello di backup del sistema operativo. È possibile controllare le pianificazioni dei processi di backup usando il comando seguente:
```
#crontab –l
```
È possibile modificare la pianificazione del backup in qualsiasi momento usando il comando seguente:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Esecuzione di un backup manuale

Il backup del sistema operativo file system è già pianificato usando un **processo cron** . Tuttavia, è possibile eseguire il backup del sistema operativo a livello di file system anche manualmente. Per eseguire un backup manuale, usare il comando seguente:

```
#rear -v mkbackup
```
Nella schermata seguente è illustrato il backup manuale di esempio:

![Procedura](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Procedura di ripristino di un backup

È possibile ripristinare un backup completo o un singolo file dal backup. A tale scopo, eseguire il comando seguente:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Dopo il ripristino il file viene recuperato nella directory di lavoro corrente.

Il comando seguente consente di eseguire il ripristino di un file */etc/fstab* dal file di backup *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>È necessario copiare il file nella posizione desiderata dopo il ripristino dal backup.

Lo screenshot seguente mostra il ripristino di un backup completo:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Installazione dello strumento ReaR e modifica della configurazione 

I pacchetti ReaR (Relax-and-Recover) sono **preinstallati** nelle **SKU di tipo II** delle istanze Large di HANA. Non è pertanto richiesto alcun intervento da parte dell'utente. È possibile iniziare a usare subito il pacchetto ReaR per il backup del sistema operativo.
Tuttavia, se è necessario installare pacchetti personalizzati, è possibile eseguire le procedure descritte di seguito per installare e configurare lo strumento ReaR.

Per installare i pacchetti **ReaR** per il backup, usare il comando seguente:

Per il sistema operativo **SLES**, usare il comando seguente:
```
#zypper install <rear rpm package>
```
Per il sistema operativo **RHEL**, usare il comando seguente: 
```
#yum install rear -y
```
Per configurare lo strumento ReaR, è necessario aggiornare i parametri **OUTPUT_URL** e **BACKUP_URL** nel *file /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Lo screenshot seguente mostra il ripristino di un backup completo: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
