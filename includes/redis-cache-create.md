---
title: File di inclusione
description: File di inclusione
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 6a96bd7c3d7d02f181a7d7513edb3bb39881274f
ms.sourcegitcommit: 7de1432648c4ff3bcd09530c079418477d9f4d00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2018
ms.locfileid: "35719431"
---
1. Per creare una cache, accedere al [portale di Azure](https://portal.azure.com). Selezionare quindi **Crea una risorsa** > **Database** > **Cache Redis**.

    ![New cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. In **Nuova cache Redis**, configurare le impostazioni per la nuova cache.

    | Impostazione      | Valore consigliato  | DESCRIZIONE |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome DNS** | Nome globalmente univoco | Nome della cache. Deve essere una stringa contenente da 1 a 63 caratteri che possono includere solo numeri, lettere e il carattere `-`. Il nome della cache non può iniziare o terminare con il carattere `-` e i caratteri `-` consecutivi non sono validi.  | 
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova istanza di Cache Redis di Azure. | 
    | **Gruppo di risorse** |  *TestResources* | Nome del nuovo gruppo di risorse in cui creare la cache. Inserendo tutte le risorse per un'app in un gruppo è possibile gestirle insieme. Ad esempio, eliminando il gruppo di risorse si eliminano tutte le risorse associate all'app. | 
    | **Posizione** | Stati Uniti orientali | Scegliere una [regione](https://azure.microsoft.com/regions/) nelle vicinanze di altri servizi che utilizzeranno la cache. |
    | **[Piano tariffario](https://azure.microsoft.com/pricing/details/cache/)** |  C0 Basic (cache da 250 MB) |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere [Panoramica della cache Redis di Azure](../articles/redis-cache/cache-overview.md). |
    | **Aggiungi al dashboard** |  Selezionato | Aggiungere la nuova cache al dashboard per renderla più semplice da trovare. |

    ![Create cache](media/redis-cache-create/redis-cache-cache-create.png) 

3. Dopo aver configurato le impostazioni della nuova cache, selezionare **Crea**. 

    La creazione della cache può richiedere alcuni minuti. Per verificare lo stato è possibile monitorare l'avanzamento nel dashboard. Dopo la creazione, lo stato della cache sarà **In esecuzione** e sarà possibile usarla subito.

    ![Cache created](media/redis-cache-create/redis-cache-cache-created.png)

