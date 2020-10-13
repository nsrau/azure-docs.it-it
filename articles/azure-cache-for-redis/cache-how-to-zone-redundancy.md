---
title: Abilitare la ridondanza della zona per cache di Azure per Redis (anteprima)
description: Informazioni su come configurare la ridondanza della zona per la cache di Azure di livello Premium per le istanze di redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 33c346fa2e4572799ad6341bd5115cdd6e3b9ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569993"
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

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selezionare Cache di Azure per Redis.":::
   
1. Nella pagina **nozioni di base** configurare le impostazioni per la nuova cache.
   
    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Sottoscrizione** | Selezionare la propria sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
    | **Gruppo di risorse** | Selezionare un gruppo di risorse oppure selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
    | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
    | **Posizione** | Selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
    | **Tipo di cache** | Selezionare una cache di [livello Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |
   
1. Nella pagina **Avanzate** scegliere numero di **repliche**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Selezionare Cache di Azure per Redis.":::

1. Selezionare **zone di disponibilità**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Selezionare Cache di Azure per Redis.":::

1. Lasciare le impostazioni predefinite per le altre opzioni. 

    > [!NOTE]
    > Il supporto per la ridondanza della zona funziona attualmente solo con le cache non in cluster e non con replica geografica. Inoltre, non supporta collegamenti privati, scalabilità, persistenza dei dati o importazione/esportazione.
    >

1. Fare clic su **Crea**. 
   
    La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.
   
    > [!NOTE]
    > Non è possibile modificare le zone di disponibilità dopo la creazione di una cache.
    >

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

> [!div class="nextstepaction"]
> [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)
