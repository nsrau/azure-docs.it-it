---
title: 'Esempio di criteri di Gestione API di Azure: autorizzare l''accesso con token OAuth Google | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come autorizzare l'accesso agli endpoint usando Google come provider di token OAuth.
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
ms.openlocfilehash: 3889c6fcaef0845c05e455ad173c6880ef053892
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizzare l'accesso con token OAuth Google

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come autorizzare l'accesso agli endpoint usando Google come provider di token OAuth. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

