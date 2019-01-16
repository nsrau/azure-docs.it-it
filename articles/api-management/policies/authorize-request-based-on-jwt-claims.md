---
title: "Esempio di criteri di Gestione API di Azure: autorizzare l'accesso in base ad attestazioni JWT | Microsoft Docs"
description: Esempio di criteri di Gestione API di Azure che illustra come autorizzare l'accesso a specifici metodi HTTP in un'API in base ad attestazioni JWT.
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
ms.openlocfilehash: d656cf7c7bed1d40bbde654f9c2484efcc5df25d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157922"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizzare l'accesso in base ad attestazioni JWT

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come autorizzare l'accesso a specifici metodi HTTP in un'API in base ad attestazioni JWT. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

