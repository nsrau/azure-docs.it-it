---
title: Aggiungere repliche alla cache di Azure per Redis (anteprima)
description: Informazioni su come aggiungere più repliche alla cache di Azure di livello Premium per le istanze di redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566789"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Aggiungere repliche alla cache di Azure per Redis (anteprima)
Questo articolo illustra come configurare un'istanza di cache di Azure con repliche aggiuntive usando il portale di Azure.

La cache di Azure per i livelli standard e Premium di redis offre ridondanza ospitando ogni cache in due macchine virtuali (VM) dedicate. Queste macchine virtuali sono configurate come primarie e di replica. Quando la macchina virtuale primaria non è più disponibile, la replica rileva che e acquisisce automaticamente il nuovo database primario. È ora possibile aumentare fino a tre il numero di repliche in una cache Premium, offrendo un totale di quattro macchine virtuali che eseguono il backup di una cache. La presenza di più repliche comporta una resilienza maggiore rispetto a quella fornita da una singola replica.

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
   
1. Nella pagina **nozioni di base** configurare le impostazioni per la nuova cache.
   
    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Sottoscrizione** | Selezionare la propria sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
    | **Gruppo di risorse** | Selezionare un gruppo di risorse oppure selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
    | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
    | **Posizione** | Selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
    | **Tipo di cache** | Selezionare una cache di [livello Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |
   
1. Nella pagina **Avanzate** scegliere numero di **repliche**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Selezionare cache di Azure per Redis.":::

1. Lasciare le impostazioni predefinite per le altre opzioni. 

    > [!NOTE]
    > Il supporto di più repliche funziona solo con le cache non cluster attualmente.
    >

1. Fare clic su **Crea**.
   
    La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.

    > [!NOTE]
    > Il numero di repliche in una cache non può essere modificato dopo la creazione.
    >

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

> [!div class="nextstepaction"]
> [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)
