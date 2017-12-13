---
title: 'Esempio di criteri di Gestione API di Azure: filtrare il contenuto della risposta | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come filtrare elementi dati dal payload della risposta in base al prodotto associato alla richiesta.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 2acaad9e97f18cca22893c8c41ead4533ac5ae85
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="filter-response-content"></a>Filtrare il contenuto della risposta

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come filtrare elementi dati dal payload della risposta in base al prodotto associato alla richiesta. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere [Esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

