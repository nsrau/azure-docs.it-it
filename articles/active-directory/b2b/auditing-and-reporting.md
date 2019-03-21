---
title: Il controllo e creazione di report un utente di collaborazione B2B - Azure Active Directory | Microsoft Docs
description: Le proprietà di un utente guest sono configurabili in Collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5763a7e5f122702ddaf86246fbfbd18326878146
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294196"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Controllo e creazione di report per un utente di Collaborazione B2B
Con gli utenti guest, sono disponibili funzionalità di controllo analoghe a quelle degli utenti membri. 

## <a name="access-reviews"></a>Verifiche di accesso
È possibile usare le verifiche di accesso per verificare periodicamente se gli utenti guest hanno ancora necessità di accedere alle risorse. La funzionalità **Verifiche di accesso** è disponibile in **Azure Active Directory** in **Gestisci** > **Relazioni aziendali**. (È anche possibile cercare le "verifiche di accesso" da **Tutti i servizi** nel portale di Azure.) Per informazioni su come usare le verifiche di accesso, vedere [Gestire l'accesso guest con le verifiche di accesso di Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema e utente, incluse le attività avviate da parte degli utenti guest. Per accedere ai log di controllo in **Azure Active Directory**, in **Monitoraggio**, selezionare **Log di controllo**. Di seguito è riportato un esempio di cronologia di inviti e riscatti dell'invitato Sam Oogle:

![Screenshot che mostra e un esempio di controllo Registra output](./media/auditing-and-reporting/audit-log.png)

È possibile selezionare ogni evento per visualizzare i dettagli. È ad esempio possibile esaminare i dettagli relativi all'accettazione.

![Screenshot che mostra e un esempio di output di dettagli attività](./media/auditing-and-reporting/activity-details.png)

È anche possibile esportare i log da Azure AD e usare lo strumento di creazione di report per ottenere report personalizzati.

### <a name="next-steps"></a>Passaggi successivi

- [Proprietà dell'utente di Collaborazione B2B](user-properties.md)

