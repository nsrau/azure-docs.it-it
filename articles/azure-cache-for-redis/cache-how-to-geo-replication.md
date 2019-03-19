---
title: Come configurare la replica geografica per Cache Redis di Azure | Microsoft Docs
description: Informazioni su come replicare le istanze di Cache Redis di Azure tra aree geografiche.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991570"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Come configurare la replica geografica per Cache Redis di Azure

La replica geografica fornisce un meccanismo per il collegamento di due istanze Cache Redis di Azure di livello Premium. Una cache viene scelto come la cache collegata primaria e l'altro come la cache collegata secondaria. La cache secondaria collegata diventa di sola lettura e i dati scritti nella cache primaria vengono replicati nella cache collegata secondaria. Questa funzionalità può essere usata per replicare una cache nelle aree di Azure. In questo articolo viene fornita una guida alla configurazione della replica geografica per le istanze Cache Redis di Azure di livello Premium.

## <a name="geo-replication-prerequisites"></a>Prerequisiti per la replica geografica

Per configurare la replica geografica tra due cache, devono essere soddisfatti i prerequisiti seguenti:

- Entrambe le cache vengono [livello Premium](cache-premium-tier-intro.md) memorizza nella cache.
- Entrambe le cache sono nella stessa sottoscrizione di Azure.
- La cache collegata secondaria è la stessa dimensione della cache o una dimensione della cache maggiore la cache collegata primaria.
- Entrambe le cache vengono create e in esecuzione.

Alcune funzionalità non sono supportate con la replica geografica:

- La persistenza non è supportata con la replica geografica.
- Il clustering è supportato se entrambe le cache clustering abilitato e avere lo stesso numero di partizioni.
- Le cache nella stessa rete virtuale sono supportate.
- Le cache in diverse reti virtuali sono supportate con avvertenze. Visualizzare [è possibile usare la replica geografica con le cache in una rete virtuale?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) per altre informazioni.

Dopo aver configurato la replica geografica, si applicano le restrizioni seguenti alla coppia di cache collegate:

- La cache collegata secondaria è di sola lettura. È possibile leggere da essa, ma non è possibile scrivervi dati. 
- Tutti i dati presenti nella cache collegata secondaria prima dell'aggiunta del collegamento vengono rimossi. Se la replica geografica è successiva rimosse, tuttavia, i dati replicati rimangono nella cache collegata secondaria.
- Non è possibile [scalabilità](cache-how-to-scale.md) entrambe le cache mentre le cache collegate.
- Non è possibile [modificare il numero di partizioni](cache-how-to-premium-clustering.md) se la cache ha il clustering abilitato.
- Non è possibile abilitare la persistenza in nessuna delle cache.
- È possibile [esportare](cache-how-to-import-export-data.md#export) da entrambe le cache.
- Non è possibile [importazione](cache-how-to-import-export-data.md#import) nella cache collegata secondaria.
- È possibile eliminare cache collegata o il gruppo di risorse che li contiene, fino a quando non si scollega la cache. Per altre informazioni, vedere [Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se la cache si trovano in aree diverse, i costi di uscita di rete si applicano ai dati spostati tra le aree. Per altre informazioni, vedere [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Il failover automatico non avviene tra la cache collegata primaria e secondaria. Per altre informazioni e le informazioni su come eseguire il failover di un'applicazione client, vedere [come funziona il failover per la cache collegata secondaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Aggiungere un collegamento di replica geografica

1. Per collegare due cache per la replica geografica, prima fare clic su **replica geografica** dal menu delle risorse della cache che si intende primario collegate della cache. Fare quindi clic **Aggiungi collegamento di replica della cache** dalle **Geo-replication** pannello.

    ![Aggiungi collegamento](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Fare clic sul nome della cache secondaria desiderata dal **cache compatibili** elenco. Se la cache secondaria non viene visualizzata nell'elenco, verificare che il [prerequisiti per la replica geografica](#geo-replication-prerequisites) per la cache secondaria vengono soddisfatti. Per filtrare le cache per area, fare clic sull'area della mappa per visualizzare solo quelle cache nel **cache compatibili** elenco.

    ![Cache compatibili con la replica geografica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    È anche possibile avviare il processo di collegamento o visualizzare i dettagli riguardanti la cache secondaria tramite il menu di scelta rapida.

    ![Menu di scelta rapida della replica geografica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Fare clic su **Collega** per collegare le due cache e iniziare il processo di replica.

    ![Collega cache](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. È possibile visualizzare lo stato di avanzamento del processo di replica nel pannello **Replica geografica**.

    ![Stato del collegamento](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    È anche possibile visualizzare lo stato del collegamento nel pannello **Panoramica** per le cache primaria e secondaria.

    ![Stato della cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Dopo aver completato il processo di replica, **Link status** (Stato collegamento) visualizza **Riuscito**.

    ![Stato della cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    La cache collegata primaria resta disponibile per l'uso durante il processo di collegamento. La cache collegata secondaria non è disponibile finché non viene completato il processo di collegamento.

## <a name="remove-a-geo-replication-link"></a>Rimuovere un collegamento di replica geografica

1. Per rimuovere il collegamento tra due cache e arrestare la replica geografica, fare clic su **Unlink caches** (Scollega cache) dal pannello **Replica geografica**.
    
    ![Scollega cache](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Al termine del processo di scollegamento, la cache secondaria è disponibile per le operazioni di lettura e scrittura.

>[!NOTE]
>Quando viene rimosso il collegamento di replica geografica, i dati replicati dalla cache collegata primaria restano nella cache secondaria.
>
>

## <a name="geo-replication-faq"></a>Domande frequenti sulla replica geografica

- [È possibile usare la replica geografica con una cache di livello Standard o Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [È possibile collegare più di due cache?](#can-i-link-more-than-two-caches-together)
- [È possibile collegare due cache da diverse sottoscrizioni di Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [È possibile collegare due cache con dimensioni diverse?](#can-i-link-two-caches-with-different-sizes)
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

No, la replica geografica è disponibile solo per le cache del livello Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?

- Quando il collegamento, la cache collegata primaria resta disponibile durante il processo di collegamento viene completata.
- Durante il collegamento, la cache collegata secondaria non è disponibile finché non viene completato il processo di collegamento.
- Durante lo scollegamento, entrambe le cache rimangono disponibili durante il processo di scollegamento completato.

### <a name="can-i-link-more-than-two-caches-together"></a>È possibile collegare più di due cache?

No, è possibile collegare solo due cache insieme.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>È possibile collegare due cache da diverse sottoscrizioni di Azure?

No, entrambe le cache devono trovarsi nella stessa sottoscrizione di Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>È possibile collegare due cache di dimensioni diverse?

Sì, purché la cache collegata secondaria sia più grande della cache collegata primaria.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>È possibile usare la replica geografica con il clustering abilitato?

Sì, purché entrambe le cache abbiano lo stesso numero di partizioni.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>È possibile usare la replica geografica con le cache in una rete virtuale?

Sì, la replica geografica di cache nelle reti virtuali è supportata con alcune avvertenze:

- La replica geografica tra le cache nella stessa rete virtuale è supportata.
- È supportata anche la replica geografica tra cache nelle reti virtuali diverse.
  - Se le reti virtuali si trovano nella stessa area, è possibile connetterle usando [peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o una [connessione Gateway VPN VNET-VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Se le reti virtuali si trovano in aree diverse, la replica geografica usando il peering non è supportato a causa di un vincolo con servizi di bilanciamento del carico interno di base. Per altre informazioni sui vincoli di peering della rete virtuale, vedere [vincoli e requisiti della rete virtuale - Peering -](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). La soluzione consigliata consiste nell'utilizzare una connessione Gateway VPN VNET-VNET.

Usando [questo modello di Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), è possibile distribuire rapidamente due cache con replica geografica in una rete virtuale connesse con una connessione Gateway VPN VNET-VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Che cos'è la pianificazione della replica per la replica geografica Redis?

La replica è continua e asincrona e non viene eseguita su una pianificazione specifica. Tutte le operazioni di scrittura eseguite alla replica primaria vengono replicati in modo asincrono e istantaneamente nella replica secondaria.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo richiede la replica geografica?

La replica è incrementale, asincrona e continua e il tempo richiesto non è molto diverso dalla latenza tra le aree. In determinate circostanze, la cache secondaria potrebbe essere necessario eseguire una sincronizzazione completa dei dati dal database primario. Il tempo di replica in questo caso è dipendente da diversi fattori, ad esempio: carico nella cache primaria, larghezza di banda disponibile e la latenza tra le aree. È stato trovato il tempo di replica per una coppia completa a 53 GB con replica geografica può essere compreso tra 5 e 10 minuti.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Il punto di recupero della replica è garantito?

Per le cache in una modalità con replica geografica, la persistenza è disabilitata. Se una coppia con replica geografica è scollegata, ad esempio un failover avviato dal cliente, la cache collegata secondaria mantiene i dati sincronizzati fino a quel punto nel tempo. Nessun punto di ripristino è garantito in tali situazioni.

Per ottenere un punto di ripristino [esportare](cache-how-to-import-export-data.md#export) da entrambe le cache. È possibile in un secondo momento [importazione](cache-how-to-import-export-data.md#import) nella cache collegata primaria.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?

Sì, la replica geografica può essere gestita tramite il portale di Azure, PowerShell o CLI di Azure. Per altre informazioni, vedere la [PowerShell docs](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) oppure [documentazione di CLI di Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto costa replicare i dati nelle aree di Azure?

Quando si usa la replica geografica, i dati dalla cache collegata primaria vengono replicati nella cache collegata secondaria. Non sono previsti addebiti per il trasferimento dei dati se le due cache collegate si trovano nella stessa area. Se le due cache collegate si trovano in aree diverse, l'addebito per il trasferimento dei dati è il costo di rete in uscita dei dati in movimento attraverso l'area. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Perché, quando si è tentato di eliminare la cache collegata, l'operazione non è riuscita?

Le cache con replica geografica e i relativi gruppi di risorse non possono essere eliminati se collegato fino a quando non si rimuove il collegamento di replica geografica. Se si tenta di eliminare il gruppo di risorse che contiene una o entrambe le cache collegate, vengono eliminate le altre risorse nel gruppo di risorse, ma il gruppo di risorse rimane nello stato `deleting` mentre le cache collegate nel gruppo di risorse rimangono nello stato `running`. Per eliminare completamente il gruppo di risorse e le cache collegate all'interno di esso, scollegare le cache come descritto in [rimuovere un collegamento di replica geografica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Quale area si deve usare per la cache collegata secondaria?

In generale, è consigliabile per la cache a esistere nella stessa area di Azure dell'applicazione che vi accede. Per le applicazioni con aree primarie e di fallback separate, è consigliabile che la cache primaria e secondaria presenti in quelle stesse aree. Per altre informazioni sulle aree abbinate, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Come funziona il failover nella cache collegata secondaria?

Il failover automatico tra aree di Azure non è supportato per le cache di replica geografica. In uno scenario di ripristino di emergenza, i clienti devono portare lo stack dell'intera applicazione in modo coordinato nella rispettiva area di backup. Consentendo applicazione singoli componenti di decidere quando attivare i backup in modo autonomo può influire negativamente sulle prestazioni. Uno dei principali vantaggi di Redis è che è un archivio di latenza molto bassa. Se l'applicazione del cliente principale è in un'area diversa rispetto a propria cache, il tempo di round trip aggiunto avrebbe un impatto notevole sulle prestazioni. Per questo motivo, sarà possibile evitare il failover automaticamente a causa di problemi temporanei di disponibilità.

Per avviare un failover avviato dal cliente, è prima necessario scollegare le cache. Modificare quindi i client Redis per usare l'endpoint della connessione della cache secondaria (in precedenza collegata). Quando non vengono scollegate le due cache, la cache secondaria diventa nuovamente una cache di lettura / scrittura regolare e accetta le richieste direttamente dai client di Redis.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [livello Premium di Cache Redis di Azure](cache-premium-tier-intro.md).
