---
title: 'Criteri di gestione API di esempio: autorizzare la richiesta usando un provider di autorizzazioni esterno'
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
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076233"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizzare richieste usando un provider di autorizzazioni esterno

Questo articolo mostra un esempio di criteri di gestione API di Azure che illustra come proteggere l'accesso all'API usando un provider di autorizzazioni esterno incapsulando una logica di autenticazione/autorizzazione personalizzata. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-reference.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di restrizione di accesso](../api-management-access-restriction-policies.md)
+ [Esempi di criteri](../policy-reference.md)