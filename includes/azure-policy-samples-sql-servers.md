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
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590945"
---
### <a name="sql-servers"></a>Server SQL

|  |  |
|---------|---------|
| [Audit no Azure Active Directory administrator](../articles/governance/policy/samples/audit-no-aad-admin.md) (Controlla se non è presente un amministratore di Azure Active Directory) | Controlla quando non vi è nessun amministratore di Azure Active Directory assegnato a SQL Server. |
| [Controllare l'impostazione di rilevamento delle minacce a livello di server](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Controlla i criteri degli avvisi di sicurezza del database SQL se tali criteri non sono impostati sullo stato specificato. Si specifica un valore che indica se il rilevamento delle minacce è abilitato o disabilitato.  |
| [Controllare l'impostazione di controllo di SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Controlla SQL Server se sono attivate le impostazioni di controllo di SQL Server. |
| [Controllare l'impostazione di controllo a livello di SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Controlla le impostazioni di controllo di SQL server se tali impostazioni non corrispondono a un'impostazione specificata. Si specifica un valore che indica se le impostazioni di controllo devono essere abilitate o disabilitate. |