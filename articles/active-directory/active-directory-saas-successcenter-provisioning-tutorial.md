---
title: 'Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Cornerstone OnDemand.
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8aed9bdcdd54f2f0ef3cd81f979635e5716f0b5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Esercitazione: Configurare Cornerstone OnDemand per il provisioning utenti automatico


Questa esercitazione descrive la procedura da eseguire in Cornerstone OnDemand e Azure Active Directory (Azure AD) per configurare Azure AD in modo da eseguire automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Cornerstone OnDemand.


> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   Tenant di Cornerstone OnDemand
*   Un account utente in Cornerstone OnDemand con autorizzazioni di amministratore


> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sul [servizio Web Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), disponibile per i team Cornerstone OnDemand.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Aggiunta di Cornerstone OnDemand dalla raccolta
Prima di configurare Cornerstone OnDemand per il provisioning utenti automatico con Azure AD, è necessario aggiungere Cornerstone OnDemand all'elenco delle applicazioni SaaS gestite dalla raccolta di applicazioni di Azure AD.

**Per aggiungere Cornerstone OnDemand dalla raccolta di applicazioni di Azure AD, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]
    
3. Per aggiungere Cornerstone OnDemand fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Cornerstone OnDemand**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearch.png)

5. Nel pannello dei risultati selezionare **Cornerstone OnDemand** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Cornerstone OnDemand al proprio elenco di applicazioni SaaS.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Assegnazione di utenti a Cornerstone OnDemand

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurazione e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Cornerstone OnDemand. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Cornerstone OnDemand seguendo le istruzioni riportate nell'articolo seguente:

*   [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Suggerimenti importanti per l'assegnazione di utenti a Cornerstone OnDemand

*   È consigliabile assegnare un singolo utente di Azure AD a Cornerstone OnDemand per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Cornerstone OnDemand, è necessario un ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Configurazione del provisioning utenti automatico per Cornerstone OnDemand

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD in modo da creare, aggiornare e disabilitare utenti e/o gruppi in Cornerstone OnDemand in base alle assegnazioni di utenti e/o gruppi in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Per configurare in provisioning utenti automatico per Cornerstone OnDemand in Azure AD:


1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Cornerstone OnDemand nell'elenco di applicazioni SaaS.
 
    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Successcenter2.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere **Nome utente amministratore**, **Password amministratore** e **Dominio** dell'account di Cornerstone OnDemand.

    *   Nel campo **Nome utente amministratore** inserire il nome utente dell'account amministratore nel tenant di Cornerstone OnDemand. Ad esempio, admin.

    *   Nel campo **Password amministratore** inserire la password corrispondente al nome utente amministratore.

    *   Nel campo **Dominio** inserire l'URL del servizio Web di Cornerstone OnDemand. Esempio: il servizio si trova in `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, per Contoso il dominio è `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`.

6. Dopo aver compilato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Cornerstone OnDemand. Se la connessione non riesce, verificare che l'account di Cornerstone OnDemand abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/TestConnection.png)

7. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/EmailNotification.png)

8. Fare clic su **Save**.

9. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Sincronizza utenti di Azure Active Directory in Cornerstone OnDemand).

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMapping.png)

10. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Cornerstone OnDemand. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Cornerstone OnDemand per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMappingAttributes.png)

11. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./active-directory-saas-scoping-filters.md).

12. Per abilitare il servizio di provisioning di Azure AD per Cornerstone OnDemand, impostare lo **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningStatus.png)

13. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Cornerstone OnDemand selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/SyncScope.png)

14. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Save.png) 


L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Cornerstone OnDemand.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](./active-directory-saas-provisioning-reporting.md).
## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_03.png
