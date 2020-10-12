---
title: 'Criteri di gestione API di esempio: impostare la durata della cache della risposta'
titleSuffix: Azure API Management
description: Esempio di criteri di Gestione API di Azure - Illustra come impostare la durata della cache della risposta usando il valore maxAge nell'intestazione Cache-Control inviata dal back-end.
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442385"
---
# <a name="set-response-cache-duration"></a>Impostare la durata della cache della risposta

Questo articolo descrive un esempio di criteri di Gestione API di Azure che spiega come impostare la durata della cache della risposta usando il valore maxAge nell'intestazione Cache-Control inviata dal back-end. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Policy

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

