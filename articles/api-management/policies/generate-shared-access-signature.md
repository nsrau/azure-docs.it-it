---
title: 'Esempio di criteri di Gestione API di Azure: generare una firma di accesso condiviso | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come generare una firma di accesso condiviso tramite espressioni e inoltrare la richiesta ad Archiviazione di Azure con il criterio rewrite-uri.
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
ms.openlocfilehash: 2c3adaa6f4e113f09e676583c2c35b5f1fbdb622
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062191"
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

