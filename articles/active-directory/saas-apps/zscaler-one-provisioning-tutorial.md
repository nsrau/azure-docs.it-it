---
title: 'Esercitazione: Configurazione di Zscaler One per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 3d6210fa215792fc7ff049eb597df5905926c193
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670883"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Zscaler One per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Zscaler One e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare automaticamente il provisioning e il deprovisioning di utenti e gruppi a Zscaler One.

> [!NOTE]
> Questa esercitazione descrive un connettore che si basa il servizio di provisioning utenti di Azure AD. Per informazioni sul funzionamento di questo servizio, come funziona e domande frequenti, vedere [automatizzare il provisioning e il deprovisioning utenti in applicazioni software-as-a-service (SaaS) con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Questo connettore è attualmente disponibile come anteprima. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni d'uso aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione si presuppone di avere:

* Un tenant di Azure AD.
* Un tenant di Zscaler One.
* Un account utente in Zscaler One con autorizzazioni di amministratore.

> [!NOTE]
> Integrazione del provisioning di Azure AD si basa sull'API SCIM uno Zscaler. Questa API è disponibile per gli sviluppatori di Zscaler One per gli account con il pacchetto dell'organizzazione.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Aggiunta di Zscaler One da Azure Marketplace

Prima di configurare Zscaler One per provisioning utenti automatico con Azure AD, aggiungere Zscaler One da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere Zscaler One dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![L'icona di Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Zscaler One** e selezionare **Zscaler One** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Add**.

    ![Zscaler One nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Assegnare gli utenti a Zscaler One

Azure Active Directory Usa il concetto *assegnazioni* per determinare gli utenti che dovranno ricevere l'accesso alle App selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, stabilire quali utenti o gruppi in Azure AD devono poter accedere a Zscaler One. Per assegnare tali utenti o gruppi a Zscaler One, seguire le istruzioni in [assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler One

* È consigliabile assegnare un singolo utente di Azure AD a Zscaler One per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Zscaler One, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurare il provisioning utenti automatico a Zscaler One

Questa sezione descrive i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in Zscaler One base utente o le assegnazioni dei gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare basato su SAML single sign-on per Zscaler One. Seguire le istruzioni di [Zscaler One single sign-on di esercitazione](zscaler-One-tutorial.md). Accesso Single sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari tra loro.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurare il provisioning utenti automatico per Zscaler One in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni aziendali** > **tutte le applicazioni** > **Zscaler One**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Il collegamento di Zscaler One nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Zscaler una modalità di Provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sotto il **credenziali di amministratore** sezione, compilare il **URL Tenant** e **Token segreto** caselle con le impostazioni di Zscaler One account come descritto nel passaggio 6.

6. Per il tenant di URL e il token segreto, visitare **Administration** > **le impostazioni di autenticazione** nel portale dell'interfaccia utente di Zscaler One. In **Authentication Type** (Tipo di autenticazione) selezionare **SAML**.

    ![Impostazioni di autenticazione di Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selezionare **configurare SAML** per aprire il **Configura SAML** opzioni.

    ![Zscaler One configurare SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selezionare **Provisioning Enable SCIM-Based** per ottenere le impostazioni **URL di Base** e **Bearer Token**. Quindi salvare le impostazioni. Copia il **URL di Base** se si imposta su **URL Tenant** nel portale di Azure. Copia il **Bearer Token** se si imposta su **Token segreto** nel portale di Azure.

7. Dopo aver inserito le caselle indicate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure AD possa connettersi a Zscaler One. Se la connessione non riesce, verificare che l'account di Zscaler One abbia autorizzazioni di amministratore e riprovare.

    ![Connessione di un Test di Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Nel **notifica tramite posta elettronica** casella, immettere l'indirizzo di posta elettronica della persona o il gruppo per la ricezione di notifiche di errore di provisioning. Selezionare il **invia una notifica di posta elettronica quando si verifica un errore** casella di controllo.

    ![Notifica di posta elettronica di Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selezionare **Salva**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users a Zscaler One**.

    ![Sincronizzazione utente di Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Zscaler One nel **mapping di attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Zscaler One per operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Attributi utente corrispondenti Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory a Zscaler One**.

    ![Sincronizzazione dei gruppi di Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Zscaler One nel **mapping di attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in Zscaler One per operazioni di aggiornamento. Per salvare le modifiche, selezionare **salvare**.

    ![Attributi del gruppo corrispondenti Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Seguire le istruzioni per configurare i filtri di ambito, il [esercitazione filtro ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il provisioning del servizio per Zscaler One, in AD Azure i **le impostazioni** sezione, modificare **stato del Provisioning** a **su**.

    ![Zscaler uno stato di Provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti o gruppi che si desidera eseguire il provisioning in Zscaler One. Nel **le impostazioni** , selezionare i valori desiderati nelle **ambito**.

    ![Ambito di Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, selezionare **salvare**.

    ![Zscaler uno salvare](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nella **ambito** nel **impostazioni** sezione. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano circa ogni 40 minuti fino a quando viene eseguito il servizio di provisioning di Azure AD. 

È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti per il rapporto attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio in Zscaler One di provisioning di Azure AD.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
