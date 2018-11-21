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
ms.openlocfilehash: 03fe587ede297ac7dea90b7a5fb2d5323f60659e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628156"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per sottoscrizione | 15 servizi di sincronizzazione archiviazione per area | No  |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Yes |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Yes |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Yes |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No  |
| Endpoint server per server | 33-99 endpoint server | Sì, ma varia in base alla configurazione (CPU, memoria, volumi, varianza del file, numero di file e così via) |
| Dimensioni endpoint | 4 TiB | No  |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 25 milioni di oggetti | No  |
| Numero massimo di oggetti file system (directory e file) in una directory | 200.000 oggetti | Yes |
| Lunghezza massima del nome dell'oggetto (directory e file) | 255 caratteri | Yes |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 4 KiB | Yes |
| Dimensioni complete | 100 GiB | No  |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Yes |
| Sessioni di sincronizzazione simultanee | Agente V4: il limite dipende dalle risorse di sistema disponibili. <BR> Agente V3: 2 sessioni di sincronizzazione attive per ogni processore o fino a 8 sessioni di sincronizzazione attive per ogni server | Yes
