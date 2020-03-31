---
title: Come configurare la replica geografica per la cache di Azure per Redis Documenti Microsoft
description: Informazioni su come replicare le istanze di Cache Redis di Azure tra aree geografiche.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129420"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Come configurare la replica geografica per la cache di Azure per RedisHow to set up geo-replication for Azure Cache for Redis

La replica geografica fornisce un meccanismo per il collegamento di due istanze Cache Redis di Azure di livello Premium. Una cache viene scelta come cache collegata primaria e l'altra come cache collegata secondaria. La cache secondaria collegata diventa di sola lettura e i dati scritti nella cache primaria vengono replicati nella cache collegata secondaria. Questa funzionalità può essere usata per replicare una cache nelle aree di Azure. Questo articolo fornisce una guida alla configurazione della replica geografica per la cache di Azure di livello Premium per le istanze Redis.This article provides a guide to configuring geo-replication for your Premium tier Azure Cache for Redis instances.

## <a name="geo-replication-prerequisites"></a>Prerequisiti per la replica geografica

Per configurare la replica geografica tra due cache, è necessario che siano soddisfatti i prerequisiti seguenti:To configure geo-replication between two caches, the following prerequisites must be met:

- Entrambe le cache sono cache [di livello Premium.Both caches](cache-premium-tier-intro.md) are Premium tier caches.
- Entrambe le cache si trovano nella stessa sottoscrizione di Azure.Both caches are in the same Azure subscription.
- La cache collegata secondaria è la stessa dimensione della cache o una dimensione della cache maggiore rispetto alla cache collegata primaria.
- Entrambe le cache vengono create e in esecuzione.

Alcune funzionalità non sono supportate con la replica geografica:Some features aren't supported with geo-replication:

- La persistenza non è supportata con la replica geografica.
- Il clustering è supportato se entrambe le cache hanno il clustering abilitato e hanno lo stesso numero di partizioni.
- Sono supportate le cache nella stessa rete virtuale.
- Le cache in diversi VNET sono supportate con avvertenze. Per altre informazioni, vedere È possibile usare la [replica geografica con le cache in una rete virtuale.](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Dopo aver configurato la replica geografica, alla coppia di cache collegata vengono applicate le restrizioni seguenti:After geo-replication is configured, the following restrictions apply to your linked cache pair:

- La cache collegata secondaria è di sola lettura. È possibile leggere da essa, ma non è possibile scrivervi dati. 
- Tutti i dati presenti nella cache collegata secondaria prima dell'aggiunta del collegamento vengono rimossi. Se la replica geografica viene rimossa in un secondo momento, tuttavia, i dati replicati rimangono nella cache collegata secondaria.
- Non è possibile [ridimensionare](cache-how-to-scale.md) la cache mentre le cache sono collegate.
- Non è possibile [modificare il numero di partizioni](cache-how-to-premium-clustering.md) se nella cache è abilitato il clustering.
- Non è possibile abilitare la persistenza in nessuna delle cache.
- È possibile esportare da [entrambe](cache-how-to-import-export-data.md#export) le cache.
- Non è possibile [importare](cache-how-to-import-export-data.md#import) nella cache collegata secondaria.
- Non è possibile eliminare la cache collegata o il gruppo di risorse che li contiene, finché non si scollegano le cache. Per altre informazioni, vedere [Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se le cache si trovano in aree diverse, i costi in uscita della rete si applicano ai dati spostati tra le aree. Per altre informazioni, vedere [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Il failover automatico non si verifica tra la cache collegata primaria e quella secondaria. Per altre informazioni e informazioni su come eseguire il failover di un'applicazione client, vedere [Come funziona il failover nella cache collegata secondaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Aggiungere un collegamento di replica geograficaAdd a geo-replication link

1. Per collegare due cache per la replica geografica, scegliere **Replica geografica** dal menu Risorsa della cache che si intende essere la cache collegata primaria. Successivamente, fare clic su **Aggiungi collegamento di replica della cache** dal pannello Replica **geografica.**

    ![Aggiungi collegamento](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Fare clic sul nome della cache secondaria desiderata dall'elenco **Cache compatibili.** Se la cache secondaria non è visualizzata nell'elenco, verificare che siano soddisfatti [i prerequisiti](#geo-replication-prerequisites) di replica geografica per la cache secondaria. Per filtrare le cache in base all'area, fare clic sull'area nella mappa per visualizzare solo le cache nell'elenco **Cache compatibili.**

    ![Cache compatibili con la replica geografica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    È inoltre possibile avviare il processo di collegamento o visualizzare i dettagli relativi alla cache secondaria utilizzando il menu di scelta rapida.

    ![Menu di scelta rapida della replica geografica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Fare clic su **Collega** per collegare le due cache e iniziare il processo di replica.

    ![Collega cache](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. È possibile visualizzare lo stato di avanzamento del processo di replica nel pannello **Replica geografica**.

    ![Stato del collegamento](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    È anche possibile visualizzare lo stato del collegamento nel pannello **Panoramica** per le cache primaria e secondaria.

    ![Stato della cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Dopo aver completato il processo di replica, **Link status** (Stato collegamento) visualizza **Riuscito**.

    ![Stato della cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    La cache collegata primaria rimane disponibile per l'utilizzo durante il processo di collegamento. La cache collegata secondaria non è disponibile fino al completamento del processo di collegamento.

## <a name="remove-a-geo-replication-link"></a>Rimuovere un collegamento di replica geografica

1. Per rimuovere il collegamento tra due cache e interrompere la replica geografica, fare clic su **Scollega cache** dal pannello **Replica geografica.**
    
    ![Scollega cache](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Al termine del processo di scollegamento, la cache secondaria è disponibile per le operazioni di lettura e scrittura.

>[!NOTE]
>Quando il collegamento di replica geografica viene rimosso, i dati replicati dalla cache collegata primaria rimangono nella cache secondaria.
>
>

## <a name="geo-replication-faq"></a>Domande frequenti sulla replica geografica

- [È possibile usare la replica geografica con una cache di livello Standard o Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [È possibile collegare più di due cache?](#can-i-link-more-than-two-caches-together)
- [È possibile collegare due cache da diverse sottoscrizioni di Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [È possibile collegare due cache di dimensioni diverse?](#can-i-link-two-caches-with-different-sizes)
- [È possibile usare la replica geografica con il clustering abilitato?](#can-i-use-geo-replication-with-clustering-enabled)
- [È possibile usare la replica geografica con le cache in una rete virtuale?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Che cos'è la pianificazione della replica per la replica geografica Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo richiede la replica geografica?](#how-long-does-geo-replication-replication-take)
- [Il punto di recupero della replica è garantito?](#is-the-replication-recovery-point-guaranteed)
- [È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Quale area si deve usare per la cache collegata secondaria?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Come funziona il failover nella cache collegata secondaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>È possibile usare la replica geografica con una cache di livello Standard o Basic?

No, la replica geografica è disponibile solo per le cache di livello Premium.No, geo-replication is only available for Premium tier caches.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?

- Durante il collegamento, la cache collegata primaria rimane disponibile durante il completamento del processo di collegamento.
- Durante il collegamento, la cache collegata secondaria non è disponibile fino al completamento del processo di collegamento.
- Durante lo scollegamento, entrambe le cache rimangono disponibili al termine del processo di scollegamento.

### <a name="can-i-link-more-than-two-caches-together"></a>È possibile collegare più di due cache?

No, è possibile collegare solo due cache.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>È possibile collegare due cache da diverse sottoscrizioni di Azure?

No, entrambe le cache devono trovarsi nella stessa sottoscrizione di Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>È possibile collegare due cache di dimensioni diverse?

Sì, purché la cache collegata secondaria sia più grande della cache collegata primaria.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>È possibile usare la replica geografica con il clustering abilitato?

Sì, purché entrambe le cache abbiano lo stesso numero di partizioni.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>È possibile usare la replica geografica con le cache in una rete virtuale?

Sì, la replica geografica delle cache nelle VNET è supportata con gli avvertimenti:

- La replica geografica tra le cache nella stessa rete virtuale è supportata.
- È supportata anche la replica geografica tra le cache in diversi VNET.
  - Se i VNET si trovano nella stessa area, è possibile connetterli utilizzando il [peering VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o una [connessione vNET-to-VNET gateway VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)
  - Se le reti virtuali si trovano in aree diverse, la replica geografica tramite peering VNET non è supportata a causa di un vincolo con servizi di bilanciamento del carico interni di base. Per ulteriori informazioni sui vincoli di peering VNET, vedere [Rete virtuale - Peering - Requisiti e vincoli](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). La soluzione consigliata consiste nell'utilizzare una connessione da VNET a VNET del gateway VPN.

Usando questo modello di [Azure,](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)è possibile distribuire rapidamente due cache con replica geografica in una rete virtuale connessa con una connessione da VNET a VNET del gateway VPN.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Che cos'è la pianificazione della replica per la replica geografica Redis?

La replica è continua e asincrona e non avviene in base a una pianificazione specifica. Tutte le scritture eseguite nel database primario vengono replicate istantaneamente e in modo asincrono nel database secondario.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo richiede la replica geografica?

La replica è incrementale, asincrona e continua e il tempo impiegato non è molto diverso dalla latenza tra le aree. In determinate circostanze, la cache secondaria potrebbe essere necessaria per eseguire una sincronizzazione completa dei dati dal database primario. Il tempo di replica in questo caso dipende dal numero di fattori come il carico nella cache primaria, la larghezza di banda di rete disponibile e la latenza tra aree. Abbiamo trovato il tempo di replica per una coppia completa da 53 GB con replica geografica può essere compresa tra 5 e 10 minuti.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Il punto di recupero della replica è garantito?

Per le cache in modalità con replica geografica, la persistenza viene disabilitata. Se una coppia con replica geografica non è collegata, ad esempio un failover avviato dal cliente, la cache collegata secondaria mantiene i dati sincronizzati fino a quel momento. In tali situazioni non è garantito alcun punto di ripristino.

Per ottenere un punto di ripristino, esportare da [entrambe](cache-how-to-import-export-data.md#export) le cache. È possibile [eseguire l'importazione](cache-how-to-import-export-data.md#import) in un secondo momento nella cache collegata primaria.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?

Sì, la replica geografica può essere gestita tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.Yes, geo-replication can be managed using the Azure portal, PowerShell, or Azure CLI. Per altre informazioni, vedere i documenti di PowerShell o dell'interfaccia della riga di comando di Azure.For more information, see the [PowerShell docs](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) or [Azure CLI docs.](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto costa replicare i dati nelle aree di Azure?

Quando si usa la replica geografica, i dati della cache collegata primaria vengono replicati nella cache collegata secondaria. Non è previsto alcun costo per il trasferimento dei dati se le due cache collegate si trovano nella stessa area. Se le due cache collegate si trovano in aree diverse, l'addebito per il trasferimento dei dati è il costo in uscita della rete dei dati in entrambe le aree. Per ulteriori informazioni, vedere [Dettagli sui prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?

Le cache con replica geografica e i relativi gruppi di risorse non possono essere eliminati durante il collegamento finché non si rimuove il collegamento di replica geografica. Se si tenta di eliminare il gruppo di risorse che contiene una o entrambe le cache collegate, vengono eliminate le altre risorse nel gruppo di risorse, ma il gruppo di risorse rimane nello stato `deleting` mentre le cache collegate nel gruppo di risorse rimangono nello stato `running`. Per eliminare completamente il gruppo di risorse e le cache collegate al suo interno, scollegare le cache come descritto in [Rimuovere un collegamento](#remove-a-geo-replication-link)di replica geografica .

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Quale area si deve usare per la cache collegata secondaria?

In generale, è consigliabile che la cache esista nella stessa area di Azure dell'applicazione che vi accede. Per le applicazioni con aree primarie e di fallback separate, è consigliabile che le cache primarie e secondarie esistano in quelle stesse aree. Per altre informazioni sulle aree abbinate, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Come funziona il failover nella cache collegata secondaria?

Il failover automatico tra aree di Azure non è supportato per le cache con replica geografica. In uno scenario di ripristino di emergenza, i clienti devono visualizzare l'intero stack di applicazioni in modo coordinato nell'area di backup. Consentire ai singoli componenti dell'applicazione di decidere autonomamente quando passare ai backup da soli può influire negativamente sulle prestazioni. Uno dei vantaggi principali di Redis è che si tratta di un archivio a latenza molto bassa. Se l'applicazione principale del cliente si trova in un'area diversa rispetto alla cache, il tempo di andata e ritorno aggiunto avrebbe un impatto notevole sulle prestazioni. Per questo motivo, evitiamo il failover automatico a causa di problemi di disponibilità temporanei.

Per avviare un failover avviato dal cliente, scollegare prima le cache. Quindi, modificare il client Redis per utilizzare l'endpoint di connessione della cache secondaria (precedentemente collegata). Quando le due cache sono scollegate, la cache secondaria diventa di nuovo una normale cache di lettura/scrittura e accetta le richieste direttamente dai client Redis.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [livello Premium di Cache Redis di Azure](cache-premium-tier-intro.md).
