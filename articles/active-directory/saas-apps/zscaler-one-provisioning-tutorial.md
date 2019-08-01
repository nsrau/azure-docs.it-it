---
title: 'Esercitazione: Configurare zScaler One per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in zScaler One.
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
ms.openlocfilehash: de9dce04b6f27b6ae6f5c5caeed5728370359558
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515376"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Esercitazione: Configurare zScaler One per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in zScaler One e Azure Active Directory (Azure AD) per configurare Azure AD per eseguire automaticamente il provisioning e il deprovisioning di utenti e gruppi in zScaler One.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning utenti Azure AD. Per informazioni sul funzionamento di questo servizio, sul suo funzionamento e sulle domande frequenti, vedere automatizzare il provisioning e il deprovisioning [utenti in applicazioni SaaS (software-as-a-Service) con Azure Active Directory](../active-directory-saas-app-provisioning.md).


## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che siano disponibili:

* Un tenant di Azure AD.
* Un tenant zScaler One.
* Un account utente in zScaler One con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione del provisioning Azure AD si basa sull'API zScaler One SCIM. Questa API è disponibile per zScaler uno sviluppatore per gli account con il pacchetto Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Aggiungere zScaler One da Azure Marketplace

Prima di configurare zScaler One per il provisioning utenti automatico con Azure AD, aggiungere zScaler One da Azure Marketplace al proprio elenco di applicazioni SaaS gestite.

Per aggiungere zScaler One dal Marketplace, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory**.

    ![Icona Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **zScaler One** e selezionare **zScaler One** nel pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Zscaler One nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Assegnare gli utenti a zScaler One

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare gli utenti che devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning utenti automatico, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, decidere quali utenti o gruppi in Azure AD necessario accedere a zScaler One. Per assegnare questi utenti o gruppi a zScaler One, seguire le istruzioni riportate in [assegnare un utente o un gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Suggerimenti importanti per l'assegnazione di utenti a zScaler One

* È consigliabile assegnare un singolo utente Azure AD a zScaler One per testare la configurazione del provisioning utenti automatico. È possibile assegnare altri utenti o gruppi in un secondo momento.

* Quando si assegna un utente a zScaler One, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurare il provisioning utenti automatico in zScaler One

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Usarlo per creare, aggiornare e disabilitare utenti o gruppi in zScaler uno in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È anche possibile abilitare l'accesso Single Sign-on basato su SAML per zScaler One. Seguire le istruzioni nell' [esercitazione sull'accesso Single Sign-on di zScaler One](zscaler-One-tutorial.md). Il Single Sign-on può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurare il provisioning utenti automatico per zScaler One in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **applicazioni** > aziendali**tutte le applicazioni** > **zScaler una**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Zscaler One**.

    ![Collegamento zScaler One nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![ZScaler un provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![ZScaler una modalità di provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Nella sezione **credenziali amministratore** compilare le caselle **URL tenant** e **token segreto** con le impostazioni per l'account zScaler One come descritto nel passaggio 6.

6. Per ottenere l'URL del tenant e il token segreto, passare ad **Amministrazione** > **impostazioni di autenticazione** nell'interfaccia utente del portale zScaler One. In **Authentication Type** (Tipo di autenticazione) selezionare **SAML**.

    ![ZScaler One Authentication Settings](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selezionare **Configure SAML** per aprire le opzioni **Configure SAML** .

    ![ZScaler One Configure SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selezionare **Abilita provisioning basato su SCIM** per ottenere le impostazioni nell' **URL di base** e nel **token di porta**. Quindi salvare le impostazioni. Copiare l'impostazione dell' **URL di base** in **url tenant** nel portale di Azure. Copiare l'impostazione del **token di porta** per il **token segreto** nell'portale di Azure.

7. Dopo aver compilato le caselle visualizzate nel passaggio 5, selezionare **Test connessione** per assicurarsi che Azure ad possa connettersi a zScaler One. Se la connessione non riesce, verificare che il zScaler un account disponga delle autorizzazioni di amministratore e riprovare.

    ![ZScaler una connessione di test](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Nella casella **posta elettronica di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche degli errori di provisioning. Selezionare la casella di controllo **Invia una notifica di posta elettronica quando si verifica un errore** .

    ![ZScaler un messaggio di posta elettronica di notifica](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selezionare **Salva**.

10. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory utenti a zScaler One**.

    ![ZScaler una sincronizzazione utente](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Esaminare gli attributi utente che vengono sincronizzati da Azure AD a zScaler uno nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in zScaler One per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![ZScaler un attributo utente corrispondente](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Nella sezione **mapping** selezionare **Sincronizza Azure Active Directory gruppi per zScaler uno**.

    ![ZScaler una sincronizzazione di gruppo](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Esaminare gli attributi di gruppo sincronizzati da Azure AD a zScaler uno nella sezione **mapping degli attributi** . Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in zScaler One per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![ZScaler un attributo di gruppo corrispondente](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Per configurare i filtri di ambito, seguire le istruzioni riportate nell'esercitazione relativa al [filtro di ambito](./../active-directory-saas-scoping-filters.md).

15. Per abilitare il servizio di provisioning Azure AD per zScaler One, nella sezione **Settings** impostare **stato** del provisioning **su on**.

    ![ZScaler uno stato di provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Definire gli utenti o i gruppi di cui si vuole eseguire il provisioning in zScaler One. Nella sezione **Impostazioni** selezionare i valori desiderati nell' **ambito**.

    ![ZScaler un ambito](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![ZScaler One Save](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o gruppi definiti nell' **ambito** nella sezione **Impostazioni** . La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti a condizione che venga eseguito il servizio di provisioning Azure AD. 

È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning. Il report descrive tutte le azioni eseguite dal servizio di provisioning Azure AD in zScaler One.

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
