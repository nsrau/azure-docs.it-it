---
title: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy con archiviazione di Azure | Microsoft Docs
description: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7e79f186688f3b6531ac24df4e3ae4201cf1903c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282433"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi di AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di eseguire attività di configurazione avanzate e di risolvere i problemi che possono verificarsi durante l'uso di AzCopy.

> [!NOTE]
> Se si sta cercando contenuto per iniziare a usare AzCopy, vedere gli articoli seguenti:
> - [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
> - [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)
> - [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
> - [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy per AzCopy, impostare la `https_proxy` variabile di ambiente. Se si esegue AzCopy in Windows, AzCopy rileva automaticamente le impostazioni proxy, quindi non è necessario usare questa impostazione in Windows. Se si sceglie di usare questa impostazione in Windows, verrà eseguito l'override del rilevamento automatico.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | In un prompt dei comandi usare:`set https_proxy=<proxy IP>:<proxy port>`<br> In PowerShell usare:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **macOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Attualmente, AzCopy non supporta i proxy che richiedono l'autenticazione con NTLM o Kerberos.

### <a name="bypassing-a-proxy"></a>Ignorare un proxy ###

Se si esegue AzCopy in Windows e si vuole indicare che non deve usare _alcun_ proxy (anziché rilevare automaticamente le impostazioni), usare questi comandi. Con queste impostazioni, AzCopy non cercherà o tenterà di usare un proxy.

| Sistema operativo | Environment | Comandi:  |
|--------|-----------|----------|
| **Windows** | Prompt dei comandi (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

In altri sistemi operativi è sufficiente lasciare invariata la variabile HTTPS_PROXY se non si vuole usare alcun proxy.

## <a name="optimize-performance"></a>Ottimizzare le prestazioni

È possibile effettuare il benchmark delle prestazioni e quindi usare i comandi e le variabili di ambiente per trovare un compromesso ottimale tra le prestazioni e l'utilizzo delle risorse.

Questa sezione consente di eseguire queste attività di ottimizzazione:

> [!div class="checklist"]
> * Eseguire test di benchmark
> * Ottimizzare la velocità effettiva
> * Ottimizzazione dell'utilizzo della memoria 
> * Ottimizza sincronizzazione file

### <a name="run-benchmark-tests"></a>Eseguire test di benchmark

È possibile eseguire un test di benchmark delle prestazioni in contenitori BLOB o condivisioni file specifici per visualizzare le statistiche generali sulle prestazioni e i colli di bottiglia delle prestazioni di identità. È possibile eseguire il test caricando o scaricando i dati di test generati. 

Usare il comando seguente per eseguire un test di benchmark delle prestazioni.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Esempio** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> In questo esempio vengono racchiusi gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

Questo comando esegue un benchmark delle prestazioni caricando i dati di test in una destinazione specificata. I dati di test vengono generati in memoria, caricati nella destinazione, quindi eliminati dalla destinazione dopo il completamento del test. È possibile specificare il numero di file da generare e le dimensioni che si desidera utilizzare con i parametri facoltativi del comando.

Se si preferisce eseguire questo test scaricando i dati, impostare il `mode` parametro su `download` . Per informazioni dettagliate sulla documentazione di riferimento, vedere [benchmark azcopy](storage-ref-azcopy-bench.md). 

### <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

È possibile usare il `cap-mbps` flag nei comandi per posizionare un limite massimo sulla velocità di dati della velocità effettiva. Il comando seguente, ad esempio, riprende un processo e la velocità effettiva delle maiuscole in `10` megabit (MB) al secondo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

La velocità effettiva può ridursi durante il trasferimento di file di piccole dimensioni. È possibile aumentare la velocità effettiva impostando la `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente. Questa variabile specifica il numero di richieste simultanee che possono verificarsi.  

Se il computer dispone di meno di 5 CPU, il valore di questa variabile è impostato su `32` . Altrimenti, il valore predefinito è pari a 16 moltiplicato per il numero di CPU. Il valore predefinito massimo di questa variabile è `3000` , ma è possibile impostare manualmente questo valore su un valore superiore o inferiore. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Usare `azcopy env` per controllare il valore corrente della variabile. Se il valore è vuoto, è possibile leggere quale valore viene usato osservando l'inizio di un file di log AzCopy. Il valore selezionato e il motivo per cui è stato selezionato vengono segnalati.

Prima di impostare questa variabile, è consigliabile eseguire un test di benchmark. Il processo di test di benchmark segnalerà il valore della concorrenza consigliata. In alternativa, se le condizioni della rete e i payload variano, impostare questa variabile sulla parola `AUTO` anziché su un numero specifico. In questo modo AzCopy eseguirà sempre lo stesso processo di ottimizzazione automatica utilizzato nei test di benchmark.

### <a name="optimize-memory-use"></a>Ottimizzazione dell'utilizzo della memoria

Impostare la `AZCOPY_BUFFER_GB` variabile di ambiente per specificare la quantità massima di memoria di sistema che si desidera venga utilizzata da AzCopy durante il download e il caricamento di file.
Esprimere questo valore in gigabyte (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Ottimizza sincronizzazione file

Il comando di [sincronizzazione](storage-ref-azcopy-sync.md) identifica tutti i file nella destinazione, quindi confronta i nomi di file e i timestamp dell'Ultima modifica prima di avviare l'operazione di sincronizzazione. Se si dispone di un numero elevato di file, è possibile migliorare le prestazioni eliminando questa elaborazione iniziale. 

A tale scopo, usare invece il comando [copy di azcopy](storage-ref-azcopy-copy.md) e impostare il `--overwrite` flag su `ifSourceNewer` . AzCopy Confronta i file quando vengono copiati senza eseguire analisi e confronti iniziali. In questo modo viene fornito un margine per le prestazioni nei casi in cui è presente un numero elevato di file da confrontare.

Il comando [copy di azcopy](storage-ref-azcopy-copy.md) non elimina i file dalla destinazione, pertanto se si desidera eliminare i file nella destinazione quando non sono più presenti nell'origine, utilizzare il comando [azcopy Sync](storage-ref-azcopy-sync.md) con il `--delete-destination` flag impostato sul valore `true` o `prompt` . 

## <a name="troubleshoot-issues"></a>Risolvere i problemi

AzCopy crea file di log e di piano per ogni processo. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. 

I log conterranno lo stato di errore ( `UPLOADFAILED` , `COPYFAILED` e `DOWNLOADFAILED` ), il percorso completo e il motivo dell'errore.

Per impostazione predefinita, i file di log e di piano si trovano nella `%USERPROFILE%\.azcopy` directory di Windows o nella `$HOME$\.azcopy` directory in Mac e Linux, ma è possibile modificare tale percorso se lo si desidera.

L'errore pertinente non è necessariamente il primo errore visualizzato nel file. Per gli errori, ad esempio errori di rete, timeout ed errori di server occupato, AzCopy tenterà un tentativo fino a 20 volte e in genere il processo di ripetizione dei tentativi riesce.  Il primo errore visualizzato potrebbe essere qualcosa di innocuo che è stato eseguito un nuovo tentativo.  Quindi, invece di esaminare il primo errore nel file, cercare gli errori vicini a `UPLOADFAILED` , `COPYFAILED` o `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Quando si invia una richiesta di supporto tecnico Microsoft (o si risolvono i problemi relativi a terze parti), condividere la versione redatta del comando che si desidera eseguire. Ciò garantisce che la firma di accesso condiviso non venga accidentalmente condivisa con nessuno. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori con `UPLOADFAILED` stato dal `04dc9ca9-158f-7945-5933-564021086c79` log:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Visualizzare e riprendere i processi

Ogni operazione di trasferimento creerà un processo di AzCopy. Usare il comando seguente per visualizzare la cronologia dei processi:

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

Usare il comando seguente per riprendere un processo non riuscito/annullato. Questo comando usa il relativo identificatore insieme al token di firma di accesso condiviso perché non è persistente per motivi di sicurezza:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Racchiudere gli argomenti del percorso, ad esempio il token di firma di accesso condiviso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd.exe). Se si usa una shell dei comandi di Windows (cmd.exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

Quando si riprende un processo, AzCopy esamina il file del piano di processo. Il file del piano elenca tutti i file identificati per l'elaborazione quando il processo è stato creato per la prima volta. Quando si riprende un processo, AzCopy tenterà di trasferire tutti i file elencati nel file di piano che non sono stati ancora trasferiti.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Modificare il percorso del piano e dei file di log

Per impostazione predefinita, i file di piano e di log si trovano nella `%USERPROFILE%\.azcopy` directory di Windows o nella `$HOME$\.azcopy` directory in Mac e Linux. È possibile modificare questo percorso.

### <a name="change-the-location-of-plan-files"></a>Modificare il percorso dei file del piano

Usare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> In un prompt dei comandi usare::`set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Usare `azcopy env` per controllare il valore corrente della variabile. Se il valore è vuoto, i file del piano vengono scritti nel percorso predefinito.

### <a name="change-the-location-of-log-files"></a>Modificare il percorso dei file di log

Usare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_LOG_LOCATION="<value>"` <br> In un prompt dei comandi usare::`set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Usare `azcopy env` per controllare il valore corrente della variabile. Se il valore è vuoto, i log vengono scritti nel percorso predefinito.

## <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, il livello di registrazione AzCopy è impostato su `INFO` . Se si desidera ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere questa impostazione utilizzando l' ``--log-level`` opzione. 

I livelli di log disponibili sono:,,, `NONE` `DEBUG` `INFO` `WARNING` , `ERROR` , `PANIC` e `FATAL` .

## <a name="remove-plan-and-log-files"></a>Rimuovere i file di piano e di log

Se si desidera rimuovere tutti i piani e i file di log dal computer locale per risparmiare spazio su disco, utilizzare il `azcopy jobs clean` comando.

Per rimuovere il piano e i file di log associati a un solo processo, utilizzare `azcopy jobs rm <job-id>` . Sostituire il `<job-id>` segnaposto in questo esempio con l'ID processo del processo.


