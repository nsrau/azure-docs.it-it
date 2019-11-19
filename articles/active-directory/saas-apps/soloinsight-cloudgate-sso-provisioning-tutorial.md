---
title: "Esercitazione: configurare l'accesso SSO Soloinsight-CloudGate per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs"
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Soloinsight-CloudGate SSO.
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
ms.openlocfilehash: 0d16c5e811de99bc9f9e35cbd023db51d5997bb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152710"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Esercitazione: configurare l'accesso SSO Soloinsight-CloudGate per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Soloinsight-CloudGate SSO e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Soloinsight-CloudGate SSO.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD
* [Tenant SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Un account utente in Soloinsight-CloudGate SSO con autorizzazioni di amministratore.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Assegnazione di utenti a Soloinsight-CloudGate SSO

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a Soloinsight-CloudGate SSO. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a Soloinsight-CloudGate SSO seguendo le istruzioni riportate qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Suggerimenti importanti per l'assegnazione di utenti a Soloinsight-CloudGate SSO

* È consigliabile assegnare un singolo utente Azure AD a Soloinsight-CloudGate SSO per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Soloinsight-CloudGate SSO, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Configurare Soloinsight-CloudGate SSO per il provisioning

1. Accedere alla console di [Amministrazione SSO Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Passare ad **amministrazione > impostazioni di sistema**.

    ![Soloinsight-console di amministrazione SSO CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Passare a **generale**.

    ![Soloinsight-CloudGate-SSO aggiungere SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Scorrere fino alla fine della pagina per ottenere l'URL del **tenant** e il **token segreto**. Copiare il **token Secret**. Questo valore verrà immesso nel campo token segreto nella scheda provisioning dell'applicazione Soloinsight-CloudGate SSO nell'portale di Azure.

    ![Soloinsight-CloudGate-crea token SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Aggiungere Soloinsight-CloudGate SSO dalla raccolta

Prima di configurare Soloinsight-CloudGate SSO per il provisioning utenti automatico con Azure AD, è necessario aggiungere Soloinsight-CloudGate SSO dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Soloinsight-CloudGate SSO dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Soloinsight-CLOUDGATE SSO**, selezionare **SOLOINSIGHT-CloudGate SSO** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Soloinsight-CloudGate SSO nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Configurazione del provisioning utenti automatico in Soloinsight-CloudGate SSO 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Soloinsight-CloudGate SSO in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'Single Sign-On basata su SAML per Soloinsight-CloudGate SSO, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on SSO Soloinsight-CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Soloinsight-CloudGate SSO in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Soloinsight-CloudGate SSO**.

    ![Collegamento di Soloinsight-CloudGate SSO nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **credenziali amministratore** immettere `https://sigateway.com/scim/v2/sync/serviceproviderconfig` in **URL tenant**. Immettere il valore del **token di autenticazione scim** recuperato in precedenza in **token segreto**. Fare clic su **Test connessione** per verificare che Azure ad possibile connettersi a Soloinsight-CloudGate SSO. Se la connessione non riesce, verificare che l'account SSO Soloinsight-CloudGate disponga delle autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Save**.

8. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a SOLOINSIGHT-CloudGate SSO**.

    ![Soloinsight-mapping utente SSO CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Soloinsight-CloudGate SSO nella sezione **mapping attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Soloinsight-CloudGate SSO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Soloinsight-CloudGate-attributi utente SSO](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to SOLOINSIGHT-CloudGate SSO**.

    ![Soloinsight-mapping dei gruppi SSO CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Soloinsight-CloudGate SSO nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Soloinsight-CloudGate SSO per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Soloinsight-attributi del gruppo SSO CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning Azure AD per Soloinsight-CloudGate SSO, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Soloinsight-CloudGate SSO scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning Azure ad in Soloinsight-CloudGate SSO.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

