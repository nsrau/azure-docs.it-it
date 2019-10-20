---
title: Risolvere un problema con Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Informazioni su come risolvere gli errori di sistema con i ruoli in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: f54382d652c3fc59b8ff462d41ec8c0fbdbe8498
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596700"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Risolvere un problema con Privileged Identity Management

Si è riscontrato un problema con Privileged Identity Management (PIM) in Azure Active Directory (Azure AD)? le informazioni seguenti possono aiutare a ripristinare il corretto funzionamento.

## <a name="access-to-azure-resources-denied"></a>Accesso alle risorse di Azure negato

### <a name="problem"></a>Problema

Un proprietario attivo o un amministratore di accesso utente per una risorsa di Azure può visualizzare la risorsa all'interno Privileged Identity Management ma non può eseguire azioni come la creazione di un'assegnazione idonea o la visualizzazione di un elenco di assegnazioni di ruolo dalla risorsa pagina panoramica. Ognuna di queste azioni comporta un errore di autorizzazione.

### <a name="cause"></a>Causa

Questo problema può verificarsi quando il ruolo amministratore accesso utenti per l'entità servizio PIM è stato accidentalmente rimosso dalla sottoscrizione. Affinché il servizio Privileged Identity Management sia in grado di accedere alle risorse di Azure, è necessario che all'entità servizio MS-PIM sia sempre assegnato il [ruolo amministratore accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) sulla sottoscrizione di Azure.

### <a name="resolution"></a>Risoluzione

Assegnare il ruolo amministratore accesso utenti al nome dell'entità servizio (MS-PIM) Privileged Identity Management a livello di sottoscrizione. Questa assegnazione dovrebbe consentire al servizio Privileged Identity Management di accedere alle risorse di Azure. Il ruolo può essere assegnato a livello di gruppo di gestione o a livello di sottoscrizione, a seconda dei requisiti. Per ulteriori informazioni sulle entità servizio, vedere [assegnare un'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Passaggi successivi

- [Requisiti di licenza da usare Privileged Identity Management](subscription-requirements.md)
- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuisci Privileged Identity Management](pim-deployment-plan.md)
