---
title: Iscriversi ad Azure con un account Office 365 | Documentazione Microsoft
description: Informazioni su come condividere il tenant di Azure AD per Office 365 e i relativi utenti con la sottoscrizione di Azure o viceversa
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 488ea81fcdad156371b89b2c94ef7c90bb83ca33


---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Usare un account Office 365 esistente con la sottoscrizione di Azure o viceversa
Scenario: è già disponibile un abbonamento a Office 365 e si è pronti per una sottoscrizione di Azure, ma si vuole usare uno o più account utente di Office 365 esistenti per la sottoscrizione di Azure. Scenario alternativo: è disponibile una sottoscrizione di Azure e si vuole ottenere un abbonamento a Office 365 per gli utenti nell'istanza di Azure Active Directory esistente. Questo articolo mostra come è facile ottenere entrambi.

> [!NOTE]
> Questo articolo non si applica ai clienti con Contratto Enterprise. Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
>
>

## <a name="quick-guidance"></a>Indicazioni rapide
* Se è già disponibile un abbonamento a Office 365 e si vuole ottenere una sottoscrizione di Azure, usare l'opzione **Accedere con il proprio account aziendale**. Continuare il processo di sottoscrizione di Azure con l'account Office 365. Vedere i [passaggi dettagliati più avanti in questo articolo](#s1).
* Se è già disponibile una sottoscrizione di Azure e si vuole ottenere un abbonamento a Office 365, accedere a Office 365 con il proprio account Azure. Procedere quindi con l'iscrizione. Dopo aver completato l'iscrizione, l'abbonamento a Office 365 viene aggiunto alla stessa istanza di Azure Active Directory a cui appartiene la sottoscrizione di Azure. Per altre informazioni, vedere la sezione con i [passaggi dettagliati più avanti in questo articolo](#s2).

> [!NOTE]
> Per ottenere un abbonamento a Office 365, l'account usato per l'iscrizione deve essere un membro del ruolo della directory Amministratore globale o Amministratore fatturazione nel tenant di Azure Active Directory. [Informazioni su come determinare il ruolo in Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).
>
>

Per comprendere come funziona l'aggiunta di una sottoscrizione a un account, vedere la sezione Informazioni generali più avanti nell'articolo.

## <a name="detailed-steps"></a>Procedura dettagliata
<a id="s1"></a>

### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Scenario 1: utenti di Office 365 che pianificano l'acquisto di Azure
In questo scenario si presuppone che Kelley Wall sia un utente con un abbonamento a Office 365 che intende acquistare una sottoscrizione di Azure. Ci sono altri due utenti attivi: Jane e Tricia. L'account di Kelley è admin@contoso.onmicrosoft.com.

![Interfaccia di amministrazione utenti di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Per iscriversi ad Azure, seguire questa procedura:

1. Iscriversi ad Azure alla pagina [Azure.com](https://azure.microsoft.com/). Fare clic su **Account gratuito**. Nella pagina successiva fare clic su **Inizia gratuitamente**.

    ![Provare Azure gratuitamente.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. Accedere con il proprio account di Office 365. In questo caso, l'account di Office 365 di Kelley.

    ![Accedere con il proprio account di Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
3. Immettere le informazioni e completare la procedura d'accesso.

    ![Immettere le informazioni e completare la sottoscrizione.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Fare clic su Inizia a gestire il servizio.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Le impostazioni sono state completate. Nel portale di Azure verranno visualizzati gli stessi utenti. A questo scopo, seguire questa procedura:

1. Fare clic su **Inizia a gestire il servizio** nella schermata precedente.
2. Fare clic su **Esplora** e quindi su **Active Directory**.

    ![Fare clic su Esplora e quindi su Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. Fare clic su **UTENTI**.

    ![La scheda Utenti](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. Tutti gli utenti inclusa Kelley saranno elencati come previsto.

    ![Elenco di utenti](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Scenario 2: utenti di Azure che pianificano l'acquisto di Office 365
In questo scenario Kelley Wall è un utente che ha una sottoscrizione di Azure con l'account admin@contoso.onmicrosoft.com. Kelley vuole effettuare la sottoscrizione di Office 365 e usare la stessa directory che possiede già in Azure.

> [!NOTE]
> Per ottenere un abbonamento a Office 365, l'account usato per l'accesso deve essere un membro del ruolo della directory Amministratore globale o Amministratore fatturazione nel tenant di Azure Active Directory. [Informazioni su come conoscere il ruolo in Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).
>
>

![Impostazioni della sottoscrizione nel portale di Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Utenti di Active Directory del portale di Azure](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Per eseguire la sottoscrizione di Office 365, seguire questa procedura:

1. Passare alla [pagina del prodotto Office 365](https://products.office.com/business)e selezionare un piano adatto alle proprie esigenze.
2. Dopo aver selezionato il piano viene visualizzata la pagina seguente. Non compilare i dati. Fare clic su **Accedi** nell'angolo superiore destro della pagina.

    ![Pagina di prova di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. Accedere con le credenziali dell'account. In questo caso, è l'account di Kelley.

    ![Pagina di accesso di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. Fare clic su **Prova adesso**.

    ![Confermare l'ordine per Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. Nella pagina di conferma dell'ordine fare clic su **Continua**.

    ![Ricezione ordine per Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Le impostazioni sono state completate. Nell'interfaccia di amministrazione di Office 365 verranno visualizzati gli utenti dalla directory di Contoso presenti come utenti attivi. A questo scopo, seguire questa procedura:

1. Aprire l'interfaccia di amministrazione di Office 365.
2. Espandere **UTENTI** e quindi fare clic su **Utenti attivi**.

    ![Utenti dell'interfaccia di amministrazione di Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Informazioni su come conoscere il proprio ruolo di Azure Active Directory
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Esplora** e quindi su **Active Directory**.

    ![Active Directory nel portale di Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. Fare clic su **UTENTI**.

    ![Utenti di Active Directory predefiniti del portale di Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. Fare clic sull'utente. In questo esempio, l'utente è Kelley Wall.

    Prendere nota del campo **RUOLO AZIENDALE**.

    ![Identità dell'utente del portale di Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Informazioni base sulla sottoscrizione di Azure e l'abbonamento a Office 365
Office 365 e Azure usano il servizio Azure Active Directory per gestire utenti e sottoscrizioni. Considerare una directory di Azure come un contenitore in cui raggruppare utenti e sottoscrizioni. Per usare lo stesso account utente per la sottoscrizione di Azure e l'abbonamento a Office 365, è necessario assicurarsi che vengano creati nella stessa directory. Tenere presente quanto segue:

* Una sottoscrizione viene creata in una directory, non viceversa.
* Gli utenti appartengono alle directory, non viceversa.
* Una sottoscrizione viene inserita nella directory dell'utente che crea la sottoscrizione. Di conseguenza, l'abbonamento a Office 365 è associato allo stesso account della sottoscrizione di Azure usato per creare l'abbonamento a Office 365.

![Informazioni generali](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

> [!NOTE]
> Le sottoscrizioni di Azure appartengono a singoli utenti nella directory.
>
> [!NOTE]
> Gli abbonamenti a Office 365 appartengono alla directory stessa. Gli utenti all'interno della directory possono agire sulle sottoscrizioni se hanno le autorizzazioni necessarie.
>
>

## <a name="next-steps"></a>Passaggi successivi
Se in precedenza la sottoscrizione di Azure e l'abbonamento a Office 365 sono stati acquistati separatamente e si vuole poter accedere al tenant di Office 365 dalla sottoscrizione di Azure, vedere [Associare un tenant di Office 365 con una sottoscrizione di Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [!NOTE]
> Per eventuali domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
>
>



<!--HONumber=Feb17_HO2-->


