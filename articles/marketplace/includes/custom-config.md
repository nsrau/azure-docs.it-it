---
title: File di inclusione
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284324"
---
Se è necessaria una configurazione aggiuntiva, usare un'attività pianificata che viene eseguita all'avvio per apportare le modifiche finali alla macchina virtuale dopo la distribuzione. Tenere inoltre in considerazione quanto segue:

- Per le attività da eseguire una sola volta, l'attività dovrebbe eliminarsi automaticamente dopo il completamento.
- Le configurazioni non devono basarsi su unità diverse da C o D, perché solo queste due unità sono sempre disponibili (l'unità C è il disco del sistema operativo e l'unità D è il disco locale temporaneo).

Per altre informazioni sulle personalizzazioni di Linux, vedere [Estensioni della macchina virtuale e funzionalità per Linux](../../virtual-machines/extensions/features-linux.md).