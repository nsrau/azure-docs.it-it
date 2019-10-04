---
title: 'Esercitazione: Configurare Wrike per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Wrike.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 46972209a8fa509ff2f17832ab8329aa3cef2548
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840319"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Esercitazione: Configurare Wrike per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Wrike e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti o gruppi in Wrike.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni importanti sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS (software-as-a-Service) con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per ulteriori informazioni sulle condizioni per l'utilizzo di Microsoft Azure generali per le funzionalità di anteprima, vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant di Wrike](https://www.wrike.com/price/)
* Un account utente in Wrike con autorizzazioni di amministratore

## <a name="assign-users-to-wrike"></a>Assegnare gli utenti a Wrike
Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a Wrike. Assegnare quindi tali utenti o gruppi a Wrike seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Suggerimenti importanti per l'assegnazione di utenti a Wrike

* È consigliabile assegnare un singolo utente Azure AD a Wrike per testare la configurazione del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a Wrike, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di accesso predefinito vengono esclusi dal provisioning.

## <a name="set-up-wrike-for-provisioning"></a>Configurare Wrike per il provisioning

Prima di configurare Wrike per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning di sistema per la gestione delle identità tra domini (SCIM) su Wrike.

1. Accedere a [Wrike Admin Console](https://www.Wrike.com/login/). Passare all'ID tenant. Selezionare **app & integrazioni**.

    ![Integrazioni & app](media/Wrike-provisioning-tutorial/admin.png)

2.  Passare a **Azure ad** e selezionarlo.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selezionare SCIM. Copiare l' **URL di base**.

    ![URL di base](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selezionare **API** > **Azure scim**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Viene visualizzata una finestra popup. Immettere la stessa password creata in precedenza per creare un account.

    ![Wrike creare token](media/Wrike-provisioning-tutorial/password.png)

6.  Copiare il **token Secret**e incollarlo in Azure ad. Selezionare **Save (Salva** ) per completare la configurazione del provisioning in Wrike.

    ![Token di accesso permanente](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Aggiungere Wrike dalla raccolta

Prima di configurare Wrike per il provisioning utenti automatico con Azure AD, aggiungere Wrike dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Wrike dalla raccolta di applicazioni Azure AD, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Wrike**, selezionare **Wrike** nel pannello dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

    ![Wrike nell'elenco risultati](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Configurare il provisioning utenti automatico in Wrike 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti o gruppi in Wrike in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> Per abilitare la Single Sign-On basata su SAML per Wrike, seguire le istruzioni disponibili nell' [esercitazione su Wrike Single Sign-on](wrike-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Configurare il provisioning utenti automatico per Wrike in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni**.

    ![Tutte le applicazioni](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Wrike**.

    ![Collegamento di Wrike nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning impostata su automatica](common/provisioning-automatic.png)

5. Nella sezione credenziali amministratore immettere l'URL di **base** e i valori dei **token di accesso permanenti** recuperati in precedenza rispettivamente in **URL tenant** e **token segreto**. Selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a Wrike. Se la connessione non riesce, verificare che l'account Wrike disponga delle autorizzazioni di amministratore e riprovare.

    ![URL tenant + token](common/provisioning-testconnection-tenanturltoken.png)

7. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![Indirizzo di posta elettronica per le notifiche](common/provisioning-notification-email.png)

8. Selezionare **Salva**.

9. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a Wrike**.

    ![Mapping utente Wrike](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Wrike nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Wrike per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Wrike](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning Azure AD per Wrike, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in Wrike selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito del provisioning](common/provisioning-scope.png)

14. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nell' **ambito** nella sezione **Impostazioni** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per l'esecuzione del provisioning da parte di utenti o gruppi, vedere [quanto tempo sarà necessario per il provisioning degli utenti](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su Wrike. Per altre informazioni, vedere [controllare lo stato del provisioning dell'utente](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)
