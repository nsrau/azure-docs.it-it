---
title: Impostare la versione di redis per cache di Azure per Redis (anteprima)
description: Informazioni su come configurare la versione di redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ed0f486afe466d31388fa99b4ce5f5754210533f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571502"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Impostare la versione di redis per cache di Azure per Redis (anteprima)
Questo articolo illustra come configurare la versione del software Redis da usare con l'istanza della cache. Cache di Azure per Redis offre la versione principale più recente di redis e almeno una versione precedente. Queste versioni vengono aggiornate regolarmente quando viene rilasciato il software Redis più recente. È possibile scegliere tra le due versioni disponibili. Tenere presente che la cache verrà aggiornata automaticamente alla versione successiva se la versione attualmente in uso non è più supportata.

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
    | **Tipo di cache** | Selezionare un [livello e una dimensione della cache](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |
   
1. Nella pagina **Avanzate** scegliere una versione di redis da usare.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Selezionare cache di Azure per Redis.":::

1. Fare clic su **Crea**. 
   
    La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.

    > [!NOTE]
    > Al momento, non è possibile modificare la versione di redis dopo la creazione di una cache.
    >

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

> [!div class="nextstepaction"]
> [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)
