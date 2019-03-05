---
title: Azure Stack convalida le procedure consigliate. | Microsoft Docs
description: Questo articolo contiene le procedure consigliate per la convalida come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: b61c4a22d0c5343d41e35f2bbd66829b2b9e8d48
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336973"
---
# <a name="best-practices-for-validation-as-a-service"></a>Le procedure consigliate per la convalida come servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Questo articolo illustra le procedure consigliate per la gestione delle risorse nella convalida come servizio (VaaS). Per una panoramica delle risorse VaaS, vedere [convalida come un concetti chiave di servizio](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Gestione della soluzione

### <a name="naming-convention-for-vaas-solutions"></a>Convenzione di denominazione per le soluzioni VaaS

Usare una convenzione di denominazione coerente per tutte le soluzioni registrate in VaaS. Il nome della soluzione, ad esempio, può essere costruito dalle proprietà hardware sotto come indicato di seguito:

|Nome prodotto | Elemento Hardware univoco 1 | Elemento Hardware univoco 2 | Nome soluzione
|---|---|---|---|
La mia soluzione XYZ |  Tutti i Flash | Il commutatore X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Questa opzione per creare una nuova soluzione VaaS

Usare la stessa soluzione VaaS durante l'esecuzione dei flussi di lavoro per lo stesso hardware SKU. Una nuova soluzione VaaS deve essere creata solo quando viene apportata una modifica SKU all'hardware.

## <a name="workflow-management"></a>Gestione del flusso di lavoro

### <a name="naming-convention-for-vaas-workflows"></a>Convenzione di denominazione per i flussi di lavoro VaaS

Usare una convenzione di denominazione coerente per tutte le esecuzioni del flusso di lavoro VaaS. Ad esempio, il costrutto di un flusso di lavoro denominare dalle proprietà di compilazione seguente come indicato di seguito:

|(Maggiore) numero di build | Data | Dimensioni di soluzione | Nome flusso di lavoro
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [convalida come un concetti chiave di servizio](azure-stack-vaas-key-concepts.md)
