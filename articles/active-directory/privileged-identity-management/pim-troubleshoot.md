---
title: Risolvere un problema con Gestione identità privilegiate - Azure Active Directory Documenti Microsoft
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
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299670"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Risolvere un problema con Gestione identità privilegiate

Si è verificato un problema con Privileged Identity Management (PIM) in Azure Active Directory (Azure AD)? le informazioni seguenti possono aiutare a ripristinare il corretto funzionamento.

## <a name="access-to-azure-resources-denied"></a>Accesso alle risorse di Azure negatoAccess to Azure resources denied

### <a name="problem"></a>Problema

In qualità di proprietario attivo o amministratore dell'accesso utente per una risorsa di Azure, è possibile visualizzare la risorsa all'interno di Gestione identità con privilegi, ma non è possibile eseguire azioni, ad esempio l'assegnazione di un'assegnazione idonea o la visualizzazione di un elenco di assegnazioni di ruolo dalla risorsaAs active owner or user access administrator for an Azure resource, you are able to see your resource inside Privileged Identity Management but you can't perform any actions such as making an eligible assignment or viewing a list of role assignments from the resource pagina di panoramica. Ognuna di queste azioni genera un errore di autorizzazione.

### <a name="cause"></a>Causa

Questo problema può verificarsi quando il ruolo di amministratore di accesso utente per l'entità servizio PIM è stato accidentalmente rimosso dalla sottoscrizione. Affinché il servizio Gestione identità con privilegi sia in grado di accedere alle risorse di Azure, all'entità servizio MS-PIM deve sempre essere assegnato il ruolo di amministratore di accesso utente tramite la sottoscrizione di Azure.For the Privileged Identity Management service to be able to access Azure resources, the MS-PIM service principal should always have assigned the [User Access Administrator role](../../role-based-access-control/built-in-roles.md#user-access-administrator) over the Azure subscription.

### <a name="resolution"></a>Risoluzione

Assegnare il ruolo di amministratore di accesso utente al nome dell'entità servizio Gestione identità con privilegi (MS-PIM) a livello di sottoscrizione. Questa assegnazione deve consentire al servizio Gestione identità con privilegi di accedere alle risorse di Azure.This assignment should allow the Privileged identity Management service to access the Azure resources. Il ruolo può essere assegnato a livello di gruppo di gestione o a livello di sottoscrizione, a seconda dei requisiti. Per altre informazioni sulle entità servizio, vedere [Assegnare un'applicazione a un ruolo.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)

## <a name="next-steps"></a>Passaggi successivi

- [Requisiti di licenza per usare Privileged Identity Management](subscription-requirements.md)
- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuire Privileged Identity Management](pim-deployment-plan.md)
