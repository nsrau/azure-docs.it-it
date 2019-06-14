---
title: Configurare, ottimizzare e risolvere i problemi di AzCopy con archiviazione di Azure | Microsoft Docs
description: Configurare, ottimizzare e risolvere i problemi di AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: bf58256b05c6c1260078c0dba547295e08328ef5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073690"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurare, ottimizzare e risolvere i problemi di AzCopy

AzCopy è un'utilità della riga di comando che è possibile usare per copiare BLOB o file da o verso un account di archiviazione. Questo articolo illustra come eseguire attività di configurazione avanzate e aiuta a risolvere i problemi che possono verificarsi durante l'uso di AzCopy.

> [!NOTE]
> Se si sta cercando il contenuto che consentono di iniziare con AzCopy, vedere uno degli articoli seguenti:
> - [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
> - [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)
> - [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
> - [Trasferire dati con AzCopy e bucket Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy

Per configurare le impostazioni proxy per AzCopy, impostare il `https_proxy` variabile di ambiente.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | In un prompt dei comandi usare: `set https_proxy=<proxy IP>:<proxy port>`<br> In PowerShell usare: `$env:https_proxy=<proxy IP>:<proxy port>`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Attualmente, AzCopy non supporta i proxy che richiedono l'autenticazione NTLM o Kerberos.

## <a name="optimize-throughput"></a>Ottimizzare la velocità effettiva

Impostare il `AZCOPY_CONCURRENCY_VALUE` variabile di ambiente per configurare il numero di richieste simultanee e per controllare la velocità effettiva delle prestazioni e utilizzo di risorse. Se il computer dispone di meno di 5 CPU, quindi il valore di questa variabile è impostato su `32`. In caso contrario, il valore predefinito è uguale a 16 moltiplicato per il numero di CPU. Il valore massimo predefinito di questa variabile è `300`, ma è possibile impostare manualmente questo valore superiore o inferiore.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Usare il `azcopy env` per controllare il valore corrente di questa variabile.  Se il valore è vuoto, il `AZCOPY_CONCURRENCY_VALUE` variabile è impostata sul valore predefinito di `300`.

## <a name="change-the-location-of-the-log-files"></a>Modificare il percorso dei file di log

Per impostazione predefinita, i file di log si trovano nel `%USERPROFILE\\.azcopy` directory su Windows o nel `$HOME\\.azcopy` directory su Mac e Linux. Se è necessario usando questi comandi, è possibile modificare questo percorso.

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Usare il `azcopy env` per controllare il valore corrente di questa variabile. Se il valore è vuoto, i log vengono scritti nel percorso predefinito.

## <a name="change-the-default-log-level"></a>Modificare il livello predefinito del log

Per impostazione predefinita, AzCopy log livello è impostato su `INFO`. Se si desidera ridurre il livello di dettaglio del log per risparmiare spazio su disco, sovrascrivere questa impostazione usando il ``--log-level`` opzione. 

Livelli di log disponibili sono: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, e `FATAL`.

## <a name="troubleshoot-issues"></a>Risolvere i problemi

AzCopy crea file di log e piano per ogni processo. È possibile usare i log per analizzare e risolvere eventuali problemi potenziali. 

Il log conterrà lo stato di errore (`UPLOADFAILED`, `COPYFAILED`, e `DOWNLOADFAILED`), il percorso completo e il motivo dell'errore.

Per impostazione predefinita, i file di log e piano si trovano nel `%USERPROFILE\\.azcopy` directory su Windows o `$HOME\\.azcopy` directory su Mac e Linux.

> [!IMPORTANT]
> Quando si invia una richiesta di supporto tecnico Microsoft (o alla risoluzione del problema che interessa di terze parti), condividere la versione con alcune modifiche del comando da eseguire. In questo modo che la firma di accesso condiviso non viene accidentalmente condivise con altri. È possibile trovare la versione con modifiche all'inizio del file di log.

### <a name="review-the-logs-for-errors"></a>Esaminare i log degli errori

Il comando seguente riceverà tutti gli errori con `UPLOADFAILED` lo stato dal `04dc9ca9-158f-7945-5933-564021086c79` log:

**Windows**

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

Usare il comando seguente per riprendere un processo non riuscito/annullato. Questo comando Usa il relativo identificatore insieme al token di firma di accesso condiviso poiché non è persistente per motivi di sicurezza:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Quando si riprende un processo, AzCopy esamina il file del piano di processo. Il file del piano sono elencati tutti i file che sono stati identificati per l'elaborazione quando è stato creato prima di tutto il processo. Quando si riprende un processo, AzCopy tenterà di trasferire tutti i file elencati nel file di piano che non sono stati già trasferito.