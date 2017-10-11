---
title: Iscriversi ad Azure con un account Office 365 | Microsoft Docs
description: Informazioni su come creare una sottoscrizione di Azure tramite un account di Office 365
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: cjiang
ms.openlocfilehash: 923b3ca8a2e2b1d7f44cd77946a02e1d30839f14
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Iscriversi a una sottoscrizione di Azure con il proprio account di Office 365
Se si dispone di un abbonamento a Office 365, è possibile usare l'account di Office 365 per creare una sottoscrizione di Azure. Accedere al [portale di Azure](https://portal.azure.com/) usando il nome utente e la password di Office 365. Se si vuole configurare le macchine virtuali o usare altri servizi di Azure, è necessario ottenere una sottoscrizione di Azure. È possibile condividere la sottoscrizione di Azure con altri utenti e [usare controllo degli accessi in base al ruolo per gestire l'accesso alle risorse e alla sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)

Se si dispone già sia di un account di Office 365 sia di una sottoscrizione di Azure, vedere [Associare un tenant di Office 365 a una sottoscrizione di Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Ottenere una sottoscrizione di Azure tramite l'account di Office 365 personale

Se si accede ad Azure usando il nome utente e la password di Office 365, è possibile risparmiare tempo ed evitare la proliferazione di account. 

1. Acceder ad [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Accedere usando il nome utente e la password di Office 365. Per l'account usato non è necessario disporre delle autorizzazioni di amministratore. Se si hanno più account di Office 365, assicurarsi di usare le credenziali dell'account di Office 365 che si desidera associare alla sottoscrizione di Azure. 

   ![Screenshot che mostra la pagina di accesso](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Immettere le informazioni richieste e completare la procedura di accesso. È possibile che alcune informazioni non siano necessarie se si dispone già di un account Office 365.

    ![Screenshot che mostra il modulo di iscrizione.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se è necessario aggiungere altri utenti dell'organizzazione alla sottoscrizione di Azure, vedere [Introduzione alla gestione degli accessi nel portale di Azure](../active-directory/role-based-access-control-what-is.md). 

## <a id="more-about-subs">Altre informazioni su sottoscrizioni di Azure e abbonamenti a Office 365</a>
Office 365 e Azure usano il servizio Azure AD per gestire utenti e sottoscrizioni. La directory di Azure è come un contenitore in cui raggruppare utenti e sottoscrizioni. Per usare gli stessi account utente per la sottoscrizione di Azure e l'abbonamento a Office 365, è necessario assicurarsi che le sottoscrizioni di Azure vengano create nella stessa directory delle sottoscrizioni di Office 365. Tenere presente quanto segue:

* Una sottoscrizione viene creata in una directory
* Gli utenti appartengono alle directory
* Una sottoscrizione viene inserita nella directory dell'utente che crea la sottoscrizione. L'abbonamento a Office 365 è pertanto legato allo stesso account della sottoscrizione di Azure.
* Le sottoscrizioni di Azure appartengono a singoli utenti nella directory
* Gli abbonamenti a Office 365 appartengono alla directory stessa. Gli utenti con le autorizzazioni appropriate all'interno della directory possono gestire le sottoscrizioni.

![Screenshot che mostra la relazione tra directory, utenti e sottoscrizioni.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.
Se si necessita ancora di assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema. 
