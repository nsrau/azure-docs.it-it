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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67080913"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Azure Batch per area per sottoscrizione | 1-3 |50 |
| Core dedicati per account Batch | 90-900 | Contatta il supporto tecnico |
| Core a bassa priorità per account Batch | 10-100 | Contatta il supporto tecnico |
| Processi **[attivi](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e pianificazioni di processi per account batch (i processi**completati** non hanno limiti) | 100-300 | 1\.000<sup>1</sup> |
| Pool per account Batch | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Per richiedere un aumento oltre questo limite, contattare il supporto tecnico di Azure.
