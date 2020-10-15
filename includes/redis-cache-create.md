---
title: File di inclusione
description: File di inclusione
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828989"
---
1. Per creare una cache, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Selezionare Cache di Azure per Redis.":::

   
1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Selezionare Cache di Azure per Redis.":::
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | Nell'elenco a discesa selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Piano tariffario** | Nell'elenco a discesa selezionare un [piano tariffario](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](../articles/azure-cache-for-redis/cache-overview.md). |

1. Selezionare la scheda **Rete** o fare clic sul pulsante **Rete** nella parte inferiore della pagina.

1. Nella scheda **Rete** selezionare il metodo di connettività.

1. Fare clic sul pulsante **Avanti: Avanzate** oppure fare clic sulla scheda **Avanti: Avanzate** nella parte inferiore della pagina.

1. Nella scheda **Avanzate** per un'istanza della cache Basic o Standard selezionare l'interruttore Abilita se si vuole abilitare una porta non TLS.

1. Nella scheda **Avanzate** per l'istanza della cache Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati.

1. Fare clic sul pulsante **Avanti: Tag** o fare clic sulla scheda **Avanti: Tag** nella parte inferiore della pagina.

1. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa. 

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla scheda Rivedi e crea in cui Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio di convalida verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina  **Panoramica**  della cache di Azure per Redis. Quando la voce  **Stato**  indica  **In esecuzione**, la cache è pronta per l'uso. 