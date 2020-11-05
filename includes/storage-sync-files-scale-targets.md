---
title: includere file
description: File di inclusione
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331156"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per area | 100 servizi di sincronizzazione archiviazione | Sì |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 200 gruppi di sincronizzazione | Sì |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Sì |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Sì |
| Endpoint server per gruppo di sincronizzazione | 100 endpoint server | Sì |
| Endpoint server per server | 30 endpoint server | Sì |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 100 milioni di oggetti | No |
| Numero massimo di oggetti file system (directory e file) in una directory | 5 milioni di oggetti | Sì |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 64 KiB | Sì |
| Dimensione del file | 100 GiB | No |
| Dimensioni minime per un file da rendere a livelli | V9 e più recenti: basate sulle dimensioni del cluster del file system (raddoppia le dimensioni del cluster del file system). Se, ad esempio, le dimensioni del cluster del file system sono 4 KB, le dimensioni minime del file saranno 8 KB.<br> V8 e versioni precedenti: 64 KiB  | Sì |

> [!Note]  
> Le dimensioni di un endpoint di Sincronizzazione file di Azure possono aumentare fino a quelle di una condivisione file di Azure. Se viene raggiunto il limite delle dimensioni della condivisione file di Azure, la sincronizzazione non funzionerà.
