---
title: 'Esercitazione: configurare Zendesk per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zendesk.
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
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166b7727362549aaf054e3f0282c564eca687eb9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672868"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Esercitazione: configurare Zendesk per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Zendesk e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare il provisioning e il deprovisioning di utenti e gruppi a Zendesk automaticamente.

> [!NOTE]
> Questa esercitazione descrive un connettore che si basa il servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio, come funziona e domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni software-as-a-service (SaaS) con Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione si presuppone di avere:

* Un tenant di Azure AD.
* Un tenant di Zendesk con il [Enterprise](https://www.zendesk.com/product/pricing/) pianificare o superiore abilitato.
* Un account utente in Zendesk con autorizzazioni di amministratore.

> [!NOTE]
> Integrazione del provisioning di Azure AD si basa sul [API Rest di Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction). Questa API è disponibile per i team Zendesk con piano Enterprise o superiore.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Aggiungere Zendesk da Azure Marketplace

Prima di configurare Zendesk per provisioning utenti automatico con Azure AD, aggiungere Zendesk da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Zendesk da Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![L'icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Zendesk** e selezionare **Zendesk** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Add**.

    ![Zendesk nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Assegnare gli utenti a Zendesk

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, stabilire quali utenti o gruppi in Azure AD devono poter accedere a Zendesk. Per assegnare tali utenti o gruppi a Zendesk, seguire le istruzioni in [assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Suggerimenti importanti per l'assegnazione di utenti a Zendesk

* Oggi, i ruoli di Zendesk sono automaticamente e in modo dinamico popolati nel portale di Azure dell'interfaccia utente. Prima di assegnare i ruoli di Zendesk per gli utenti, assicurarsi che venga completata una sincronizzazione iniziale in Zendesk per recuperare i ruoli più recenti nel tenant di Zendesk.

* È consigliabile assegnare un singolo utente di Azure AD a Zendesk per testare il configurazione del provisioning utenti automatico iniziale. È possibile assegnare utenti o gruppi aggiuntivi in un secondo momento dopo i test hanno esito positivo.

* Quando si assegna un utente a Zendesk, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurare il provisioning utenti automatico a Zendesk 

Questa sezione descrive i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Zendesk in base alle assegnazioni utente o gruppo in Azure AD.

> [!TIP]
> È anche possibile abilitare basato su SAML single sign-on per Zendesk. Seguire le istruzioni di [esercitazione Zendesk single sign-on](zendesk-tutorial.md). Accesso Single sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari tra loro.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurare il provisioning utenti automatico per Zendesk in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni** > **Zendesk**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Zendesk**.

    ![Il collegamento di Zendesk nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Modalità di Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Sotto il **credenziali amministratore** sezione, immettere il nome utente amministratore, token segreto e dominio dell'account Zendesk. Ecco alcuni esempi di questi valori:

   * Nel **Admin Username** casella, immettere il nome utente dell'account amministratore nel tenant di Zendesk. Un esempio è admin@contoso.com.

   * Nel **Token segreto** casella, inserire il token del segreto come descritto nel passaggio 6.

   * Nel **dominio** casella, immettere il sottodominio del tenant di Zendesk. Ad esempio, per un account con un URL del tenant `https://my-tenant.zendesk.com`, il sottodominio è **tenant**.

6. Il token del segreto per l'account Zendesk si trova nella **Admin** > **API** > **impostazioni**. Verificare che l'opzione **Token di accesso** è impostata su **abilitato**.

    ![Impostazioni dell'amministratore di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token segreto Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Dopo aver inserito le caselle indicate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure AD possa connettersi a Zendesk. Se la connessione non riesce, verificare che l'account Zendesk abbia autorizzazioni di amministratore e riprovare.

    ![Connessione di Test di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Nel **notifica tramite posta elettronica** casella, immettere l'indirizzo di posta elettronica della persona o il gruppo per la ricezione di notifiche di errore di provisioning. Selezionare il **invia una notifica di posta elettronica quando si verifica un errore** casella di controllo.

    ![Notifica di posta elettronica di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selezionare **Salva**.

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Zendesk** (Sincronizza utenti di Azure Active Directory con Zendesk).

    ![Sincronizzazione degli utenti di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Zendesk nella **mapping di attributi** sezione. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Zendesk per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Attributi utente corrispondenti di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Sotto il **mapping** sezione, selezionare **sincronizzazione Azure Active Directory i gruppi a Zendesk**.

    ![Sincronizzazione dei gruppi di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Zendesk nella **mapping di attributi** sezione. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Zendesk per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Attributi del gruppo corrispondenti di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Seguire le istruzioni per configurare i filtri di ambito, il [esercitazione filtro ambito](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Per abilitare Azure AD per Zendesk, servizio di provisioning nel **le impostazioni** sezione, modificare **stato del Provisioning** al **su**.

    ![Stato di Provisioning di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definire gli utenti o gruppi che si desidera eseguire il provisioning in Zendesk. Nel **le impostazioni** , selezionare i valori desiderati nelle **ambito**.

    ![Ambito di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **salvare**.

    ![Salvataggio di Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nella **ambito** nel **impostazioni** sezione. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano circa ogni 40 minuti fino a quando viene eseguito il servizio di provisioning di Azure AD. 

È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti per il rapporto attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio in Zendesk di provisioning di Azure AD.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

* Zendesk supporta l'uso di gruppi per gli utenti che dispongono **agente** solo ruoli. Per altre informazioni, vedere la [Zendesk documentazione](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando un ruolo personalizzato viene assegnato a un utente o gruppo, l'utente di Azure AD automatico servizio di provisioning anche assegna il ruolo predefinito **agente**. Solo gli agenti possono essere assegnati a un ruolo personalizzato. Per altre informazioni, vedere la [documentazione dell'API di Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
