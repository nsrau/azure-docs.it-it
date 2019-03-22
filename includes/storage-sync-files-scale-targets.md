---
title: File di inclusione
description: File di inclusione
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: dfba8db87dab12f856fbd97d578321477e9f92b5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553655"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 15 servizi di sincronizzazione archiviazione | Sì |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Sì |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Sì |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Sì |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No  |
| Endpoint server per server | 30 endpoint server | Sì |
| Dimensioni endpoint | 4 TiB | No  |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 25 milioni di oggetti | No  |
| Numero massimo di oggetti file system (directory e file) in una directory | 1 milione di oggetti | Sì |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 4 KiB | Sì |
| Dimensioni complete | 100 GiB | No  |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Sì |
| Sessioni di sincronizzazione simultanee | Agente versione 4 e successive: Il limite varia in base alle risorse di sistema disponibili. <BR> Agente V3: Due sessioni di sincronizzazione attivi per ogni processore o un massimo di otto sessioni di sincronizzazione attivi per ogni server. | Sì
