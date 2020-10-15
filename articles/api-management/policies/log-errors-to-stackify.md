---
title: Criteri di gestione API di esempio-inviare errori a Stackify per la registrazione
titleSuffix: Azure API Management
description: Esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072068"
---
# <a name="send-errors-to-stackify-for-logging"></a>Inviare errori a Stackify per la registrazione

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-reference.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-reference.md)