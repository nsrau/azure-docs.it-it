---
title: file di inclusione
description: file di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795637"
---
| Gruppi | Obiettivo | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 20 servizi di sincronizzazione archiviazione | SÌ |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | SÌ |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | SÌ |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | SÌ |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No |
| Endpoint server per server | 30 endpoint server | SÌ |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | oggetti di 100 milioni | No |
| Numero massimo di oggetti file system (directory e file) in una directory | oggetti di 5 milioni | SÌ |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | SÌ |
| Dimensioni complete | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | V9: in base alle dimensioni del cluster file system (doppia file system dimensioni del cluster). Se, ad esempio, le dimensioni del cluster file system sono 4KB, le dimensioni minime del file saranno 8KB.<br> V8 e versioni precedenti: 64 KiB  | SÌ |

> [!Note]  
> Un endpoint Sincronizzazione file di Azure può essere scalato fino alle dimensioni di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni della condivisione file di Azure, la sincronizzazione non sarà in grado di funzionare.
