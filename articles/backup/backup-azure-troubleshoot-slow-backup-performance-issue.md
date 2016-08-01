<properties
   pageTitle="Risoluzione dei problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure| Microsoft Azure"
   description="Fornisce indicazioni sulla risoluzione dei problemi per facilitare la diagnosi e limitare la causa del problema di prestazioni di Backup di Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="markgal"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/20/2016"
    ms.author="genli"/>

# Risoluzione dei problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure

Questo articolo fornisce indicazioni sulla risoluzione dei problemi per facilitare la diagnosi e limitare la causa del rallentamento delle prestazioni di backup per file e cartelle con Backup di Azure. Quando si usa l'agente di Backup di Azure per eseguire il backup dei file, è possibile che il processo richieda più tempo del previsto. Questo problema può dipendere da una o più delle cause seguenti:

-	[Colli di bottiglia delle prestazioni nel computer in cui viene eseguito il backup](#cause1).
-	[Un altro processo o un software antivirus interferisce con Backup di Azure](#cause2).
-	[L'agente di Backup è in esecuzione in una macchina virtuale di Azure](#cause3).
-	[Si sta tentando di eseguire il backup di un numero elevato di file (diversi milioni)](#cause4).

La sezione successiva descrive come determinare la causa specifica del problema.

Prima di iniziare a risolvere il problema, è consigliabile scaricare e installare l'[agente di Backup di Azure più recente](http://aka.ms/azurebackup_agent). L'agente di Backup viene aggiornato di frequente per risolvere diversi problemi, aggiungere funzionalità e migliorare le prestazioni.

È anche consigliabile vedere l'articolo [Servizio Backup di Azure: Domande frequenti](backup-azure-backup-faq.md) per assicurarsi che non si tratti di problemi di configurazione comuni.

## Passaggi per la risoluzione dei problemi
<a id="cause1"></a>
## Causa 1: Backup lento a causa di colli di bottiglia delle prestazioni nel computer in cui viene eseguito il backup

### Soluzione

Nel computer di cui viene eseguito il backup possono verificarsi colli di bottiglia che causano ritardi. Ad esempio, capacità del computer di leggere o scrivere su disco, larghezze di banda per inviare dati in rete e così via.

Windows include uno strumento predefinito chiamato [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) per rilevare questi colli di bottiglia. Nella tabella seguente sono riepilogati i contatori delle prestazioni e gli intervalli per ottenere backup ottimali.

Ecco alcuni contatori delle prestazioni e intervalli che possono essere utili per diagnosticare i colli di bottiglia.

| Contatore | Stato |
|---|---|
|Disco logico (disco fisico) - % inattività | • Da 100% inattivo a 50% inattivo = integro</br>• Da 49% inattivo a 20% inattivo = avviso o monitoraggio</br>• Da 19% inattivo a 0% inattivo = critico o fuori specifica|
| Disco logico (disco fisico) - % Media letture o scritture disco/sec | • Da 0,001 ms a 0,015 ms = integro</br>• Da 0,015 ms a 0,025 = avviso o monitoraggio</br>• Da 0,026 ms o più = critico o fuori specifica|
| Disco logico (disco fisico) - Lunghezza corrente coda del disco (per tutte le istanze) | 80 richieste per più di 6 minuti |
| Memoria - Byte del pool non di paging|• Meno del 60% del pool utilizzato = integro<br>• Da 61% a 80% del pool utilizzato = avviso o monitoraggio</br>• Più dell'80% del pool utilizzato = critico o fuori specifica|
| Memoria - Byte del pool di paging |• Meno del 60% del pool utilizzato = integro</br>• Da 61% a 80% del pool utilizzato = avviso o monitoraggio</br>• Più dell'80% del pool utilizzato = critico o fuori specifica|
| Memoria - MByte disponibili| • 50% o più di memoria libera disponibile = integro</br>• 25% di memoria libera disponibile = monitoraggio</br>• 10% di memoria libera disponibile = avviso</br>• Meno di 100 MB o del 5% di memoria libera disponibile = critico o fuori specifica|
|Processore - \\% Tempo processore (tutte le istanze)|• Meno del 60% utilizzato = integro</br>• Da 61% a 90% utilizzato = monitoraggio o attenzione</br>• Da 91% a 100% utilizzato = critico|


> [AZURE.NOTE] Se l'infrastruttura viene isolata come possibile causa del problema, è consigliabile deframmentare i dischi protetti a intervalli regolari per ottenere prestazioni migliori.

<a id="cause2"></a>
## Causa 2: Un altro processo o un software antivirus interferisce con Backup di Azure

Sono state identificate diverse istanze in cui altri processi all'interno del sistema Windows hanno influito negativamente sulle prestazioni del processo dell'agente di Backup di Azure. Ad esempio, se si usa l'agente di Backup di Azure e un altro programma per eseguire il backup dei dati o è in esecuzione un software antivirus con un blocco sui file di cui eseguire il backup, i diversi blocchi sui file possono causare conflitti. In questo caso, il backup può non riuscire oppure il processo può richiedere più tempo del previsto.

### Soluzione

L'indicazione migliore in questo scenario consiste nel disattivare l'altro programma di backup per vedere se cambia il tempo di backup per l'agente di Backup di Azure. In genere, è sufficiente assicurarsi che non siano in esecuzione più processi di backup contemporaneamente per impedire che interferiscano reciprocamente.

Per i programmi antivirus è consigliabile escludere i file e percorsi seguenti:

- Escludere C:\\Programmi\\Microsoft Azure Recovery Services Agent\\bin\\cbengine.exe come processo.
- Escludere le cartelle C:\\Programmi\\\Microsoft Azure Recovery Services Agent\\.
- Escludere lo spazio di lavoro, se non si usa il percorso standard precedente.

<a id="cause3"></a>
## Causa 3: L'agente di Backup è in esecuzione in una macchina virtuale di Azure

### Soluzione

Se si esegue l'agente di Backup in una macchina virtuale, le prestazioni risulteranno rallentate rispetto all'esecuzione in un computer fisico. Questo è un comportamento previsto a causa delle limitazioni delle operazioni di I/O al secondo. È tuttavia possibile ottimizzare le prestazioni trasferendo le unità dati da includere nel backup all'Archiviazione Premium. La correzione di questo problema è in fase di risoluzione e sarà disponibile nelle versioni future.

<a id="cause4"></a>
## Causa 4: Viene eseguito il backup di un numero elevato di file (diversi milioni)

### Soluzione

È previsto che lo spostamento di un grande volume di dati richiederà più tempo rispetto a un volume inferiore. Tuttavia, in alcuni casi, il tempo di backup non è correlato solo alle dimensioni dei dati, ma anche al numero di file o cartelle, in particolare in uno scenario in cui vengono inclusi nel backup milioni di file di piccole dimensioni (da pochi byte a pochi kilobyte).

Questo comportamento si verifica perché mentre si esegue il backup dei dati e lo spostamento in Azure, i file vengono contemporaneamente catalogati e, in alcuni casi rari, questa operazione di catalogo può richiedere più tempo.

Seguire questa procedura per individuare il collo di bottiglia e agire di conseguenza nei passaggi successivi:

a. **L'interfaccia utente visualizza lo stato di avanzamento per la quantità di dati trasferiti**: in questo caso, i dati sono ancora in fase di trasferimento e la larghezza di banda di rete o le dimensioni dei dati possono causare ritardi.

b. **L'interfaccia utente non visualizza lo stato di avanzamento**: in questo caso, aprire i log disponibili in "C:\\Microsoft Azure Recovery Services Agent\\Temp" e quindi cercare la voce "FileProvider::EndData" nei log. Questa voce indica che il trasferimento dei dati è stato completato e che l'operazione di catalogo è in corso. Non annullare i processi di backup, ma attendere ancora qualche istante che il catalogo venga completato. Se il problema persiste, contattare il [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).

<!---HONumber=AcomDC_0720_2016-->