---
title: 'Esercitazione: Configurare il cibo per il provisioning utenti automatico usando Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in un alimento.
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
ms.openlocfilehash: abf2a752eaf0f1d0a9a8b07072dfc0b4c1ae45b7
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812711"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Esercitazione: Configurare il foodee per il provisioning utenti automatico

Questo articolo illustra come configurare Azure Active Directory (Azure AD) in foodee e Azure AD per effettuare automaticamente il provisioning o il deprovisioning di utenti o gruppi in un alimento.

> [!NOTE]
> Questo articolo descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni sul funzionamento di questo servizio e sul relativo funzionamento e per ottenere risposte alle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima. Per altre informazioni sulle funzionalità di anteprima per le funzionalità di anteprima di Azure, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione si presuppone che siano stati soddisfatti i prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant del cibo](https://www.food.ee/about/)
* Un account utente in foodee con autorizzazioni di amministratore

## <a name="assign-users-to-foodee"></a>Assegnare gli utenti al cibo 

Azure AD usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario decidere quali utenti o gruppi in Azure AD necessario accedere al cibo. Dopo aver apportato questa decisione, è possibile assegnare questi utenti o gruppi al cibo seguendo le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Suggerimenti importanti per l'assegnazione di utenti a un alimento 

Quando si assegnano utenti, tenere presenti i suggerimenti seguenti:

* Si consiglia di assegnare un singolo utente Azure AD al cibo per testare la configurazione del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a un alimento, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nel riquadro **assegnazione** . Gli utenti che hanno il ruolo di *accesso predefinito* vengono esclusi dal provisioning.

## <a name="set-up-foodee-for-provisioning"></a>Configurare un alimento per il provisioning

Prima di configurare gli utenti per il provisioning utenti automatico usando Azure AD, è necessario abilitare il provisioning di sistema per la gestione delle identità tra domini (SCIM) in Foode.

1. Accedere a [foodee](https://www.food.ee/login/), quindi selezionare l'ID tenant.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. In **Enterprise Portal**selezionare **Single Sign-on**.

    ![Menu del riquadro a sinistra del cibo Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Copiare il valore nella casella **token API** per usarlo in seguito. Immetterlo nella casella del **token Secret** nella scheda **provisioning** dell'applicazione Foods nella portale di Azure.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Aggiungere un alimento dalla raccolta

Per configurare gli utenti per il provisioning utenti automatico usando Azure AD, è necessario aggiungere Foode dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

Per aggiungere un alimento dalla raccolta di applicazioni di Azure AD, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Comando Azure Active Directory](common/select-azuread.png)

1. Selezionare **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Per aggiungere una nuova applicazione, selezionare **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

1. Nella casella di ricerca immettere **Foode**, selezionare **Foode** nel riquadro dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Foode nell'elenco dei risultati](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configurare il provisioning utenti automatico per il cibo 

In questa sezione si configura il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare gli utenti o i gruppi in foodee in base alle assegnazioni di utenti o gruppi nel Azure AD.

> [!TIP]
> È anche possibile abilitare la Single Sign-On basata su SAML per foodee seguendo le istruzioni riportate nell' [esercitazione Single Sign-on](Foodee-tutorial.md)per i buongustai. È possibile configurare Single Sign-On indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

Configurare il provisioning utenti automatico per i buongustai in Azure AD eseguendo le operazioni seguenti:

1. Nella [portale di Azure](https://portal.azure.com)selezionare **applicazioni aziendali** > **tutte le applicazioni**.

    ![Riquadro Applicazioni aziendali](common/enterprise-applications.png)

1. Nell'elenco delle **applicazioni** selezionare **foodi**.

    ![Collegamento del cibo nell'elenco delle applicazioni](common/all-applications.png)

1. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

1. Nell'elenco a discesa **modalità di provisioning** selezionare **automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

1. In **credenziali amministratore**eseguire le operazioni seguenti:

   a. Nella casella **URL tenant** immettere il valore **https://concierge.food.ee/scim/v2** recuperato in precedenza.

   b. Nella casella **token segreto** immettere il valore del **token API** recuperato in precedenza.
   
   c. Per assicurarsi che Azure AD possa connettersi a Foode, selezionare **Test connessione**. Se la connessione ha esito negativo, verificare che l'account del cibo abbia le autorizzazioni di amministratore, quindi riprovare.

    ![Collegamento Test connessione](common/provisioning-testconnection-tenanturltoken.png)

1. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che deve ricevere le notifiche degli errori di provisioning, quindi selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Casella di testo della posta elettronica di notifica](common/provisioning-notification-email.png)

1. Selezionare **Salva**.

1. In **mapping**selezionare **Sincronizza Azure Active Directory utenti al cibo**.

    ![Mapping utenti del cibo](media/Foodee-provisioning-tutorial/usermapping.png)

1. In **mapping degli attributi**esaminare gli attributi utente che vengono sincronizzati dal Azure ad al cibo. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli *account utente* in foodee per le operazioni di aggiornamento. 

    ![Mapping utenti del cibo](media/Foodee-provisioning-tutorial/userattribute.png)

1. Per eseguire il commit delle modifiche, selezionare **Salva**.
1. In **mapping**selezionare **Sincronizza Azure Active Directory gruppi al cibo**.

    ![Mapping utenti del cibo](media/Foodee-provisioning-tutorial/groupmapping.png)

1. In **mapping degli attributi**esaminare gli attributi utente che vengono sincronizzati dal Azure ad al cibo. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli *account di gruppo* in foodee per le operazioni di aggiornamento.

    ![Mapping utenti del cibo](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Per eseguire il commit delle modifiche, selezionare **Salva**.
1. Configurare i filtri di ambito. Per informazioni, vedere le istruzioni nell'esercitazione relativa al [filtro di ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Per abilitare il servizio di provisioning Azure AD per Foode, nella sezione **Impostazioni** impostare stato del **provisioning** **su**attivato.

    ![Opzione stato provisioning](common/provisioning-toggle-on.png)

1. In **Impostazioni**, nell'elenco a discesa **ambito** , definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in foodee.

    ![Elenco a discesa dell'ambito del provisioning](common/provisioning-scope.png)

1. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Pulsante Salva configurazione di provisioning](common/provisioning-configuration-save.png)

Con l'operazione precedente viene avviata la sincronizzazione iniziale degli utenti o dei gruppi definiti nell'elenco a discesa **ambito** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni, vedere [quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report descrive tutte le azioni che vengono eseguite dal servizio di provisioning Azure AD in Foode. Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
