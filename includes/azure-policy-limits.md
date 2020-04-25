---
title: File di inclusione
description: File di inclusione
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 04e607296478520298a2febab61a7edac911a59c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82132147"
---
Per Criteri di Azure è previsto un numero massimo per ogni tipo di oggetto. La voce _Ambito_ indica la sottoscrizione o il [gruppo di gestione](../articles/governance/management-groups/overview.md).

| Where | Cosa | Numero massimo |
|---|---|---|
| Scope | Definizioni dei criteri | 500 |
| Scope | Definizioni di iniziativa | 100 |
| Tenant | Definizioni di iniziativa | 1\.000 |
| Scope | Assegnazioni di criteri o iniziative | 100 |
| Definizione di criteri | Parametri | 20 |
| Definizione di iniziativa | Criteri | 100 |
| Definizione di iniziativa | Parametri | 100 |
| Assegnazioni di criteri o iniziative | Esclusioni (notScopes) | 400 |
| Regola dei criteri | Istruzioni condizionali annidate | 512 |
| Attività di correzione | Risorse | 500 |
