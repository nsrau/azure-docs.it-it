---
title: Chiudere l'account aziendale o dell'istituto di istruzione in una directory non gestita - Azure Active Directory | Microsoft Docs
description: Come chiudere l'account aziendale o dell'istituto di istruzione in un Azure Active Directory non gestita.
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
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65958001"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Chiudere l'account aziendale o dell'istituto di istruzione in una directory non gestita

Se sei un utente in un'organizzazione di Azure Active Directory (Azure AD) non gestita e non è più necessario usare le app di tale organizzazione o mantenere qualsiasi associazione a è, è possibile chiudere l'account in qualsiasi momento. Una directory non gestita non è un amministratore globale. Gli utenti in una directory non gestita possono chiudere i propri account in modo indipendente, senza la necessità di contattare un amministratore.

Gli utenti in una directory non gestita vengono spesso creati durante la modalità self-service per l'abbonamento. Un esempio potrebbe essere un information worker in un'organizzazione che effettua l'iscrizione per un servizio gratuito. Per altre informazioni sull'iscrizione Self-Service, vedere [che cos'è self-service per Azure Active Directory per l'abbonamento?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Prima di chiudere l'account, è necessario verificare gli elementi seguenti:

* Assicurarsi di essere un utente di un'istanza non gestita di Azure directory di AD. Se si appartiene a una directory gestita, è possibile chiudere l'account. Se si appartiene a una directory gestita e si vuole chiudere l'account, è necessario contattare l'amministratore. Per informazioni su come determinare se si appartiene a una directory non gestita, vedere [eliminare l'utente dal Tenant non gestito](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Salvare tutti i dati che si desidera mantenere. Per informazioni su come inviare una richiesta di esportazione, vedere [accesso e l'esportazione dei log generati dal sistema per i tenant non gestito](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Chiusura dell'account non può essere annullata. Quando si chiude l'account, tutti i dati personali verranno rimossi. Non sarà non sarà più possibile accedere all'account e ai dati associati all'account.

## <a name="close-your-account"></a>Chiudi il tuo account

Per chiudere un non gestito account aziendale o dell'istituto di istruzione, seguire questa procedura:

1. Accedere al [chiuda il tuo account](https://go.microsoft.com/fwlink/?linkid=873123), usando l'account che si desidera chiudere.

1. Sul **richieste dati personali**, selezionare **Chiudi account**.

    ![Richieste dati personali - Chiudi account](./media/users-close-account/close-account.png)

1. Esaminare il messaggio di conferma e quindi selezionare **Sì**.

    ![Richieste dati personali - Chiudi conferma](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è self-service per Azure Active Directory per l'abbonamento?](directory-self-service-signup.md)
- [Eliminare l'utente dal Tenant non gestito](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [L'accesso e l'esportazione dei log generati dal sistema per i tenant non gestito](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
