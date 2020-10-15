---
title: Criteri di gestione API di esempio-generare una firma di accesso condiviso
titleSuffix: Azure API Management
description: Esempio di criteri di Gestione API di Azure che illustra come generare una firma di accesso condiviso tramite espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri.
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
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078426"
---
# <a name="generate-shared-access-signature"></a>Generare una firma di accesso condiviso

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come generare una [firma di accesso condiviso](../../storage/common/storage-sas-overview.md) tramite espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-reference.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-reference.md)