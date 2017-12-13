---
title: 'Esempio di criteri di Gestione API di Azure: implementare il modello X-CSRF | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come implementare il modello X-CSRF usato da molte API. L'esempio riguarda specificamente il gateway SAP.
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
ms.openlocfilehash: a99ff25f7f8a3851936ba8d28e76eff98b1b0458
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="implement-x-csrf-pattern"></a>Implementare il modello X-CSRF

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come implementare il modello X-CSRF usato da molte API. L'esempio riguarda specificamente il gateway SAP. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere [Esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

