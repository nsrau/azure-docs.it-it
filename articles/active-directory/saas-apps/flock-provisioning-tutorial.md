---
title: 'Esercitazione: Configurare Flock per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Flock.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Flock.
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
ms.openlocfilehash: cd7aae05b064657c7b9072402f4bc4d4d7fef7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057865"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>Esercitazione: Configurare Flock per il provisioning automatico degli utentiTutorial: Configure Flock for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Flock and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Flock.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un affittuario Flock](https://flock.com/pricing/)
* Un account utente in Flock con autorizzazioni di amministratore.

## <a name="assigning-users-to-flock"></a>Assegnazione di utenti a Flock 

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Flock. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Flock seguendo le istruzioni qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>Suggerimenti importanti per l'assegnazione di utenti a Flock 

* È consigliabile assegnare a Flock un singolo utente di Azure AD per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Flock, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="setup-flock--for-provisioning"></a>Configurazione di Flock per il provisioning

Prima di configurare Flock per il provisioning automatico degli utenti con Azure AD, è necessario abilitare il provisioning SCIM in Flock.

1. Accedere a [Flock](https://web.flock.com/?). Fare clic su **Icona** > impostazioni**Gestisci il team**.

    ![Flock](media/flock-provisioning-tutorial/icon.png)

2. Selezionare **Autenticazione e provisioning**.

    ![Flock](media/Flock-provisioning-tutorial/auth.png)

3. Copiare il **token API**. Questi valori verranno immessi nel campo **Token segreto** nella scheda Provisioning dell'applicazione Flock nel portale di Azure.These values will be entered in the Secret Token field in the Provisioning tab of your Flock application in the Azure portal.

    ![Flock](media/Flock-provisioning-tutorial/provisioning.png)


## <a name="add-flock--from-the-gallery"></a>Aggiungi Flock dalla galleria

Per configurare Flock per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Flock dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Flock dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Flock from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Flock**, selezionare **Flock** nel riquadro dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Flock nell'elenco dei risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Configurazione del provisioning automatico degli utenti su Flock  

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Flock in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Flock, seguendo le istruzioni fornite nell'esercitazione Single [Sign-On](Flock-tutorial.md)di Flock . Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Flock in Azure AD:To configure automatic user provisioning for Flock in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Flock**.

    ![Il collegamento Flock nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella `https://api.flock-staging.com/v2/scim` sezione Credenziali amministratore immettere i valori del token **API** e recuperati in precedenza rispettivamente in URL **tenant** e token **segreto.** Fare clic su Test connessione per verificare che Azure AD possa connettersi a Flock.Click **Test Connection** to ensure Azure AD can connect to Flock. Se la connessione non riesce, assicurati che l'account Flock disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con flock**.

    ![Mappature degli utenti di Flock](media/flock-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Flock nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Flock per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Flock](media/flock-provisioning-tutorial/userattribute.png)

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Per abilitare il servizio di provisioning di Azure AD per Flock, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Flock, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

13. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Flock scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Per ulteriori informazioni sul tempo necessario per il provisioning degli utenti e/o dei gruppi, vedere [Quanto tempo è necessario per eseguire il provisioning degli utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione Stato corrente per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su Flock.You can use the **Current Status** section to monitor progress and follow links to your provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Flock. Per ulteriori informazioni, consultate [Controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) Per leggere i log di provisioning di Azure AD, vedere Creazione di report sul provisioning automatico degli [account utente.](../app-provisioning/check-status-user-account-provisioning.md)



## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)