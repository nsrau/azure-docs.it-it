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
ms.openlocfilehash: e035b49d9e386287baf67bba756f7b58a764acc5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34173119"
---
Per creare una cache, accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Crea una risorsa** > **Database** > **Cache Redis**.

![New cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

In **Nuova cache Redis**, configurare le impostazioni per la nuova cache.

| Impostazione      | Valore consigliato  | DESCRIZIONE |
| ------------ |  ------- | -------------------------------------------------- |
| **Nome DNS** | Nome globalmente univoco | Il nome della cache deve essere una stringa contenente da 1 a 63 caratteri che possono includere solo numeri, lettere e il carattere `-` . Il nome della cache non può iniziare o terminare con il carattere `-` e i caratteri `-` consecutivi non sono validi.  | 
| **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova cache Redis di Azure. | 
| **Gruppo di risorse** |  *TestResources* | Nome del nuovo gruppo di risorse in cui creare la cache. Inserendo tutte le risorse per un'app in un gruppo è possibile gestirle insieme. Ad esempio, eliminando il gruppo di risorse si eliminano tutte le risorse associate all'app. | 
| **Posizione** | Stati Uniti orientali | Scegliere una [regione](https://azure.microsoft.com/regions/) nelle vicinanze di altri servizi che utilizzeranno la cache. |
| **[Piano tariffario](https://azure.microsoft.com/pricing/details/cache/)** |  C0 Basic (cache da 250 MB) |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere [Panoramica della cache Redis di Azure](../articles/redis-cache/cache-overview.md). |
| **Aggiungi al dashboard** |  Selezionato | L'aggiunta della nuova cache al dashboard la rende più semplice da trovare. |

![Create cache](media/redis-cache-create/redis-cache-cache-create.png) 

Dopo aver configurato le impostazioni della nuova cache, fare clic su **Crea**. 

La creazione della cache può richiedere alcuni minuti. Per verificare lo stato è possibile monitorare l'avanzamento nel dashboard. Lo stato della cache dopo la creazione sarà **In esecuzione** e sarà possibile usarla subito.

![Cache created](media/redis-cache-create/redis-cache-cache-created.png)

