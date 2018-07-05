---
title: Backup e ripristino del sistema operativo per SKU di tipo II di istanze Large di SAP HANA in Azure | Microsoft Docs
description: Eseguire il backup e il ripristino del sistema operativo per SKU di tipo II di istanze Large di SAP HANA in Azure
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f01a32612b335003856a372ece15ef300b9d93db
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063275"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Backup e ripristino del sistema operativo per SKU di tipo II

Questo documento descrive la procedura per eseguire il backup e il ripristino del sistema operativo per **SKU di tipo II** di SAP HANA in istanze Large. 

>[!NOTE]
>Gli script di backup del sistema operativo usano il software ReaR preinstallato nel server.  

Dopo che il team di gestione dei servizi Microsoft ha completato il provisioning, per impostazione predefinita il server risulta configurato per la pianificazione dell'esecuzione di due backup del sistema operativo a livello di file system. È possibile controllare la pianificazione del processo di backup tramite il comando seguente:
```
#crontab –l
```
È possibile modificare la pianificazione del backup qualsiasi momento usando il comando seguente:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Esecuzione di un backup manuale

Il backup del file system del sistema operativo viene già pianificato usando un **processo cron**. Tuttavia, è possibile eseguire il backup del sistema operativo a livello di file system anche manualmente. Per eseguire un backup manuale, usare il comando seguente:

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

Nella schermata seguente è illustrato il ripristino di un backup completo:

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

Nella schermata seguente è illustrato il ripristino di un backup completo: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
