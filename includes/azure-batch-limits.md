---
title: includere file
description: includere file
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 54a789aba793d9a6b44d5fe9a1e0f42023002b5a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025583"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Azure Batch per area per sottoscrizione | 1-3 |50 |
| Core dedicati per account Batch | 90-900 | Contattare il supporto tecnico |
| Core a bassa priorità per account Batch | 10-100 | Contattare il supporto tecnico |
| Processi **[attivi](/rest/api/batchservice/job/get#jobstate)** e pianificazioni di processi per ogni account Batch (i **processi** completati non hanno limiti) | 100-300 | 1\.000<sup>1</sup> |
| Pool per account Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Per richiedere un aumento oltre questo limite, contattare il supporto di Azure.

> [!IMPORTANT]
> La modalità di richiesta e gestione della quota dedicata verranno cambiate.  Attualmente il valore applicato è il numero totale di vCPU dedicate, ma presto verrà applicata una quota dedicata per ogni serie di VM. La quota con priorità bassa continuerà a essere applicata in base al limite totale; non verrà applicata in base a serie di VM.

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Per una migliore gestione della capacità durante la pandemia sanitaria, in alcune aree e per alcuni tipi di sottoscrizione le quote predefinite di core per i nuovi account Batch sono state ridotte rispetto all'intervallo di valori indicato sopra, in alcuni casi fino a zero core. Quando si crea un nuovo account Batch, [controllare la quota di core](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [richiedere un aumento](../articles/batch/batch-quota-limit.md#increase-a-quota), se necessario. In alternativa, provare a riutilizzare gli account Batch che hanno già una quota sufficiente.