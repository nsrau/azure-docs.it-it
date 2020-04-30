---
title: Procedure consigliate per RBAC di Azure
description: Procedure consigliate per l'uso del controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726774"
---
# <a name="best-practices-for-azure-rbac"></a>Procedure consigliate per RBAC di Azure

Questo articolo descrive alcune procedure consigliate per l'uso del controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Queste procedure consigliate derivano dall'esperienza con il controllo degli accessi in base al ruolo di Azure e dalle esperienze dei clienti.

## <a name="only-grant-the-access-users-need"></a>Concedi solo agli utenti di accesso necessità

Con il controllo degli accessi in base al ruolo di Azure, è possibile separare i compiti all'interno del team e concedere solo la quantità di accesso agli utenti necessari per svolgere i propri processi. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni in un particolare ambito.

Quando si pianifica la strategia di controllo degli accessi, è consigliabile concedere agli utenti almeno il privilegio per completare la propria sessione di lavoro. Il diagramma seguente mostra uno schema consigliato per l'uso del controllo degli accessi in base al ruolo.

![Controllo degli accessi in base al ruolo e privilegi minimi](./media/best-practices/rbac-least-privilege.png)

Per informazioni su come aggiungere assegnazioni di ruolo, vedere [aggiungere o rimuovere assegnazioni di ruolo](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Limitare il numero di proprietari di sottoscrizioni

È necessario disporre di un massimo di 3 proprietari di sottoscrizioni per ridurre il rischio di violazione da parte di un proprietario compromesso. Questa raccomandazione può essere monitorata nel centro sicurezza di Azure. Per altre indicazioni relative a identità e accesso nel centro sicurezza, vedere [raccomandazioni sulla sicurezza: Guida di riferimento](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>USA Azure AD Privileged Identity Management

Per proteggere gli account con privilegi da attacchi informatici, è possibile usare Azure Active Directory Privileged Identity Management (PIM) per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità nel loro uso tramite report e avvisi. PIM aiuta a proteggere gli account con privilegi offrendo un accesso con privilegi JIT per Azure AD e risorse di Azure. L'accesso può essere associato al tempo trascorso il quale i privilegi vengono revocati automaticamente. 

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi relativi a RBAC di Azure](troubleshooting.md)