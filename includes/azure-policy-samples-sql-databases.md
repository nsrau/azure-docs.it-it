---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664606"
---
### <a name="sql-databases"></a>DATABASE SQL

|  |  |
|---------|---------|
| [Allowed SQL DB SKUs](../articles/azure-policy/scripts/allowed-sql-db-skus.md) (SKU del database SQL consentiti) | Richiede ai database SQL di usare uno SKU approvato. Si specifica una matrice di ID di SKU consentiti o una matrice di nomi di SKU consentiti. |
| [Controllare l'impostazione di rilevamento delle minacce a livello di database](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Controlla i criteri degli avvisi di sicurezza del database SQL se tali criteri non sono impostati sullo stato specificato. Si specifica un valore che indica se il rilevamento delle minacce è abilitato o disabilitato.  |
| [Controllare la crittografia del database SQL](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Controlla se Transparent Data Encryption del database SQL non è abilitato. |
| [Controllare l'impostazione di controllo a livello di database SQL](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Controlla le impostazioni di controllo del database SQL se tali impostazioni non corrispondono a un'impostazione specificata. Si specifica un valore che indica se le impostazioni di controllo devono essere abilitate o disabilitate.  |
| [Audit transparent data encryption status](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) (Controllare lo stato di Transparent Data Encryption) | Controlla Transparent Data Encryption del database SQL se non è abilitata.  |