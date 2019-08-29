---
title: 'Esempio di criteri di Gestione API di Azure: inviare errori a Stackify per la registrazione | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione.
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
ms.openlocfilehash: 82aab34a9815f080fe8abb8c1d8b6de66866806e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067587"
---
# <a name="send-errors-to-stackify-for-logging"></a>Inviare errori a Stackify per la registrazione

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

