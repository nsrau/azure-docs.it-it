---
title: Risolvere i problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure
description: Fornisce indicazioni sulla risoluzione dei problemi per diagnosticare la causa del rallentamento delle prestazioni di backup per file e cartelle di Backup di Azure
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: saurabhsensharma
ms.openlocfilehash: 1bc9c7b4f6e2a4f2a7c712d966caac74b73518df
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565655"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Risolvere i problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure
Questo articolo fornisce indicazioni sulla risoluzione dei problemi per diagnosticare la causa del rallentamento delle prestazioni di backup per file e cartelle quando si usa Backup di Azure. Quando si usa l'agente di Backup di Azure per eseguire il backup dei file, è possibile che il processo richieda più tempo del previsto. Questo ritardo può dipendere da una o più delle cause seguenti:

* [Sono presenti colli di bottiglia delle prestazioni nel computer di cui viene eseguito il backup.](#cause1)
* [Un altro processo o un software antivirus interferisce con il processo di Backup di Azure.](#cause2)
* [L'agente di Backup è in esecuzione in una macchina virtuale di Azure.](#cause3)  
* [Viene eseguito il backup di un numero elevato di file (milioni).](#cause4)

Prima di iniziare a risolvere i problemi, è consigliabile scaricare e installare l' [agente di Backup di Azure più recente](https://aka.ms/azurebackup_agent). L'agente di Backup viene aggiornato di frequente per risolvere diversi problemi, aggiungere funzionalità e migliorare le prestazioni.

È anche consigliabile vedere l'articolo [Servizio Backup di Azure: Domande frequenti](backup-azure-backup-faq.md) per assicurarsi che non si tratti di problemi di configurazione comuni.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Colli di bottiglia nel computer
I colli di bottiglia nel computer in cui viene eseguito il backup possono causare ritardi. Ad esempio, i colli di bottiglia possono essere causati dalla capacità del computer di leggere o scrivere su disco o dalla larghezza di banda disponibile per inviare dati in rete.

Per rilevare i colli di bottiglia, Windows fornisce uno strumento predefinito chiamato [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon).

Ecco alcuni contatori delle prestazioni e intervalli che possono essere utili per diagnosticare i colli di bottiglia e ottenere backup ottimali.

| Contatore | Stato |
| --- | --- |
| Disco logico (disco fisico) - % inattività |• Da 100% inattivo a 50% inattivo = integro</br>• Da 49% inattivo a 20% inattivo = avviso o monitoraggio</br>• Da 19% inattivo a 0% inattivo = critico o fuori specifica |
| Disco logico (disco fisico) - % Media letture o scritture disco/sec |• Da 0,001 ms a 0,015 ms = integro</br>• Da 0,015 ms a 0,025 ms = avviso o monitoraggio</br>• Da ms 0,026 e oltre = critico o fuori specifica |
| Disco logico (disco fisico) - Lunghezza corrente coda del disco (per tutte le istanze) |80 richieste per più di 6 minuti |
| Memoria - Byte del pool non di paging |• Meno del 60% del pool utilizzato = integro<br>• Dal 61% all'80% del pool usato = avviso o monitoraggio</br>• Più dell'80% del pool utilizzato = critico o fuori specifica |
| Memoria - Byte del pool di paging |• Meno del 60% del pool utilizzato = integro</br>• Dal 61% all'80% del pool usato = avviso o monitoraggio</br>• Più dell'80% del pool utilizzato = critico o fuori specifica |
| Memoria - MByte disponibili |• 50% o più di memoria libera disponibile = integro</br>• 25% di memoria libera disponibile = monitoraggio</br>• 10% di memoria libera disponibile = avviso</br>• Meno di 100 MB o 5% della memoria disponibile = critico o fuori specifica |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processore - \`Tempo processore (tutte le istanze)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: saurabhsensharma
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Meno del 60% utilizzato = integro</br>• Dal 61% al 90% usato = monitoraggio o attenzione</br>• Dal 91% al 100% utilizzato = critico |

> [!NOTE]
> Se si determina che l'infrastruttura è la causa del problema, è consigliabile deframmentare i dischi a intervalli regolari per ottenere prestazioni migliori.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Un altro processo o un software antivirus interferisce con Backup di Azure
Sono state identificate diverse istanze in cui altri processi nel sistema Windows hanno influito negativamente sulle prestazioni del processo dell'agente di Backup di Azure. Ad esempio, se si usa l'agente di Backup di Azure e un altro programma per eseguire il backup dei dati o se è in esecuzione un software antivirus con un blocco sui file di cui eseguire il backup, i diversi blocchi sui file possono causare conflitti. In questo caso, il backup può non riuscire oppure il processo può richiedere più tempo del previsto.

L'indicazione migliore in questo scenario consiste nel disattivare l'altro programma di backup per vedere se cambia il tempo di backup per l'agente di Backup di Azure. In genere, è sufficiente assicurarsi che non siano in esecuzione più processi di backup contemporaneamente per evitare che interferiscano reciprocamente.

Per i programmi antivirus è consigliabile escludere i file e percorsi seguenti:

* C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\bin\cbengine.exe come processo
* Cartelle in C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\
* Spazio di lavoro, se non si usa il percorso standard precedente

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente di backup in esecuzione in una macchina virtuale di Azure
Se si esegue l'agente di Backup in una macchina virtuale, le prestazioni risulteranno rallentate rispetto all'esecuzione in un computer fisico. Questo è un comportamento previsto a causa delle limitazioni delle operazioni di I/O al secondo.  È tuttavia possibile ottimizzare le prestazioni trasferendo le unità dati da includere nel backup ad Archiviazione Premium di Azure. La correzione di questo problema è in fase di risoluzione e sarà disponibile nelle versioni future.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Backup di un numero elevato (milioni) di file
Lo spostamento di un grande volume di dati richiederà più tempo rispetto allo spostamento di un volume di dati inferiore. In alcuni casi, i tempi di backup sono correlati non solo alle dimensioni dei dati, ma anche al numero di file o cartelle. Ciò vale soprattutto quando sono sottoposti a backup milioni di file di piccole dimensioni (da pochi byte a pochi kilobyte).

Questo comportamento è dovuto al fatto che mentre è attivo il backup e lo spostamento dei dati in Azure, Azure cataloga contemporaneamente i file. In alcuni scenari rari, questa operazione può richiedere più tempo del previsto.

Gli indicatori seguenti possono risultare utili per individuare il collo di bottiglia e agire di conseguenza nei passaggi successivi:

* **L'interfaccia utente visualizza lo stato di avanzamento per il trasferimento dei dati**. I dati sono ancora in fase di trasferimento. La larghezza di banda di rete o le dimensioni dei dati potrebbero causare ritardi.
* **L'interfaccia utente non visualizza lo stato di avanzamento per il trasferimento dei dati**. Aprire i log disponibili in "C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\Temp" e quindi cercare la voce FileProvider::EndData nei log. Questa voce indica che il trasferimento dei dati è stato completato e che l'operazione di catalogo è in corso. Non annullare i processi di backup. Al contrario, attendere ancora qualche istante che l'operazione di catalogo venga completata. Se il problema persiste, contattare il [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).
