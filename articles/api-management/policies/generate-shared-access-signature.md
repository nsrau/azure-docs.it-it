---
title: Criteri di gestione API di esempio - Genera firma di accesso condivisoSample API management policy - Generate Shared Access Signature
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
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442469"
---
# <a name="generate-shared-access-signature"></a>Generare una firma di accesso condiviso

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come generare una [firma di accesso condiviso](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) tramite espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

