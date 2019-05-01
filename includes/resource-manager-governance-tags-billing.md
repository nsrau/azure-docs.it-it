---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: e7fcb72cecbfad2de80b844ed5281267d5e4c0c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "64732751"
---
Dopo l'applicazione dei tag alle risorse è possibile visualizzare i costi per le risorse con i tag specificati. La visualizzazione dell'utilizzo più recente nell'analisi dei costi richiede tempo, quindi è possibile che i costi non siano ancora visualizzati. Quando i costi sono disponibili, è possibile visualizzare i costi per le risorse nei gruppi di risorse della sottoscrizione. Per visualizzare i costi, gli utenti devono avere l'[accesso a livello di sottoscrizione alle informazioni di fatturazione](../articles/billing/billing-manage-access.md).

Per visualizzare i costi in base al tag nel portale, selezionare la sottoscrizione e quindi **Analisi dei costi**.

![Analisi dei costi](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Filtrare quindi in base al valore del tag e selezionare **Applica**.

![Visualizzare il costo in base al tag](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

È anche possibile usare le [API di fatturazione di Azure](../articles/billing/billing-usage-rate-card-overview.md) per visualizzare i costi a livello di programmazione.
