---
title: 'Esercitazione: Configurare Soloinsight-CloudGate SSO per il provisioning automatico degli utenti con Azure Active Directory . Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Soloinsight-CloudGate SSO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063196"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Esercitazione: Configurare Soloinsight-CloudGate SSO per il provisioning automatico degli utentiTutorial: Configure Soloinsight-CloudGate SSO for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Soloinsight-CloudGate SSO e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Soloinsight-CloudGate SSO.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Un tenant SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Un account utente in Soloinsight-CloudGate SSO con autorizzazioni di amministratore.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Assegnazione di utenti a Soloinsight-CloudGate SSO

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere a Soloinsight-CloudGate SSO. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a Soloinsight-CloudGate SSO seguendo le istruzioni riportate di seguito:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Suggerimenti importanti per l'assegnazione di utenti a Soloinsight-CloudGate SSO

* È consigliabile assegnare un singolo utente di Azure AD a Soloinsight-CloudGate SSO per testare la configurazione del provisioning automatico degli utenti. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Soloinsight-CloudGate SSO, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Configurare Soloinsight-CloudGate SSO per il provisioning

1. Accedere alla Console di [amministrazione SSO Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Passare ad **Amministrazione > Impostazioni di sistema**.

    ![Console di amministrazione SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Passare a **Generale**.

    ![Soloinsight-CloudGate SSO Aggiungi SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Scorrere verso il basso fino alla fine della pagina per ottenere **l'URL tenant** e il **token segreto**. Copiare il **token segreto**. Questo valore verrà immesso nel campo Token segreto nella scheda Provisioning dell'applicazione SSO Soloinsight-CloudGate nel portale di Azure.This value will be entered in the Secret Token field in the Provisioning tab of your Soloinsight-CloudGate SSO application in the Azure portal.

    ![Soloinsight-CloudGate SSO Crea token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Aggiungere Soloinsight-CloudGate SSO dalla galleria

Prima di configurare Soloinsight-CloudGate SSO per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere Soloinsight-CloudGate SSO dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere Soloinsight-CloudGate SSO dalla raccolta di applicazioni di Azure AD, eseguire la procedura seguente:To add Soloinsight-CloudGate SSO from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Soloinsight-CloudGate SSO**, selezionare **Soloinsight-CloudGate SSO** nel riquadro dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Soloinsight-CloudGate SSO nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Configurazione del provisioning automatico degli utenti per Soloinsight-CloudGate SSO 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Soloinsight-CloudGate SSO in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Soloinsight-CloudGate SSO , seguendo le istruzioni fornite [nell'esercitazione Soloinsight-CloudGate SSO Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per Soloinsight-CloudGate SSO in Azure AD:To configure automatic user provisioning for Soloinsight-CloudGate SSO in Azure AD:

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Soloinsight-CloudGate SSO**.

    ![Collegamento di Soloinsight-CloudGate SSO nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali di** `https://sigateway.com/scim/v2/sync/serviceproviderconfig` amministratore immettere **l'input**in URL tenant. Immettere il valore del token di **autenticazione SCIM** recuperato in precedenza in **Secret Token**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Soloinsight-CloudGate SSO. Se la connessione non riesce, verificare che l'account SSO Soloinsight-CloudGate disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Sincronizza utenti di Azure Active Directory con Soloinsight-CloudGate SSO**.

    ![Mapping utenti SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Soloinsight-CloudGate SSO nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con gli account utente in Soloinsight-CloudGate SSO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Nella sezione **Mapping** selezionare Sincronizza gruppi di Azure Active Directory con **Soloinsight-CloudGate SSO**.

    ![Mapping di gruppi SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Soloinsight-CloudGate SSO nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per trovare una corrispondenza con i gruppi in Soloinsight-CloudGate SSO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi del gruppo SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Soloinsight-CloudGate SSO, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for Soloinsight-CloudGate SSO, change the **Provisioning Status** to On in the **Settings** section.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in Soloinsight-CloudGate SSO scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Soloinsight-CloudGate SSO.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

