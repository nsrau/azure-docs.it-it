---
title: Risolvere i problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure | Microsoft Docs
description: Fornisce indicazioni sulla risoluzione dei problemi per diagnosticare la causa del rallentamento delle prestazioni di backup per file e cartelle di Backup di Azure
services: backup
documentationcenter: 
author: genlin
manager: jimpark
editor: 
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ac68ae449a03b6d9e77e6093531a47c905654f16


---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Risolvere i problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure
Questo articolo fornisce indicazioni sulla risoluzione dei problemi per diagnosticare la causa del rallentamento delle prestazioni di backup per file e cartelle quando si usa Backup di Azure. Quando si usa l'agente di Backup di Azure per eseguire il backup dei file, è possibile che il processo richieda più tempo del previsto. Questo ritardo può dipendere da una o più delle cause seguenti:

* [Sono presenti colli di bottiglia delle prestazioni nel computer di cui viene eseguito il backup.](#cause1)
* [Un altro processo o un software antivirus interferisce con il processo di Backup di Azure.](#cause2)
* [L'agente di Backup è in esecuzione in una macchina virtuale di Azure.](#cause3)  
* [Viene eseguito il backup di un numero elevato di file (milioni).](#cause4)

Prima di iniziare a risolvere i problemi, è consigliabile scaricare e installare l' [agente di Backup di Azure più recente](http://aka.ms/azurebackup_agent). L'agente di Backup viene aggiornato di frequente per risolvere diversi problemi, aggiungere funzionalità e migliorare le prestazioni.

È anche consigliabile vedere l'articolo [Servizio Backup di Azure: Domande frequenti](backup-azure-backup-faq.md) per assicurarsi che non si tratti di problemi di configurazione comuni.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Colli di bottiglia delle prestazioni nel computer
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
| Processore - \%Tempo processore (tutte le istanze) |• Meno del 60% utilizzato = integro</br>• Dal 61% al 90% usato = monitoraggio o attenzione</br>• Dal 91% al 100% utilizzato = critico |

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

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: L'agente di Backup è in esecuzione in una macchina virtuale di Azure
Se si esegue l'agente di Backup in una macchina virtuale, le prestazioni risulteranno rallentate rispetto all'esecuzione in un computer fisico. Questo è un comportamento previsto a causa delle limitazioni delle operazioni di I/O al secondo.  È tuttavia possibile ottimizzare le prestazioni trasferendo le unità dati da includere nel backup ad Archiviazione Premium di Azure. La correzione di questo problema è in fase di risoluzione e sarà disponibile nelle versioni future.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Viene eseguito il backup di un numero elevato di file (milioni)
Lo spostamento di un grande volume di dati richiederà più tempo rispetto allo spostamento di un volume di dati inferiore. In alcuni casi, i tempi di backup sono correlati non solo alle dimensioni dei dati, ma anche al numero di file o cartelle. Ciò vale soprattutto quando sono sottoposti a backup milioni di file di piccole dimensioni (da pochi byte a pochi kilobyte).

Questo comportamento è dovuto al fatto che mentre è attivo il backup e lo spostamento dei dati in Azure, Azure cataloga contemporaneamente i file. In alcuni scenari rari, questa operazione può richiedere più tempo del previsto.

Gli indicatori seguenti possono risultare utili per individuare il collo di bottiglia e agire di conseguenza nei passaggi successivi:

* **L'interfaccia utente visualizza lo stato di avanzamento per il trasferimento dei dati**. I dati sono ancora in fase di trasferimento. La larghezza di banda di rete o le dimensioni dei dati potrebbero causare ritardi.
* **L'interfaccia utente non visualizza lo stato di avanzamento per il trasferimento dei dati**. Aprire i log disponibili in "C:\Agente di Servizi di ripristino di Microsoft Azure\Temp" e quindi cercare la voce FileProvider::EndData nei log. Questa voce indica che il trasferimento dei dati è stato completato e che l'operazione di catalogo è in corso. Non annullare i processi di backup. Al contrario, attendere ancora qualche istante che l'operazione di catalogo venga completata. Se il problema persiste, contattare il [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).



<!--HONumber=Nov16_HO3-->


