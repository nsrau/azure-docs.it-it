---
title: Configurare la persistenza dei dati-cache di Azure Premium per Redis
description: Informazioni su come configurare e gestire la persistenza dei dati per le istanze Cache Redis di Azure di livello Premium
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: fbfd384787d35317a4e45c4f91cf8a3ad4ba5a61
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000007"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Come configurare la persistenza dei dati per una Cache Redis Premium di Azure
In questo articolo si apprenderà come configurare la persistenza in una cache di Azure Premium per l'istanza di redis tramite il portale di Azure. Cache Redis di Azure include diverse soluzioni cache che offrono flessibilità di scelta riguardo alle dimensioni e alle funzionalità della cache, tra cui le funzionalità del livello Premium come clustering, persistenza e supporto per reti virtuali. 

## <a name="what-is-data-persistence"></a>Che cos'è la persistenza dei dati?
La [persistenza Redis](https://redis.io/topics/persistence) consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi. 

Cache Redis di Azure offre la persistenza Redis usando i modelli seguenti:

* **Persistenza RDB**: quando si configura la persistenza RDB (database Redis), Cache Redis di Azure salva in modo permanente uno snapshot della cache Redis di Azure in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando lo snapshot più recente. Altre informazioni sono disponibili sui [vantaggi](https://redis.io/topics/persistence#rdb-advantages) e gli [svantaggi](https://redis.io/topics/persistence#rdb-disadvantages) della persistenza RDB.
* **Persistenza AOF**: quando si configura la persistenza AOF ("Append only file", file di solo accodamento), Cache Redis di Azure salva ogni operazione di scrittura in un log che viene salvato almeno una volta al secondo in un account di archiviazione di Azure. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando le operazioni di scrittura più recenti. Altre informazioni sono disponibili sui [vantaggi](https://redis.io/topics/persistence#aof-advantages) e gli [svantaggi](https://redis.io/topics/persistence#aof-disadvantages) della persistenza AOF.

La persistenza scrive i dati Redis in un account di archiviazione di Azure di cui si è proprietari e gestiti. È possibile configurare dal **nuovo pannello cache di Azure per Redis** durante la creazione della cache e nel **menu risorse** per le cache Premium esistenti.

> [!NOTE]
> 
> Archiviazione di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente. È possibile usare le proprie chiavi per la crittografia. Per ulteriori informazioni, vedere [chiavi gestite dal cliente con Azure Key Vault](/azure/storage/common/storage-service-encryption).
> 
> 

1. Per creare una cache Premium, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. Oltre a creare cache nel portale di Azure, è possibile crearle usando modelli di Resource Manager, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni sulla creazione di un'istanza di Cache Redis di Azure, vedere [Creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Creare una risorsa.":::
   
2. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Creare una risorsa.":::

3. Nella pagina **nuova cache Redis** configurare le impostazioni per la nuova cache Premium.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa di lunghezza compresa tra 1 e 63 caratteri che contengono solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | A discesa e selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | A discesa e selezionare un gruppo di risorse oppure selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | A discesa e selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Tipo di cache** | A discesa e selezionare una cache Premium per configurare le funzionalità Premium. Per informazioni dettagliate, vedere [cache di Azure per i prezzi di redis](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |

4. Selezionare la scheda **Rete** o fare clic sul pulsante **Rete** nella parte inferiore della pagina.

5. Nella scheda **Rete** selezionare il metodo di connettività. Per le istanze di cache Premium è possibile connettersi pubblicamente, tramite indirizzi IP pubblici o endpoint di servizio, oppure privatamente, usando un endpoint privato.

6. Fare clic sul pulsante **Avanti: Avanzate** oppure fare clic sulla scheda **Avanti: Avanzate** nella parte inferiore della pagina.

7. Nella scheda **Avanzate** per un'istanza di cache Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati. Per la persistenza dei dati, è possibile scegliere la persistenza **RDB** o **aof** . 

8. Per abilitare la persistenza RDB, fare clic su **RDB** e configurare le impostazioni. 
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Frequenza di backup** | A discesa e selezionare un intervallo di backup, le scelte includono **15 minuti**, **30 minuti**, **60 minuti**, **6 ore**, **12 ore**e **24 ore**. | Il conto alla rovescia per l'intervallo inizia dopo il corretto completamento dell'operazione di backup precedente e al termine viene avviato un nuovo backup. | 
   | **Storage Account** | A discesa e selezionare l'account di archiviazione. | È necessario scegliere un account di archiviazione nella stessa area della cache ed è consigliato un account **Archiviazione Premium** , poiché archiviazione premium ha una velocità effettiva maggiore.  | 
   | **Chiave di archiviazione** | A discesa e scegliere la chiave **primaria** o la **chiave secondaria** da usare. | Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario riconfigurare la chiave desiderata dall'elenco a discesa **Chiave di archiviazione**. | 

    Il primo backup viene avviato una volta trascorso l'intervallo di frequenza di backup.

9. Per abilitare la persistenza AOF, fare clic su **aof** e configurare le impostazioni. 
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Primo account di archiviazione** | A discesa e selezionare l'account di archiviazione. | Questo account deve trovarsi nella stessa area della cache e deve essere preferibilmente un account di **Archiviazione Premium** perché questo tipo di archiviazione offre una velocità effettiva superiore. | 
   | **Prima chiave di archiviazione** | A discesa e scegliere la chiave **primaria** o la **chiave secondaria** da usare. | Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario riconfigurare la chiave desiderata dall'elenco a discesa **Chiave di archiviazione**. | 
   | **Secondo account di archiviazione** | Opzionale A discesa e scegliere la chiave **primaria** o la **chiave secondaria** da usare. | Facoltativamente, è possibile configurare un account di archiviazione aggiuntivo. Se si configura un secondo account di archiviazione, è qui che vengono scritte le operazioni di scrittura alla cache di replica. | 
   | **Seconda chiave di archiviazione** | Opzionale A discesa e scegliere la chiave **primaria** o la **chiave secondaria** da usare. | Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario riconfigurare la chiave desiderata dall'elenco a discesa **Chiave di archiviazione**. | 

    Quando la persistenza AOF è abilitata, le operazioni di scrittura alla cache vengono salvate nell'account di archiviazione designato (o negli account se è stato configurato un secondo account di archiviazione). In caso di un errore irreversibile che danneggia sia la cache primaria che quella di replica, viene usato il log AOF archiviato per ricreare la cache.

10. Fare clic sul pulsante **Avanti: Tag** o fare clic sulla scheda **Avanti: Tag** nella parte inferiore della pagina.

11. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa. 

12. Selezionare **Rivedi e crea**. Si viene reindirizzati alla scheda Rivedi e crea in cui Azure convalida la configurazione.

13. Quando viene visualizzato il messaggio di convalida verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina  **Panoramica**  della cache di Azure per Redis. Quando la voce  **Stato**  indica  **In esecuzione**, la cache è pronta per l'uso. 

## <a name="persistence-faq"></a>Domande frequenti sulla persistenza
Nell'elenco seguente sono fornite risposte a domande frequenti sulla persistenza di Cache Redis di Azure.

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
* Se si è passati a una dimensione minore che non è abbastanza grande per contenere tutti i dati del backup più recente, le chiavi verranno rimosse durante il processo di ripristino, in genere usando il criterio di rimozione [allkeys-lru](https://redis.io/topics/lru-cache) .

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>È possibile modificare la frequenza di backup RDB dopo avere creato la cache?
Sì, è possibile modificare la frequenza di backup per la persistenza RDB nel pannello **persistenza dei dati** . Per istruzioni, vedere Configurare la persistenza di Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Perché trascorrono più di 60 minuti tra i backup se è stata impostata una frequenza di backup RDB di 60 minuti?
L'intervallo di frequenza di backup della persistenza RDB inizia solo dopo il completamento del processo di backup precedente. Se la frequenza di backup è 60 minuti e per il corretto completamento del processo di backup sono richiesti 15 minuti, il backup successivo verrà avviato solo 75 minuti dopo l'ora di inizio del backup precedente.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Cosa accade ai backup RDB precedenti quando viene eseguito un nuovo backup?
A eccezione di quello più recente, tutti i backup della persistenza RDB vengono eliminati automaticamente. L'eliminazione potrebbe non avvenire immediatamente, ma i backup meno recenti non vengono mantenuti per un tempo illimitato.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando è consigliabile usare un secondo account di archiviazione?

È consigliabile usare un secondo account di archiviazione per la persistenza AOF quando si ritiene di avere un numero di operazioni set nella cache superiore a quello previsto.  L'impostazione del secondo account di archiviazione aiuta a garantire che la cache non raggiunga i limiti di larghezza di banda di archiviazione.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>La persistenza AOF influisce sulla velocità effettiva, la latenza o le prestazioni della cache?

La persistenza AOF influisce sulla velocità effettiva di circa il 15-20% quando la cache è al di sotto del carico massimo (carico di CPU e server inferiore al 90%). Quando la cache lavora entro questi limiti, non si verificano problemi di latenza. La cache raggiunge tuttavia questi limiti prima con la persistenza AOF abilitata.

### <a name="how-can-i-remove-the-second-storage-account"></a>Come si rimuove il secondo account di archiviazione?

È possibile rimuovere l'account di archiviazione secondario della persistenza AOF impostandolo come il primo account di archiviazione. Per le cache esistenti, è possibile accedere al pannello di **persistenza dei dati** dal **menu delle risorse** per la cache. Per disabilitare la persistenza AOF, fare clic su **disabilitato**.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Che cos'è un'operazione di riscrittura e in che modo influisce sulla cache?

Quando il file AOF diventa sufficientemente grande, viene automaticamente accodata una riscrittura nella cache. L'operazione di riscrittura ridimensiona il file AOF con il set minimo di operazioni necessarie per creare il set di dati corrente. Durante l'operazione di riscrittura è normale raggiungere prima i limiti di prestazioni, soprattutto quando si usano grandi set di dati. L'operazione di riscrittura avviene meno spesso man mano che il file AOF diventa più grande, ma quando a quel punto si renderà necessaria, richiederà molto più tempo.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Quali potrebbero essere gli effetti del ridimensionamento di una cache con la persistenza AOF abilitata?

Se il file AOF al momento del ridimensionamento è molto grande, l'operazione di ridimensionamento richiede più tempo del previsto perché ricarica il file dopo che il ridimensionamento è stato completato.

Per altre informazioni sul ridimensionamento, vedere [Cosa accade se si è passati a una dimensione diversa e viene ripristinato un backup creato prima dell'operazione di ridimensionamento?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Come vengono organizzati i dati AOF nello spazio di archiviazione?

I dati archiviati nei file AOF sono suddivisi in più BLOB di pagine per ogni nodo per migliorare le prestazioni nel salvataggio dei dati nello spazio di archiviazione. La tabella seguente illustra il numero di BLOB di pagine usato per ogni piano tariffario:

| Livello Premium | BLOB |
|--------------|-------|
| P1           | 4 per partizione    |
| P2           | 8 per partizione    |
| P3           | 16 per partizione   |
| P4           | 20 per partizione   |

Quando è abilitato il clustering, ogni partizione nella cache ha un proprio set di BLOB di pagine, come indicato nella tabella precedente. Una cache P2 con tre partizioni, ad esempio, distribuisce il proprio file AOF su 24 BLOB di pagine (8 BLOB per partizione, con 3 partizioni).

Dopo una riscrittura, nello spazio di archiviazione sono presenti due set di file AOF. L'operazione di riscrittura avviene in background e si accoda al primo set di file, mentre le operazioni set che vengono inviate alla cache durante la riscrittura si accodano al secondo set. Una copia di backup viene archiviata temporaneamente durante la riscrittura in caso di errore, ma viene eliminata immediatamente dopo il completamento della riscrittura.


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
