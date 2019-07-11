---
title: 'Esercitazione: Configurare Zscaler Three per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler Three.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 93efb44668a3a0b189e96c1b1212766dee075ef3
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672837"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler Three per il provisioning utenti automatico

In questa esercitazione si apprenderà come configurare Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Zscaler Three.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e per risposte a domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questa esercitazione, sono necessari gli elementi seguenti:

* Un tenant di Azure AD.
* Un tenant di Zscaler Three.
* Un account utente in Zscaler Three con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API SCIM di Zscaler ZSCloud, disponibile per gli account Enterprise.

## <a name="adding-zscaler-three-from-the-gallery"></a>Aggiunta di Zscaler Three dalla raccolta

Prima di configurare Zscaler Three per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler Three dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

![Selezionare Azure Active Directory](common/select-azuread.png)

Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**:

![Applicazioni aziendali](common/enterprise-applications.png)

Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

![Selezionare Nuova applicazione](common/add-new-app.png)

Nella casella di ricerca immettere **Zscaler Three**. Selezionare **Zscaler Three** nei risultati e quindi **Aggiungi**.

![Elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Assegnare gli utenti a Zscaler Three

È necessario assegnare agli utenti di Azure AD l'accesso alle app selezionate per consentire loro di usarle. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zscaler Three. Dopo aver deciso, è possibile assegnare gli utenti e i gruppi a Zscaler Three seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler Three

* È consigliabile assegnare prima un singolo utente di Azure AD a Zscaler Three per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti e gruppi in un secondo momento.

* Quando si assegna un utente a Zscaler Three, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-automatic-user-provisioning"></a>Configurare il provisioning utenti automatico

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e gruppi in Zscaler Three in base alle assegnazioni di utenti e gruppi in Azure AD.

> [!TIP]
> Può anche essere opportuno abilitare per Zscaler Three l'accesso Single Sign-On basato su SAML. In tal caso, seguire le istruzioni riportate nell'[esercitazione per l'accesso Single Sign-On di Zscaler Three](zscaler-three-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, ma le due funzionalità sono complementari.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Zscaler Three**:

    ![Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler Three**:

    ![Elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**:

    ![Provisioning di Zscaler Three](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**:

    ![Impostare la modalità di provisioning](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali amministratore** specificare i campi **URL tenant** e **Token segreto** dell'account Zscaler Three, come descritto nel passaggio successivo.

6. Per ottenere i valori di **URL tenant** e **Token segreto**, scegliere **Administration** (Amministrazione)  >  **Authentication Settings** (Impostazioni di autenticazione) nel portale di Zscaler Three e selezionare **SAML** in **Authentication Type** (Tipo di autenticazione):

    ![Impostazioni di autenticazione di Zscaler Three](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Selezionare **Configure SAML** (Configura SAML) per aprire la finestra **Configure SAML** (Configura SAML):

    ![Finestra per la configurazione di SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Selezionare **Enable SCIM-Based Provisioning** (Abilita provisioning basato su SCIM), copiare il valore di **Base URL** (URL di base) e **Bearer Token** (Token di connessione) e quindi salvare le impostazioni. Nel portale di Azure incollare il valore di **Base URL** (URL di base) nella casella **URL tenant** e il valore di **Bearer Token** (Token di connessione) nella casella **Token segreto**.

7. Dopo aver immesso i valori nelle caselle **URL tenant** e **Token segreto**, selezionare **Test connessione** per verificare che Azure AD riesca a connettersi a Zscaler Three. Se la connessione non riesce, verificare che l'account Zscaler Three disponga di autorizzazioni di amministratore e riprovare.

    ![Testare la connessione](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare **Invia una notifica di posta elettronica in caso di errore**:

    ![Configurare l'indirizzo di posta elettronica per le notifiche](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to ZscalerThree** (Sincronizza utenti di Azure Active Directory in ZscalerThree):

    ![Sincronizzare gli utenti di Azure AD](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Zscaler Three. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler Three per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to ZscalerThree** (Sincronizza gruppi di Azure Active Directory con ZscalerThree):

    ![Sincronizzare i gruppi di Azure AD](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che vengono sincronizzati da Azure AD a Zscaler Three. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler Three per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zscaler Three, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**:

    ![Stato del provisioning](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Zscaler Three selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**:

    ![Valori di ambito](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**:

    ![Selezionare Salva](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti e gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che vengono eseguite circa ogni 40 minuti, purché il servizio di provisioning di Azure AD sia in esecuzione. È possibile monitorare lo stato di avanzamento nella sezione **Dettagli sincronizzazione**. È anche possibile selezionare i collegamenti a un report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD per Zscaler Three.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
