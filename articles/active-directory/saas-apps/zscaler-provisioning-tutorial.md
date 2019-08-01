---
title: 'Esercitazione: Configurare zScaler per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in zScaler.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d88aae8887cbfd872a8d3e82fb2166043c312e5a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515404"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Esercitazione: Configurare zScaler per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in zScaler e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in zScaler.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD.
* Tenant di zScaler.
* Un account utente in zScaler con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sull'API SCIM di zScaler, disponibile per gli sviluppatori zScaler per gli account con il pacchetto Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Aggiunta di Zscaler dalla raccolta

Prima di configurare zScaler per il provisioning utenti automatico con Azure AD, è necessario aggiungere zScaler dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere zScaler dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler**, selezionare **Zscaler** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Assegnazione di utenti a zScaler

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a zScaler. Dopo aver stabilito questo, è possibile assegnare gli utenti e/o i gruppi a zScaler seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Suggerimenti importanti per l'assegnazione di utenti a zScaler

* È consigliabile assegnare un singolo Azure AD utente a zScaler per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a zScaler, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Configurazione del provisioning utenti automatico in zScaler

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in zScaler in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per zScaler, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di zScaler](zscaler-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Per configurare il provisioning utenti automatico per zScaler in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **zScaler**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Zscaler**.

    ![Collegamento di Zscaler nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant** e il **token segreto** dell'account zScaler, come descritto nel passaggio 6.

6. Per ottenere l' **URL del tenant** e il **token del segreto**, passare ad **Amministrazione > impostazioni di autenticazione** nell'interfaccia utente del portale di zScaler e fare clic su **SAML** in **tipo di autenticazione**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Fare clic su **Configura SAML** per aprire le opzioni **SAML di configurazione** .

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selezionare **Abilita provisioning basato su SCIM** per recuperare l' **URL di base** e il token di **porta**, quindi salvare le impostazioni. Copiare l' **URL di base** nell' **URL del tenant**e il token di **porta** nel token di **segreto** nel portale di Azure.

7. Quando si popolano i campi indicati nel passaggio 5, fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a zScaler. Se la connessione non riesce, verificare che l'account zScaler disponga delle autorizzazioni di amministratore e riprovare.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/notification.png)

9. Fare clic su **Save**.

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a zScaler**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a zScaler nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in zScaler per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to zScaler**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a zScaler nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in zScaler per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning Azure AD per zScaler, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in zScaler selezionando i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di zScaler](./media/zscaler-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning su zScaler.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
