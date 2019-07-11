---
title: 'Esercitazione: Configurare Zscaler Two per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler Two.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0147f0bea30a13cea17baab8d40e550c0910a3fa
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672823"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler Two per il provisioning utenti automatico

In questa esercitazione si apprenderà come configurare Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Zscaler Two.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e per risposte a domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questa esercitazione, sono necessari gli elementi seguenti:

* Un tenant di Azure AD.
* Un tenant di Zscaler Two.
* Un account utente in Zscaler Two con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API SCIM di Zscaler Two, disponibile per gli account Enterprise.

## <a name="add-zscaler-two-from-the-gallery"></a>Aggiungere Zscaler Two dalla raccolta

Prima di configurare Zscaler Two per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler Two dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

![Selezionare Azure Active Directory](common/select-azuread.png)

Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**:

![Applicazioni aziendali](common/enterprise-applications.png)

Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

![Selezionare Nuova applicazione](common/add-new-app.png)

Nella casella di ricerca immettere **Zscaler Two**. Selezionare **Zscaler Two** nei risultati e quindi **Aggiungi**.

![Elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Assegnare gli utenti a Zscaler Two

È necessario assegnare agli utenti di Azure AD l'accesso alle app selezionate per consentire loro di usarle. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zscaler Two. Dopo aver deciso, è possibile assegnare gli utenti e i gruppi a Zscaler Two seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler Two

* È consigliabile assegnare prima un singolo utente di Azure AD a Zscaler Two per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti e gruppi in un secondo momento.

* Quando si assegna un utente a Zscaler Two, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-automatic-user-provisioning"></a>Configurare il provisioning utenti automatico

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e gruppi in Zscaler Two in base alle assegnazioni di utenti e gruppi in Azure AD.

> [!TIP]
> Può anche essere opportuno abilitare per Zscaler Two l'accesso Single Sign-On basato su SAML. In tal caso, seguire le istruzioni riportate nell'[esercitazione per l'accesso Single Sign-On di Zscaler Two](zscaler-two-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, ma le due funzionalità sono complementari.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Zscaler Two**:

    ![Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler Two**:

    ![Elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**:

    ![Provisioning di Zscaler Two](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**:

    ![Impostare la modalità di provisioning](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali amministratore** specificare i campi **URL tenant** e **Token segreto** dell'account Zscaler Two, come descritto nel passaggio successivo.

6. Per ottenere i valori di **URL tenant** e **Token segreto**, scegliere **Administration** (Amministrazione)  >  **Authentication Settings** (Impostazioni di autenticazione) nel portale di Zscaler Two e selezionare **SAML** in **Authentication Type** (Tipo di autenticazione):

    ![Impostazioni di autenticazione di Zscaler Two](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Selezionare **Configure SAML** (Configura SAML) per aprire la finestra **Configure SAML** (Configura SAML):

    ![Finestra per la configurazione di SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Selezionare **Enable SCIM-Based Provisioning** (Abilita provisioning basato su SCIM), copiare il valore di **Base URL** (URL di base) e **Bearer Token** (Token di connessione) e quindi salvare le impostazioni. Nel portale di Azure incollare il valore di **Base URL** (URL di base) nella casella **URL tenant** e il valore di **Bearer Token** (Token di connessione) nella casella **Token segreto**.

7. Dopo aver immesso i valori nelle caselle **URL tenant** e **Token segreto**, selezionare **Test connessione** per verificare che Azure AD riesca a connettersi a Zscaler Two. Se la connessione non riesce, verificare che l'account Zscaler Two disponga di autorizzazioni di amministratore e riprovare.

    ![Testare la connessione](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare **Invia una notifica di posta elettronica in caso di errore**:

    ![Configurare l'indirizzo di posta elettronica per le notifiche](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to ZscalerTwo** (Sincronizza utenti di Azure Active Directory in ZscalerTwo):

    ![Sincronizzare gli utenti di Azure AD](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Zscaler Two. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler Two per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to ZscalerTwo** (Sincronizza gruppi di Azure Active Directory con ZscalerTwo):

    ![Sincronizzare i gruppi di Azure AD](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che vengono sincronizzati da Azure AD a Zscaler Two. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler Two per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zscaler Two, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**:

    ![Stato del provisioning](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Zscaler Two selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**:

    ![Valori di ambito](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**:

    ![Selezionare Salva](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti e gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che vengono eseguite circa ogni 40 minuti, purché il servizio di provisioning di Azure AD sia in esecuzione. È possibile monitorare lo stato di avanzamento nella sezione **Dettagli sincronizzazione**. È anche possibile selezionare i collegamenti a un report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD per Zscaler Two.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
