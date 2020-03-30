---
title: File di inclusione
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795637"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 20 Servizi di sincronizzazione archiviazione | Sì |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Sì |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Sì |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Sì |
| Endpoint server per gruppo di sincronizzazioneServer endpoints per sync group | 50 endpoint server | No |
| Endpoint server per server | 30 endpoint server | Sì |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 100 milioni di oggetti | No |
| Numero massimo di oggetti file system (directory e file) in una directory | 5 milioni di oggetti | Sì |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Sì |
| Dimensione del file | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | V9: in base alle dimensioni del cluster del file system (dimensioni del cluster del file system doppio). Ad esempio, se la dimensione del cluster del file system è 4kb, la dimensione minima del file sarà 8kb.<br> V8 e versioni precedenti: 64 KiB  | Sì |

> [!Note]  
> Un endpoint di Sincronizzazione file di Azure può essere scalato alle dimensioni di una condivisione file di Azure.An Azure File Sync endpoint can scale up to the size of an Azure file share. Se viene raggiunto il limite di dimensioni della condivisione file di Azure, la sincronizzazione non sarà in grado di funzionare.
