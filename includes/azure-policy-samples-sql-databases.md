---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226904"
---
### <a name="sql-databases"></a>DATABASE SQL

|  |  |
|---------|---------|
| [Allowed SQL DB SKUs](../articles/governance/policy/samples/allowed-sql-db-skus.md) (SKU del database SQL consentiti) | Richiede ai database SQL di usare uno SKU approvato. Si specifica una matrice di ID di SKU consentiti o una matrice di nomi di SKU consentiti. |
| [Controllare l'impostazione di rilevamento delle minacce a livello di database](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Controlla i criteri degli avvisi di sicurezza del database SQL se tali criteri non sono impostati sullo stato specificato. Si specifica un valore che indica se il rilevamento delle minacce è abilitato o disabilitato.  |
| [Controllare la crittografia del database SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Controlla se Transparent Data Encryption del database SQL non è abilitato. |
| [Controllare l'impostazione di controllo a livello di database SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Controlla le impostazioni di controllo del database SQL se tali impostazioni non corrispondono a un'impostazione specificata. Si specifica un valore che indica se le impostazioni di controllo devono essere abilitate o disabilitate.  |