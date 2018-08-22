---
title: 'Esercitazione: Configurare Tableau Online per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: v-wingf-msft
ms.openlocfilehash: bbee7f0e6022e2945563c102a851819734d52e77
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2018
ms.locfileid: "40107114"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Esercitazione: Configurare Tableau Online per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Tableau Online e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Tableau Online.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

*   Un tenant di Azure AD
*   [Tenant di Tableau Online](https://www.tableau.com/)
*   Account utente in Tableau Online con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'[API Rest Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) disponibile per gli sviluppatori di Tableau Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Aggiunta di Tableau Online dalla raccolta
Prima di configurare Tableau Online per il provisioning utenti automatico con Azure AD, è necessario aggiungere Tableau Online dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Tableau Online dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]

3. Per aggiungere Tableau Online, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Tableau Online**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/AppSearch.png)

5. Nel pannello dei risultati selezionare **Tableau Online** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Tableau Online al proprio elenco di applicazioni SaaS.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/AppSearchResults.png)

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-tableau-online"></a>Assegnazione di utenti a Tableau Online

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Tableau Online. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Tableau Online seguendo le istruzioni fornite qui:

*   [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Suggerimenti importanti per l'assegnazione di utenti a Tableau Online

*   È consigliabile assegnare un singolo utente di Azure AD a Tableau Online per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Tableau Online, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Configurazione del provisioning utenti automatico in Tableau Online

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Tableau Online in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Tableau Online, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Tableau Online](tableauonline-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Tableau Online in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Tableau Online nell'elenco delle applicazioni SaaS.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/AppInstanceSearch.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Dominio**, **Nome utente amministratore**, **Password amministratore** e **URL del contenuto dell'account Tableau Online**:

    *   Nel campo **Dominio** inserire il sottodominio come descritto nel passaggio 6.

    *   Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Clarizen. Esempio: admin@contoso.com.

    *   Nel campo **Password amministratore** inserire la password dell'account amministratore corrispondente al nome utente amministratore.

    *   Nel campo **URL del contenuto** inserire il sottodominio come descritto nel passaggio 6.

6. Dopo avere effettuato l'accesso al proprio account amministrativo di Tableau Online, i valori per **Dominio** e **URL del contenuto** possono essere estratti dall'URL della pagina di amministrazione.

    *   Il campo **Dominio** per l'account di Tableau Online può essere copiato da questa parte dell'URL: ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    *   L'**URL del contenuto** per Tableau Online account, che può essere copiato da questa sezione, consiste in un valore definito durante la configurazione dell'account. In questo esempio, il valore è "contoso": ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Il **Dominio** potrebbe essere diverso da quello illustrato di seguito. 


7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Tableau Online. Se la connessione non riesce, verificare che l'account Tableau Online abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

10. Fare clic su **Save**.

11. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Tableau** (Sincronizza utenti di Azure Active Directory in Tableau).

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

12. Esaminare gli attributi utente sincronizzati da Azure AD a Tableau Online nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

13. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Tableau** (Sincronizza gruppi di Azure Active Directory con Tableau).

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

14. Esaminare gli attributi del gruppo sincronizzati da Azure AD a Tableau Online nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Tableau Online per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

15. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../active-directory-saas-scoping-filters.md).

16. Per abilitare il servizio di provisioning di Azure AD per Tableau Online, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

17. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Tableau Online selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

18. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Tableau Online](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Tableau Online.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
