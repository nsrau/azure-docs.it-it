---
title: 'Esercitazione: Configurare Looop per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Looop.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057440"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Esercitazione: Configurare Looop per il provisioning automatico degli utentiTutorial: Configure Looop for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Looop e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Looop.The objective of this tutorial is to demonstrate the steps to be performed in Looop and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users to Looop.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un inquilino Looop](https://www.looop.co/pricing/)
* Un account utente su un Looop con autorizzazioni di amministratore.

## <a name="assign-users-to-looop"></a>Assegnare gli utenti a LooopAssign users to Looop

Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Looop. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Looop seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Suggerimenti importanti per l'assegnazione di utenti a Looop

* È consigliabile assegnare un singolo utente di Azure AD a Looop per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Looop, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-looop-for-provisioning"></a>Configurare Looop per il provisioning

Prima di configurare Looop per il provisioning automatico degli utenti con Azure AD, è necessario recuperare alcune informazioni di provisioning da Looop.

1. Accedi a [Looop Admin Console](https://app.looop.co/#/login) e seleziona **Account**. In **Impostazioni account** selezionare **Autenticazione**.

    ![Looop Aggiungi SCIM](media/looop-provisioning-tutorial/admin.png)

2. Generare un nuovo token facendo clic su **Reimposta token** in **Integrazione SCIM**.

    ![Looop Aggiungi SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Copiare **l'endpoint SCIM** e il **token**. Questi valori verranno immessi nei campi URL tenant e **Token segreto** nella scheda Provisioning dell'applicazione Looop nel portale di Azure.These values will be entered in the **Tenant URL** and Secret Token fields in the Provisioning tab of your Looop application in the Azure portal. 

    ![Token di creazione Looop](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Aggiungi Looop dalla galleria

Per configurare Looop per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Looop dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Looop**, selezionare **Looop** nel pannello dei risultati. 

    ![Looop nell'elenco dei risultati](common/search-new-app.png)

5. Seleziona il pulsante **Iscriviti a Looop** per reindirizzarti alla pagina di accesso di Looop. 

    ![Looop OIDC Aggiungi](media/looop-provisioning-tutorial/signup.png)

6. Poiché Looop è un'app OpenIDConnect, scegli di accedere a Looop utilizzando il tuo account aziendale Microsoft.

    ![Accesso OIDC Looop](media/looop-provisioning-tutorial/msftlogin.png)

7. Dopo una corretta autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e verrà reindirizzato all'account Looop.

    ![Consenso OIDc di Looop](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configurare il provisioning automatico degli utenti in LooopConfigure automatic user provisioning to Looop 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Looop in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Looop in Azure AD:To configure automatic user provisioning for Looop in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Looop**.

    ![Il collegamento Looop nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://<organisation_domain>.looop.co/scim/v2` amministratore immettere **l'input**in URL tenant. Ad esempio `https://demo.looop.co/scim/v2`. Immettere il valore recuperato e salvato in precedenza da Looop in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Looop.Click **Test Connection** to ensure Azure AD can connect to Looop. Se la connessione non riesce, verificare che l'account Looop disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory con Looop**.

    ![Mapping degli utenti di Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Looop nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Looop per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Looop](media/looop-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con Meta Networks Connector**.

    ![Mapping dei gruppi Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Meta Networks Connector nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Meta Networks Connector per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo Looop](media/looop-provisioning-tutorial/groupattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Looop, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Looop, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Looop scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Looop.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Looop.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)


