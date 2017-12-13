---
title: 'Esempio di criteri di Gestione API di Azure: generare una firma di accesso condiviso | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come generare una firma di accesso condiviso tramite espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri.
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
ms.openlocfilehash: e1f17f9f4e17a3eebb55e4ec1905aec19a2165a5
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
---
# <a name="generate-shared-access-signature"></a>Generare una firma di accesso condiviso

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come generare una [firma di accesso condiviso](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) tramite espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere [Esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

