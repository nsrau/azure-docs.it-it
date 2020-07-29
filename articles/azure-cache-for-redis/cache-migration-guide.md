---
title: Eseguire la migrazione alla cache di Azure per Redis
description: Informazioni su come eseguire la migrazione della cache esistente alla cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 554724c334cb6c51b8744de0eedd4d6815d707b5
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172581"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Eseguire la migrazione alla cache di Azure per Redis
Questo articolo descrive alcuni approcci per eseguire la migrazione di una cache Redis esistente in esecuzione in locale o in un altro servizio cloud alla cache di Azure per Redis.

## <a name="migration-scenarios"></a>Scenari di migrazione
Redis open source può essere eseguito in molti ambienti di calcolo. Alcuni esempi comuni sono:

- **Locale** : cache Redis in esecuzione in data center privati.
- **VM basate su cloud** : cache Redis in esecuzione su macchine virtuali di Azure, AWS EC2 e così via.
- Servizi di **hosting** -Servizi Redis gestiti come AWS ElastiCache.
- **Aree diverse** : cache Redis che si trovano in un'altra area di Azure.

Se si dispone di una cache di questo tipo, potrebbe essere possibile spostarla in cache di Azure per Redis con un'interruzione o un tempo di inattività minimo.

## <a name="migration-options"></a>Opzioni di migrazione

Esistono diversi modi in cui è possibile passare da una cache a un'altra. A seconda della posizione della cache e del modo in cui l'applicazione interagisce con esso, un metodo sarà più utile rispetto alle altre. Di seguito sono descritte alcune delle strategie di migrazione usate di frequente.

   | Opzione       | Vantaggi | Svantaggi |
   | ------------ | ---------- | ------------- |
   | Creare una nuova cache | Più semplice da implementare. | È necessario ripopolare i dati nella nuova cache, che potrebbero non funzionare con molte applicazioni. |
   | Esportare e importare dati tramite il file RDB | Compatibile con qualsiasi cache Redis in genere. | Alcuni dati potrebbero andare persi se vengono scritti nella cache esistente dopo la generazione del file RDB. | 
   | Doppia scrittura di dati in due cache | Nessuna perdita di dati o Downtown. Operazioni senza interruzioni della cache esistente. Test più semplice della nuova cache. | Richiede due cache per un lungo periodo di tempo. | 
   | Eseguire la migrazione dei dati a livello di codice | Controllo completo sulla modalità di spostamento dei dati. | Richiede codice personalizzato. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Creare una nuova cache di Azure per Redis

Questo approccio tecnicamente non è una migrazione. Se la perdita di dati non è un problema, il modo più semplice per passare alla cache di Azure per Redis consiste nel creare un'istanza della cache e connettervi l'applicazione. Se ad esempio si usa Redis come cache di ricerca di record di database, è possibile ricompilare facilmente la cache da zero.

I passaggi generali per implementare questa opzione sono:

1. Creare una nuova cache di Azure per l'istanza di Redis.

2. Aggiornare l'applicazione per usare la nuova istanza.

3. Eliminare la vecchia istanza di Redis.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Esportare i dati in un file RDB e importarli in cache di Azure per Redis

Redis Open Source definisce un meccanismo standard per creare uno snapshot del set di dati in memoria di una cache e salvarlo in un file. Questo file, denominato RDB, può essere letto da un'altra cache Redis. Il [livello Premium di cache di Azure per Redis](cache-premium-tier-intro.md) supporta l'importazione di dati in un'istanza di cache tramite file RDB. È possibile usare un file RDB per trasferire i dati da una cache esistente a cache di Azure per Redis.

> [!IMPORTANT]
> Il formato del file RDB può variare tra le versioni di redis e non può mantenere la compatibilità con le versioni precedenti. La versione Redis della cache da cui si sta esportando deve essere uguale o inferiore alla versione fornita da cache di Azure per Redis.
>

I passaggi generali per implementare questa opzione sono:

1. Creare una nuova cache di Azure per l'istanza di redis nel livello Premium con le stesse dimensioni (o superiori) della cache esistente.

2. Salva uno snapshot della cache Redis esistente. È possibile [configurare Redis per salvare](https://redis.io/topics/persistence) periodicamente gli snapshot oppure eseguire il processo manualmente usando i comandi [Save](https://redis.io/commands/save) o [BGSAVE](https://redis.io/commands/bgsave) . Per impostazione predefinita, il file RDB è denominato "dump. RDB" e si trova nel percorso specificato nel file di configurazione *Redis. conf* .

    > [!NOTE]
    > Se si esegue la migrazione dei dati nella cache di Azure per Redis, vedere le [istruzioni su come esportare un file RDB](cache-how-to-import-export-data.md) o usare il [cmdlet Export di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) .
    >

3. Copiare il file RDB in un account di archiviazione di Azure nell'area in cui si trova la nuova cache. Per questa attività è possibile usare AzCopy.

4. Importare il file RDB nella nuova cache usando queste [istruzioni di importazione](cache-how-to-import-export-data.md) o il [cmdlet di importazione di PowerShell](https://docs.microsoft.com/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0).

5. Aggiornare l'applicazione per usare la nuova istanza della cache.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Scrivi in due cache Redis contemporaneamente durante il periodo di migrazione

Anziché trasferire direttamente i dati tra le cache, è possibile usare l'applicazione per scrivere i dati sia in una cache esistente che in quella nuova che si sta configurando. L'applicazione continuerà a leggere i dati dalla cache esistente inizialmente. Quando la nuova cache dispone dei dati necessari, l'applicazione viene spostata nella cache e il vecchio viene ritirato. Supponiamo, ad esempio, di usare Redis come archivio di sessione e le sessioni dell'applicazione sono valide per sette giorni. Dopo la scrittura nelle due cache per una settimana, sarà possibile verificare che la nuova cache contenga tutte le informazioni sulla sessione non scadute. È possibile affidarsi in modo sicuro a tale punto in avanti senza preoccuparsi della perdita di dati.

I passaggi generali per implementare questa opzione sono:

1. Creare una nuova cache di Azure per l'istanza di redis nel livello Premium con le stesse dimensioni (o superiori) della cache esistente.

2. Modificare il codice dell'applicazione per scrivere nelle istanze nuove e originali.

3. Continuare a leggere i dati dall'istanza originale fino a quando la nuova istanza non è sufficientemente popolata con i dati.

4. Aggiornare il codice dell'applicazione per la lettura e la scrittura solo dalla nuova istanza.

5. Eliminare l'istanza originale.

### <a name="migrate-programmatically"></a>Eseguire la migrazione a livello di codice

È possibile creare un processo di migrazione personalizzato leggendo i dati di una cache esistente a livello di codice e scrivendoli in cache di Azure per Redis. Questo [strumento open source](https://github.com/deepakverma/redis-copy) può essere usato per copiare i dati da una cache di Azure per l'istanza di redis a un'altra. Questo strumento è utile per lo stato di trasferimento dei dati tra le istanze della cache in diverse aree della cache di Azure. È disponibile anche una [versione compilata](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) . È anche possibile trovare il codice sorgente come guida utile per la scrittura di uno strumento di migrazione personalizzato.

> [!NOTE]
> Questo strumento non è ufficialmente supportato da Microsoft. 
>

I passaggi generali per implementare questa opzione sono:

1. Creare una macchina virtuale nell'area in cui si trova la cache esistente. Se il set di dati è di grandi dimensioni, scegliere una macchina virtuale relativamente potente per ridurre i tempi di copia.

2. Creare una nuova cache di Azure per l'istanza di Redis.

3. Svuotare i dati dalla nuova cache per assicurarsi che sia vuota. Questo passaggio è necessario perché lo strumento di copia non sovrascrive alcuna chiave esistente nella cache di destinazione.

    > [!IMPORTANT]
    > Assicurarsi di non scaricare dalla cache di origine.
    >

4. Usare un'applicazione come lo strumento open source precedente per automatizzare la copia dei dati dalla cache di origine alla destinazione. Tenere presente che il completamento del processo di copia potrebbe richiedere alcuni minuti a seconda delle dimensioni del set di dati.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Introduzione al livello Premium di Cache Redis di Azure](cache-premium-tier-intro.md)
* [Importa dati](cache-how-to-import-export-data.md#import)
