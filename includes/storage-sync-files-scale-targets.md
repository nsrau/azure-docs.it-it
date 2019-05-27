---
title: File di inclusione
description: File di inclusione
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114540"
---
| Resource | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 20 servizi di sincronizzazione archiviazione | Sì |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Sì |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Sì |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Sì |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | N. |
| Endpoint server per server | 30 endpoint server | Sì |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 25 milioni di oggetti | N. |
| Numero massimo di oggetti file system (directory e file) in una directory | 1 milione di oggetti | Sì |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Sì |
| Dimensione file | 100 GiB | N. |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Sì |
| Sessioni di sincronizzazione simultanee | Agente versione 4 e successive: Il limite varia in base alle risorse di sistema disponibili. <BR> Agente V3: Due sessioni di sincronizzazione attivi per ogni processore o un massimo di otto sessioni di sincronizzazione attivi per ogni server. | Sì

> [!Note]  
> Un endpoint di sincronizzazione File di Azure è possibile aumentare le dimensioni di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni di condivisione file di Azure, sincronizzazione non sarà in grado di operare.