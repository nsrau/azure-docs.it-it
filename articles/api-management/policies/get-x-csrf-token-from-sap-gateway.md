---
title: 'Esempio di criteri di Gestione API di Azure: implementare il modello X-CSRF | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come implementare il modello X-CSRF usato da molte API. L'esempio riguarda specificamente il gateway SAP.
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
ms.openlocfilehash: 71e76b234b614f5935775d5c387c9897e1dcb968
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937673"
---
# <a name="implement-x-csrf-pattern"></a>Implementare il modello X-CSRF

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come implementare il modello X-CSRF usato da molte API. L'esempio riguarda specificamente il gateway SAP. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

