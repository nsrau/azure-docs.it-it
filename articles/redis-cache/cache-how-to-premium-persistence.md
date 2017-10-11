---
title: Come configurare la persistenza dei dati per una Cache Redis di Azure Premium
description: Informazioni su come configurare e gestire la persistenza dei dati per le istanze di Cache Redis di Azure di livello Premium
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: sdanie
ms.openlocfilehash: 638f0154d3a4fd091197a2da86374a053b31c4c0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Come configurare la persistenza dei dati per una Cache Redis di Azure Premium
Cache Redis di Azure dispone di diverse offerte di cache che assicurano flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluse le funzionalità del livello Premium quali clustering, persistenza e supporto della rete virtuale. In questo articolo viene descritto come configurare la persistenza in un'istanza Premium di Cache Redis di Azure.

Per informazioni su altre funzionalità di cache premium, vedere [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Che cos'è la persistenza dei dati?
La [persistenza Redis](https://redis.io/topics/persistence) consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi. 

Il servizio Cache Redis di Azure offre i modelli di persistenza Redis seguenti:

* **Persistenza RDB**: quando si configura la persistenza RDB (database Redis), Cache Redis di Azure salva in modo permanente uno snapshot della cache Redis in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando lo snapshot più recente. Altre informazioni sono disponibili sui [vantaggi](https://redis.io/topics/persistence#rdb-advantages) e gli [svantaggi](https://redis.io/topics/persistence#rdb-disadvantages) della persistenza RDB.
* **Persistenza AOF**: quando si configura la persistenza AOF (Append only file), Cache Redis di Azure salva ogni operazione di scrittura in un log che viene salvato almeno una volta al secondo in un account di archiviazione di Azure. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando le operazioni di scrittura più recenti. Altre informazioni sono disponibili sui [vantaggi](https://redis.io/topics/persistence#aof-advantages) e gli [svantaggi](https://redis.io/topics/persistence#aof-disadvantages) della persistenza AOF.

La persistenza può essere configurata nel pannello **Nuova cache Redis** durante la creazione della cache e nel **menu Risorse** per le cache premium esistenti.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Dopo aver selezionato un piano tariffario Premium, fare clic su **Persistenza Redis**.

![Persistenza Redis][redis-cache-persistence]

La procedura illustrata nella sezione che segue descrive come configurare la persistenza Redis per la nuova cache premium. Una volta configurata la persistenza di Redis, fare clic su **Crea** per creare la nuova cache premium con persistenza di Redis.

## <a name="enable-redis-persistence"></a>Abilitare la persistenza Redis

È possibile abilitare la persistenza Redis nel pannello **Persistenza dei dati Redis** scegliendo tra la persistenza **RDB** o **AOF**. Per le nuove cache, questo pannello è accessibile durante il processo di creazione della cache, come descritto nella sezione precedente. Per le cache esistenti, è possibile accedere al pannello **Persistenza dei dati di Redis** dal **menu Risorse** della cache.

![Impostazioni di Redis][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Configurare la persistenza RDB

Per abilitare la persistenza RDB, fare clic su **RDB**. Per disabilitare la persistenza RDB in una cache premium precedentemente abilitata, fare clic su **Disabilita**.

![Persistenza RDB di Redis][redis-cache-rdb-persistence]

Per configurare l'intervallo di backup, selezionare una **Frequenza di Backup** dall'elenco a discesa. Le opzioni disponibili includono **15 minuti**, **30 minuti**, **60 minuti**, **6 ore**, **12 ore** e **24 ore**. Il conto alla rovescia per l'intervallo inizia dopo il corretto completamento dell'operazione di backup precedente e al termine viene avviato un nuovo backup.

Fare clic su **Account di archiviazione** per selezionare l'account di archiviazione da usare e scegliere la **Chiave primaria** o la **Chiave secondaria** da usare dall'elenco a discesa **Chiave di archiviazione**. È necessario scegliere un account di archiviazione nella stessa area della cache ed è consigliato un account **Archiviazione Premium** , poiché archiviazione premium ha una velocità effettiva maggiore. 

> [!IMPORTANT]
> Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario riconfigurare la chiave desiderata dall'elenco a discesa **Chiave di archiviazione**.
> 
> 

Fare clic su **OK** per salvare la configurazione della persistenza.

Una volta trascorso l'intervallo di frequenza di backup, viene avviato il backup successivo (o il primo backup per le nuove cache).

## <a name="configure-aof-persistence"></a>Configurare la persistenza AOF

Per abilitare la persistenza AOF, fare clic su **AOF**. Per disabilitare la persistenza AOF in una cache premium precedentemente abilitata, fare clic su **Disabilita**.

![Persistenza AOF di Redis][redis-cache-aof-persistence]

Per configurare la persistenza AOF, specificare un **primo account di archiviazione**. Questo account deve trovarsi nella stessa area della cache e deve essere preferibilmente un account di **Archiviazione Premium** perché questo tipo di archiviazione offre una velocità effettiva superiore. Facoltativamente, è possibile configurare un account di archiviazione aggiuntivo denominato **secondo account di archiviazione**. Se si configura un secondo account di archiviazione, è qui che vengono scritte le operazioni di scrittura alla cache di replica. Per ogni account di archiviazione configurato, scegliere la **Chiave primaria** o la **Chiave secondaria** da usare dall'elenco a discesa **Chiave di archiviazione**. 

> [!IMPORTANT]
> Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario riconfigurare la chiave desiderata dall'elenco a discesa **Chiave di archiviazione**.
> 
> 

Quando la persistenza AOF è abilitata, le operazioni di scrittura alla cache vengono salvate nell'account di archiviazione designato (o negli account se è stato configurato un secondo account di archiviazione). In caso di un errore irreversibile che danneggia sia la cache primaria che quella di replica, viene usato il log AOF archiviato per ricreare la cache.

## <a name="persistence-faq"></a>Domande frequenti sulla persistenza
Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla persistenza di Cache Redis di Azure.

* [È possibile abilitare la persistenza per una cache creata in precedenza?](#can-i-enable-persistence-on-a-previously-created-cache)
* [È possibile abilitare la persistenza AOF e RDB allo stesso tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Quale modello di persistenza è consigliabile scegliere?](#which-persistence-model-should-i-choose)
* [Cosa accade se si è passati a una dimensione diversa e viene ripristinato un backup creato prima dell'operazione di ridimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Persistenza RDB
* [È possibile modificare la frequenza di backup RDB dopo avere creato la cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Perché trascorrono più di 60 minuti tra i backup se è stata impostata una frequenza di backup RDB di 60 minuti?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Cosa accade ai backup RDB precedenti quando viene eseguito un nuovo backup?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>Persistenza AOF
* [Quando è consigliabile usare un secondo account di archiviazione?](#when-should-i-use-a-second-storage-account)
* [La persistenza AOF influisce sulla velocità effettiva, la latenza o le prestazioni della cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Come si rimuove il secondo account di archiviazione?](#how-can-i-remove-the-second-storage-account)
* [Che cos'è un'operazione di riscrittura e in che modo influisce sulla cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Quali potrebbero essere gli effetti del ridimensionamento di una cache con la persistenza AOF abilitata?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Come vengono organizzati i dati AOF nello spazio di archiviazione?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>È possibile abilitare la persistenza per una cache creata in precedenza?
Sì, la persistenza di Redis può essere configurata sia al momento della creazione della cache che nelle cache premium esistenti.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>È possibile abilitare la persistenza AOF e RDB allo stesso tempo?

No, è necessario scegliere una delle due. Non è possibile usarle entrambe contemporaneamente.

### <a name="which-persistence-model-should-i-choose"></a>Quale modello di persistenza è consigliabile scegliere?

La persistenza AOF salva ogni operazione di scrittura in un log e questa operazione ha un certo impatto sulla velocità effettiva. La persistenza RDB invece salva i backup in base all'intervallo di backup configurato con un impatto minimo sulle prestazioni. È consigliabile scegliere la persistenza AOF se lo scopo principale è ridurre al minimo la perdita di dati e si è in grado di gestire una diminuzione della velocità effettiva per la cache. Se si desidera invece mantenere la velocità effettiva ottimale nella cache e avere comunque un meccanismo per il ripristino dei dati, è preferibile scegliere la persistenza RDB.

* Altre informazioni sono disponibili sui [vantaggi](https://redis.io/topics/persistence#rdb-advantages) e gli [svantaggi](https://redis.io/topics/persistence#rdb-disadvantages) della persistenza RDB.
* Altre informazioni sono disponibili sui [vantaggi](https://redis.io/topics/persistence#aof-advantages) e gli [svantaggi](https://redis.io/topics/persistence#aof-disadvantages) della persistenza AOF.

Per altre informazioni sulle prestazioni quando si usa la persistenza AOF, vedere [La persistenza AOF influisce sulla velocità effettiva, la latenza o le prestazioni della cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Cosa accade se si è passati a una dimensione diversa e viene ripristinato un backup creato prima dell'operazione di ridimensionamento?

Per la persistenza sia RDB sia AOF:

* Se si è passati a una dimensione maggiore, non ci sono conseguenze.
* Se si è passati a una dimensione minore e l'impostazione dei [database](cache-configure.md#databases) personalizzata è superiore al [limite dei database](cache-configure.md#databases) per la nuova dimensione, i dati di questi database non vengono ripristinati. Per altre informazioni, vedere [L'impostazione databases personalizzata viene modificata durante il ridimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se si è passati a una dimensione minore che non è abbastanza grande per contenere tutti i dati del backup più recente, le chiavi verranno rimosse durante il processo di ripristino, in genere usando il criterio di rimozione [allkeys-lru](http://redis.io/topics/lru-cache) .

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>È possibile modificare la frequenza di backup RDB dopo avere creato la cache?
Sì, è possibile modificare la frequenza di backup per la persistenza RDB nel pannello **Persistenza dei dati di Redis**. Per istruzioni, vedere [Configurare la persistenza di Redis](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Perché trascorrono più di 60 minuti tra i backup se è stata impostata una frequenza di backup RDB di 60 minuti?
L'intervallo di frequenza di backup della persistenza RDB inizia solo dopo il completamento del processo di backup precedente. Se la frequenza di backup è 60 minuti e per il corretto completamento del processo di backup sono richiesti 15 minuti, il backup successivo verrà avviato solo 75 minuti dopo l'ora di inizio del backup precedente.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Cosa accade ai backup RDB precedenti quando viene eseguito un nuovo backup?
A eccezione di quello più recente, tutti i backup della persistenza RDB vengono eliminati automaticamente. L'eliminazione potrebbe non avvenire immediatamente, ma i backup meno recenti non vengono mantenuti per un tempo illimitato.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando è consigliabile usare un secondo account di archiviazione?

È consigliabile usare un secondo account di archiviazione per la persistenza AOF quando si ritiene di avere un numero di operazioni set nella cache superiore a quello previsto.  L'impostazione del secondo account di archiviazione aiuta a garantire che la cache non raggiunga i limiti di larghezza di banda di archiviazione.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>La persistenza AOF influisce sulla velocità effettiva, la latenza o le prestazioni della cache?

La persistenza AOF influisce sulla velocità effettiva di circa il 15-20% quando la cache è al di sotto del carico massimo (carico di CPU e server inferiore al 90%). Quando la cache lavora entro questi limiti, non si verificano problemi di latenza. La cache raggiunge tuttavia questi limiti prima con la persistenza AOF abilitata.

### <a name="how-can-i-remove-the-second-storage-account"></a>Come si rimuove il secondo account di archiviazione?

È possibile rimuovere l'account di archiviazione secondario della persistenza AOF impostandolo come il primo account di archiviazione. Per istruzioni, vedere [Configurare la persistenza AOF](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Che cos'è un'operazione di riscrittura e in che modo influisce sulla cache?

Quando il file AOF diventa sufficientemente grande, viene automaticamente accodata una riscrittura nella cache. L'operazione di riscrittura ridimensiona il file AOF con il set minimo di operazioni necessarie per creare il set di dati corrente. Durante l'operazione di riscrittura è normale raggiungere prima i limiti di prestazioni, soprattutto quando si usano grandi set di dati. L'operazione di riscrittura avviene meno spesso man mano che il file AOF diventa più grande, ma quando a quel punto si renderà necessaria, richiederà molto più tempo.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Quali potrebbero essere gli effetti del ridimensionamento di una cache con la persistenza AOF abilitata?

Se il file AOF al momento del ridimensionamento è molto grande, l'operazione di ridimensionamento richiede più tempo del previsto perché ricarica il file dopo che il ridimensionamento è stato completato.

Per altre informazioni sul ridimensionamento, vedere [Cosa accade se si è passati a una dimensione diversa e viene ripristinato un backup creato prima dell'operazione di ridimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Come vengono organizzati i dati AOF nello spazio di archiviazione?

I dati archiviati nei file AOF sono suddivisi in più BLOB di pagine per ogni nodo per migliorare le prestazioni nel salvataggio dei dati nello spazio di archiviazione. La tabella seguente illustra il numero di BLOB di pagine usato per ogni piano tariffario:

| Livello Premium | Blobs |
|--------------|-------|
| P1           | 4 per partizione    |
| P2           | 8 per partizione    |
| P3           | 16 per partizione   |
| P4           | 20 per partizione   |

Quando è abilitato il clustering, ogni partizione nella cache ha un proprio set di BLOB di pagine, come indicato nella tabella precedente. Una cache P2 con tre partizioni, ad esempio, distribuisce il proprio file AOF su 24 BLOB di pagine (8 BLOB per partizione, con 3 partizioni).

Dopo una riscrittura, nello spazio di archiviazione sono presenti due set di file AOF. L'operazione di riscrittura avviene in background e si accoda al primo set di file, mentre le operazioni set che vengono inviate alla cache durante la riscrittura si accodano al secondo set. Una copia di backup viene archiviata temporaneamente durante la riscrittura in caso di errore, ma viene eliminata immediatamente dopo il completamento della riscrittura.


## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

* [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
