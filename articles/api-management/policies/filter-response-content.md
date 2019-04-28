---
title: 'Esempio di criteri di gestione API di Azure: filtrare il contenuto della risposta | Microsoft Docs'
description: 'Esempio di criteri di gestione API di Azure: dimostra come filtrare gli elementi dati dal payload della risposta in base al prodotto associato alla richiesta.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 26829dfc04acdac2a25fe0d4fdc3e95e4d219057
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637064"
---
# <a name="filter-response-content"></a>Filtrare il contenuto della risposta

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come filtrare elementi dati dal payload della risposta in base al prodotto associato alla richiesta. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

