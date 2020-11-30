---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309949"
---
No: la rimozione di un endpoint server non è simile al riavvio di un server. La rimozione e la ricreazione dell'endpoint server non è quasi mai una soluzione appropriata per risolvere i problemi di sincronizzazione, suddivisione in livelli cloud o altri aspetti del Sincronizzazione file di Azure. La rimozione di un endpoint server è un'operazione distruttiva. È possibile che si verifichi una perdita di dati nel caso in cui i file a livelli esistano al di fuori dello spazio dei nomi dell'endpoint server. Per ulteriori informazioni, vedere [il motivo per cui sono presenti file a livelli all'esterno dello spazio dei nomi dell'endpoint server](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) . In alternativa, è possibile che si verifichino file inaccessibili per i file a livelli presenti nello spazio dei nomi dell'endpoint server. Questi problemi non si risolveranno quando viene ricreato l'endpoint server. I file a livelli potrebbero esistere all'interno dello spazio dei nomi dell'endpoint server anche se la suddivisione a livelli del cloud non è mai stata abilitata. Per questo motivo è consigliabile non rimuovere l'endpoint server, a meno che non si desideri interrompere l'utilizzo di Sincronizzazione file di Azure con questa cartella specifica oppure che sia stato esplicitamente richiesto da un tecnico Microsoft. Per altre informazioni sulla rimozione degli endpoint server, vedere [Rimuovere un endpoint server](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
