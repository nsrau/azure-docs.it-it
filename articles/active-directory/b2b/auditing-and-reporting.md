---
title: Controllo e creazione di report per un utente di Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: Le proprietà di un utente guest sono configurabili in Collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: a31f9a5a0f613d6c70e8c95e584d8caca87e93be
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434156"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Controllo e creazione di report per un utente di Collaborazione B2B
Con gli utenti guest, sono disponibili funzionalità di controllo analoghe a quelle degli utenti membri. 

## <a name="access-reviews"></a>Verifiche di accesso
È possibile usare le verifiche di accesso per verificare periodicamente se gli utenti guest hanno ancora necessità di accedere alle risorse. La funzionalità **Verifiche di accesso** è disponibile in **Azure Active Directory** in **Gestisci** > **Relazioni aziendali**. (È anche possibile cercare le "verifiche di accesso" da **Tutti i servizi** nel portale di Azure.) Per informazioni su come usare le verifiche di accesso, vedere [Gestire l'accesso guest con le verifiche di accesso di Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Log di controllo

I log di controllo di Azure AD forniscono i record delle attività di sistema e utente, incluse le attività avviate da parte degli utenti guest. Per accedere ai log di controllo in **Azure Active Directory**, in **Monitoraggio**, selezionare **Log di controllo**. Di seguito è riportato un esempio di cronologia di inviti e riscatti dell'invitato Sam Oogle:

![log di controllo](./media/auditing-and-reporting/audit-log.png)

È possibile selezionare ogni evento per visualizzare i dettagli. È ad esempio possibile esaminare i dettagli relativi all'accettazione.

![dettagli dell'attività](./media/auditing-and-reporting/activity-details.png)

È anche possibile esportare i log da Azure AD e usare lo strumento di creazione di report per ottenere report personalizzati.

### <a name="next-steps"></a>Passaggi successivi

- [Proprietà dell'utente di Collaborazione B2B](user-properties.md)

