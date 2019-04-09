---
title: 'Esercitazione: Configurare BlueJeans per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fedb372fb245b7bc35cb440bd758336ab2a68
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057626"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Esercitazione: Configurare BlueJeans per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in BlueJeans e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in BlueJeans.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Un tenant di BlueJeans con il piano [My Company](https://www.BlueJeans.com/pricing) o superiore abilitato
* Un account utente in BlueJeans con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API di BlueJeans](https://BlueJeans.github.io/developer), disponibile per i team di BlueJeans con piano Standard o superiore.

## <a name="adding-bluejeans-from-the-gallery"></a>Aggiunta di BlueJeans dalla raccolta

Prima di configurare BlueJeans per il provisioning utenti automatico con Azure AD, è necessario aggiungere BlueJeans dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere BlueJeans dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel  **[portale di Azure](https://portal.azure.com)**, nel riquadro di spostamento sinistro, selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare la **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **BlueJeans**, selezionare **BlueJeans** nel pannello dei risultati e quindi selezionare il **Add** pulsante per aggiungere l'applicazione.

    ![BlueJeans nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Assegnazione di utenti a BlueJeans

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a BlueJeans. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a BlueJeans seguendo le istruzioni fornite qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Suggerimenti importanti per l'assegnazione di utenti a BlueJeans

* È consigliabile assegnare un singolo utente di Azure AD a BlueJeans per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a BlueJeans, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configurazione del provisioning utenti automatico per BlueJeans

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in BlueJeans in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per BlueJeans, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per BlueJeans](bluejeans-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Per configurare il provisioning utenti automatico per BlueJeans in Azure AD:

1. Accedi per il [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **BlueJeans**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **BlueJeans**.

    ![Collegamento a BlueJeans nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Nome utente amministratore** e **Password amministratore** dell'account BlueJeans. Ecco alcuni esempi di questi valori:

   * Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di BlueJeans. Esempio: admin@contoso.com.

   * Nel campo **Password amministratore** inserire la password corrispondente al nome utente amministratore.

6. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a BlueJeans. Se la connessione non riesce, verificare che l'account BlueJeans abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Fare clic su **Save**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to BlueJeans** (Sincronizza utenti di Azure Active Directory con BlueJeans).

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Esaminare gli attributi utente sincronizzati da Azure AD a BlueJeans nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in BlueJeans per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per BlueJeans, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in BlueJeans selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning per BlueJeans](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in BlueJeans.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* BlueJeans non consente nomi utente che superano i 30 caratteri.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione di provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
