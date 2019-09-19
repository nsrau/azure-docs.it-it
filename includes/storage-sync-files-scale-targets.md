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
ms.openlocfilehash: e7aa2b4389fe60eed80b15aff04d6f7fcbc7b013
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "68968885"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 20 servizi di sincronizzazione archiviazione | Yes |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Sì |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Yes |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Yes |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No |
| Endpoint server per server | 30 endpoint server | Sì |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | oggetti di 50 milioni | No |
| Numero massimo di oggetti file system (directory e file) in una directory | oggetti di 5 milioni | Yes |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Sì |
| Dimensione file | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Sì |
| Sessioni di sincronizzazione simultanee | Agente versione 4 e successive: Il limite varia in base alle risorse di sistema disponibili. <BR> Agente V3: Due sessioni di sincronizzazione attive per processore o un massimo di otto sessioni di sincronizzazione attive per server. | Sì

> [!Note]  
> Un endpoint Sincronizzazione file di Azure può essere scalato fino alle dimensioni di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni della condivisione file di Azure, la sincronizzazione non sarà in grado di funzionare.
