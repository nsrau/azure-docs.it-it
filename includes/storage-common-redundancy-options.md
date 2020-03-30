---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157220"
---
Le opzioni di ridondanza per un account di archiviazione includono:Redundancy options for a storage account include:

* Archiviazione con ridondanza locale (LRS): una strategia di ridondanza semplice e a basso costo. I dati vengono copiati in modo sincrono tre volte all'interno dell'area primaria.
* Archiviazione con ridondanza di zona: ridondanza per scenari che richiedono disponibilità elevata. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria.
* Archiviazione con ridondanza geografica (GRS): ridondanza interregionale per la protezione contro le interruzioni regionali. I dati vengono copiati in modo sincrono tre volte nell'area primaria, quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS).
* Archiviazione con ridondanza geografica (G-RS) (anteprima): ridondanza per scenari che richiedono sia disponibilità elevata che durata massima. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria, quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza di zona geografica ad accesso in lettura (RA-G-RS).

Per altre informazioni sulle opzioni di ridondanza in Archiviazione di Azure, vedere [Ridondanza](../articles/storage/common/storage-redundancy.md)di Archiviazione di Azure.For more information about redundancy options in Azure Storage, see Azure Storage redundancy .
