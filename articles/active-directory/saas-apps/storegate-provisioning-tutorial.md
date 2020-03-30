---
title: 'Esercitazione: Configurare Storegate per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Storegate.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064258"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Esercitazione: Configurare Storegate per il provisioning automatico degli utentiTutorial: Configure Storegate for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Storegate e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi per Storegate.The objective of this tutorial is to demonstrate the steps to be performed in Storegate and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and/o provision users to Storegate.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Storegate](https://www.storegate.com)
* Un account utente in un Archivio Storegate con autorizzazioni di amministratore.

## <a name="assign-users-to-storegate"></a>Assegnare gli utenti a StoregateAssign users to Storegate

Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Storegate. Una volta deciso, puoi assegnare questi utenti e/o gruppi a Storegate seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Suggerimenti importanti per l'assegnazione di utenti a Storegate

* È consigliabile assegnare un singolo utente di Azure AD a Storegate per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Storegate, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-storegate-for-provisioning"></a>Configurare Storegate per il provisioning

Prima di configurare Storegate per il provisioning automatico degli utenti con Azure AD, è necessario recuperare alcune informazioni di provisioning da Storegate.Before configuring Storegate for automatic user provisioning with Azure AD, you will need to retrieve some provisioning information from Storegate.

1. Accedi alla [Console di amministrazione Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e vai alle impostazioni facendo clic sull'icona dell'utente nell'angolo in alto a destra e seleziona **Impostazioni account**.

    ![Storegate Aggiungi SCIM](media/storegate-provisioning-tutorial/admin.png)

2. All'interno delle impostazioni passare a **Impostazioni > team** e verificare che l'interruttore di attivazione/disattivazione sia attivato nella sezione Single **Sign-On.**

    ![Impostazioni Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Pulsante di attivazione/disattivazione Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copiare **l'URL** tenant e il **token**. Questi valori verranno immessi rispettivamente nei campi URL tenant e **Token segreto** rispettivamente nella scheda Provisioning dell'applicazione Storegate nel portale di Azure.These values will be entered in the **Tenant URL** and Secret Token fields respectively in the Provisioning tab of your Storegate application in the Azure portal. 

    ![Token di creazione StoregateStoregate Create Token](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Aggiungi Storegate dalla raccolta

Per configurare Storegate per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Storegate dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immetti **Storegate**, seleziona **Storegate** nel pannello dei risultati. 

    ![Storegate nell'elenco dei risultati](common/search-new-app.png)

5. Seleziona il pulsante **Iscriviti a Storegate** che ti reindirizzerà alla pagina di accesso di Storegate. 

    ![Storegate OIDC Aggiungi](media/storegate-provisioning-tutorial/signup.png)

6.  Accedi alla [Console di amministrazione Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e vai alle impostazioni facendo clic sull'icona dell'utente nell'angolo in alto a destra e seleziona **Impostazioni account**.

    ![Accesso Storegate](media/storegate-provisioning-tutorial/admin.png)

7. All'interno delle impostazioni passa a **Impostazioni > Team** e fai clic sull'interruttore attiva/disattiva nella sezione Single Sign-On, questo avvierà il flusso di consenso. Fare clic su **Attiva**.

    ![Squadra Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Attivazione Storegate](media/storegate-provisioning-tutorial/activate.png)

8. Poiché Storegate è un'app OpenIDConnect, scegli di accedere a Storegate usando il tuo account di lavoro Microsoft.

    ![Accesso OIDC Storegate](media/storegate-provisioning-tutorial/login.png)

9. Dopo una corretta autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e verrà reindirizzato all'account Storegate.

    ![Consenso OIDc Storegate](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configurare il provisioning automatico degli utenti per StoregateConfigure automatic user provisioning to Storegate 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Storegate in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per ulteriori informazioni sull'endpoint SCIM di Storegate, fare riferimento a [questa opzione](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Storegate in Azure ADTo configure automatic user provisioning for Storegate in Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Storegate**.

    ![Collegamento Storegate nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://dialpad.com/scim` amministratore immettere **l'input**in URL tenant. Immettere il valore recuperato e salvato in precedenza da Storegate in **Secret Token**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Storegate.Click **Test Connection** to ensure Azure AD can connect to Storegate. Se la connessione non riesce, assicurati che l'account Storegate disponga delle autorizzazioni di amministratore e riprova.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory da archiviare**.

    ![Mapping utenti Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Storegate nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Storegate per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per Storegate, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Storegate, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Storegate scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Storegate.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Storegate.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
