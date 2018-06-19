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
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664594"
---
### <a name="sql-servers"></a>Server SQL

|  |  |
|---------|---------|
| [Audit no Azure Active Directory administrator](../articles/azure-policy/scripts/audit-no-aad-admin.md) (Controlla se non è presente un amministratore di Azure Active Directory) | Controlla quando non vi è nessun amministratore di Azure Active Directory assegnato a SQL Server. |
| [Controllare l'impostazione di rilevamento delle minacce a livello di server](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Controlla i criteri degli avvisi di sicurezza del database SQL se tali criteri non sono impostati sullo stato specificato. Si specifica un valore che indica se il rilevamento delle minacce è abilitato o disabilitato.  |
| [Controllare l'impostazione di controllo di SQL Server](../articles/azure-policy/scripts/sql-server-audit.md) | Controlla SQL Server se sono attivate le impostazioni di controllo di SQL Server. |
| [Controllare l'impostazione di controllo a livello di SQL Server](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Controlla le impostazioni di controllo di SQL server se tali impostazioni non corrispondono a un'impostazione specificata. Si specifica un valore che indica se le impostazioni di controllo devono essere abilitate o disabilitate. |
| [Richiedere SQL Server versione 12.0](../articles/azure-policy/scripts/req-sql-12.md) | Richiede ai server SQL di usare la versione 12.0.  |