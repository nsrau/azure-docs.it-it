---
title: Chiudere un account aziendale o dell'Istituto di istruzione in un'organizzazione Azure AD non gestita
description: Come chiudere l'account aziendale o dell'Istituto di istruzione in un Azure Active Directory non gestito.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142143f96314539051ea44c00c6ff95cb2650566
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650207"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>Chiudere l'account aziendale o dell'Istituto di istruzione in un'organizzazione Azure AD non gestita

Se si è un utente in un'organizzazione Azure Active Directory non gestita (Azure AD) e non è più necessario usare le app di tale organizzazione o gestire qualsiasi associazione, è possibile chiudere l'account in qualsiasi momento. Un'organizzazione non gestita non dispone di un amministratore globale. Gli utenti di un'organizzazione non gestita possono chiudere gli account in modo autonomo, senza dover contattare un amministratore.

Gli utenti di un'organizzazione non gestita vengono spesso creati durante l'iscrizione self-service. Un esempio potrebbe essere un Information Worker in un'organizzazione che si iscrive a un servizio gratuito. Per ulteriori informazioni sull'iscrizione self-service, vedere informazioni sull' [iscrizione self-service per Azure Active Directory](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter chiudere l'account, è necessario verificare gli elementi seguenti:

* Assicurarsi di essere un utente di un'organizzazione Azure AD non gestita. Non è possibile chiudere l'account se si appartiene a un'organizzazione gestita. Se si appartiene a un'organizzazione gestita e si vuole chiudere l'account, è necessario contattare l'amministratore. Per informazioni su come determinare se si appartiene a un'organizzazione non gestita, vedere [eliminare l'utente dal tenant non gestito](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Salvare i dati che si desidera conservare. Per informazioni su come inviare una richiesta di esportazione, vedere [accesso e esportazione dei log generati dal sistema per i tenant non gestiti](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> La chiusura dell'account è irreversibile. Quando si chiude l'account, tutti i dati personali verranno rimossi. Non sarà più possibile accedere all'account e ai dati associati all'account.

## <a name="close-your-account"></a>Chiudere l'account

Per chiudere un account aziendale o dell'Istituto di istruzione non gestito, attenersi alla procedura seguente:

1. Accedere per [chiudere l'account](https://go.microsoft.com/fwlink/?linkid=873123), usando l'account che si vuole chiudere.

1. In **richieste dati personali** selezionare **Chiudi account**.

    ![Richieste dati personali-Chiudi account](./media/users-close-account/close-account.png)

1. Esaminare il messaggio di conferma e quindi selezionare **Sì**.

    ![Richieste dati personali-conferma chiusura](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'iscrizione self-service per Azure Active Directory.](directory-self-service-signup.md)
- [Eliminare l'utente dal tenant non gestito](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Accesso ed esportazione dei log generati dal sistema per i tenant non gestiti](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)