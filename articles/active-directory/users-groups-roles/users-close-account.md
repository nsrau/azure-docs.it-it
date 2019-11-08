---
title: Chiudere l'account aziendale o dell'Istituto di istruzione in una directory di Azure AD non gestita
description: Come chiudere l'account aziendale o dell'Istituto di istruzione in un Azure Active Directory non gestito.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815727"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Chiudere l'account aziendale o dell'Istituto di istruzione in una directory non gestita

Se si è un utente in un'organizzazione Azure Active Directory non gestita (Azure AD) e non è più necessario usare le app di tale organizzazione o gestire qualsiasi associazione, è possibile chiudere l'account in qualsiasi momento. Una directory non gestita non dispone di un amministratore globale. Gli utenti di una directory non gestita possono chiudere gli account in modo autonomo, senza dover contattare un amministratore.

Gli utenti in una directory non gestita vengono spesso creati durante l'iscrizione self-service. Un esempio potrebbe essere un Information Worker in un'organizzazione che si iscrive a un servizio gratuito. Per ulteriori informazioni sull'iscrizione self-service, vedere informazioni sull' [iscrizione self-service per Azure Active Directory](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter chiudere l'account, è necessario verificare gli elementi seguenti:

* Assicurarsi di essere un utente di una directory di Azure AD non gestita. Non è possibile chiudere l'account se si appartiene a una directory gestita. Se si appartiene a una directory gestita e si vuole chiudere l'account, è necessario contattare l'amministratore. Per informazioni su come determinare se si appartiene a una directory non gestita, vedere [eliminare l'utente dal tenant non gestito](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Salvare i dati che si desidera conservare. Per informazioni su come inviare una richiesta di esportazione, vedere [accesso e esportazione dei log generati dal sistema per i tenant non gestiti](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> La chiusura dell'account è irreversibile. Quando si chiude l'account, tutti i dati personali verranno rimossi. Non sarà più possibile accedere all'account e ai dati associati all'account.

## <a name="close-your-account"></a>Chiudi l'account

Per chiudere un account aziendale o dell'Istituto di istruzione non gestito, attenersi alla procedura seguente:

1. Accedere per [chiudere l'account](https://go.microsoft.com/fwlink/?linkid=873123), usando l'account che si vuole chiudere.

1. In **richieste dati personali**selezionare **Chiudi account**.

    ![Richieste dati personali-Chiudi account](./media/users-close-account/close-account.png)

1. Esaminare il messaggio di conferma e quindi selezionare **Sì**.

    ![Richieste dati personali-conferma chiusura](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'iscrizione self-service per Azure Active Directory](directory-self-service-signup.md)
- [Eliminare l'utente dal tenant non gestito](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Accesso ed esportazione dei log generati dal sistema per i tenant non gestiti](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
