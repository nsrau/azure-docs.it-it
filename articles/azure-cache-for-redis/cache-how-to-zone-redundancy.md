---
title: Abilitare la ridondanza della zona per cache di Azure per Redis (anteprima)
description: Informazioni su come configurare la ridondanza della zona per la cache di Azure di livello Premium per le istanze di redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 3c396d6d5b9da9a48e0d68a2d7d49561d6f688de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344620"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Abilitare la ridondanza della zona per cache di Azure per Redis (anteprima)
Questo articolo illustra come configurare un'istanza di cache di Azure con ridondanza della zona usando il portale di Azure.

Cache di Azure per i livelli standard e Premium di redis offre ridondanza incorporata ospitando ogni cache in due macchine virtuali (VM) dedicate. Anche se queste VM si trovano in domini di aggiornamento e di [errore di Azure](/azure/virtual-machines/windows/manage-availability) separati e a disponibilità elevata, sono soggette a errori a livello di Data Center. Cache di Azure per Redis supporta anche la ridondanza della zona nel livello Premium. Una cache con ridondanza della zona viene eseguita in macchine virtuali distribuite in più [zone di disponibilità](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures). Offre maggiore resilienza e disponibilità.

> [!IMPORTANT]
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per ulteriori informazioni, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Prerequisiti
* Sottoscrizione di Azure- [crearne una gratuitamente](https://azure.microsoft.com/free/)

> [!NOTE]
> Questa funzionalità è attualmente in anteprima: [Contattaci](mailto:azurecache@microsoft.com) se sei interessato.
>

## <a name="create-a-cache"></a>Creare una cache
Per creare una cache, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**.
  
1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selezionare cache di Azure per Redis.":::
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
    | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
    | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
    | **Posizione** | Nell'elenco a discesa selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
    | **Piano tariffario** | Elenco a discesa e selezionare una cache di [livello Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |
    | **Numero di repliche** | Scorrere per scegliere il numero di repliche. | Il valore predefinito è 1. |
    | **Zone di disponibilità** | Elenco a discesa e selezionare le zone da usare. | Le macchine virtuali per la cache verranno distribuite in modo più uniforme possibile nelle zone selezionate. Ad esempio, se la cache ha tre repliche e usa due zone, saranno presenti due macchine virtuali in ogni zona. |
   
1. Dopo aver selezionato una cache di livello Premium, verrà chiesto se abilitare o meno il clustering di Redis. Lascia il **clustering** come *disabilitato*. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Configurare il cluster Redis.":::

    > [!NOTE]
    > Il supporto per la ridondanza della zona funziona attualmente solo con le cache non in cluster e non con replica geografica. Inoltre, non supporta collegamenti privati, scalabilità, persistenza dei dati o importazione/esportazione.
    >

1. Fare clic su **Crea**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Creare cache di Azure per Redis.":::
   
    La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.

    > [!NOTE]
    > Non è possibile modificare le zone di disponibilità dopo la creazione di una cache.
    >

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

> [!div class="nextstepaction"]
> [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)
