---
title: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy con archiviazione di Azure | Microsoft Docs
description: Configurare, ottimizzare e risolvere i problemi relativi a AzCopy.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 3843eb2e906e3fb8d390e509e17117b7849ac220
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244707"
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

## <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

È possibile utilizzare il flag `cap-mbps` per inserire un limite massimo sulla velocità di dati della velocità effettiva. Ad esempio, il comando seguente consente di riportare la velocità effettiva a `10` megabit (MB) al secondo.

```azcopy
azcopy cap-mbps 10
```

La velocità effettiva può ridursi durante il trasferimento di file di piccole dimensioni. È possibile aumentare la velocità effettiva impostando la variabile di ambiente `AZCOPY_CONCURRENCY_VALUE`. Questa variabile specifica il numero di richieste simultanee che possono verificarsi.  Se il computer dispone di meno di 5 CPU, il valore di questa variabile viene impostato su `32`. In caso contrario, il valore predefinito è pari a 16 moltiplicato per il numero di CPU. Il valore predefinito massimo di questa variabile è `300`, ma è possibile impostare manualmente questo valore su un valore superiore o inferiore.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilizzare `azcopy env` per verificare il valore corrente della variabile.  Se il valore è vuoto, la variabile `AZCOPY_CONCURRENCY_VALUE` viene impostata sul valore predefinito di `300`.

## <a name="change-the-location-of-the-log-files"></a>Modificare il percorso dei file di log

Per impostazione predefinita, i file di log si trovano nella directory `%USERPROFILE%\.azcopy` in Windows o nella directory `$HOME\\.azcopy` in Mac e Linux. È possibile modificare questo percorso se necessario usando questi comandi.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilizzare `azcopy env` per verificare il valore corrente della variabile. Se il valore è vuoto, i log vengono scritti nel percorso predefinito.

## <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, il livello di registrazione AzCopy è impostato su `INFO`. Se si desidera ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere questa impostazione utilizzando l'opzione ``--log-level``. 

I livelli di log disponibili sono: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` e `FATAL`.

## <a name="troubleshoot-issues"></a>Risolvere i problemi

AzCopy crea file di log e di piano per ogni processo. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. 

I log conterranno lo stato di errore (`UPLOADFAILED`, `COPYFAILED` e `DOWNLOADFAILED`), il percorso completo e il motivo dell'errore.

Per impostazione predefinita, i file di log e di piano si trovano nella directory `%USERPROFILE\\.azcopy` in Windows o `$HOME\\.azcopy` in Mac e Linux.

> [!IMPORTANT]
> Quando si invia una richiesta di supporto tecnico Microsoft (o si risolvono i problemi relativi a terze parti), condividere la versione redatta del comando che si desidera eseguire. Ciò garantisce che la firma di accesso condiviso non venga accidentalmente condivisa con nessuno. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente otterrà tutti gli errori con lo stato `UPLOADFAILED` dal registro `04dc9ca9-158f-7945-5933-564021086c79`:

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
