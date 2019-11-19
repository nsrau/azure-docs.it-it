---
title: 'Esercitazione: Configurare G Suite per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: beaa8561028a9e21d0623c0eb8e19592f3cad055
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167871"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Esercitazione: Configurare G Suite per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in G Suite e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in G Suite.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> Il connettore G Suite è stato aggiornato di recente il 2019 ottobre. Le modifiche apportate al connettore G Suite includono:
- Aggiunta del supporto per altri attributi di gruppo e utente di G Suite. 
- I nomi degli attributi di destinazione di G Suite sono stati aggiornati in base a quanto definito [qui](https://developers.google.com/admin-sdk/directory).
- Mapping degli attributi predefiniti aggiornati.

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con G Suite, sono necessari gli elementi seguenti:

- Un tenant di Azure AD
- [Tenant di G Suite](https://gsuite.google.com/pricing.html)
- Un account utente in una suite G con autorizzazioni di amministratore.

## <a name="assign-users-to-g-suite"></a>Assegnare utenti a G Suite

Azure Active Directory usa un concetto denominato assegnazioni per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a G Suite. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a G Suite seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Suggerimenti importanti per l'assegnazione di utenti a G Suite

* Per testare la configurazione del provisioning utenti automatico, è consigliabile assegnare un singolo Azure AD utente a G Suite. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a G Suite, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="setup-g-suite-for-provisioning"></a>Configurare G Suite per il provisioning

Prima di configurare G Suite per il provisioning utenti automatico con Azure AD, sarà necessario abilitare il provisioning di SCIM in G Suite.

1. Accedere alla console di [amministrazione di G Suite](https://admin.google.com/) con l'account amministratore e quindi selezionare **sicurezza**. Se il collegamento non è visibile, è possibile che sia nascosto sotto il menu **More Controls** (Altri controlli) nella parte inferiore della schermata.

    ![Selezionare Security (Sicurezza).][10]

2. Nella pagina **Security** (Sicurezza) fare clic su **API Reference** (Riferimento API).

    ![Selezionare API Reference (Riferimento API).][15]

3. Selezionare **Enable API access**.

    ![Selezionare API Reference (Riferimento API).][16]

   > [!IMPORTANT]
   > Per ogni utente di cui si intende eseguire il provisioning in G Suite, il nome utente in Azure AD **deve** essere associato a un dominio personalizzato. Ad esempio, nomi utente simili a bob@contoso.onmicrosoft.com non vengono accettati da G Suite. mentre bob@contoso.com viene accettato. È possibile modificare il dominio di un utente esistente seguendo le istruzioni riportate [qui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4.  Dopo aver aggiunto e verificato i domini personalizzati desiderati con Azure AD, è necessario verificarli di nuovo con G Suite. Per verificare i domini in G Suite, fare riferimento alla procedura seguente:

    a. Nella [console di amministrazione di G Suite](https://admin.google.com/)selezionare **domini**.

    ![Selezionare i domini][20]

    b. Selezionare **Add a domain or a domain alias** (Aggiungi un dominio o un alias di dominio).

    ![Aggiungere un nuovo dominio][21]

    C. Selezionare **Add another domain** (Aggiungi un altro dominio) e quindi digitare il nome del dominio che si vuole aggiungere.

    ![Digitare il nome di dominio][22]

    d. Selezionare **Continue and verify domain ownership** (Continua e verifica la proprietà del dominio). Seguire i passaggi per verificare che si è proprietari del nome di dominio. Per istruzioni complete su come verificare il dominio con Google, vedere [verificare la proprietà del sito](https://support.google.com/webmasters/answer/35179).

    e. Ripetere i passaggi precedenti per tutti i domini aggiuntivi che si intende aggiungere a G Suite.

5. Determinare quindi l'account amministratore che si vuole usare per gestire il provisioning utenti in G Suite. Passare a **ruoli di amministratore**.

    ![Selezionare Google Apps][26]
    
6. Per il **ruolo di amministratore** dell'account, modificare i **privilegi** per quel ruolo. Assicurarsi di abilitare tutti i **privilegi dell'API di amministratore** in modo che l'account possa essere usato per il provisioning.

    ![Selezionare Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Aggiungere G Suite dalla raccolta

Per configurare G Suite per il provisioning utenti automatico con Azure AD, sarà necessario aggiungere G Suite dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite. 

1. Nel riquadro di spostamento a sinistra del **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory**.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **g Suite**, selezionare **g Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![G Suite nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configurazione del provisioning utenti automatico in G Suite 

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in G Suite in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare la Single Sign-On basata su SAML per G Suite, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di g Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

> [!NOTE]
> Per altre informazioni sull'endpoint dell'API directory di G Suite, vedere [API directory](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Per configurare il provisioning utenti automatico per G Suite in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali**e quindi selezionare **tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **G Suite**.

    ![Collegamento G Suite nell'elenco Applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Scheda Provisioning](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** selezionare **Autorizza**. Verrà aperta una finestra di dialogo di autorizzazione di Google in una nuova finestra del browser.

    ![Autorizzare G Suite](media/google-apps-provisioning-tutorial/authorize.png)

6. Confermare che si desidera concedere Azure AD autorizzazioni per apportare modifiche al tenant di G Suite. Selezionare **Accetto**.

    ![Verificare le autorizzazioni.][28]

7. Nel portale di Azure selezionare **Test connessione** per verificare che Azure AD possa connettersi all'app. Se la connessione non riesce, verificare che l'account di G Suite disponga delle autorizzazioni di amministratore di team. Ripetere quindi il passaggio per l'**autorizzazione**.

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

8. Fare clic su **Save**.

9. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a G Suite**.

    ![Mapping utente G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

10. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a G Suite nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in G Suite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

11. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to G Suite**.

    ![Mapping del gruppo G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

12. Esaminare gli attributi di gruppo sincronizzati da Azure AD a G Suite nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in G Suite per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi gruppo G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning Azure AD per G Suite, impostare **stato del provisioning** **su** attivato nella sezione **Impostazioni** .

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

15. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in G Suite scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in G Suite.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)


<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
