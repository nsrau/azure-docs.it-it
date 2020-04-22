---
title: Procedure consigliate per il controllo degli accessi in base al ruolo di AzureBest practices for
description: Procedure consigliate per l'uso del controllo degli accessi in base al ruolo di Azure
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726774"
---
# <a name="best-practices-for-azure-rbac"></a>Procedure consigliate per il controllo degli accessi in base al ruolo di AzureBest practices for

Questo articolo descrive alcune procedure consigliate per l'uso del controllo degli accessi in base al ruolo di Azure (controllo degli accessi in base al ruolo di Azure). Queste procedure consigliate derivano dalla nostra esperienza con il controllo degli accessi in base al ruolo di Azure e dalle esperienze di clienti come te.

## <a name="only-grant-the-access-users-need"></a>Concedere solo l'accesso di cui gli utenti hanno bisogno

Usando il controllo degli accessi in base al ruolo di Azure, è possibile separare i compiti all'interno del team e concedere solo la quantità di accesso agli utenti necessari per eseguire il proprio lavoro. Invece di concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, è possibile consentire solo determinate azioni in un particolare ambito.

Quando si pianifica la strategia di controllo degli accessi, è consigliabile concedere agli utenti almeno il privilegio per completare la propria sessione di lavoro. Il diagramma seguente mostra uno schema consigliato per l'uso del controllo degli accessi in base al ruolo.

![Controllo degli accessi in base al ruolo e privilegi minimi](./media/best-practices/rbac-least-privilege.png)

Per informazioni su come aggiungere assegnazioni di ruolo, vedere [Aggiungere o rimuovere assegnazioni](role-assignments-portal.md)di ruolo .

## <a name="limit-the-number-of-subscription-owners"></a>Limitare il numero di proprietari di sottoscrizioni

È necessario disporre di un massimo di 3 proprietari di sottoscrizioni per ridurre il potenziale di violazione da parte di un proprietario compromesso. Questa raccomandazione può essere monitorata nel Centro sicurezza di Azure.This recommendation can be monitored in Azure Security Center. Per altre raccomandazioni relative all'identità e all'accesso nel Centro sicurezza, vedere Consigli sulla [sicurezza, ovvero una guida di riferimento](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Usare La gestione delle identità con privilegi di Azure ADUse Azure AD Privileged Identity Management

Per proteggere gli account con privilegi da attacchi informatici, è possibile usare Azure Active Directory Privileged Identity Management (PIM) per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità nel loro uso tramite report e avvisi. PIM consente di proteggere gli account con privilegi fornendo l'accesso con privilegi just-in-time alle risorse di Azure AD e Azure.PIM helps protect privileged accounts by providing just-in-time privileged access to Azure AD and Azure resources. L'accesso può essere limitato nel tempo dopo il quale i privilegi vengono revocati automaticamente. 

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md) (Che cos'è Azure AD Privileged Identity Management?).

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi relativi al controllo degli accessi in base](troubleshooting.md)