---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964602"
---
È previsto un numero massimo per ogni tipo di oggetto per Criteri di Azure. La voce _Ambito_ indica la sottoscrizione o il [gruppo di gestione](../articles/governance/management-groups/overview.md).

| Where | Cosa | Numero massimo |
|---|---|---|
| Scope | Definizioni dei criteri | 250 |
| Scope | Definizioni di iniziativa | 100 |
| Tenant | Definizioni di iniziativa | 1000 |
| Scope | Assegnazioni di criteri/iniziative | 100 |
| Definizione di criteri | Parametri | 20 |
| Definizione di iniziativa | Criteri | 100 |
| Definizione di iniziativa | Parametri | 100 |
| Assegnazioni di criteri/iniziative | Esclusioni (notScopes) | 250 |
| Regola dei criteri | Istruzioni condizionali annidate | 512 |
