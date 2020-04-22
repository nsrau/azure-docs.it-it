---
title: File di inclusione
description: File di inclusione
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81735288"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Batch di Azure per ogni area per sottoscrizioneAzure Batch accounts per region per subscription | 1-3 |50 |
| Core dedicati per account Batch | 90-900 | Contattare il supporto tecnico |
| Core a bassa priorità per account Batch | 10-100 | Contattare il supporto tecnico |
| **[Processi attivi](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e pianificazioni processi per account batch (i processi**completati** non hanno limiti) | 100-300 | 1.000<sup>1</sup> |
| Pool per account Batch | 20-100 | 500<sup>1</sup> |

<sup>1 : il</sup> nome del Per richiedere un aumento oltre questo limite, contattare il supporto di Azure.To request an increase beyond this limit, contact Azure Support.

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Per aiutarci a gestire meglio la capacità durante la pandemia sanitaria globale, le quote di base predefinite per i nuovi account Batch in alcune aree e per alcuni tipi di sottoscrizione sono state ridotte dall'intervallo di valori precedente, in alcuni casi a zero core. Quando si crea un nuovo account Batch, [controllare la quota principale](../articles/batch/batch-quota-limit.md#view-batch-quotas) e richiedere un aumento della quota [principale,](../articles/batch/batch-quota-limit.md#increase-a-quota)se necessario. 
