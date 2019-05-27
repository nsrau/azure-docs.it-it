---
title: File di inclusione
description: File di inclusione
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127552"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Azure Batch per area per sottoscrizione | 1-3 |50 |
| Core dedicati per account Batch | 10-100 | N/D<sup>1</sup> |
| Core a bassa priorità per account Batch | 10-100 | N/A<sup>2</sup> |
| I processi attivi e le pianificazioni dei processi<sup>3</sup> per account Batch | 100-300 | 1,000<sup>4</sup> |
| Pool per account Batch | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>il numero di core dedicati per account Batch può essere aumentato, ma il numero massimo è specificato. Per discutere le opzioni di aumento, contattare il supporto di Azure.

<sup>2</sup>il numero di core a bassa priorità per account Batch può essere aumentato, ma il numero massimo è specificato. Per discutere le opzioni di aumento, contattare il supporto di Azure.

<sup>3</sup>i processi completati e le pianificazioni dei processi non sono limitate.

<sup>4</sup>per richiedere un aumento oltre questo limite, contattare il supporto tecnico di Azure.
