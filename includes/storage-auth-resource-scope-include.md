---
title: includere il file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024894"
---
Prima di assegnare un ruolo di controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che deve avere l'entità di sicurezza. Le procedure consigliate stabiliscono che è sempre preferibile concedere solo l'ambito più ristretto possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse di Accodamento e BLOB di Azure, a partire dall'ambito più restrittivo:

- **Un singolo contenitore.** In questo ambito, un'assegnazione di ruolo si applica a tutti i BLOB nel contenitore, nonché alle proprietà e ai metadati del contenitore.
- **Una singola coda.** In questo ambito, un'assegnazione di ruolo si applica ai messaggi nella coda, nonché alle proprietà e ai metadati della coda.
- **L'account di archiviazione.** A questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori e ai relativi BLOB o a tutte le code e ai relativi messaggi.
- **Gruppo di risorse.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione nel gruppo di risorse.
- **Sottoscrizione.** In questo ambito, un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione in tutti i gruppi di risorse nella sottoscrizione.
