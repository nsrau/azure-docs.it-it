---
title: Criteri di gestione API di esempio - Autorizzare la richiesta usando l'autorizzazione esternaSample API management policy - Authorize request using external authorizer
titleSuffix: Azure API Management
description: 'Esempio di criteri di gestione API di Azure: illustra come autorizzare le richieste usando un provider di autorizzazioni esterno che incapsula una logica di autenticazione/autorizzazione personalizzata o legacy.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442490"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizzare richieste usando un provider di autorizzazioni esterno

Questo articolo mostra un esempio di criteri di gestione API di Azure che illustra come proteggere l'accesso all'API usando un provider di autorizzazioni esterno incapsulando una logica di autenticazione/autorizzazione personalizzata. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di restrizione di accesso](../api-management-access-restriction-policies.md)
+ [Esempi di criteri](../policy-samples.md)
