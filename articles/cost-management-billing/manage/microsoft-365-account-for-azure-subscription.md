---
title: Iscriversi ad Azure con un account di Microsoft 365
description: Informazioni su come creare una sottoscrizione di Azure tramite un account di Microsoft 365
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: banders
ms.openlocfilehash: 2d3711e33a5fa9b95ce43b4d2f4730517df0a449
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810296"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Iscriversi a una sottoscrizione di Azure con il proprio account di Microsoft 365

Se si ha una sottoscrizione di Microsoft 365, è possibile usare l'account di Microsoft 365 per creare una sottoscrizione di Azure. Accedere al [portale di Azure](https://portal.azure.com/) usando il nome utente e la password di Microsoft 365. Se si vuole configurare le macchine virtuali o usare altri servizi di Azure, è necessario ottenere una sottoscrizione di Azure. È possibile condividere la sottoscrizione di Azure con altri utenti e [usare controllo degli accessi in base al ruolo per gestire l'accesso alle risorse e alla sottoscrizione di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Se si ha già sia un account di Microsoft 365 sia una sottoscrizione di Azure, vedere [Associare un tenant di Microsoft 365 a una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Ottenere una sottoscrizione di Azure tramite l'account di Microsoft 365 personale

Se si accede ad Azure usando il nome utente e la password di Microsoft 365, è possibile risparmiare tempo ed evitare la proliferazione di account.

1. Acceder ad [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Accedere usando il nome utente e la password di Microsoft 365. Per l'account usato non è necessario disporre delle autorizzazioni di amministratore. Se si hanno più account di Microsoft 365, assicurarsi di usare le credenziali dell'account di Microsoft 365 che si vuole associare alla sottoscrizione di Azure.

   ![Screenshot che mostra la pagina di accesso](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Immettere le informazioni richieste e completare la procedura di accesso. È possibile che alcune informazioni non siano necessarie se si ha già un account di Microsoft 365.

    ![Screenshot che mostra il modulo di iscrizione.](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se è necessario aggiungere altri utenti dell'organizzazione alla sottoscrizione di Azure, vedere [Introduzione alla gestione degli accessi nel portale di Azure](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Altre informazioni su sottoscrizioni di Azure e sottoscrizioni di Microsoft 365</a>

Microsoft 365 e Azure usano il servizio Azure AD per gestire utenti e sottoscrizioni. La directory di Azure è come un contenitore in cui raggruppare utenti e sottoscrizioni. Per usare gli stessi account utente per le sottoscrizioni di Azure e di Microsoft 365, è necessario assicurarsi che le sottoscrizioni di Azure vengano create nella stessa directory delle sottoscrizioni di Microsoft 365. Tenere presente quanto segue:

* Una sottoscrizione viene creata in una directory
* Gli utenti appartengono alle directory
* Una sottoscrizione viene inserita nella directory dell'utente che crea la sottoscrizione. La sottoscrizione di Microsoft 365 è pertanto legata allo stesso account della sottoscrizione di Azure.
* Le sottoscrizioni di Azure appartengono a singoli utenti nella directory
* Le sottoscrizioni di Microsoft 365 appartengono alla directory stessa. Gli utenti con le autorizzazioni appropriate all'interno della directory possono gestire le sottoscrizioni.

![Screenshot che mostra la relazione tra directory, utenti e sottoscrizioni.](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- È possibile condividere la sottoscrizione di Azure con altri utenti e [usare controllo degli accessi in base al ruolo per gestire l'accesso alle risorse e alla sottoscrizione di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).