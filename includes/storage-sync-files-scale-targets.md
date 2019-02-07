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
ms.openlocfilehash: fea31bf1c5869164dde1eb7014b2aacc8fcc5e9c
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757530"
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
| Numero massimo di oggetti file system (directory e file) in una directory | 200.000 oggetti | Sì |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 4 KiB | Sì |
| Dimensioni complete | 100 GiB | No  |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Sì |
| Sessioni di sincronizzazione simultanee | Agente V4: il limite dipende dalle risorse di sistema disponibili. <BR> Agente V3: 2 sessioni di sincronizzazione attive per ogni processore o fino a 8 sessioni di sincronizzazione attive per ogni server | Sì
