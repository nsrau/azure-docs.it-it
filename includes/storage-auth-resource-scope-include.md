---
title: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534134"
---
Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che dovrà avere. In base alle procedure consigliate, è sempre preferibile concedere solo l'ambito più restrittivo possibile.

L'elenco seguente descrive i livelli in cui è possibile definire l'ambito di accesso alle risorse BLOB e code di Azure, a partire dall'ambito più restrittivo:

- **Un singolo contenitore.** In questo ambito un'assegnazione di ruolo si applica a tutti i BLOB nel contenitore, nonché alle proprietà e ai metadati del contenitore.
- **Una singola coda.** In questo ambito un'assegnazione di ruolo si applica a tutti i messaggi nella coda, nonché alle proprietà e ai metadati della coda.
- **L'account di archiviazione.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori e ai relativi BLOB oppure a tutte le code e ai relativi messaggi.
- **Il gruppo di risorse.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione del gruppo di risorse.
- **La sottoscrizione.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione di tutti i gruppi di risorse della sottoscrizione.
- **Un gruppo di gestione.** In questo ambito un'assegnazione di ruolo si applica a tutti i contenitori o le code in tutti gli account di archiviazione di tutti i gruppi di risorse di tutte le sottoscrizioni nel gruppo di risorse.

Per altre informazioni sulle assegnazioni di ruoli e l'ambito, vedere [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../articles/role-based-access-control/overview.md)
