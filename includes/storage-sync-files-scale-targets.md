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
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391677"
---
| Gruppi | Obiettivo | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 20 servizi di sincronizzazione archiviazione | SÌ |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | SÌ |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | SÌ |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | SÌ |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No |
| Endpoint server per server | 30 endpoint server | SÌ |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | oggetti di 50 milioni | No |
| Numero massimo di oggetti file system (directory e file) in una directory | oggetti di 5 milioni | SÌ |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | SÌ |
| Dimensioni complete | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | SÌ |
| Sessioni di sincronizzazione simultanee | Agente V4 e versioni successive: il limite varia in base alle risorse di sistema disponibili. <BR> Agente V3: due sessioni di sincronizzazione attive per processore o un massimo di otto sessioni di sincronizzazione attive per server. | SÌ

> [!Note]  
> Un endpoint Sincronizzazione file di Azure può essere scalato fino alle dimensioni di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni della condivisione file di Azure, la sincronizzazione non sarà in grado di funzionare.
