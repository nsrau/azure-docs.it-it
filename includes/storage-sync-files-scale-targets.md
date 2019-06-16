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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114540"
---
| Resource | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 20 servizi di sincronizzazione archiviazione | Yes |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Yes |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Yes |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Yes |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No |
| Endpoint server per server | 30 endpoint server | Yes |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 25 milioni di oggetti | No |
| Numero massimo di oggetti file system (directory e file) in una directory | 1 milione di oggetti | Yes |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Yes |
| Dimensioni complete | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Yes |
| Sessioni di sincronizzazione simultanee | Agente versione 4 e successive: Il limite varia in base alle risorse di sistema disponibili. <BR> Agente V3: Due sessioni di sincronizzazione attivi per ogni processore o un massimo di otto sessioni di sincronizzazione attivi per ogni server. | Yes

> [!Note]  
> Un endpoint di sincronizzazione File di Azure è possibile aumentare le dimensioni di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni di condivisione file di Azure, sincronizzazione non sarà in grado di operare.