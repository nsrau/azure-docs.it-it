---
title: Iscriversi ad Azure con un account Office 365 | Microsoft Docs
description: Informazioni su come creare una sottoscrizione di Azure tramite un account di Office 365
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 265d9bcafa7caf9af0f8c4c7f33de044fa02349c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199586"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Iscriversi a una sottoscrizione di Azure con il proprio account di Office 365
Se si dispone di un abbonamento a Office 365, è possibile usare l'account di Office 365 per creare una sottoscrizione di Azure. Accedere al [portale di Azure](https://portal.azure.com/) usando il nome utente e la password di Office 365. Se si vuole configurare le macchine virtuali o usare altri servizi di Azure, è necessario ottenere una sottoscrizione di Azure. È possibile condividere la sottoscrizione di Azure con altri utenti e [usare controllo degli accessi in base al ruolo per gestire l'accesso alle risorse e alla sottoscrizione di Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Se si dispone già sia di un account di Office 365 sia di una sottoscrizione di Azure, vedere [Associare un tenant di Office 365 a una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Ottenere una sottoscrizione di Azure tramite l'account di Office 365 personale

Se si accede ad Azure usando il nome utente e la password di Office 365, è possibile risparmiare tempo ed evitare la proliferazione di account.

1. Acceder ad [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Accedere usando il nome utente e la password di Office 365. Per l'account usato non è necessario disporre delle autorizzazioni di amministratore. Se si hanno più account di Office 365, assicurarsi di usare le credenziali dell'account di Office 365 che si desidera associare alla sottoscrizione di Azure.

   ![Screenshot che mostra la pagina di accesso](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Immettere le informazioni richieste e completare la procedura di accesso. È possibile che alcune informazioni non siano necessarie se si dispone già di un account Office 365.

    ![Screenshot che mostra il modulo di iscrizione.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se è necessario aggiungere altri utenti dell'organizzazione alla sottoscrizione di Azure, vedere [Introduzione alla gestione degli accessi nel portale di Azure](../../role-based-access-control/overview.md).

## <a id="more-about-subs">Altre informazioni su sottoscrizioni di Azure e abbonamenti a Office 365</a>
Office 365 e Azure usano il servizio Azure AD per gestire utenti e sottoscrizioni. La directory di Azure è come un contenitore in cui raggruppare utenti e sottoscrizioni. Per usare gli stessi account utente per la sottoscrizione di Azure e l'abbonamento a Office 365, è necessario assicurarsi che le sottoscrizioni di Azure vengano create nella stessa directory delle sottoscrizioni di Office 365. Tenere presente quanto segue:

* Una sottoscrizione viene creata in una directory
* Gli utenti appartengono alle directory
* Una sottoscrizione viene inserita nella directory dell'utente che crea la sottoscrizione. L'abbonamento a Office 365 è pertanto legato allo stesso account della sottoscrizione di Azure.
* Le sottoscrizioni di Azure appartengono a singoli utenti nella directory
* Gli abbonamenti a Office 365 appartengono alla directory stessa. Gli utenti con le autorizzazioni appropriate all'interno della directory possono gestire le sottoscrizioni.

![Screenshot che mostra la relazione tra directory, utenti e sottoscrizioni.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
