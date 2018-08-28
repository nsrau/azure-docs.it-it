---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/16/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 0e5d1214a8e1af8299cb40d1a3b31ff6eafc8c5c
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "40185449"
---
È previsto un numero massimo per ogni tipo di oggetto per Criteri di Azure. La voce _Ambito_ indica la sottoscrizione o il [gruppo di gestione](../articles/azure-resource-manager/management-groups-overview.md).

| Where | Cosa | Numero massimo |
|---|---|---|
| Scope | Definizioni dei criteri | 250 |
| Scope | Definizioni di iniziativa | 100 |
| Tenant | Definizioni di iniziativa | 1000 |
| Scope | Assegnazioni di criteri/iniziative | 100 |
| Definizione di criteri | Parametri | 20 |
| Definizione di iniziativa | Criteri | 100 |
| Definizione di iniziativa | Parametri | 100 |
| Assegnazioni di criteri/iniziative | Esclusioni (notScopes) | 100 |
| Regola dei criteri | Istruzioni condizionali annidate | 512 |
