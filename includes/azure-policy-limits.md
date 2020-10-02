---
title: includere file
description: includere file
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91605787"
---
Per Criteri di Azure è previsto un numero massimo per ogni tipo di oggetto. Per le definizioni la voce _Ambito_ indica la sottoscrizione o il [gruppo di gestione](../articles/governance/management-groups/overview.md).
Per le assegnazioni e le esenzioni la voce _Ambito_ indica la sottoscrizione, il gruppo di risorse, una singola risorsa o il [gruppo di gestione](../articles/governance/management-groups/overview.md).

| Where | Cosa | Numero massimo |
|---|---|---|
| Scope | Definizioni dei criteri | 500 |
| Scope | Definizioni di iniziativa | 200 |
| Tenant | Definizioni di iniziativa | 2\.500 |
| Scope | Assegnazioni di criteri o iniziative | 200 |
| Ambito | Esenzioni | 1000 |
| Definizione di criteri | Parametri | 20 |
| Definizione di iniziativa | Criteri | 1000 |
| Definizione di iniziativa | Parametri | 100 |
| Assegnazioni di criteri o iniziative | Esclusioni (notScopes) | 400 |
| Regola dei criteri | Istruzioni condizionali annidate | 512 |
| Attività di correzione | Risorse | 500 |
