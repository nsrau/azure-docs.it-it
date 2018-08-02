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
ms.openlocfilehash: 9f94a4c6434265d780b39143b7e4b062badd5fbf
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145945"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per sottoscrizione | 15 servizi di sincronizzazione archiviazione | No  |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 30 gruppi di sincronizzazione | Yes |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Yes |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Yes |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No  |
| Endpoint server per server | 33-99 endpoint server | Sì, ma variabile a seconda della configurazione |
| Dimensioni endpoint | 4 TiB | No  |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 25 milioni di oggetti | No  |
| Numero massimo di oggetti file system (directory e file) in una directory | 200.000 oggetti | No  |
| Lunghezza massima del nome dell'oggetto (directory e file) | 255 caratteri | Yes |
| Dimensioni complete | 100 GiB | No  |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Yes |