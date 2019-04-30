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
ms.openlocfilehash: 19c570be71583a2933a39780ba948edeb9f68498
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129101"
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
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Sì |
| Dimensioni complete | 100 GiB | No  |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Sì |
| Sessioni di sincronizzazione simultanee | Agente versione 4 e successive: Il limite varia in base alle risorse di sistema disponibili. <BR> Agente V3: Due sessioni di sincronizzazione attivi per ogni processore o un massimo di otto sessioni di sincronizzazione attivi per ogni server. | Sì
