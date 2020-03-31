---
title: File di inclusione
description: File di inclusione
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080913"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Batch di Azure per ogni area per sottoscrizioneAzure Batch accounts per region per subscription | 1-3 |50 |
| Core dedicati per account Batch | 90-900 | Contattare il supporto tecnico |
| Core a bassa priorità per account Batch | 10-100 | Contattare il supporto tecnico |
| **[Processi attivi](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e pianificazioni processi per account batch (i processi**completati** non hanno limiti) | 100-300 | 1.000<sup>1</sup> |
| Pool per account Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1 : il</sup> nome del Per richiedere un aumento oltre questo limite, contattare il supporto di Azure.To request an increase beyond this limit, contact Azure Support.
