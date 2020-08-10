---
title: includere file
description: includere file
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: e22594a50a9c0d814ef7d66443f8253f5832cb1d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038487"
---
Per Criteri di Azure è previsto un numero massimo per ogni tipo di oggetto. La voce _Ambito_ indica la sottoscrizione o il [gruppo di gestione](../articles/governance/management-groups/overview.md).

| Where | Cosa | Numero massimo |
|---|---|---|
| Scope | Definizioni dei criteri | 500 |
| Scope | Definizioni di iniziativa | 200 |
| Tenant | Definizioni di iniziativa | 2\.500 |
| Scope | Assegnazioni di criteri o iniziative | 200 |
| Definizione di criteri | Parametri | 20 |
| Definizione di iniziativa | Criteri | 1000 |
| Definizione di iniziativa | Parametri | 100 |
| Assegnazioni di criteri o iniziative | Esclusioni (notScopes) | 400 |
| Regola dei criteri | Istruzioni condizionali annidate | 512 |
| Attività di correzione | Risorse | 1000 |
