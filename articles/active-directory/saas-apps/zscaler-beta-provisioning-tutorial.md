---
title: 'Esercitazione: Configurare la versione Beta di scalare per il provisioning automatico degli utenti con Azure Active Directory. Documenti Microsoft'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in versione Beta.
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
ms.openlocfilehash: 505cd7a3350c937885f0f03268ef326a4f784258
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062730"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Esercitazione: Configurare la versione Beta di scalare per il provisioning automatico degli utentiTutorial: Configure 'scaler Beta for automatic user provisioning

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Beta e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e/o gruppi in Versione Beta.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>


## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Un tenant Beta di scalare
* Un account utente in Beta di scalar con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'API SCIM beta di Scalar, disponibile per gli sviluppatori Beta per gli account con il pacchetto Enterprise.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Aggiunta di Zscaler Beta dalla raccolta

Prima di configurare la versione Beta di scaler per il provisioning automatico degli utenti con Azure AD, è necessario aggiungere la versione Beta di Scalar dalla raccolta di applicazioni di Azure AD all'elenco delle applicazioni SaaS gestite.

**Per aggiungere la versione Beta di Azure AD, eseguire la procedura seguente:To add sscaler Beta from the Azure AD application gallery, perform the following steps:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona **di Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler Beta**, selezionare **Zscaler Beta** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler Beta nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-beta"></a>Assegnazione di utenti a beta di scalare

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, è necessario decidere quali utenti e/o gruppi in Azure AD devono accedere alla versione Beta di scalar. Una volta deciso, è possibile assegnare questi utenti e/o gruppi a beta scalare seguendo le istruzioni qui:

* [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Suggerimenti importanti per l'assegnazione di utenti a .scaler Beta

* Per testare la configurazione del provisioning automatico degli utenti, è consigliabile assegnare un singolo utente di Azure AD a .scaler Beta. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente alla versione Beta di scala, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido (se disponibile) nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurazione del provisioning automatico degli utenti su Beta di Scalar

In questa sezione vengono illustrati i passaggi per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi nella versione Beta di scalar in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per la versione Beta di scalars, seguendo le istruzioni fornite [nell'esercitazione sull'accesso Single Sign-On di .scaler Beta](zscaler-beta-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Per configurare il provisioning automatico degli utenti per la versione Beta di Scalar in Azure AD:To configure automatic user provisioning for sscaler Beta in Azure AD:

1. Accedere al [portale](https://portal.azure.com) di Azure e selezionare **Applicazioni aziendali**, selezionare **Tutte le applicazioni**, quindi selezionare Beta **scalare**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler Beta**.

    ![Collegamento di Zscaler Beta nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **Credenziali di amministratore** immettere l'URL **tenant** e il **token segreto** dell'account Beta di scala, come descritto nel passaggio 6.

6. Per ottenere **l'URL tenant** e il token **segreto**, passare ad **Impostazioni di autenticazione > amministrazione** nell'interfaccia utente del portale Beta con scalare e fare clic su **SAML** in **Tipo di autenticazione**.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)

    Fare clic su **Configura SAML** per aprire le opzioni **SAML di configurazione.**

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)

    Selezionare **Abilita provisioning basato su SCIM** per recuperare **l'URL di base** e il token **Bearer**, quindi salvare le impostazioni. Copiare **l'URL di base** nell'URL **tenant**e il **token Bearer** in **Secret Token** nel portale di Azure.

7. Al momento di popolare i campi illustrati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD sia in grado di connettersi a . Se la connessione non riesce, verificare che l'account Beta di scalasia disponga delle autorizzazioni di amministratore e riprovare.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Fare clic su **Salva**.

10. Nella sezione **Mapping** selezionare Sincronizza gli utenti di **Azure Active Directory con la versione Beta di scalare**.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a Beta scalare nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare gli account utente in Beta di scala re scaler per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con beta con scaler**.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a Beta scalare nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono utilizzati per abbinare i gruppi in Beta di scala resbarramento per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning di Azure AD per la versione Beta di Scalar, modificare lo stato di provisioning **su Attivato** nella sezione Impostazioni.To enable the Azure AD provisioning service for sscaler Beta, change the **Provisioning Status** to On in the **Settings** section.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o i gruppi di cui si desidera eseguire il provisioning in versione Beta di scala rescaler scegliendo i valori desiderati in **Ambito** nella sezione **Impostazioni.**

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning Beta scalare](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD nella versione Beta di scalar.You can use the Synchronization Details section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on .scaler Beta.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
