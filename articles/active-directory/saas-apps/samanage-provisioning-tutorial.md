---
title: 'Esercitazione: Configurare Samanage per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per il provisioning e il deprovisioning automatico degli account utente in Samanage.Learn how to configure Azure Active Directory to automatically provision and deprovision user accounts to Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060491"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Esercitazione: Configurare Samanage per il provisioning utenti automatico

Questa esercitazione illustra i passaggi da eseguire in Samanage e Azure Active Directory (Azure AD) per configurare Azure AD per il provisioning e il deprovisioning automatico di utenti e gruppi in Samanage.This tutorial demonstrates the steps to perform in Samanage and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and deprovision users and groups to Samanage.

> [!NOTE]
> Questa esercitazione descrive un connettore basato sul servizio di provisioning degli utenti di Azure AD. Per informazioni sulle operazioni eseguite da questo servizio, sul suo funzionamento e sulle domande frequenti, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS (Software-as-A-Service) con Azure Active Directory.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questa esercitazione presuppone che si dispone di:The scenario outlined in this tutorial assumes that you have:

* Un tenant di Azure AD.
* Un [tenant Samanage](https://www.samanage.com/pricing/) con il pacchetto Professional.
* Un account utente in Samanage con autorizzazioni di amministratore.

> [!NOTE]
> L'integrazione di provisioning di Azure AD si basa [sull'API Samanage Rest.](https://www.samanage.com/api/) Questa API è disponibile per gli sviluppatori Samanage per gli account con il pacchetto Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Aggiungere Samanage da Azure MarketplaceAdd Samanage from the Azure Marketplace

Prima di configurare Samanage per il provisioning automatico degli utenti con Azure AD, aggiungere Samanage da Azure Marketplace all'elenco delle applicazioni SaaS gestite.

Per aggiungere Samanage dal Marketplace, attenersi alla seguente procedura.

1. Nel [portale di Azure,](https://portal.azure.com)nel riquadro di spostamento a sinistra, selezionare **Azure Active Directory.**

    ![Icona di Azure Active Directory](common/select-azuread.png)

2. Passare a **Applicazioni aziendali**, quindi selezionare Tutte **le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, immettere **Samanage** e selezionare **Samanage** dal pannello dei risultati. Per aggiungere l'applicazione, selezionare **Aggiungi**.

    ![Samanage nell'elenco risultati](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Assegnare utenti a SamanageAssign users to Samanage

Azure Active Directory usa un concetto denominato *assegnazioni* per determinare quali utenti devono ricevere l'accesso alle app selezionate. Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti o i gruppi assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning automatico degli utenti, decidere quali utenti o gruppi in Azure AD devono accedere a Samanage. Per assegnare questi utenti o gruppi a Samanage, seguire le istruzioni in [Assegnare un utente o un gruppo a un'app aziendale.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Suggerimenti importanti per l'assegnazione di utenti a Samanage

*    Oggi, i ruoli Samanage vengono popolati automaticamente e dinamicamente nell'interfaccia utente del portale di Azure.Today, Samanage roles are automatically and dynamically populated in the Azure portal UI. Prima di assegnare i ruoli Samanage agli utenti, assicurarsi che venga completata una sincronizzazione iniziale in Samanage per recuperare i ruoli più recenti nel tenant Samanage.

*    È consigliabile assegnare un singolo utente di Azure AD a Samanage per testare la configurazione iniziale del provisioning automatico degli utenti. È possibile assegnare altri utenti e gruppi in un secondo momento dopo che i test hanno esito positivo.

*    Quando si assegna un utente a Samanage, selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** sono esclusi dal provisioning.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurare il provisioning automatico degli utenti in SamanageConfigure automatic user provisioning to Samanage

Questa sezione illustra i passaggi per configurare il servizio di provisioning di Azure AD. Utilizzarlo per creare, aggiornare e disabilitare utenti o gruppi in Samanage in base alle assegnazioni di utenti o gruppi in Azure AD.

> [!TIP]
> È inoltre possibile abilitare l'accesso Single Sign-On basato su SAML per Samanage. Seguire le istruzioni [dell'esercitazione Single Sign-On di Samanage](samanage-tutorial.md). Single Sign-On può essere configurato indipendentemente dal provisioning automatico degli utenti, anche se queste due funzionalità si completano a vicenda.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurare il provisioning automatico degli utenti per Samanage in Azure ADConfigure automatic user provisioning for Samanage in Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . Selezionare **Applicazioni** > aziendali**Tutte le applicazioni** > **Samanage**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco di applicazioni selezionare **Samanage**.

    ![Il collegamento Samanage nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning.**

    ![Provisioning di Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Impostare la **modalità di provisioning** su **Automatico**.

    ![Scheda Provisioning](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'URL **tenant** Samanage e il **token segreto**. Fare clic su Test connessione per verificare che Azure AD possa connettersi a Samanage.Click **Test Connection** to ensure Azure AD can connect to Samanage. Se la connessione non riesce, verificare che l'account Samanage abbia autorizzazioni di amministratore e riprovare.

    ![Connessione di prova Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. Nella casella **Email di notifica** immettere l'indirizzo di posta elettronica della persona o del gruppo per ricevere le notifiche di errore di provisioning. Selezionare la casella di controllo **Invia una notifica tramite posta elettronica quando si verifica un errore.**

    ![E-mail di notifica Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare Sincronizza utenti di **Azure Active Directory da gestire**.

    ![Sincronizzazione utente Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Nella sezione **Mapping degli attributi** esaminare gli attributi utente che vengono sincronizzati da Azure AD a Samanage. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Samanage attributi utente corrispondenti](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Per consentire il mapping di gruppo, nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Samanage** (Sincronizza gruppi di Azure Active Directory con Samanage).

    ![Sincronizzazione dei gruppi Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. Impostare **Abilitato** su **Sì** per sincronizzare i gruppi. Esaminare gli attributi gruppo sincronizzati da Azure AD a Samanage nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Samanage per le operazioni di aggiornamento. Per salvare le modifiche, selezionare **Salva**.

    ![Attributi del gruppo corrispondente Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Per configurare i filtri di ambito, seguire le istruzioni nell'esercitazione sui [filtri di ambito.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Per abilitare il servizio di provisioning di Azure AD per Samanage, nella sezione **Impostazioni** modificare **Stato provisioning** su **Attivato**.

    ![Stato provisioning Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definire gli utenti o i gruppi di cui si desidera eseguire il provisioning in Samanage. Nella sezione **Impostazioni** selezionare i valori desiderati in **Ambito**. Quando si seleziona l'opzione Sincronizza tutti gli utenti e i **gruppi,** considerare le limitazioni come descritto nella sezione seguente "Limitazioni del connettore".

    ![Ambito Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Quando si è pronti per il provisioning, selezionare **Salva**.

    ![Salva Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Questa operazione avvia la sincronizzazione iniziale di tutti gli utenti o i gruppi definiti in **Ambito** nella sezione **Impostazioni.** L'esecuzione della sincronizzazione iniziale richiede più tempo rispetto alle sincronizzazioni successive. Si verificano approssimativamente ogni 40 minuti, purché venga eseguito il servizio di provisioning di Azure AD. 

È possibile utilizzare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e seguire i collegamenti al report attività di provisioning. The report describes all the actions performed by the Azure AD provisioning service on Samanage.

Per informazioni su come leggere i log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitazioni dei connettori

Se si seleziona l'opzione **Sincronizza tutti gli utenti e gruppi** e si configura un valore per l'attributo **Ruoli** sagesti, il valore nella casella Valore predefinito se **null (è facoltativo)** deve essere espresso nel formato seguente:

- ""nomeVisualizzato":"ruolo", dove ruolo è il valore predefinito desiderato.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestire il provisioning degli account utente per le app aziendaliManage user account provisioning for enterprise apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
