---
title: 'Esercitazione: Configurazione di Zscaler One per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e deprovisioning degli account utente in Zscaler One.
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
ms.author: v-ant-msft
ms.openlocfilehash: 26448d5056e58cf1110e825ad04c5123fca20684
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058425"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Esercitazione: Configurazione di Zscaler One per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire in Zscaler One e Azure Active Directory (Azure AD) per configurare Azure AD per effettuare il provisioning e deprovisioning di utenti e/o gruppi a Zscaler One automaticamente.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Questo connettore è attualmente in anteprima pubblica. Per altre informazioni sulle condizioni di Microsoft Azure generale di utilizzo per le funzionalità di anteprima, vedere [condizioni per l'utilizzo aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Un tenant di Zscaler One
* Un account utente in Zscaler One con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione di provisioning di Azure AD si basa sull'API SCIM uno Zscaler, disponibile per gli sviluppatori di Zscaler One per gli account con il pacchetto di Enterprise.

## <a name="adding-zscaler-one-from-the-gallery"></a>Aggiunta di Zscaler One dalla raccolta

Prima configurazione di Zscaler One per provisioning utenti automatico con Azure AD, è necessario aggiungere Zscaler One dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Zscaler One dalla raccolta di applicazioni di Azure AD, seguire i passaggi seguenti:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Zscaler One**, selezionare **Zscaler One** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Zscaler One nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-one"></a>Assegnazione di utenti a Zscaler One

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono accedere a Zscaler One. Dopo aver stabilito questo, è possibile assegnare tali utenti e/o gruppi a Zscaler One, seguire le istruzioni riportate qui:

* [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Suggerimenti importanti per l'assegnazione di utenti a Zscaler One

* È consigliabile che un singolo utente di Azure AD viene assegnato a Zscaler One per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Zscaler One, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-zscaler-one"></a>Configurazione del provisioning utenti automatico a Zscaler One

Questa sezione descrive i passaggi per configurare il provisioning di Azure AD del servizio per creare, aggiornare e disabilitare utenti e/o gruppi in Zscaler One basata su utente e/o le assegnazioni dei gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare basato su SAML single sign-on per Zscaler One, seguendo le istruzioni disponibili nel [Zscaler One single sign-on di esercitazione](zscaler-One-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Zscaler One in Azure AD:

1. Accedi per il [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **Zscaler One**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Il collegamento di Zscaler One nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sotto il **credenziali di amministratore** sezione di input il **URL Tenant** e **Token segreto** dell'account di Zscaler One, come descritto nel passaggio 6.

6. Per ottenere il **URL Tenant** e **Token segreto**, passare a **amministrazione > Impostazioni di autenticazione** nell'interfaccia utente del portale Zscaler One e fare clic su  **SAML** sotto **tipo di autenticazione**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    Fare clic su **configurare SAML** per aprire **SAML Configuration** opzioni.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    Selezionare **Provisioning Enable SCIM-Based** recuperare **URL di Base** e **Bearer Token**, quindi salvare le impostazioni. Copia il **URL di Base** al **URL Tenant** e **Bearer Token** al **Token segreto** nel portale di Azure.

7. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Zscaler One. Se la connessione non riesce, verificare che l'account di Zscaler One abbia autorizzazioni di amministratore e riprovare.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Fare clic su **Save**.

10. Sotto il **mapping** sezione, selezionare **Synchronize Azure Active Directory Users a Zscaler One**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente sincronizzati da Azure AD a Zscaler One nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** le proprietà utilizzate per soddisfare gli account utente in Zscaler One per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Sotto il **mapping** sezione, selezionare **sincronizzare Azure gruppi di Active Directory a Zscaler One**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi gruppo sincronizzati da Azure AD a Zscaler One nel **Mapping degli attributi** sezione. Gli attributi selezionati come **corrispondenti** proprietà usate devono rispettare i gruppi in Zscaler One per operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il provisioning del servizio per Zscaler One AD Azure, impostare il **stato del Provisioning** al **sul** nel **impostazioni** sezione.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti e/o gruppi di cui si vuole eseguire il provisioning in Zscaler One selezionando i valori desiderati in **ambito** nel **impostazioni** sezione.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Il Provisioning di Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la **Dettagli sincronizzazione** sezione per monitorare lo stato di avanzamento e selezionare i collegamenti ai report di attività, che descrive tutte le azioni eseguite da Azure Active Directory il provisioning in Zscaler One servizio di provisioning.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione di provisioning degli account utente per le app aziendali](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
