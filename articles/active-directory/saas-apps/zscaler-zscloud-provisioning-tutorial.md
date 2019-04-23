---
title: 'Esercitazione: Configurare Zscaler ZSCloud per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: In questa esercitazione si apprenderà come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler ZSCloud.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 13231fe75ff173999f3a7fa4728f583c6f04c54d
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579173"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zscaler ZSCloud per il provisioning utenti automatico

In questa esercitazione si apprenderà come configurare Azure Active Directory (Azure AD) per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Zscaler ZSCloud.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e per risposte a domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questa esercitazione, sono necessari gli elementi seguenti:

* Un tenant di Azure AD.
* Un tenant di Zscaler ZSCloud.
* Un account utente in Zscaler ZSCloud con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API SCIM di Zscaler ZSCloud, disponibile per gli account Enterprise.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Aggiungere Zscaler ZSCloud dalla raccolta

Prima di configurare Zscaler ZSCloud per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler ZSCloud dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro sinistro:

![Selezionare Azure Active Directory](common/select-azuread.png)

Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**:

![Applicazioni aziendali](common/enterprise-applications.png)

Per aggiungere un'applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra:

![Selezionare Nuova applicazione](common/add-new-app.png)

Nella casella di ricerca immettere **Zscaler ZSCloud**. Selezionare **Zscaler ZSCloud** nei risultati e quindi **Aggiungi**.

![Elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Assegnare gli utenti a Zscaler ZSCloud

È necessario assegnare agli utenti di Azure AD l'accesso alle app selezionate per consentire loro di usarle. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zscaler ZSCloud. Dopo aver deciso, è possibile assegnare gli utenti e i gruppi a Zscaler SCloud seguendo le istruzioni riportate in [Assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler ZSCloud

* È consigliabile assegnare prima un singolo utente di Azure AD a Zscaler ZSCloud per testare la configurazione del provisioning utenti automatico. Sarà possibile assegnare altri utenti e gruppi in un secondo momento.

* Quando si assegna un utente a Zscaler ZSCloud, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-automatic-user-provisioning"></a>Configurare il provisioning utenti automatico

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e gruppi in Zscaler ZSCloud in base alle assegnazioni di utenti e gruppi in Azure AD.

> [!TIP]
> Può anche essere opportuno abilitare per Zscaler ZSCloud l'accesso Single Sign-On basato su SAML. In tal caso, seguire le istruzioni riportate nell'[esercitazione per l'accesso Single Sign-On di Zscaler ZSCloud](zscaler-zsCloud-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, ma le due funzionalità sono complementari.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **Zscaler ZSCloud**:

    ![Applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler ZSCloud**:

    ![Elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**:

    ![Provisioning di Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Impostare **Modalità di provisioning** su **Automatico**:

    ![Impostare la modalità di provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Nella sezione **Credenziali amministratore** specificare i campi **URL tenant** e **Token segreto** dell'account di Zscaler ZSCloud, come descritto nel passaggio successivo.

6. Per ottenere i valori di **URL tenant** e **Token segreto**, scegliere **Administration** (Amministrazione)  >  **Authentication Settings** (Impostazioni di autenticazione) nel portale di Zscaler ZSCloud e selezionare **SAML** in **Authentication Type** (Tipo di autenticazione):

    ![Impostazioni di autenticazione di Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Selezionare **Configure SAML** (Configura SAML) per aprire la finestra **Configure SAML** (Configura SAML):

    ![Finestra per la configurazione di SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Selezionare **Enable SCIM-Based Provisioning** (Abilita provisioning basato su SCIM), copiare il valore di **Base URL** (URL di base) e **Bearer Token** (Token di connessione) e quindi salvare le impostazioni. Nel portale di Azure incollare il valore di **Base URL** (URL di base) nella casella **URL tenant** e il valore di **Bearer Token** (Token di connessione) nella casella **Token segreto**.

7. Dopo aver immesso i valori nelle caselle **URL tenant** e **Token segreto**, selezionare **Test connessione** per verificare che Azure AD riesca a connettersi a Zscaler ZSCloud. Se la connessione non riesce, verificare che l'account Zscaler ZSCloud disponga di autorizzazioni di amministratore e riprovare.

    ![Testare la connessione](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Nel campo **Indirizzo di posta elettronica per le notifiche** immettere l'indirizzo di posta elettronica di una persona o di un gruppo che riceverà le notifiche di errore relative al provisioning. Selezionare **Invia una notifica di posta elettronica in caso di errore**:

    ![Configurare l'indirizzo di posta elettronica per le notifiche](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to ZscalerZSCloud** (Sincronizza utenti di Azure Active Directory in ZscalerZSCloud):

    ![Sincronizzare gli utenti di Azure AD](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Zscaler ZSCloud. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zscaler ZSCloud per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to ZscalerZSCloud** (Sincronizza gruppi di Azure Active Directory con ZscalerZSCloud):

    ![Sincronizzare i gruppi di Azure AD](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che vengono sincronizzati da Azure AD a Zscaler ZSCloud. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zscaler ZSCloud per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    ![Mapping degli attributi](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni riportate nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zscaler ZSCloud, impostare **Stato del provisioning** su **Attivato** nella sezione **Impostazioni**:

    ![Stato del provisioning](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Zscaler ZSCloud selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**:

    ![Valori di ambito](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **Salva**:

    ![Selezionare Salva](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti e gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che vengono eseguite circa ogni 40 minuti, purché il servizio di provisioning di Azure AD sia in esecuzione. È possibile monitorare lo stato di avanzamento nella sezione **Dettagli sincronizzazione**. È anche possibile selezionare i collegamenti a un report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD per Zscaler ZSCloud.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
