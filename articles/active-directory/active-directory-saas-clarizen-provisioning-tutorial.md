---
title: 'Esercitazione: Configurare Clarizen per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Clarizen.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 3e75ce17f2806e4cb66f90f233b265398cdd0878
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Esercitazione: Configurare Clarizen per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Clarizen e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Clarizen.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Tenant di Clarizen con il piano [Enterprise Edition](https://www.clarizen.com/product/pricing/) o superiore abilitato 
*   Un account utente in Clarizen con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API Clarizen](https://api.clarizen.com/v2.0/services/), disponibile per i team Clarizen con piano Enterprise Edition o superiore.

## <a name="adding-clarizen-from-the-gallery"></a>Aggiunta di Clarizen dalla raccolta
Prima di configurare Clarizen per il provisioning utenti automatico con Azure AD, è necessario aggiungere Clarizen dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Clarizen dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]
    
3. Per aggiungere Clarizen, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Clarizen**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearch.png)

5. Nel pannello dei risultati selezionare **Clarizen** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Clarizen al proprio elenco di applicazioni SaaS.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Assegnazione di utenti a Clarizen

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Clarizen. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Clarizen seguendo le istruzioni fornite qui:

*   [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Suggerimenti importanti per l'assegnazione di utenti a Clarizen

*   È consigliabile assegnare un singolo utente di Azure AD a Clarizen per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Clarizen, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Configurazione del provisioning utenti automatico per Clarizen 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Clarizen in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Clarizen, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Clarizen](active-directory-saas-clarizen-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Clarizen in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Clarizen nell'elenco delle applicazioni SaaS.
 
    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Selezionare la scheda **Provisioning**.
    
    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Dominio**, **Nome utente amministratore** e **Password amministratore** dell'account Clarizen. Ecco alcuni esempi di questi valori:

    *   Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Clarizen. Esempio: admin@contoso.com.

    *   Nel campo **Password amministratore** inserire la password dell'account amministratore corrispondente al nome utente amministratore.

    *   Nel campo **Dominio** inserire il sottodominio come descritto nel passaggio 6.
    
6. Recuperare il parametro **serverLocation** per l'account di Clarizen seguendo i passaggi indicati nella sezione **Authentication** della [Guida API Rest di Clarizen](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2) (in inglese). Dopo aver ottenuto il parametro serverLocation, usare il sottodominio dell'URL come evidenziato di seguito per popolare il campo **Dominio**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ClarizenDomain.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Clarizen. Se la connessione non riesce, verificare che l'account Clarizen abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/TestConnection.png)
    
8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/NotificationEmail.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Clarizen** (Sincronizza utenti di Azure Active Directory in Clarizen).

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMapping.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Clarizen nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Clarizen per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Clarizen** (Sincronizza gruppi di Azure Active Directory in Clarizen).

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMapping.png)

13. Esaminare gli attributi del gruppo sincronizzati da Azure AD a Clarizen nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Clarizen per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning di Azure AD per Clarizen, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Clarizen selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/ScopeSelection.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Clarizen](./media/active-directory-saas-clarizen-provisioning-tutorial/SaveProvisioning.png)


L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Clarizen.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
