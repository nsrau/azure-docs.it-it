---
title: 'Esercitazione: Configurare Robin per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Robin Powered.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Robin Powered.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: fabd8a1953bedf6c3db6da443903a6dbd965b01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060990"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Esercitazione: Configurare Robin per il provisioning automatico degli utentiTutorial: Configure Robin for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Robin and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Robin.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un inquilino Robin](https://robinpowered.com/pricing/)
* Un account utente in Robin con autorizzazioni di amministratore.

## <a name="assigning-users-to-robin"></a>Assegnazione di utenti a RobinAssigning users to Robin

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Robin. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Robin seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Suggerimenti importanti per l'assegnazione di utenti a Robin

* È consigliabile assegnare un singolo utente di Azure AD a Robin per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Robin, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-robin-for-provisioning"></a>Configurare Robin per il provisioning

1. Accedi a [Robin Admin Console.](https://dashboard.robinpowered.com/login) Passare a **Gestisci integrazioni > > SCI >M Gestisci**.

    ![Console di amministrazione con robin](media/robin-provisioning-tutorial/robin-admin.png)

2.  Generare un nuovo token dell'organizzazione. Se si perde questo token, è sempre possibile crearne uno nuovo senza influire sugli utenti esistenti.

    ![robin alimentato Aggiungi SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  Copiare il token di **autenticazione SCIM**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione Robin nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Robin application in the Azure portal.



## <a name="add-robin-from-the-gallery"></a>Aggiungi Robin dalla galleria

Prima di configurare Robin per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Robin dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Robin dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Robin from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Robin**, selezionare **Robin** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Robin nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Configurazione del provisioning automatico degli utenti in Robin 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Robin in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Robin , seguendo le istruzioni fornite [nell'esercitazione Robin Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Robin in Azure AD:To configure automatic user provisioning for Robin in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Robin**.

    ![Il collegamento con robin nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://api.robinpowered.com/v1.0/scim-2` amministratore immettere **l'input**in URL tenant. Immettere il valore del token di **autenticazione SCIM** recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Robin.Click **Test Connection** to ensure Azure AD can connect to Robin. Se la connessione non riesce, assicurati che il tuo account Robin disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Robin.**

    ![Mappature utente alimentate da robin](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Robin nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Robin per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente alimentati a pettese](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Robin.**

    ![mappature di gruppo alimentate da robin](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Robin nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Robin per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi di gruppo alimentati da robin](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Robin, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Robin, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Robin scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Robin.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Robin.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).



## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

