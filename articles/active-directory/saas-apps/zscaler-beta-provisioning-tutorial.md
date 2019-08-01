---
title: 'Esercitazione: Configurare zScaler beta per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in zScaler beta.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 83db6b8d-503b-48f3-b918-f9fba1369d53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: d5ca1efb607160447bdbca59522c8ac72bc7eeb3
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515525"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Esercitazione: Configurare zScaler beta per il provisioning utenti automatico

Questa esercitazione descrive i passaggi da eseguire in zScaler beta e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in zScaler beta.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Tenant di zScaler beta
* Un account utente in zScaler beta con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sull'API zScaler beta SCIM, disponibile per gli sviluppatori zScaler beta per gli account con il pacchetto Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Aggiunta di Zscaler Beta dalla raccolta

Prima di configurare zScaler beta per il provisioning utenti automatico con Azure AD, è necessario aggiungere zScaler beta dalla raccolta di applicazioni Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere zScaler beta dalla raccolta di applicazioni Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler Beta**, selezionare **Zscaler Beta** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Beta nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Assegnazione di utenti a zScaler beta

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD necessario accedere a zScaler beta. Dopo aver stabilito questo, è possibile assegnare questi utenti e/o gruppi a zScaler Beta seguendo le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Suggerimenti importanti per l'assegnazione di utenti a zScaler beta

* È consigliabile assegnare un singolo utente Azure AD a zScaler beta per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a zScaler beta, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurazione del provisioning utenti automatico in zScaler beta

Questa sezione illustra i passaggi per configurare il servizio di provisioning Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in zScaler beta in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-on basato su SAML per zScaler beta, seguendo le istruzioni fornite nell' [esercitazione sull'accesso Single Sign-on di zScaler beta](zscaler-beta-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Per configurare il provisioning utenti automatico per zScaler beta nel Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **zScaler beta**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler Beta**.

    ![Collegamento di Zscaler Beta nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **credenziali amministratore** immettere l'URL del **tenant** e il **token segreto** dell'account zScaler beta come descritto nel passaggio 6.

6. Per ottenere l' **URL del tenant** e il **token segreto**, passare ad **Amministrazione > impostazioni di autenticazione** nell'interfaccia utente del portale zScaler beta e fare clic su **SAML** in **tipo di autenticazione**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Fare clic su **Configura SAML** per aprire le opzioni di SAML per la **configurazione** .

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selezionare **Abilita provisioning basato su SCIM** per recuperare l' **URL di base** e il token di **porta**, quindi salvare le impostazioni. Copiare l' **URL di base** nell' **URL del tenant**e il token di **porta** nel token di **segreto** nel portale di Azure.

7. Quando si popolano i campi indicati nel passaggio 5, fare clic su **Test connessione** per assicurarsi che Azure ad possa connettersi a zScaler beta. Se la connessione non riesce, verificare che l'account zScaler beta disponga delle autorizzazioni di amministratore e riprovare.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Fare clic su **Save**.

10. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory Users to zScaler beta**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a zScaler beta nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in zScaler beta per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **mapping** selezionare **Synchronize Azure Active Directory groups to zScaler beta**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a zScaler beta nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in zScaler beta per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning Azure AD per zScaler beta, impostare **stato** del provisioning **su** attivato nella sezione **Impostazioni** .

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in zScaler beta scegliendo i valori desiderati in **ambito** nella sezione **Impostazioni** .

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di zScaler beta](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio Azure ad provisioning in zScaler beta.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
