---
title: 'Esercitazione: Configurare Zendesk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zendesk.
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
ms.author: v-ant
ms.openlocfilehash: 342c535464ad4145137c89fe38281e85c9dd0463
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32161374"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zendesk per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Zendesk e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in Zendesk. 

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

*   Un tenant di Azure AD
*   Tenant di Zendesk con il piano [Enterprise](https://www.zendesk.com/product/pricing/) o superiore abilitato 
*   Account utente in Zendesk con autorizzazioni di amministratore 

> [!NOTE]
> L'integrazione del provisioning di Azure AD è basata sull'[API REST di Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction), disponibile per i team di Zendesk con piano Enterprise o superiore.

## <a name="adding-zendesk-from-the-gallery"></a>Aggiunta di Zendesk dalla raccolta
Prima di configurare Zendesk per il provisioning utenti automatico con Azure AD, è necessario aggiungere Zendesk dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Zendesk dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali** > **Tutte le applicazioni**.

    ![Sezione Applicazioni aziendali][2]
    
3. Per aggiungere Zendesk, fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Zendesk**.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk6.png)

5. Nel pannello dei risultati selezionare **Zendesk** e quindi fare clic sul pulsante **Aggiungi** per aggiungere Zendesk al proprio elenco di applicazioni SaaS.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Assegnazione di utenti a Zendesk

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Zendesk. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Zendesk seguendo le istruzioni fornite qui:

*   [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Suggerimenti importanti per l'assegnazione di utenti a Zendesk

*   È consigliabile assegnare un singolo utente di Azure AD a Zendesk per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

*   Quando si assegna un utente a Zendesk, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Configurazione del provisioning utenti automatico in Zendesk 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Zendesk in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Zendesk, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Zendesk](active-directory-saas-zendesk-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Zendesk in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare a **Azure Active Directory > App aziendali > Tutte le applicazioni**.

2. Selezionare Zendesk nell'elenco delle applicazioni SaaS.
 
    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk3.png)

3. Selezionare la scheda **Provisioning**.
    
    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk16.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. Nella sezione **Credenziali amministratore** completare i campi **Nome utente amministratore**, **Token segreto** e **Dominio** dell'account Zendesk. Ecco alcuni esempi di questi valori:

    *   Nel campo **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di Zendesk. Esempio: admin@contoso.com.

    *   Nel campo **Token segreto** immettere il token del segreto come descritto nel passaggio 6.

    *   Nel campo **Dominio** immettere il sottodominio del tenant di Zendesk.
    Esempio: per un account con un URL del tenant https://my-tenant.zendesk.com, il sottodominio sarà **tenant**.

6. Il valore di **Token segreto** per l'account Zendesk si trova in **Amministratore > API > Impostazioni**. 

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk4.png) ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Zendesk. Se la connessione non riesce, verificare che l'account Zendesk abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk9.png)

9. Fare clic su **Save**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zendesk** (Sincronizza utenti di Azure Active Directory con Zendesk).

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk10.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Zendesk nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zendesk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk11.png)

12. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to ZenDesk** (Sincronizza gruppi di Azure Active Directory con Zendesk).

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk12.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Zendesk nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zendesk per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk13.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning di Azure AD per Zendesk, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Zendesk selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Zendesk](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk18.png)


L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Zendesk.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori
* Zendesk supporta l'utilizzo dei gruppi solo per gli utenti con il ruolo di agente. Per altre informazioni, vedere la [documentazione di Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](active-directory-enterprise-apps-manage-provisioning.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
