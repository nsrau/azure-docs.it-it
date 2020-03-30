---
title: 'Esercitazione: Configurare SpaceIQ per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in SpaceIQ.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to SpaceIQ.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 207c8214-6304-4687-afc6-61562f0041a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: e59e9d86f394104752ef966b2a9cad2b78a1bc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062773"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Esercitazione: Configurare SpaceIQ per il provisioning automatico degli utentiTutorial: Configure SpaceIQ for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in SpaceIQ and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to SpaceIQ.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant SpaceIQ](https://spaceiq.com/)
* Un account utente in SpaceIQ con autorizzazioni di amministratore.

## <a name="assigning-users-to-spaceiq"></a>Assegnazione di utenti a SpaceIQAssigning users to SpaceIQ

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a SpaceIQ. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a SpaceIQ seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Suggerimenti importanti per l'assegnazione di utenti a SpaceIQ

* È consigliabile assegnare un singolo utente di Azure AD a SpaceIQ per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a SpaceIQ, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-spaceiq-for-provisioning"></a>Configurare SpaceIQ per il provisioning

1. Accedi alla [Console di amministrazione di SpaceIQ](https://main.spaceiq.com/login/). Passare a **Impostazioni** selezionandolo dal menu a discesa nell'angolo in alto a destra dello schermo.

    ![Console di amministrazione SpaceIQ](media/spaceiq-provisioning-tutorial/admin.png)

2.  Dalla pagina **Impostazioni** Selezionare **integrazioni di terze parti**.

    ![SpaceIQ Aggiungi SCIM](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Passare alla scheda **Provisioning e SSO.** Cercare il riquadro **di Azure.Navigate** to Provisioning and SSO tab. Search for the Azure tile. Fare clic su **Attiva**.

    ![Provisioning SpaceIQ e SSO](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ Attiva Azure ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Copiare il **token di bearer SCIM**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione SpaceIQ nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your SpaceIQ application in the Azure portal. Fare clic su **Attiva**

    ![SpaceIQ Crea token](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>Aggiungi SpaceIQ dalla galleria

Prima di configurare SpaceIQ per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere SpaceIQ dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere SpaceIQ dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add SpaceIQ from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **SpaceIQ**, selezionare **SpaceIQ** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![SpaceIQ nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Configurazione del provisioning automatico degli utenti in SpaceIQ 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in SpaceIQ in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per SpaceIQ , seguendo le istruzioni fornite [nell'esercitazione sul single sign-on SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-tutorial). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per SpaceIQ in Azure AD:To configure automatic user provisioning for SpaceIQ in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **SpaceIQ**.

    ![Collegamento di SpaceIQ nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://api.spaceiq.com/scim` amministratore immettere **l'input**in URL tenant. Immettere il valore del token di **autenticazione SCIM** recuperato in precedenza in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a SpaceIQ.Click **Test Connection** to ensure Azure AD can connect to SpaceIQ. Se la connessione non riesce, verificare che l'account SpaceIQ disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con SpaceIQ**.

    ![Mapping utenti SpaceIQ](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a SpaceIQ nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in SpaceIQ per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente SpaceIQ](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per SpaceIQ, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for SpaceIQ, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in SpaceIQ scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in SpaceIQ.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on SpaceIQ.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)