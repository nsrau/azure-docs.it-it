---
title: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518682"
---
Prima di assegnare un ruolo di controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate stabiliscono che è sempre preferibile concedere solo l'ambito più ristretto possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse di Accodamento e BLOB di Azure, a partire dall'ambito più restrittivo:

- **Un singolo contenitore.** In questo ambito, un'assegnazione di ruolo si applica a tutti i BLOB nel contenitore, nonché alle proprietà e ai metadati del contenitore.
- **Una singola coda.** In questo ambito, un'assegnazione di ruolo si applica ai messaggi nella coda, nonché alle proprietà e ai metadati della coda.
- **L'account di archiviazione.** A questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori e ai relativi BLOB o a tutte le code e ai relativi messaggi.
- **Gruppo di risorse.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione nel gruppo di risorse.
- **Sottoscrizione.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione in tutti i gruppi di risorse nella sottoscrizione.
- **Un gruppo di gestione.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione in tutti i gruppi di risorse in tutte le sottoscrizioni del gruppo di gestione.

Per altre informazioni sulle assegnazioni di ruolo e sull'ambito RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../articles/role-based-access-control/overview.md).
