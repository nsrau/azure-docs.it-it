---
title: Criteri di gestione API di esempio-indirizzare la richiesta in base alle dimensioni del corpo del messaggio
titleSuffix: Azure API Management
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442420"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Instradare la richiesta in base alle dimensioni del corpo

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come instradare le richieste in base alle dimensioni del corpo della richiesta. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

