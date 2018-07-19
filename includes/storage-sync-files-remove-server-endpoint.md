---
title: File di inclusione
description: File di inclusione
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763096"
---
La rimozione di un endpoint server non è uguale a riavviare un server. Rimuovere e ricreare l'endpoint server non è quasi mai una soluzione appropriata per risolvere i problemi di sincronizzazione, suddivisione in livelli del cloud o altri aspetti di Sincronizzazione file di Azure. La rimozione di un endpoint server è un'operazione distruttiva e può comportare la perdita di dati nel caso esistano file a livelli all'esterno dello spazio dei nomi dell'endpoint server o in file inaccessibili per i file a livelli esistenti all'interno dello spazio dei nomi dell'endpoint server. Per altre informazioni, vedere [Why do tiered files exist outside of the server endpoint namespace](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) (Perché esistono file a livelli all'esterno dello spazio dei nomi dell'endpoint server). Questi problemi non verranno risolto ricreando l'endpoint server. I file a livelli potrebbero esistere all'interno dello spazio dei nomi dell'endpoint server anche se la suddivisione a livelli del cloud non è mai stata abilitata. È pertanto consigliabile non rimuovere l'endpoint server a meno che non si voglia interrompere l'uso di Sincronizzazione file di Azure con questa particolare cartella o non si ricevano istruzioni esplicite a tale scopo da un tecnico Microsoft. Per altre informazioni sulla rimozione degli endpoint server, vedere [Rimuovere un endpoint server](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    