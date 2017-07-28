---
title: Come configurare la replica geografica per Cache Redis di Azure | Microsoft Docs
description: Informazioni su come replicare le istanze di Cache Redis di Azure in aree geografiche diverse.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ed05b369d882d2d9853b87a87fd91fe927ab15ba
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Come configurare la replica geografica per Cache Redis di Azure

La replica geografica fornisce un meccanismo per il collegamento di due istanze di Cache Redis di Azure di livello Premium. Una cache viene definita come la cache primaria collegata, mentre l'altra come la cache collegata secondaria. La cache secondaria collegata diventa di sola lettura e i dati scritti nella cache primaria vengono replicati nella cache collegata secondaria. Questa funzionalità può essere usata per replicare una cache nelle aree di Azure. In questo articolo viene fornita una guida alla configurazione della replica geografica per le istanze di Cache Redis di Azure di livello Premium.

## <a name="geo-replication-prerequisites"></a>Prerequisiti per la replica geografica

Per configurare la replica geografica tra due cache, devono essere soddisfatti i prerequisiti seguenti:

- Entrambe le cache devono essere cache di [livello Premium](cache-premium-tier-intro.md).
- Entrambe le cache devono trovarsi nella stessa sottoscrizione di Azure.
- La cache collegata secondaria deve avere lo stesso piano tariffario o un piano tariffario maggiore rispetto alla cache collegata primaria.
- Se la cache collegata primaria dispone di clustering abilitato, la cache collegata secondaria deve avere il clustering abilitato con lo stesso numero di partizioni della cache collegata primaria.
- Entrambe le cache devono essere create e in esecuzione.
- La persistenza non deve essere attivata in nessuna delle due cache.
- La replica geografica tra le cache nella stessa rete virtuale è supportata. È supportata anche la replica geografica tra cache di diverse reti virtuali, purché le due reti virtuali siano configurate in modo tale che le risorse nelle reti virtuali siano in grado di raggiungersi tra loro tramite connessioni TCP.

Dopo aver configurato la replica geografica, si applicano le restrizioni seguenti alla coppia di cache collegate:

- La cache collegata secondaria è di sola lettura. È possibile leggere da essa, ma non è possibile scrivervi dati. 
- Tutti i dati presenti nella cache collegata secondaria prima dell'aggiunta del collegamento vengono rimossi. Tuttavia, se la replica geografica viene successivamente rimossa, i dati replicati rimarranno nella cache collegata secondaria.
- Non è possibile avviare un'[operazione di ridimensionamento](cache-how-to-scale.md) nella cache o [modificare il numero di partizioni](cache-how-to-premium-clustering.md) se la cache ha il clustering abilitato.
- Non è possibile abilitare la persistenza in nessuna delle cache.
- È possibile usare [Esporta](cache-how-to-import-export-data.md#export) con entrambe le cache, ma l'opzione [Importa](cache-how-to-import-export-data.md#import) è abilitata solo nella cache collegata primaria.
- Se le due cache si trovano in aree diverse, i costi di rete in uscita verranno applicati ai dati replicati nelle aree geografiche della cache collegata secondaria. Per altre informazioni, vedere [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Se la cache primaria (e relativa replica) si disattivano, non si verifica alcun failover automatico nella cache collegata secondaria. Per eseguire il failover delle applicazioni del client, è necessario rimuovere manualmente il collegamento di replica geografica e rivolgere le applicazioni del client verso la cache che in precedenza era la cache collegata secondaria. Per altre informazioni, vedere [Come funziona il failover nella cache collegata secondaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="to-add-a-cache-replication-link"></a>Per aggiungere un collegamento di replica della cache

1. Per collegare due cache Premium per la replica geografica, fare clic su **Replica geografica** dal menu Risorsa della cache che va considerata come quella collegata primaria e quindi fare clic su **Add cache replication link** (Aggiungi collegamento di replica della cache) dal pannello **Replica geografica**.

    ![Aggiungi collegamento](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Fare clic sul nome della cache secondaria desiderata dall'elenco **Compatible caches** (Cache compatibili). Se la cache desiderata non viene visualizzata nell'elenco, verificare che i [Prerequisiti per la replica geografica](#geo-replication-prerequisites) siano soddisfatti per la cache secondaria desiderata. Per filtrare le cache per area, fare clic sull'area desiderata nella mappa per visualizzare solo quelle cache nell'elenco **Compatible caches** (Cache compatibili).

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

    Durante il processo di collegamento, la cache collegata primaria resta disponibile per l'uso, ma la cache collegata secondaria non è disponibile finché non viene completato il processo di collegamento.

## <a name="to-remove-a-geo-replication-link"></a>Per rimuovere un collegamento di replica geografica

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
- [È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto costa replicare i dati nelle aree di Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Quale area si deve usare per la cache collegata secondaria?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Come funziona il failover nella cache collegata secondaria?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>È possibile usare la replica geografica con una cache di livello Standard o Basic?

No, la replica geografica è disponibile solo per le cache del livello Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>La cache è disponibile per l'uso durante il processo di collegamento o scollegamento?

- Quando le due cache si collegano per la replica geografica, la cache collegata primaria resta disponibile per l'uso, ma la cache collegata secondaria non è disponibile finché non viene completato il processo di collegamento.
- Quando si rimuove il collegamento di replica geografica tra due cache, entrambe le cache restano disponibili per l'uso.

### <a name="can-i-link-more-than-two-caches-together"></a>È possibile collegare più di due cache?

No, quando si usa la replica geografica è possibile collegare solo due cache.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>È possibile collegare due cache da diverse sottoscrizioni di Azure?

No, entrambe le cache devono trovarsi nella stessa sottoscrizione di Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>È possibile collegare due cache di dimensioni diverse?

Sì, purché la cache collegata secondaria sia più grande della cache collegata primaria.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>È possibile usare la replica geografica con il clustering abilitato?

Sì, purché entrambe le cache abbiano lo stesso numero di partizioni.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>È possibile usare la replica geografica con le cache in una rete virtuale?

Sì, la replica geografica di cache nelle reti virtuali è supportata. 

- La replica geografica tra le cache nella stessa rete virtuale è supportata.
- È supportata anche la replica geografica tra cache di diverse reti virtuali, purché le due reti virtuali siano configurate in modo tale che le risorse nelle reti virtuali siano in grado di raggiungersi tra loro tramite connessioni TCP.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>È possibile usare PowerShell o l'interfaccia della riga di comando di Azure per gestire la replica geografica?

In questo momento è possibile gestire la replica geografica solo tramite il portale di Azure.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto costa replicare i dati nelle aree di Azure?

Quando si usa la replica geografica, i dati dalla cache collegata primaria vengono replicati nella cache collegata secondaria. Se le due cache collegate si trovano nella stessa area di Azure, non è previsto alcun addebito per il trasferimento dei dati. Se le due cache collegate si trovano in diverse aree di Azure, l'addebito per il trasferimento dei dati della replica geografica corrisponde al costo della larghezza di banda della replica di quei dati nelle altre aree di Azure. Per altre informazioni, vedere [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Quale area si deve usare per la cache collegata secondaria?

In generale, è consigliabile che la cache sia presente nella stessa area di Azure dell'applicazione che accede ad essa. Se l'applicazione dispone di un'area primaria e di fallback, le cache primaria e secondaria devono essere presenti in quelle stesse aree. Per altre informazioni sulle aree abbinate, vedere [Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Come funziona il failover nella cache collegata secondaria?

Nella versione iniziale della replica geografica, Cache Redis di Azure non supporta il failover automatico nelle aree di Azure. La replica geografica è usata principalmente in uno scenario di ripristino di emergenza. In uno scenario di ripristino di emergenza, i clienti devono portare lo stack dell'intera applicazione in un'area di backup in modo coordinato invece di consentire che i singoli componenti dell'applicazione decidano quando attivare i backup in modo autonomo. Ciò è particolarmente importante per Redis. Uno dei principali vantaggi di Redis è quello di essere un archivio a latenza molto bassa. Se Redis usato da un'applicazione effettua il failover in un'area diversa di Azure, ma il livello di calcolo non lo fa, il tempo di round trip aggiunto avrà un impatto notevole sulle prestazioni. Per questo motivo, si desidera che Redis effettui il failover automatico a causa di problemi temporanei di disponibilità.

Attualmente, per avviare il failover, è necessario rimuovere il collegamento di replica geografica nel portale di Azure e quindi modificare l'endpoint di connessione nel client Redis dalla cache collegata primaria alla cache secondaria (in precedenza collegata). Quando vengono dissociate le due cache, la replica diventa nuovamente una cache di lettura/scrittura regolare e accetta le richieste direttamente da client Redis.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni nell'articolo [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md).


