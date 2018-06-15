---
title: 'Esercitazione: Configurare xMatters OnDemand per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in xMatters OnDemand.
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
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 52fe23f5353a68e3afbac6a91955a61c7b8fb44c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292671"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Esercitazione: Configurare xMatters OnDemand per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in xMatters OnDemand e Azure Active Directory (Azure AD) per configurare Azure AD per poter eseguire automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in xMatters OnDemand.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Un tenant di xMatters OnDemand con il piano [Starter](https://www.xmatters.com/pricing) o superiore abilitato 
*   Un account utente in xMatters OnDemand con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API di xMatters OnDemand](https://help.xmatters.com/xmAPI), disponibile per i team di xMatters OnDemand con piano Starter o superiore.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Aggiunta di xMatters OnDemand dalla raccolta

Prima di configurare xMatters OnDemand per il provisioning utenti automatico con Azure AD, è necessario aggiungere xMatters OnDemand all'elenco delle applicazioni SaaS gestite dalla raccolta di applicazioni di Azure AD.

**Per aggiungere xMatters OnDemand dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]
    
3. Per aggiungere xMatters OnDemand fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **xMatters OnDemand**.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. Nel pannello dei risultati selezionare **xMatters OnDemand** e quindi fare clic sul pulsante **Aggiungi** per aggiungere xMatters OnDemand al proprio elenco di applicazioni SaaS.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Assegnazione di utenti a xMatters OnDemand

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a xMatters OnDemand. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a xMatters OnDemand seguendo le istruzioni riportate nell'articolo seguente:

*   [Assegnare un utente o gruppo a un'app aziendale](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Suggerimenti importanti per l'assegnazione di utenti a xMatters OnDemand

*   È consigliabile assegnare un singolo utente di Azure AD a xMatters OnDemand per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a xMatters OnDemand, è necessario un ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Configurazione del provisioning utenti automatico per xMatters OnDemand 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per poter creare, aggiornare e disabilitare utenti e/o gruppi in xMatters OnDemand in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per xMatters OnDemand, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per xMatters OnDemand](active-directory-saas-xmatters-ondemand-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Per configurare in provisioning utenti automatico per xMatters OnDemand in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare xMatters OnDemand nell'elenco di applicazioni SaaS.
 
    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Selezionare la scheda **Provisioning**.
    
    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere **Nome utente amministratore**, **Password amministratore** e **Dominio** dell'account di xMatters OnDemand.

    *   Nel campo **Nome utente amministratore** inserire il nome utente dell'account amministratore nel tenant di xMatters OnDemand. Esempio: admin@contoso.com.

    *   Nel campo **Password amministratore** inserire la password corrispondente al nome utente amministratore.

    *   Nel campo **Dominio** immettere il sottodominio del tenant di xMatters OnDemand.
    Esempio: per un account con un URL del tenant https://my-tenant.xmatters.com, il sottodominio sarà **tenant**.

6. Dopo aver compilato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a xMatters OnDemand. Se la connessione non riesce, verificare che l'account di xMatters OnDemand abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Fare clic su **Save**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to xMatters OnDemand** (Sincronizza utenti di Azure Active Directory in Cornerstone OnDemand).

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a xMatters OnDemand. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in xMatters OnDemand per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to xMatters OnDemand** (Sincronizza gruppi di Azure Active Directory in Cornerstone OnDemand).

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Nella sezione **Mapping degli attributi** esaminare gli attributi gruppo che vengono sincronizzati da Azure AD a xMatters OnDemand. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in xMatters OnDemand per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./active-directory-saas-scoping-filters.md).

14. Per abilitare il servizio di provisioning di Azure AD per xMatters OnDemand, impostare lo **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in xMatters OnDemand selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in xMatters OnDemand.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
