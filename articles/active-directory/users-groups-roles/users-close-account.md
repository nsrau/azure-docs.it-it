---
title: Chiudere l'account aziendale o dell'istituto di istruzione in una directory di Azure AD non gestitaClose work or school account in an unmanaged Azure AD directory
description: Come chiudere l'account aziendale o dell'istituto di istruzione in Azure Active Directory non gestito.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815727"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Chiudere l'account aziendale o dell'istituto di istruzione in una directory non gestita

Se si è un utente in un'organizzazione di Azure Active Directory (Azure AD) non gestita e non è più necessario usare le app di tale organizzazione o mantenere qualsiasi associazione con essa, è possibile chiudere l'account in qualsiasi momento. Una directory non gestita non dispone di un amministratore globale. Gli utenti in una directory non gestita possono chiudere i propri account da soli, senza dover contattare un amministratore.

Gli utenti in una directory non gestita vengono spesso creati durante l'iscrizione self-service. Un esempio potrebbe essere un information worker in un'organizzazione che si iscrive a un servizio gratuito. Per altre informazioni sull'iscrizione self-service, vedere [Che cos'è l'iscrizione self-service per Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter chiudere l'account, devi confermare i seguenti elementi:

* Assicurarsi di essere un utente di una directory di Azure AD non gestita. Non è possibile chiudere l'account se si appartiene a una directory gestita. Se si appartiene a una directory gestita e si desidera chiudere l'account, è necessario contattare l'amministratore. Per informazioni su come determinare se si appartiene a una directory non gestita, vedere [Eliminare l'utente dal tenant non gestito](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Salvare tutti i dati che si desidera conservare. Per informazioni su come inviare una richiesta di esportazione, vedere [Accesso ed esportazione dei log generati dal sistema per i tenant non gestiti](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Chiudere il tuo account è irreversibile. Quando chiudi il tuo account, tutti i dati personali verranno rimossi. Non avrai più accesso al tuo account e ai tuoi dati associati al tuo account.

## <a name="close-your-account"></a>Chiudere l'account

Per chiudere un account aziendale o dell'istituto di istruzione non gestito, attenersi alla seguente procedura:

1. Accedi per [chiudere il tuo account](https://go.microsoft.com/fwlink/?linkid=873123)utilizzando l'account che vuoi chiudere.

1. In **Richieste di dati personali**selezionare Chiudi **account**.

    ![Le mie richieste di dati - Chiudi account](./media/users-close-account/close-account.png)

1. Esaminare il messaggio di conferma e quindi selezionare **Sì**.

    ![Le mie richieste di dati - Conferma chiusura](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è l'iscrizione self-service per Azure Active Directory?](directory-self-service-signup.md)
- [Eliminare l'utente dal tenant non gestito](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Accesso ed esportazione dei log generati dal sistema per i tenant non gestiti](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
