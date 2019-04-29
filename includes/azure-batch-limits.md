---
title: File di inclusione
description: File di inclusione
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b48dbfc33890df12209c3a242d812ad2103e07a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776380"
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
