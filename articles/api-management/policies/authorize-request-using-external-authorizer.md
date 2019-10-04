---
title: 'Esempio di criteri di gestione API di Azure: autorizzare la richiesta con un provider di autorizzazioni esterno | Microsoft Docs'
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
ms.openlocfilehash: 92836e0bfe43a41ad6547c68bc0b9a326528862c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074161"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizzare richieste usando un provider di autorizzazioni esterno

Questo articolo mostra un esempio di criteri di gestione API di Azure che illustra come proteggere l'accesso all'API usando un provider di autorizzazioni esterno incapsulando una logica di autenticazione/autorizzazione personalizzata. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di restrizione di accesso](../api-management-access-restriction-policies.md)
+ [Esempi di criteri](../policy-samples.md)
