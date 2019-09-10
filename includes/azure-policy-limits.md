---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67133428"
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
