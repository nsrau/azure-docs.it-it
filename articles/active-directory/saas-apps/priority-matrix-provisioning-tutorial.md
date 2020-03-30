---
title: 'Esercitazione: Configurare la matrice di priorità per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Priority Matrix.Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Priority Matrix.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063442"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Esercitazione: Configurare la matrice di priorità per il provisioning automatico degli utentiTutorial: Configure Priority Matrix for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Matrice di priorità e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Matrice di priorità.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant Matrice di priorità](https://appfluence.com/pricing/)
* Un account utente in una matrice di priorità con autorizzazioni di amministratore.

## <a name="assign-users-to-priority-matrix"></a>Assegnazione di utenti a matrice di prioritàAssign users to Priority Matrix

Azure Active Directory usa un concetto denominato assegnazioni per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a La matrice di priorità. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Matrice di priorità seguendo le istruzioni riportate di seguito:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Suggerimenti importanti per l'assegnazione di utenti a Priority Matrix

* È consigliabile assegnare un singolo utente di Azure AD alla matrice di priorità per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Priority Matrix, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-priority-matrix-for-provisioning"></a>Impostare la matrice di priorità per il provisioning

Prima di configurare la matrice di priorità per il provisioning automatico degli utenti con Azure AD, è necessario recuperare alcune informazioni di provisioning da Matrice di priorità.

1. Accedere alla [Console di amministrazione matrice di priorità](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Fare clic su **Token di accesso Oauth** per la matrice di priorità

    ![Matrice di priorità Aggiungere SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Fare clic sul pulsante **GET NEW TOKEN (OTTIENI NUOVO TOKEN).** Copiare la **stringa del token**. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Matrice di priorità nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Priority Matrix application in the Azure portal. 

    ![Token di creazione matrice di prioritàPriority Matrix Create Token](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Aggiungi matrice di priorità dalla raccolta

Per configurare la matrice di priorità per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere la matrice di priorità dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Matrice di priorità**, selezionare Matrice di **priorità** nel riquadro dei risultati. 

    ![Matrice di priorità nell'elenco dei risultati](common/search-new-app.png)

5. Seleziona il pulsante **Iscriviti alla matrice** di priorità per reindirizzarti alla pagina di accesso di Priority Matrix. 

    ![Matrice di priorità OIDC Aggiungi](media/priority-matrix-provisioning-tutorial/signup.png)

6. Poiché la matrice di priorità è un'app OpenIDConnect, scegliere di accedere a Priority Matrix utilizzando l'account di lavoro Microsoft.

    ![Accesso OIDC a matrice di priorità](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Dopo una corretta autenticazione, accettare la richiesta di consenso per la pagina di consenso. L'applicazione verrà quindi aggiunta automaticamente al tenant e verrà reindirizzato all'account Matrice di priorità.

    ![Consenso OIDc matrice di priorità](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Configurare il provisioning automatico degli utenti nella matrice di prioritàConfigure automatic user provisioning to Priority Matrix 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Matrice di priorità in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!NOTE]
> Per ulteriori informazioni sull'endpoint SCIM di Priority Matrix, vedere [Provisioning degli utenti e matrice di priorità](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per la matrice di priorità in Azure AD:To configure automatic user provisioning for Priority Matrix in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Matrice di priorità**.

    ![Il collegamento Matrice di priorità nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://sync.appfluence.com/scim/v2/` amministratore immettere **l'input**in URL tenant. Immettere il valore recuperato e salvato in precedenza da Matrice di priorità in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Una matrice di priorità. Se la connessione non riesce, verificare che l'account Matrice di priorità disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza gli utenti di Azure Active Directory con la matrice di **priorità.**

    ![Mapping utente matrice di priorità](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Matrice di priorità nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Matrice di priorità per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente matrice di priorità](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Per abilitare il servizio di provisioning di Azure AD per la matrice di priorità, modificare lo stato di provisioning su Attivato nella sezione Impostazioni.To enable the Azure AD provisioning service for Priority Matrix, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

12. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Matrice di priorità scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

13. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su Priority Matrix.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Priority Matrix.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)


