---
title: 'Esercitazione: Configurare Zendesk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in .endesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062745"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Esercitazione: Configurare Zendesk per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in .endesk e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning automatico e il deprovisioning di utenti e gruppi in .endesk.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni sulle operazioni eseguite da questo servizio, sul suo funzionamento e sulle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS (Software-as-A-Service) con Azure Active Directory.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che si dispone di:The scenario outlined in this tutorial assumes that you have:

* Un tenant di Azure AD.
* Un tenant di  con il piano Professional o meglio abilitato.
* Un account utente in .endesk con autorizzazioni di amministratore.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Da Azure Marketplace, aggiungere l'indirizzo .endesk da Azure Marketplace

Prima di configurare il provisioning automatico degli utenti con Azure AD, aggiungere l'account da Azure Marketplace all'elenco delle applicazioni SaaS gestite.

Per aggiungere il Marketplace, segui questi passaggi.

1. Nel [portale di Azure,](https://portal.azure.com)nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory.**

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **.endesk** e selezionare **.endesk** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Zendesk nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Assegnare gli utenti a .endesk

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a .endesk. Per assegnare questi utenti o gruppi a , seguire le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Suggerimenti importanti per l'assegnazione di utenti a Zendesk

* Oggi, i ruoli di server endesk vengono popolati automaticamente e dinamicamente nell'interfaccia utente del portale di Azure.Today, endesk roles are automatically and dynamically populated in the Azure portal UI. Prima di assegnare i ruoli di  agli utenti, assicurarsi che venga completata una sincronizzazione iniziale rispetto a  per recuperare i ruoli più recenti nel tenant di .

* È consigliabile assegnare un singolo utente di Azure AD a .ENdesk per testare la configurazione iniziale del provisioning automatico degli utenti. È possibile assegnare altri utenti o gruppi in un secondo momento dopo che i test hanno esito positivo.

* Quando si assegna un utente a .endesk, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurare il provisioning automatico degli utenti su .endesk 

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Utilizzarlo per creare, aggiornare e disabilitare utenti o gruppi in  in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare l'accesso Single Sign-On basato su SAML per .endesk. Seguire le istruzioni riportate [nell'esercitazione sull'accesso Single Sign-On di ](zendesk-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurare il provisioning automatico degli utenti per la rete in Azure ADConfigure automatic user provisioning for 'endesk in Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni** > aziendali**Tutte le applicazioni** > **.endesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Zendesk**.

    ![Il link di  nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Modalità di provisioning di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Nella sezione **Credenziali amministratore** immettere il nome utente amministratore, il token segreto e il dominio dell'account .endesk. Ecco alcuni esempi di questi valori:

   * Nella casella **Nome utente amministratore** immettere il nome utente dell'account amministratore nel tenant di .endesk. Un esempio è admin@contoso.com.

   * Nella casella **Token segreto** compilare il token segreto come descritto nel passaggio 6.

   * Nella casella **Dominio** compilare il sottodominio dell'affittuario . Ad esempio, per un account `https://my-tenant.zendesk.com`con un URL tenant di , il sottodominio è **my-tenant**.

6. Il token segreto per l'account .endesk si trova in**Impostazioni****API** >  **di amministrazione** > . Assicurarsi che **L'accesso** token sia impostato su **Abilitato**.

    ![Impostazioni di amministrazione di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token segreto di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare Test connessione per assicurarsi che Azure AD possa connettersi a .endesk.After you fill in the boxes shown in Step 5, select **Test Connection** to make sure that Azure AD can connect to 'endesk. Se la connessione non riesce, assicurati che l'account .endesk disponga delle autorizzazioni di amministratore e riprova.

    ![Connessione di prova di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Nella casella **Email di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche di errore di provisioning. Selezionare la casella di controllo **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![E-mail di notifica di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zendesk** (Sincronizza utenti di Azure Active Directory con Zendesk).

    ![Sincronizzazione degli utenti di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a .endesk nella sezione **Mapping attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zendesk per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi utente corrispondenti di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Nella sezione **Mapping** selezionare Sincronizza gruppi di **Azure Active Directory con .endesk**.

    ![Sincronizzazione dei gruppi di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Esaminare gli attributi di gruppo che vengono sincronizzati da Azure AD a .endesk nella sezione **Mapping degli attributi.** Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zendesk per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi del gruppo corrispondente a .endesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Per configurare i filtri di ambito, seguire le istruzioni nell'esercitazione sui [filtri di ambito.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Per abilitare il servizio di provisioning di Azure AD per la classe **Impostazioni,** nella sezione Impostazioni modificare **Stato provisioning** su **Attivato**.

    ![Stato di provisioning di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in .endesk. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**.

    ![Ambito di applicazione di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salvataggio di .endesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti, purché venga eseguito il servizio di provisioning di Azure AD. 

È possibile utilizzare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD su .endesk.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* L'utilizzo di gruppi solo per i ruoli **Agent** è supportato da .endesk. Per ulteriori informazioni, vedere la [documentazione relativa](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)alla sezione .

* Quando un ruolo personalizzato viene assegnato a un utente o a un gruppo, il servizio di provisioning automatico degli utenti di Azure AD assegna anche il ruolo predefinito **Agent**. È possibile assegnare un ruolo predefinito solo agli Agenti. Per ulteriori informazioni, vedere la [documentazione relativa all'API .endesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
