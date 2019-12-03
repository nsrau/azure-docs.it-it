---
title: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy con archiviazione di Azure | Microsoft Docs
description: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7097faa64319a46b1efc91233e30ea992d064246
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687646"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi relativi a AzCopy

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di eseguire attività di configurazione avanzate e consente di risolvere i problemi che possono verificarsi durante l'uso di AzCopy.

> [!NOTE]
> Se si sta cercando contenuto per iniziare a usare AzCopy, vedere gli articoli seguenti:
> - [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
> - [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)
> - [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
> - [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy per AzCopy, impostare la variabile di ambiente `https_proxy`. Se si esegue AzCopy in Windows, AzCopy rileva automaticamente le impostazioni proxy, quindi non è necessario usare questa impostazione in Windows. Se si sceglie di usare questa impostazione in Windows, eseguirà l'override del rilevamento automatico.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | In un prompt dei comandi usare: `set https_proxy=<proxy IP>:<proxy port>`<br> In PowerShell usare: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Attualmente, AzCopy non supporta i proxy che richiedono l'autenticazione con NTLM o Kerberos.

## <a name="optimize-performance"></a>Ottimizzare le prestazioni

È possibile effettuare il benchmark delle prestazioni e quindi usare i comandi e le variabili di ambiente per trovare un compromesso ottimale tra le prestazioni e l'utilizzo delle risorse.

### <a name="run-benchmark-tests"></a>Eseguire test di benchmark

È possibile eseguire un test di benchmark delle prestazioni su contenitori BLOB specifici per visualizzare le statistiche generali sulle prestazioni e i colli di bottiglia delle prestazioni di identità. 

> [!NOTE]
> Nella versione corrente questa funzionalità è disponibile solo per i contenitori di archiviazione BLOB.

Usare il comando seguente per eseguire un test di benchmark delle prestazioni.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Esempio** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

Questo comando esegue un benchmark delle prestazioni caricando i dati di test in una destinazione specificata. I dati di test vengono generati in memoria, caricati nella destinazione, quindi eliminati dalla destinazione dopo il completamento del test. È possibile specificare il numero di file da generare e le dimensioni che si desidera utilizzare con i parametri facoltativi del comando.

Per visualizzare informazioni dettagliate della Guida per questo comando, digitare `azcopy bench -h` e premere INVIO.

### <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

È possibile usare il flag di `cap-mbps` per posizionare un limite massimo sulla velocità di elaborazione dei dati. Ad esempio, il comando seguente consente di riportare la velocità effettiva a `10` megabit (MB) al secondo.

```azcopy
azcopy --cap-mbps 10
```

La velocità effettiva può ridursi durante il trasferimento di file di piccole dimensioni. È possibile aumentare la velocità effettiva impostando la variabile di ambiente `AZCOPY_CONCURRENCY_VALUE`. Questa variabile specifica il numero di richieste simultanee che possono verificarsi.  

Se il computer dispone di meno di 5 CPU, il valore di questa variabile viene impostato su `32`. In caso contrario, il valore predefinito è pari a 16 moltiplicato per il numero di CPU. Il valore predefinito massimo di questa variabile è `3000`, ma è possibile impostare manualmente questo valore su un valore superiore o inferiore. 

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilizzare il `azcopy env` per verificare il valore corrente della variabile. Se il valore è vuoto, è possibile leggere quale valore viene usato osservando l'inizio di un file di log AzCopy. Il valore selezionato e il motivo per cui è stato selezionato vengono segnalati.

Prima di impostare questa variabile, è consigliabile eseguire un test di benchmark. Il processo di test di benchmark segnalerà il valore della concorrenza consigliata. In alternativa, se le condizioni della rete e i payload variano, impostare questa variabile sulla parola `AUTO` anziché su un numero specifico. In questo modo AzCopy eseguirà sempre lo stesso processo di ottimizzazione automatica utilizzato nei test di benchmark.

### <a name="optimize-memory-use"></a>Ottimizzazione dell'utilizzo della memoria

Impostare la variabile di ambiente `AZCOPY_BUFFER_GB` per specificare la quantità massima di memoria di sistema che si desidera venga utilizzata da AzCopy durante il download e il caricamento di file.
Esprimere questo valore in gigabyte (GB).

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Risolvere i problemi

AzCopy crea file di log e di piano per ogni processo. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. 

I log conterranno lo stato dell'errore (`UPLOADFAILED`, `COPYFAILED`e `DOWNLOADFAILED`), il percorso completo e il motivo dell'errore.

Per impostazione predefinita, i file di log e di piano si trovano nella directory `%USERPROFILE$\.azcopy` in Windows o `$HOME$\.azcopy` directory su Mac e Linux, ma è possibile modificare tale percorso se lo si desidera.

> [!IMPORTANT]
> Quando si invia una richiesta di supporto tecnico Microsoft (o si risolvono i problemi relativi a terze parti), condividere la versione redatta del comando che si desidera eseguire. Ciò garantisce che la firma di accesso condiviso non venga accidentalmente condivisa con nessuno. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori con `UPLOADFAILED` stato dal registro `04dc9ca9-158f-7945-5933-564021086c79`:

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

Quando si riprende un processo, AzCopy esamina il file del piano di processo. Il file del piano elenca tutti i file identificati per l'elaborazione quando il processo è stato creato per la prima volta. Quando si riprende un processo, AzCopy tenterà di trasferire tutti i file elencati nel file di piano che non sono stati ancora trasferiti.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Modificare il percorso del piano e dei file di log

Per impostazione predefinita, i file di piano e di log si trovano nella directory `%USERPROFILE$\.azcopy` in Windows o nella directory `$HOME$\.azcopy` su Mac e Linux. È possibile modificare questo percorso.

### <a name="change-the-location-of-plan-files"></a>Modificare il percorso dei file del piano

Usare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Utilizzare il `azcopy env` per verificare il valore corrente della variabile. Se il valore è vuoto, i file del piano vengono scritti nel percorso predefinito.

### <a name="change-the-location-of-log-files"></a>Modificare il percorso dei file di log

Usare uno di questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilizzare il `azcopy env` per verificare il valore corrente della variabile. Se il valore è vuoto, i log vengono scritti nel percorso predefinito.

## <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, il livello di registrazione AzCopy è impostato su `INFO`. Se si desidera ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere questa impostazione utilizzando l'opzione ``--log-level``. 

I livelli di log disponibili sono: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`e `FATAL`.

## <a name="remove-plan-and-log-files"></a>Rimuovere i file di piano e di log

Se si desidera rimuovere tutti i piani e i file di log dal computer locale per risparmiare spazio su disco, utilizzare il comando `azcopy jobs clean`.

Per rimuovere il piano e i file di log associati a un solo processo, utilizzare `azcopy jobs rm <job-id>`. Sostituire il segnaposto `<job-id>` in questo esempio con l'ID processo del processo.


