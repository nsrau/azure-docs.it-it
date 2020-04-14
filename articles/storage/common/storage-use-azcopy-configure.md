---
title: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy con Archiviazione di Azure Documenti Microsoft
description: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy.Configure, optimize, and troubleshoot AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 87a335f44a31436de735395adbee9035493cbbd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263421"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi di AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file in o da un account di archiviazione. Questo articolo consente di eseguire attività di configurazione avanzate e consente di risolvere i problemi che possono verificarsi durante l'utilizzo di AzCopy.This article helps you to perform advanced configuration tasks and helps you to troubleshoot issues that can rise as you use AzCopy.

> [!NOTE]
> Per informazioni su come iniziare a usare AzCopy, consulta uno degli articoli seguenti:
> - [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
> - [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)
> - [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
> - [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy `https_proxy` per AzCopy, impostare la variabile di ambiente. Se si esegue AzCopy in Windows, AzCopy rileva automaticamente le impostazioni proxy, pertanto non è necessario utilizzare questa impostazione in Windows.If you run AzCopy on Windows, AzCopy automatically detects proxy settings, so you don't have to use this setting in Windows. Se si sceglie di utilizzare questa impostazione in Windows, verrà ignorato il rilevamento automatico.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | In un prompt dei comandi utilizzare:`set https_proxy=<proxy IP>:<proxy port>`<br> In PowerShell usare:In PowerShell use:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Attualmente, AzCopy non supporta proxy che richiedono l'autenticazione con NTLM o Kerberos.

## <a name="optimize-performance"></a>Ottimizzare le prestazioni

È possibile eseguire il benchmark delle prestazioni e quindi usare i comandi e le variabili di ambiente per trovare un compromesso ottimale tra prestazioni e consumo di risorse.

Questa sezione consente di eseguire queste attività di ottimizzazione:This section helps you perform these optimization tasks:

> [!div class="checklist"]
> * Eseguire test di benchmark
> * Ottimizzare la velocità effettiva
> * Ottimizzare l'utilizzo della memoria 
> * Ottimizzare la sincronizzazione dei file

### <a name="run-benchmark-tests"></a>Eseguire test di benchmark

È possibile eseguire un test di benchmark delle prestazioni in contenitori BLOB specifici per visualizzare le statistiche generali sulle prestazioni e i colli di bottiglia delle prestazioni delle identità. 

Utilizzare il comando seguente per eseguire un test di benchmark delle prestazioni.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Esempio** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> In questo esempio gli argomenti di percorso racchiudono le virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

Questo comando esegue un benchmark delle prestazioni caricando i dati di test in una destinazione specificata. I dati di test vengono generati in memoria, caricati nella destinazione, quindi eliminati dalla destinazione al termine del test. È possibile specificare il numero di file da generare e le dimensioni desiderate utilizzando parametri di comando facoltativi.

Per documenti di riferimento dettagliati, vedere [azcopy bench](storage-ref-azcopy-bench.md).

Per visualizzare istruzioni dettagliate della `azcopy bench -h` Guida per questo comando, digitare e quindi premere INVIO.

### <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

È possibile `cap-mbps` usare il flag nei comandi per inserire un limite massimo alla velocità effettiva della velocità effettiva. Ad esempio, il comando seguente riprende un processo `10` e limita la velocità effettiva in megabit (MB) al secondo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

La velocità effettiva può diminuire durante il trasferimento di file di piccole dimensioni. È possibile aumentare la velocità `AZCOPY_CONCURRENCY_VALUE` effettiva impostando la variabile di ambiente. Questa variabile specifica il numero di richieste simultanee che possono verificarsi.  

Se il computer dispone di meno di 5 CPU, `32`il valore di questa variabile è impostato su . In caso contrario, il valore predefinito è uguale a 16 moltiplicato per il numero di CPU. Il valore predefinito massimo `3000`di questa variabile è , ma è possibile impostarlo manualmente su un valore superiore o inferiore. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilizzare `azcopy env` il per controllare il valore corrente di questa variabile. Se il valore è vuoto, è possibile leggere il valore utilizzato esaminando l'inizio di qualsiasi file di log AzCopy.If the value is blank, then you can read which value is being used by looking at beginning of any AzCopy log file. Il valore selezionato e il motivo per cui è stato selezionato vengono riportati in questa posizione.

Prima di impostare questa variabile, è consigliabile eseguire un test di benchmark. Il processo di test di benchmark segnalerà il valore di concorrenza consigliato. In alternativa, se le condizioni di rete e `AUTO` i payload variano, impostare questa variabile sulla parola anziché su un numero specifico. In questo modo AzCopy eseguirà sempre lo stesso processo di ottimizzazione automatica utilizzato nei test di benchmark.

### <a name="optimize-memory-use"></a>Ottimizzare l'utilizzo della memoria

Impostare `AZCOPY_BUFFER_GB` la variabile di ambiente per specificare la quantità massima di memoria di sistema che si desidera venga utilizzata da AzCopy durante il download e il caricamento dei file.
Esprimere questo valore in gigabyte (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Ottimizzare la sincronizzazione dei file

Il comando [sync](storage-ref-azcopy-sync.md) identifica tutti i file nella destinazione, quindi confronta i nomi dei file e i timestamp delle ultime modifiche prima dell'avvio dell'operazione di sincronizzazione. Se si dispone di un numero elevato di file, è possibile migliorare le prestazioni eliminando questa elaborazione iniziale. 

A tale scopo, utilizzare invece il comando `--overwrite` [azcopy copy](storage-ref-azcopy-copy.md) e impostare il flag su `ifSourceNewer`. AzCopy confronterà i file come vengono copiati senza eseguire scansioni e confronti iniziali. Ciò fornisce un vantaggio sulle prestazioni nei casi in cui è presente un numero elevato di file da confrontare.

Il comando [azcopy copy](storage-ref-azcopy-copy.md) non elimina i file dalla destinazione, pertanto se si desidera eliminare i file nella destinazione quando non esistono più `true` `prompt`nell'origine, utilizzare il comando [azcopy sync](storage-ref-azcopy-sync.md) con il `--delete-destination` flag impostato su un valore di o . 

## <a name="troubleshoot-issues"></a>Risolvere i problemi

AzCopy crea file di log e piani per ogni processo. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. 

I registri conterranno lo`UPLOADFAILED` `COPYFAILED`stato `DOWNLOADFAILED`di errore ( , e ) il percorso completo e il motivo dell'errore.

Per impostazione predefinita, i file `%USERPROFILE%\.azcopy` di log `$HOME$\.azcopy` e piano si trovano nella directory di Windows o nella directory su Mac e Linux, ma è possibile modificare tale posizione, se lo si desidera.

L'errore pertinente non è necessariamente il primo errore visualizzato nel file. Per errori quali errori di rete, timeout ed errori di server occupato, AzCopy ritenterà fino a 20 volte e in genere il processo di ripetizione dei tentativi ha esito positivo.  Il primo errore visualizzato potrebbe essere qualcosa di innocuo che è stato ritentato con successo.  Quindi, invece di guardare il primo errore nel file, `COPYFAILED`cercare `DOWNLOADFAILED`gli errori che sono vicino `UPLOADFAILED`, , o . 

> [!IMPORTANT]
> Quando si invia una richiesta al supporto tecnico Microsoft (o si risolve il problema che coinvolge terze parti), condividere la versione redatta del comando che si desidera eseguire. In questo modo la sAS non viene accidentalmente condivisa con nessuno. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà `UPLOADFAILED` tutti `04dc9ca9-158f-7945-5933-564021086c79` gli errori con stato dal registro:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Visualizzare e riprendere i processi

Ogni operazione di trasferimento creerà un processo di AzCopy. Utilizzare il comando seguente per visualizzare la cronologia dei processi:

```
azcopy jobs list
```

Per visualizzare le statistiche del processo, usare il comando seguente:

```
azcopy jobs show <job-id>
```

Per filtrare i trasferimenti in base allo stato, usare il comando seguente:

```
azcopy jobs show <job-id> --with-status=Failed
```

Utilizzare il comando seguente per riprendere un processo non riuscito/annullato. Questo comando usa il relativo identificatore insieme al token di firma di accesso condiviso perché non è persistente per motivi di sicurezza:This command uses its identifier along with the SAS token as it isn't persistent for security reasons:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Racchiudere gli argomenti di percorso, ad esempio il token di firma di accesso condiviso tra virgolette singole (''). Utilizzare le virgolette singole in tutte le shell dei comandi, ad eccezione della shell dei comandi di Windows (cmd.exe). Se si utilizza una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti di percorso tra virgolette doppie ("") anziché virgolette singole ('').

Quando si riprende un processo, AzCopy esamina il file del piano di processo. Il file di piano elenca tutti i file identificati per l'elaborazione al momento della creazione del processo. Quando riprendete un lavoro, AzCopy tenterà di trasferire tutti i file elencati nel file di piano che non sono già stati trasferiti.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Modificare il percorso del piano e dei file di registro

Per impostazione predefinita, i `%USERPROFILE%\.azcopy` file di piano e `$HOME$\.azcopy` di log si trovano nella directory di Windows o nella directory su Mac e Linux. È possibile modificare questa posizione.

### <a name="change-the-location-of-plan-files"></a>Modificare la posizione dei file di piano

Utilizzare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Utilizzare `azcopy env` il per controllare il valore corrente di questa variabile. Se il valore è vuoto, i file di piano vengono scritti nel percorso predefinito.

### <a name="change-the-location-of-log-files"></a>Modificare il percorso dei file di registro

Utilizzare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilizzare `azcopy env` il per controllare il valore corrente di questa variabile. Se il valore è vuoto, i log vengono scritti nel percorso predefinito.

## <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, il `INFO`livello di registrazione AzCopy è impostato su . Se si desidera ridurre il livello di dettaglio del registro per ``--log-level`` risparmiare spazio su disco, sovrascrivere questa impostazione utilizzando l'opzione . 

I livelli di `NONE` `DEBUG`registrazione `INFO` `WARNING`disponibili `ERROR` `PANIC`sono: `FATAL`, , , , , , e .

## <a name="remove-plan-and-log-files"></a>Rimuovere i file di piano e di registro

Se si desidera rimuovere tutti i file di piano e di `azcopy jobs clean` registro dal computer locale per risparmiare spazio su disco, utilizzare il comando .

Per rimuovere il piano e i file `azcopy jobs rm <job-id>`di registro associati a un solo processo, utilizzare . Sostituire `<job-id>` il segnaposto in questo esempio con l'ID del processo.


