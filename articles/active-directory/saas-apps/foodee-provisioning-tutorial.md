---
title: 'Esercitazione: Configurare Foodee per il provisioning automatico degli utenti tramite Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Foodee.Learn how to configure Azure Active Directory to automatically provision and deprovision user accounts to Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057825"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Esercitazione: Configurare Foodee per il provisioning automatico degli utentiTutorial: Configure Foodee for automatic user provisioning

Questo articolo illustra come configurare Azure Active Directory (Azure AD) in Foodee e Azure AD per eseguire automaticamente il provisioning o il deprovisioning di utenti o gruppi in Foodee.This article shows you how to configure Azure Active Directory (Azure AD) in Foodee and Azure AD to automatically provision or deprovision users or groups to Foodee.

> [!NOTE]
> L'articolo descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni sulle operazioni eseguite da questo servizio e sul suo funzionamento e per ottenere risposte alle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS con Azure Active Directory.](../app-provisioning/user-provisioning.md)
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle funzionalità delle condizioni per l'utilizzo di Azure per le funzionalità di anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)di Microsoft Azure .

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che siano stati soddisfatti i prerequisiti seguenti:This tutorial assumes that you've met the following prerequisites:

* Un tenant di Azure AD
* [Un inquilino Foodee](https://www.food.ee/about/)
* Un account utente in Foodee con autorizzazioni di amministratore

## <a name="assign-users-to-foodee"></a>Assegnare utenti a Foodee 

Azure AD usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti o gruppi in Azure AD devono accedere a Foodee. Dopo aver effettuato questa determinazione, è possibile assegnare questi utenti o gruppi a Foodee seguendo le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Consigli importanti per assegnare gli utenti a Foodee 

Quando assegni gli utenti, tieni presente i seguenti suggerimenti:

* È consigliabile assegnare un solo utente di Azure AD a Foodee per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a Foodee, selezionare un ruolo specifico dell'applicazione valido, se disponibile, nel riquadro **Assegnazione.** Gli utenti che dispongono del ruolo *Accesso predefinito* vengono esclusi dal provisioning.

## <a name="set-up-foodee-for-provisioning"></a>Configurare Foodee per il provisioning

Prima di configurare Foodee per il provisioning automatico degli utenti tramite Azure AD, è necessario abilitare il provisioning SCIM (System for Cross-Domain Identity Management) in Foodee.

1. Accedere a [Foodee](https://www.food.ee/login/), quindi selezionare l'ID tenant.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. In **Enterprise Portal**selezionare Single **Sign-On**.

    ![Menu a sinistra di Foodee Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Copiare il valore nella casella **Token API** per un utilizzo successivo. Verrà immesso nella casella **Token segreto** nella scheda Provisioning dell'applicazione Foodee nel portale di Azure.You'll enter it in the Secret Token box in the **Provisioning** tab of your Foodee application in the Azure portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Aggiungi Foodee dalla galleria

Per configurare Foodee per il provisioning automatico degli utenti tramite Azure AD, è necessario aggiungere Foodee dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

Per aggiungere Foodee dalla raccolta di applicazioni di Azure AD, eseguire le operazioni seguenti:To add Foodee from the Azure AD application gallery, do the following:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Comando di Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni** > aziendali**Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

1. Nella casella di ricerca immettere **Foodee**, selezionare **Foodee** nel riquadro dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Foodee nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configurare il provisioning automatico degli utenti in Foodee 

In questa sezione viene configurato il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Foodee in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile abilitare l'accesso Single Sign-On basato su SAML per Foodee seguendo le istruzioni [nell'esercitazione sull'accesso Single Sign-On di Foodee](Foodee-tutorial.md). È possibile configurare l'accesso Single Sign-On indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

Configurare il provisioning automatico degli utenti per Foodee in Azure AD eseguendo le operazioni seguenti:Configure automatic user provisioning for Foodee in Azure AD by doing the following:

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Applicazioni** > aziendali**Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco **Applicazioni** selezionare **Foodee**.

    ![Il link Foodee nell'elenco Applicazioni](common/all-applications.png)

1. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

1. Nell'elenco a discesa **Modalità di** provisioning selezionare **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

1. In **Credenziali amministratore**eseguire le operazioni seguenti:

   a. Nella casella **URL tenant** immettere il valore **https:\//concierge.food.ee/scim/v2** recuperato in precedenza.

   b. Nella casella **Token segreto** immettere il valore **del token API** recuperato in precedenza.
   
   c. Per assicurarsi che Azure AD possa connettersi a Foodee, selezionare **Test connessione**. Se la connessione non riesce, verificare che l'account Foodee disponga delle autorizzazioni di amministratore, quindi riprovare.

    ![Il collegamento Test connessione](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **Posta elettronica notifica** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che deve ricevere le notifiche di errore di provisioning e quindi selezionare la casella di controllo Invia una notifica di posta elettronica quando si verifica un **errore.**

    ![Casella di testo E-mail di notifica](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. In **Mapping**selezionare Sincronizza utenti di Azure Active Directory con **Foodee**.

    ![Mappature degli utenti foodee](media/Foodee-provisioning-tutorial/usermapping.png)

1. In **Mapping attributi**esaminare gli attributi utente sincronizzati da Azure AD a Foodee. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare *gli account utente* in Foodee per le operazioni di aggiornamento. 

    ![Mappature degli utenti foodee](media/Foodee-provisioning-tutorial/userattribute.png)

1. Per eseguire il commit delle modifiche, selezionare **Salva**.
1. In **Mapping**selezionare Sincronizza gruppi di Azure Active Directory con **Foodee**.

    ![Mappature degli utenti foodee](media/Foodee-provisioning-tutorial/groupmapping.png)

1. In **Mapping attributi**esaminare gli attributi utente sincronizzati da Azure AD a Foodee. Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account di *gruppo* in Foodee per le operazioni di aggiornamento.

    ![Mappature degli utenti foodee](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Per eseguire il commit delle modifiche, selezionare **Salva**.
1. Configurare i filtri di ambito. Per informazioni su come, fare riferimento alle istruzioni [nell'esercitazione](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)sul filtro di ambito .

1. Per abilitare il servizio di provisioning di Azure AD per Foodee, nella sezione **Impostazioni** modificare Lo stato di **provisioning su** **Attivato**.

    ![L'opzione Stato provisioning](common/provisioning-toggle-on.png)

1. In **Impostazioni**, nell'elenco a discesa **Ambito,** definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in Foodee.

    ![Elenco a discesa Ambito provisioning](common/provisioning-scope.png)

1. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Pulsante Salva configurazione provisioning](common/provisioning-configuration-save.png)

L'operazione precedente avvia la sincronizzazione iniziale degli utenti o dei gruppi definiti nell'elenco a discesa **Ambito.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per ulteriori informazioni, vedere [Quanto tempo è necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile utilizzare la sezione **Stato corrente** per monitorare lo stato di avanzamento e seguire i collegamenti al rapporto attività di provisioning. The report describes all actions that are performed by the Azure AD provisioning service on Foodee. Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Per leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
