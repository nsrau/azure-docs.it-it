---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460513"
---
Prima di assegnare un ruolo di controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate stabiliscono che è sempre preferibile concedere solo l'ambito più ristretto possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse di Accodamento e BLOB di Azure, a partire dall'ambito più restrittivo:

- **Un singolo contenitore.** In questo ambito, un'assegnazione di ruolo si applica a tutti i BLOB nel contenitore, nonché alle proprietà e ai metadati del contenitore.
- **Una singola coda.** In questo ambito, un'assegnazione di ruolo si applica ai messaggi nella coda, nonché alle proprietà e ai metadati della coda.
- **L'account di archiviazione.** A questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori e ai relativi BLOB o a tutte le code e ai relativi messaggi.
- **Gruppo di risorse.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione nel gruppo di risorse.
- **Sottoscrizione.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione in tutti i gruppi di risorse nella sottoscrizione.

> [!IMPORTANT]
> Se la sottoscrizione include uno spazio dei nomi di Azure databricks, i ruoli che hanno come ambito la sottoscrizione non concedono l'accesso ai dati BLOB e di Accodamento. Definire l'ambito dei ruoli per il gruppo di risorse, l'account di archiviazione o il contenitore o la coda.     
