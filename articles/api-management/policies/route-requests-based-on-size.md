---
title: 'Esempio di criteri di Gestione API di Azure: instradare la richiesta in base alle dimensioni del corpo | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come instradare le richieste in base alle dimensioni del corpo della richiesta.
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
ms.openlocfilehash: f8f282597004dc73d9fe0f49bf4a41e6a80fc37f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071997"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Instradare la richiesta in base alle dimensioni del corpo

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come instradare le richieste in base alle dimensioni del corpo della richiesta. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

