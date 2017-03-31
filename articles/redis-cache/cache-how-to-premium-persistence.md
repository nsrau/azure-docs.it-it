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
ms.date: 02/09/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 50d8db29ccce1244387f1fe0e3e42e610575e483
ms.openlocfilehash: bc8c54b51f9eee653fbe84351081dcef562e62d4


---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Come configurare la persistenza dei dati per una Cache Redis di Azure Premium
Cache Redis di Azure dispone di diverse offerte di cache che assicurano flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluse le funzionalità del livello Premium quali clustering, persistenza e supporto della rete virtuale. In questo articolo viene descritto come configurare la persistenza in un'istanza Premium di Cache Redis di Azure.

Per informazioni su altre funzionalità di cache premium, vedere [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Che cos'è la persistenza dei dati?
La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi. 

Cache Redis di Azure offre la persistenza di Redis tramite il [modello RDB](http://redis.io/topics/persistence)in cui i dati vengono archiviati in un account di archiviazione di Azure. Quando si configura la persistenza, Cache Redis di Azure archivia uno snapshot della cache Redis in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando lo snapshot più recente.

La persistenza può essere configurata nel pannello **Nuova cache Redis** durante la creazione della cache e nel **menu Risorse**per le cache premium esistenti.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Dopo aver selezionato un piano tariffario Premium, fare clic su **Persistenza Redis**.

![Persistenza Redis][redis-cache-persistence]

Nei passaggi nella sezione seguente viene descritto come configurare la persistenza di Redis per la nuova cache premium. Una volta configurata la persistenza di Redis, fare clic su **Crea** per creare la nuova cache premium con persistenza di Redis.

## <a name="configure-redis-persistence"></a>Configurare la persistenza di Redis
La persistenza di Redis viene configurata nel pannello **Persistenza dei dati di Redis** . Per le nuove cache, questo pannello è accessibile durante il processo di creazione della cache, come descritto nella sezione precedente. Per le cache esistenti, è possibile accedere al pannello **Persistenza dei dati di Redis** dal **menu Risorse** della cache.

![Impostazioni di Redis][redis-cache-settings]

Per abilitare la persistenza Redis, fare clic su **Abilitata** per consentire il backup RDB (database Redis). Per disabilitare la persistenza di Redis in una cache premium abilitata in precedenza, fare clic su **Disabilita**.

Per configurare l'intervallo di backup, selezionare una **Frequenza di Backup** dall'elenco a discesa. Le opzioni disponibili includono **15 minuti**, **30 minuti**, **60 minuti**, **6 ore**, **12 ore** e **24 ore**. Il conto alla rovescia per l'intervallo inizia dopo il corretto completamento dell'operazione di backup precedente e al termine viene avviato un nuovo backup.

Fare clic su **Account di archiviazione** per selezionare l'account di archiviazione da usare e scegliere la **Chiave primaria** o la **Chiave secondaria** da usare dall'elenco a discesa **Chiave di archiviazione**. È necessario scegliere un account di archiviazione nella stessa area della cache ed è consigliato un account **Archiviazione Premium** , poiché archiviazione premium ha una velocità effettiva maggiore. 

> [!IMPORTANT]
> Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario riconfigurare la chiave desiderata dall'elenco a discesa **Chiave di archiviazione**.
> 
> 

![Persistenza Redis][redis-cache-persistence-selected]

Fare clic su **OK** per salvare la configurazione della persistenza.

Una volta trascorso l'intervallo di frequenza di backup, viene avviato il backup successivo (o il primo backup per le nuove cache).

## <a name="persistence-faq"></a>Domande frequenti sulla persistenza
Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla persistenza di Cache Redis di Azure.

* [È possibile abilitare la persistenza per una cache creata in precedenza?](#can-i-enable-persistence-on-a-previously-created-cache)
* [È possibile modificare la frequenza di backup dopo aver creato la cache?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
* [Perché trascorrono più di 60 minuti tra i backup se è stata impostata una frequenza di backup di 60 minuti?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Cosa accade ai backup precedenti quando viene eseguito un nuovo backup?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
* [Cosa accade se si è passati a una dimensione diversa e viene ripristinato un backup creato prima dell'operazione di ridimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>È possibile abilitare la persistenza per una cache creata in precedenza?
Sì, la persistenza di Redis può essere configurata sia al momento della creazione della cache che nelle cache premium esistenti.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>È possibile modificare la frequenza di backup dopo aver creato la cache?
Sì, è possibile modificare la frequenza di backup nel pannello **Persistenza dei dati di Redis** . Per istruzioni, vedere [Configurare la persistenza di Redis](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Perché trascorrono più di 60 minuti tra i backup se è stata impostata una frequenza di backup di 60 minuti?
L'intervallo di frequenza di backup inizia solo dopo il corretto completamento del processo di backup precedente. Se la frequenza di backup è 60 minuti e per il corretto completamento del processo di backup sono richiesti 15 minuti, il backup successivo verrà avviato solo 75 minuti dopo l'ora di inizio del backup precedente.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>Cosa accade ai backup precedenti quando viene eseguito un nuovo backup?
Ad eccezione di quello più recente, tutti i backup vengono eliminati automaticamente. L'eliminazione potrebbe non avvenire immediatamente, ma i backup meno recenti non vengono mantenuti per un tempo illimitato.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Cosa accade se si è passati a una dimensione diversa e viene ripristinato un backup creato prima dell'operazione di ridimensionamento?
* Se si è passati a una dimensione maggiore, non ci sono conseguenze.
* Se si è passati a una dimensione minore e si ha un'impostazione [databases](cache-configure.md#databases) personalizzata maggiore del [limite di database](cache-configure.md#databases) per la nuova dimensione, i dati in tali database non verranno ripristinati. Per altre informazioni, vedere [L'impostazione databases personalizzata viene modificata durante il ridimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se si è passati a una dimensione minore che non è abbastanza grande per contenere tutti i dati del backup più recente, le chiavi verranno rimosse durante il processo di ripristino, in genere usando il criterio di rimozione [allkeys-lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

* [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png



<!--HONumber=Feb17_HO2-->


