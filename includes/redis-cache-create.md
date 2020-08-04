---
title: File di inclusione
description: File di inclusione
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e0557f8eb2fd120baeebee96acb4ef539344e3e7
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528829"
---
1. Per creare una cache, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. 
   
   ![Selezionare Crea una risorsa](media/redis-cache-create/create-a-resource.png)
   
1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.
   
   ![Selezionare Cache di Azure per Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | Nell'elenco a discesa selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Piano tariffario** | Nell'elenco a discesa selezionare un [piano tariffario](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Selezionare **Create** (Crea). 
   
   ![Creare un'istanza di Azure Cache for Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   > [!NOTE]
   > Il provisioning della cache richiederà presumibilmente circa 15-20 minuti.   
   
   È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.
   
   ![Istanza di Azure Cache for Redis creata](media/redis-cache-create/redis-cache-cache-created.png)

