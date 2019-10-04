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
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: dd99d9ed3eebe6ada60511b3f16c53b0d57a65d6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067818"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizzare l'accesso in base ad attestazioni JWT

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come autorizzare l'accesso a specifici metodi HTTP in un'API in base ad attestazioni JWT. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

